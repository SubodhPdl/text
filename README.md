const express = require('express');
const router = express.Router();
const db = require('../db/database');

/**
 * @api {get} /api/authors Get all authors
 * @apiName GetAuthors
 * @apiGroup Authors
 * @apiVersion 1.0.0
 * @apiSuccess {Object[]} authors Array of author objects
 */
router.get('/', (req, res) => {
  try {
    const authors = db.prepare(`
      SELECT a.*, COUNT(b.id) as book_count
      FROM authors a
      LEFT JOIN books b ON a.id = b.author_id
      GROUP BY a.id
      ORDER BY a.name ASC
    `).all();
    res.json({ count: authors.length, authors });
  } catch (err) {
    res.status(500).json({ error: 'Database error', message: err.message });
  }
});

/**
 * @api {get} /api/authors/:id Get single author with their books
 * @apiName GetAuthor
 * @apiGroup Authors
 * @apiVersion 1.0.0
 * @apiParam {Number} id Author unique ID
 */
router.get('/:id', (req, res) => {
  try {
    const author = db.prepare('SELECT * FROM authors WHERE id = ?').get(req.params.id);
    if (!author) return res.status(404).json({ error: 'Author not found' });

    const books = db.prepare(`
      SELECT b.id, b.title, g.name AS genre, b.published_year, b.rating
      FROM books b
      JOIN genres g ON b.genre_id = g.id
      WHERE b.author_id = ?
      ORDER BY b.published_year ASC
    `).all(req.params.id);

    res.json({ ...author, books });
  } catch (err) {
    res.status(500).json({ error: 'Database error', message: err.message });
  }
});

/**
 * @api {post} /api/authors Create a new author
 * @apiName CreateAuthor
 * @apiGroup Authors
 * @apiVersion 1.0.0
 * @apiBody {String} name Author name (required)
 * @apiBody {String} [nationality] Nationality
 * @apiBody {Number} [birth_year] Birth year
 * @apiBody {String} [bio] Biography
 */
router.post('/', (req, res) => {
  try {
    if (!req.body.name || req.body.name.trim() === '') {
      return res.status(400).json({ error: 'Author name is required' });
    }
    const result = db.prepare(
      'INSERT INTO authors (name, nationality, birth_year, bio) VALUES (?, ?, ?, ?)'
    ).run(
      req.body.name.trim(),
      req.body.nationality || null,
      req.body.birth_year || null,
      req.body.bio || null
    );
    const newAuthor = db.prepare('SELECT * FROM authors WHERE id = ?').get(result.lastInsertRowid);
    res.status(201).json({ message: 'Author created successfully', author: newAuthor });
  } catch (err) {
    res.status(500).json({ error: 'Database error', message: err.message });
  }
});

/**
 * @api {put} /api/authors/:id Update an author
 * @apiName UpdateAuthor
 * @apiGroup Authors
 * @apiVersion 1.0.0
 * @apiParam {Number} id Author unique ID
 */
router.put('/:id', (req, res) => {
  try {
    const existing = db.prepare('SELECT * FROM authors WHERE id = ?').get(req.params.id);
    if (!existing) return res.status(404).json({ error: 'Author not found' });

    const fields = ['name', 'nationality', 'birth_year', 'bio'];
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
    db.prepare(`UPDATE authors SET ${updates.join(', ')} WHERE id = ?`).run(...values);
    const updated = db.prepare('SELECT * FROM authors WHERE id = ?').get(req.params.id);
    res.json({ message: 'Author updated successfully', author: updated });
  } catch (err) {
    res.status(500).json({ error: 'Database error', message: err.message });
  }
});

/**
 * @api {delete} /api/authors/:id Delete an author
 * @apiName DeleteAuthor
 * @apiGroup Authors
 * @apiVersion 1.0.0
 * @apiParam {Number} id Author unique ID
 */
router.delete('/:id', (req, res) => {
  try {
    const existing = db.prepare('SELECT name FROM authors WHERE id = ?').get(req.params.id);
    if (!existing) return res.status(404).json({ error: 'Author not found' });
    db.prepare('DELETE FROM authors WHERE id = ?').run(req.params.id);
    res.json({ message: `Author "${existing.name}" deleted successfully` });
  } catch (err) {
    res.status(500).json({ error: 'Database error', message: err.message });
  }
});

module.exports = router;
