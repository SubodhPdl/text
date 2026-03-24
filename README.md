/**
 * @file tests/api.test.js
 * @description Basic API tests for the Books REST API
 * Run: node tests/api.test.js  (make sure API is running first on port 3001)
 */

const http = require('http');

let passed = 0;
let failed = 0;

// Helper to make HTTP requests
function request(path, method = 'GET', body = null) {
    return new Promise((resolve, reject) => {
        const options = {
            hostname: 'localhost',
            port: 3001,
            path: path,
            method: method,
            headers: { 'Content-Type': 'application/json' }
        };

        const req = http.request(options, res => {
            let data = '';
            res.on('data', chunk => data += chunk);
            res.on('end', () => {
                try {
                    resolve({ status: res.statusCode, body: JSON.parse(data) });
                } catch (e) {
                    resolve({ status: res.statusCode, body: data });
                }
            });
        });

        req.on('error', reject);
        if (body) req.write(JSON.stringify(body));
        req.end();
    });
}

// Helper to check results
function assert(testName, condition, detail = '') {
    if (condition) {
        console.log(`  PASS  ${testName}`);
        passed++;
    } else {
        console.log(`  FAIL  ${testName} ${detail}`);
        failed++;
    }
}

// ---- TESTS ----

async function runTests() {
    console.log('\n=============================');
    console.log('  Books API - Test Suite');
    console.log('=============================\n');

    // --- Root endpoint ---
    console.log('[ Root ]');
    const root = await request('/');
    assert('GET / returns 200', root.status === 200);
    assert('GET / returns welcome message', root.body.message !== undefined);

    // --- Books ---
    console.log('\n[ Books ]');
    const books = await request('/api/books');
    assert('GET /api/books returns 200', books.status === 200);
    assert('GET /api/books returns array', Array.isArray(books.body.books));
    assert('GET /api/books has 22 books', books.body.books.length >= 22);
    assert('Book has required fields', books.body.books[0].title !== undefined && books.body.books[0].author !== undefined);

    const singleBook = await request('/api/books/1');
    assert('GET /api/books/1 returns 200', singleBook.status === 200);
    assert('GET /api/books/1 returns correct book', singleBook.body.id === 1);

    const notFound = await request('/api/books/99999');
    assert('GET /api/books/99999 returns 404', notFound.status === 404);

    const searchBooks = await request('/api/books?search=harry');
    assert('GET /api/books?search=harry returns results', searchBooks.body.books.length >= 1);

    const genreFilter = await request('/api/books?genre=Fantasy');
    assert('GET /api/books?genre=Fantasy filters correctly', genreFilter.body.books.every(b => b.genre === 'Fantasy'));

    const ratingFilter = await request('/api/books?min_rating=4.5');
    assert('GET /api/books?min_rating=4.5 filters correctly', ratingFilter.body.books.every(b => b.rating >= 4.5));

    // POST - create book
    const newBook = await request('/api/books', 'POST', {
        title: 'Test Book',
        author_id: 1,
        genre_id: 1,
        published_year: 2024,
        rating: 4.0,
        pages: 200
    });
    assert('POST /api/books creates book (201)', newBook.status === 201);
    assert('POST /api/books returns new book id', newBook.body.id !== undefined);

    const newId = newBook.body.id;

    // PUT - update book
    const updated = await request(`/api/books/${newId}`, 'PUT', { title: 'Updated Test Book' });
    assert('PUT /api/books/:id updates book (200)', updated.status === 200);
    assert('PUT /api/books/:id title changed', updated.body.book.title === 'Updated Test Book');

    // DELETE - delete book
    const deleted = await request(`/api/books/${newId}`, 'DELETE');
    assert('DELETE /api/books/:id deletes book (200)', deleted.status === 200);

    // Confirm deleted
    const confirmDeleted = await request(`/api/books/${newId}`);
    assert('Deleted book returns 404', confirmDeleted.status === 404);

    // POST validation
    const badPost = await request('/api/books', 'POST', { title: '' });
    assert('POST with missing fields returns 400', badPost.status === 400);

    // --- Authors ---
    console.log('\n[ Authors ]');
    const authors = await request('/api/authors');
    assert('GET /api/authors returns 200', authors.status === 200);
    assert('GET /api/authors returns array', Array.isArray(authors.body.authors));
    assert('GET /api/authors has 8 authors', authors.body.authors.length >= 8);

    const singleAuthor = await request('/api/authors/1');
    assert('GET /api/authors/1 returns 200', singleAuthor.status === 200);
    assert('GET /api/authors/1 includes books array', Array.isArray(singleAuthor.body.books));

    const authorNotFound = await request('/api/authors/99999');
    assert('GET /api/authors/99999 returns 404', authorNotFound.status === 404);

    // --- Genres ---
    console.log('\n[ Genres ]');
    const genres = await request('/api/genres');
    assert('GET /api/genres returns 200', genres.status === 200);
    assert('GET /api/genres returns array', Array.isArray(genres.body.genres));
    assert('GET /api/genres has 8 genres', genres.body.genres.length >= 8);
    assert('Genre has book_count field', genres.body.genres[0].book_count !== undefined);

    const singleGenre = await request('/api/genres/1');
    assert('GET /api/genres/1 returns 200', singleGenre.status === 200);

    const genreNotFound = await request('/api/genres/99999');
    assert('GET /api/genres/99999 returns 404', genreNotFound.status === 404);

    // --- Summary ---
    console.log('\n=============================');
    console.log(`  Results: ${passed} passed, ${failed} failed`);
    console.log('=============================\n');

    if (failed > 0) process.exit(1);
}

runTests().catch(err => {
    console.error('Could not connect to API. Make sure npm start is running.\n', err.message);
    process.exit(1);
});
