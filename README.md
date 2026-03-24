const express = require('express');
const router = express.Router();
const db = require('../db/database');

/**
 * @api {get} /api/genres Get all genres
 * @apiName GetGenres
 * @apiGroup Genres
 * @apiVersion 1.0.0
 * @apiSuccess {Object[]} genres Array of genre objects with book counts
 */
router.get('/', (req, res) => {
  try {
    const genres = db.prepare(`
      SELECT g.*, COUNT(b.id) as book_count
      FROM genres g
      LEFT JOIN books b ON g.id = b.genre_id
      GROUP BY g.id
      ORDER BY g.name ASC
    `).all();
    res.json({ count: genres.length, genres });
  } catch (err) {
    res.status(500).json({ error: 'Database error', message: err.message });
  }
});

/**
 * @api {post} /api/genres Create a new genre
 * @apiName CreateGenre
 * @apiGroup Genres
 * @apiVersion 1.0.0
 * @apiBody {String} name Genre name (required)
 * @apiBody {String} [description] Genre description
 */
router.post('/', (req, res) => {
  try {
    if (!req.body.name || req.body.name.trim() === '') {
      return res.status(400).json({ error: 'Genre name is required' });
    }
    const result = db.prepare(
      'INSERT INTO genres (name, description) VALUES (?, ?)'
    ).run(req.body.name.trim(), req.body.description || null);
    const newGenre = db.prepare('SELECT * FROM genres WHERE id = ?').get(result.lastInsertRowid);
    res.status(201).json({ message: 'Genre created successfully', genre: newGenre });
  } catch (err) {
    if (err.message.includes('UNIQUE constraint')) {
      return res.status(409).json({ error: 'Genre already exists' });
    }
    res.status(500).json({ error: 'Database error', message: err.message });
  }
});

/**
 * @api {put} /api/genres/:id Update a genre
 * @apiName UpdateGenre
 * @apiGroup Genres
 * @apiVersion 1.0.0
 * @apiParam {Number} id Genre unique ID
 */
router.put('/:id', (req, res) => {
  try {
    const existing = db.prepare('SELECT * FROM genres WHERE id = ?').get(req.params.id);
    if (!existing) return res.status(404).json({ error: 'Genre not found' });

    const fields = ['name', 'description'];
    const updates = [];
    const values = [];
    fields.forEach(field => {
      if (req.body[field] !== undefined) {
        updates.push(`${field} = ?`);
        values.push(req.body[field]);
      }
    });
    if (updates.length === 0) return res.status(400).json({ error: 'No fields to update' });

    values.push(req.params.id);
    db.prepare(`UPDATE genres SET ${updates.join(', ')} WHERE id = ?`).run(...values);
    const updated = db.prepare('SELECT * FROM genres WHERE id = ?').get(req.params.id);
    res.json({ message: 'Genre updated successfully', genre: updated });
  } catch (err) {
    res.status(500).json({ error: 'Database error', message: err.message });
  }
});

/**
 * @api {delete} /api/genres/:id Delete a genre
 * @apiName DeleteGenre
 * @apiGroup Genres
 * @apiVersion 1.0.0
 * @apiParam {Number} id Genre unique ID
 */
router.delete('/:id', (req, res) => {
  try {
    const existing = db.prepare('SELECT name FROM genres WHERE id = ?').get(req.params.id);
    if (!existing) return res.status(404).json({ error: 'Genre not found' });
    db.prepare('DELETE FROM genres WHERE id = ?').run(req.params.id);
    res.json({ message: `Genre "${existing.name}" deleted successfully` });
  } catch (err) {
    res.status(500).json({ error: 'Database error', message: err.message });
  }
});

module.exports = router;
