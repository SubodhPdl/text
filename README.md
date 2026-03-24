<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Books Library</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: #eef2f7;
            margin: 0;
            padding: 0;
            color: #222;
        }

        header {
            background: #1a3c6e;
            color: white;
            padding: 14px 24px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        header h1 {
            margin: 0;
            font-size: 20px;
        }

        header small {
            display: block;
            font-size: 12px;
            color: #ccc;
            margin-top: 3px;
        }

        #statusText {
            font-size: 13px;
            color: #ffdd57;
        }

        .container {
            max-width: 1100px;
            margin: 20px auto;
            padding: 0 16px;
        }

        /* Stats */
        .stats {
            display: flex;
            gap: 14px;
            margin-bottom: 18px;
            flex-wrap: wrap;
        }

        .stat-card {
            background: white;
            border: 1px solid #ccc;
            border-radius: 6px;
            padding: 14px 22px;
            text-align: center;
            min-width: 110px;
        }

        .stat-card h2 {
            margin: 0;
            font-size: 26px;
            color: #1a3c6e;
        }

        .stat-card p {
            margin: 4px 0 0;
            font-size: 12px;
            color: #666;
        }

        /* Controls */
        .controls {
            background: white;
            border: 1px solid #ccc;
            border-radius: 6px;
            padding: 12px 16px;
            margin-bottom: 20px;
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            align-items: center;
        }

        .controls input[type="text"] {
            padding: 8px 12px;
            border: 1px solid #bbb;
            border-radius: 4px;
            font-size: 14px;
            width: 240px;
        }

        .controls select {
            padding: 8px 10px;
            border: 1px solid #bbb;
            border-radius: 4px;
            font-size: 14px;
            background: white;
        }

        .btn-add {
            background: #1a3c6e;
            color: white;
            border: none;
            padding: 8px 16px;
            border-radius: 4px;
            font-size: 14px;
            cursor: pointer;
        }

        .btn-add:hover { background: #15305a; }

        /* Book grid */
        .books-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(230px, 1fr));
            gap: 16px;
        }

        .book-card {
            background: white;
            border: 1px solid #ccc;
            border-radius: 6px;
            overflow: hidden;
            cursor: pointer;
        }

        .book-card:hover {
            border-color: #1a3c6e;
        }

        .book-top {
            background: #1a3c6e;
            color: white;
            padding: 18px 14px 14px;
        }

        .book-top .book-title {
            font-size: 14px;
            font-weight: bold;
            margin-bottom: 5px;
            line-height: 1.3;
        }

        .book-top .book-author {
            font-size: 12px;
            color: #b0c4de;
        }

        .book-body {
            padding: 12px 14px;
        }

        .book-body .genre-tag {
            display: inline-block;
            background: #e8eef7;
            color: #1a3c6e;
            font-size: 11px;
            padding: 3px 8px;
            border-radius: 3px;
            margin-bottom: 8px;
        }

        .book-body .book-info {
            font-size: 12px;
            color: #555;
            margin-bottom: 4px;
        }

        .badge {
            display: inline-block;
            font-size: 11px;
            padding: 2px 8px;
            border-radius: 3px;
            color: white;
            margin-top: 6px;
        }

        .badge.available   { background: #2e7d32; }
        .badge.unavailable { background: #c62828; }

        .book-footer {
            border-top: 1px solid #eee;
            padding: 8px 14px;
            display: flex;
            gap: 8px;
        }

        .btn-edit {
            background: #1a3c6e;
            color: white;
            border: none;
            padding: 5px 12px;
            border-radius: 3px;
            font-size: 12px;
            cursor: pointer;
        }

        .btn-delete {
            background: #c62828;
            color: white;
            border: none;
            padding: 5px 12px;
            border-radius: 3px;
            font-size: 12px;
            cursor: pointer;
        }

        .empty-msg {
            text-align: center;
            padding: 50px;
            color: #777;
            background: white;
            border-radius: 6px;
            border: 1px solid #ccc;
        }

        /* Modal */
        .modal-bg {
            display: none;
            position: fixed;
            top: 0; left: 0;
            width: 100%; height: 100%;
            background: rgba(0,0,0,0.45);
            z-index: 999;
            justify-content: center;
            align-items: flex-start;
            padding-top: 40px;
        }

        .modal-bg.open { display: flex; }

        .modal-box {
            background: white;
            border-radius: 6px;
            border: 1px solid #aaa;
            padding: 24px;
            width: 470px;
            max-height: 90vh;
            overflow-y: auto;
        }

        .modal-box h2 {
            margin: 0 0 16px;
            font-size: 17px;
            color: #1a3c6e;
            border-bottom: 2px solid #1a3c6e;
            padding-bottom: 8px;
        }

        .form-row {
            display: flex;
            gap: 12px;
        }

        .form-group {
            margin-bottom: 12px;
            flex: 1;
        }

        .form-group label {
            display: block;
            font-size: 13px;
            font-weight: bold;
            margin-bottom: 4px;
            color: #333;
        }

        .form-group input,
        .form-group select,
        .form-group textarea {
            width: 100%;
            padding: 7px 10px;
            border: 1px solid #bbb;
            border-radius: 4px;
            font-size: 13px;
            box-sizing: border-box;
            font-family: Arial, sans-serif;
        }

        .modal-actions {
            margin-top: 16px;
            display: flex;
            justify-content: flex-end;
            gap: 8px;
        }

        .btn-cancel {
            background: #f0f0f0;
            color: #333;
            border: 1px solid #bbb;
            padding: 7px 16px;
            border-radius: 4px;
            font-size: 13px;
            cursor: pointer;
        }

        .btn-save {
            background: #2e7d32;
            color: white;
            border: none;
            padding: 7px 16px;
            border-radius: 4px;
            font-size: 13px;
            cursor: pointer;
        }

        /* Detail modal */
        .detail-table {
            width: 100%;
            border-collapse: collapse;
            margin-bottom: 12px;
            font-size: 13px;
        }

        .detail-table td {
            padding: 7px 10px;
            border: 1px solid #ddd;
        }

        .detail-table td:first-child {
            font-weight: bold;
            background: #f5f7fa;
            width: 100px;
            color: #333;
        }

        .synopsis-box {
            background: #f5f7fa;
            border: 1px solid #ddd;
            border-radius: 4px;
            padding: 10px 12px;
            font-size: 13px;
            line-height: 1.6;
            color: #444;
            margin-bottom: 12px;
        }

        /* Toast */
        #toast {
            position: fixed;
            bottom: 22px;
            right: 22px;
            padding: 11px 20px;
            color: white;
            font-size: 13px;
            border-radius: 4px;
            display: none;
            z-index: 9999;
        }

        #toast.success { background: #2e7d32; }
        #toast.error   { background: #c62828; }
        #toast.info    { background: #1a3c6e; }
    </style>
</head>
<body>

<header>
    <div>
        <h1>Books Library</h1>
        <small>CET252 — Books REST API Client</small>
    </div>
    <span id="statusText">Connecting...</span>
</header>

<div class="container">

    <!-- Stats -->
    <div class="stats">
        <div class="stat-card">
            <h2 id="totalBooks">—</h2>
            <p>Total Books</p>
        </div>
        <div class="stat-card">
            <h2 id="totalAuthors">—</h2>
            <p>Authors</p>
        </div>
        <div class="stat-card">
            <h2 id="totalGenres">—</h2>
            <p>Genres</p>
        </div>
        <div class="stat-card">
            <h2 id="avgRating">—</h2>
            <p>Avg Rating</p>
        </div>
    </div>

    <!-- Controls -->
    <div class="controls">
        <input type="text" id="searchInput" placeholder="Search by title or author..." oninput="filterBooks()">
        <select id="genreFilter" onchange="filterBooks()">
            <option value="">All Genres</option>
        </select>
        <select id="ratingFilter" onchange="filterBooks()">
            <option value="">All Ratings</option>
            <option value="4.5">4.5 and above</option>
            <option value="4">4.0 and above</option>
            <option value="3">3.0 and above</option>
        </select>
        <button class="btn-add" onclick="openAddModal()">+ Add Book</button>
    </div>

    <!-- Books Grid -->
    <div id="booksContainer">
        <p class="empty-msg">Loading books...</p>
    </div>
</div>

<!-- Add/Edit Modal -->
<div class="modal-bg" id="bookModal">
    <div class="modal-box">
        <h2 id="modalTitle">Add New Book</h2>

        <div class="form-group">
            <label>Title *</label>
            <input type="text" id="bookTitle" placeholder="Enter book title">
        </div>
        <div class="form-row">
            <div class="form-group">
                <label>Author *</label>
                <select id="bookAuthor">
                    <option value="">Select author</option>
                </select>
            </div>
            <div class="form-group">
                <label>Genre *</label>
                <select id="bookGenre">
                    <option value="">Select genre</option>
                </select>
            </div>
        </div>
        <div class="form-row">
            <div class="form-group">
                <label>ISBN</label>
                <input type="text" id="bookIsbn" placeholder="e.g. 978-...">
            </div>
            <div class="form-group">
                <label>Published Year</label>
                <input type="number" id="bookYear" placeholder="e.g. 2023">
            </div>
        </div>
        <div class="form-row">
            <div class="form-group">
                <label>Rating (0-5)</label>
                <input type="number" id="bookRating" placeholder="e.g. 4.5" min="0" max="5" step="0.1">
            </div>
            <div class="form-group">
                <label>Pages</label>
                <input type="number" id="bookPages" placeholder="e.g. 300">
            </div>
        </div>
        <div class="form-group">
            <label>Synopsis</label>
            <textarea id="bookSynopsis" rows="3" placeholder="Short description..."></textarea>
        </div>
        <div class="form-group">
            <label>
                <input type="checkbox" id="bookAvailable" checked>
                Available to borrow
            </label>
        </div>
        <input type="hidden" id="editingBookId">

        <div class="modal-actions">
            <button class="btn-cancel" onclick="closeModal()">Cancel</button>
            <button class="btn-save" onclick="saveBook()">Save Book</button>
        </div>
    </div>
</div>

<!-- Detail Modal -->
<div class="modal-bg" id="detailModal">
    <div class="modal-box" style="width:520px;">
        <h2>Book Details</h2>
        <div id="detailContent"></div>
        <div class="modal-actions">
            <button class="btn-cancel" onclick="closeDetailModal()">Close</button>
            <button class="btn-edit" id="detailEditBtn">Edit</button>
            <button class="btn-delete" id="detailDeleteBtn">Delete</button>
        </div>
    </div>
</div>

<div id="toast"></div>

<script>
const API = 'http://localhost:3001/api';
let allBooks = [], allAuthors = [], allGenres = [];

async function apiFetch(path, options = {}) {
    const res = await fetch(API + path, {
        headers: { 'Content-Type': 'application/json' },
        ...options
    });
    return res.json().then(data => ({ ok: res.ok, status: res.status, data }));
}

async function init() {
    try {
        const [booksRes, authorsRes, genresRes] = await Promise.all([
            apiFetch('/books'),
            apiFetch('/authors'),
            apiFetch('/genres')
        ]);

        if (!booksRes.ok) throw new Error();

        allBooks   = booksRes.data.books     || [];
        allAuthors = authorsRes.data.authors  || [];
        allGenres  = genresRes.data.genres    || [];

        document.getElementById('statusText').textContent = 'Connected';
        document.getElementById('statusText').style.color = '#90ee90';

        document.getElementById('totalBooks').textContent   = allBooks.length;
        document.getElementById('totalAuthors').textContent = allAuthors.length;
        document.getElementById('totalGenres').textContent  = allGenres.length;
        const avg = allBooks.reduce((s, b) => s + (b.rating || 0), 0) / allBooks.length;
        document.getElementById('avgRating').textContent    = avg.toFixed(1);

        const gf = document.getElementById('genreFilter');
        allGenres.forEach(g => gf.innerHTML += `<option value="${g.name}">${g.name}</option>`);

        allAuthors.forEach(a => document.getElementById('bookAuthor').innerHTML += `<option value="${a.id}">${a.name}</option>`);
        allGenres.forEach(g  => document.getElementById('bookGenre').innerHTML  += `<option value="${g.id}">${g.name}</option>`);

        renderBooks(allBooks);
    } catch (err) {
        document.getElementById('statusText').textContent = 'API Offline';
        document.getElementById('statusText').style.color = '#ff6b6b';
        document.getElementById('booksContainer').innerHTML = `<p class="empty-msg">Cannot connect to API at localhost:3001.<br>Run <strong>npm start</strong> in the api folder first.</p>`;
    }
}

function renderBooks(books) {
    const container = document.getElementById('booksContainer');
    if (books.length === 0) {
        container.innerHTML = `<p class="empty-msg">No books found. Try changing your search or filters.</p>`;
        return;
    }
    container.innerHTML = `<div class="books-grid">${books.map(bookCard).join('')}</div>`;
}

function bookCard(b) {
    return `
        <div class="book-card" onclick="showDetail(${b.id})">
            <div class="book-top">
                <div class="book-title">${b.title}</div>
                <div class="book-author">by ${b.author}</div>
            </div>
            <div class="book-body">
                <span class="genre-tag">${b.genre}</span>
                <div class="book-info">Year: ${b.published_year || 'N/A'} &nbsp;|&nbsp; Pages: ${b.pages || 'N/A'}</div>
                <div class="book-info">Rating: ${b.rating ? b.rating.toFixed(1) + ' / 5' : 'Not rated'}</div>
                <span class="badge ${b.available ? 'available' : 'unavailable'}">${b.available ? 'Available' : 'Borrowed'}</span>
            </div>
            <div class="book-footer" onclick="event.stopPropagation()">
                <button class="btn-edit"   onclick="editBook(${b.id})">Edit</button>
                <button class="btn-delete" onclick="deleteBook(${b.id}, '${b.title.replace(/'/g, "\\'")}')">Delete</button>
            </div>
        </div>`;
}

function filterBooks() {
    const search = document.getElementById('searchInput').value.toLowerCase();
    const genre  = document.getElementById('genreFilter').value;
    const rating = parseFloat(document.getElementById('ratingFilter').value) || 0;
    const filtered = allBooks.filter(b => {
        return (!search || b.title.toLowerCase().includes(search) || b.author.toLowerCase().includes(search))
            && (!genre  || b.genre === genre)
            && (!rating || b.rating >= rating);
    });
    renderBooks(filtered);
}

async function showDetail(id) {
    const res = await apiFetch(`/books/${id}`);
    if (!res.ok) return showToast('Could not load book', 'error');
    const b = res.data;
    document.getElementById('detailContent').innerHTML = `
        <table class="detail-table">
            <tr><td>Title</td><td>${b.title}</td></tr>
            <tr><td>Author</td><td>${b.author}</td></tr>
            <tr><td>Genre</td><td>${b.genre}</td></tr>
            <tr><td>Year</td><td>${b.published_year || 'N/A'}</td></tr>
            <tr><td>Rating</td><td>${b.rating || 'N/A'}</td></tr>
            <tr><td>Pages</td><td>${b.pages || 'N/A'}</td></tr>
            <tr><td>ISBN</td><td>${b.isbn || 'N/A'}</td></tr>
            <tr><td>Status</td><td>${b.available ? 'Available' : 'Borrowed'}</td></tr>
        </table>
        ${b.synopsis ? `<div class="synopsis-box"><strong>Synopsis:</strong><br><br>${b.synopsis}</div>` : ''}`;
    document.getElementById('detailEditBtn').onclick   = () => { closeDetailModal(); editBook(id); };
    document.getElementById('detailDeleteBtn').onclick = () => { closeDetailModal(); deleteBook(id, b.title); };
    document.getElementById('detailModal').classList.add('open');
}

function closeDetailModal() { document.getElementById('detailModal').classList.remove('open'); }

function openAddModal() {
    document.getElementById('modalTitle').textContent    = 'Add New Book';
    document.getElementById('editingBookId').value       = '';
    ['bookTitle','bookIsbn','bookYear','bookRating','bookPages','bookSynopsis'].forEach(id => document.getElementById(id).value = '');
    document.getElementById('bookAuthor').value          = '';
    document.getElementById('bookGenre').value           = '';
    document.getElementById('bookAvailable').checked     = true;
    document.getElementById('bookModal').classList.add('open');
}

async function editBook(id) {
    const res = await apiFetch(`/books/${id}`);
    if (!res.ok) return showToast('Could not load book', 'error');
    const b = res.data;
    document.getElementById('modalTitle').textContent   = 'Edit Book';
    document.getElementById('editingBookId').value      = b.id;
    document.getElementById('bookTitle').value          = b.title;
    document.getElementById('bookAuthor').value         = b.author_id;
    document.getElementById('bookGenre').value          = b.genre_id;
    document.getElementById('bookIsbn').value           = b.isbn || '';
    document.getElementById('bookYear').value           = b.published_year || '';
    document.getElementById('bookRating').value         = b.rating || '';
    document.getElementById('bookPages').value          = b.pages || '';
    document.getElementById('bookSynopsis').value       = b.synopsis || '';
    document.getElementById('bookAvailable').checked    = !!b.available;
    document.getElementById('bookModal').classList.add('open');
}

function closeModal() { document.getElementById('bookModal').classList.remove('open'); }

async function saveBook() {
    const id   = document.getElementById('editingBookId').value;
    const body = {
        title:          document.getElementById('bookTitle').value.trim(),
        author_id:      parseInt(document.getElementById('bookAuthor').value),
        genre_id:       parseInt(document.getElementById('bookGenre').value),
        isbn:           document.getElementById('bookIsbn').value.trim() || null,
        published_year: parseInt(document.getElementById('bookYear').value) || null,
        rating:         parseFloat(document.getElementById('bookRating').value) || 0,
        pages:          parseInt(document.getElementById('bookPages').value) || null,
        synopsis:       document.getElementById('bookSynopsis').value.trim() || null,
        available:      document.getElementById('bookAvailable').checked ? 1 : 0
    };
    if (!body.title)     return showToast('Title is required', 'error');
    if (!body.author_id) return showToast('Please select an author', 'error');
    if (!body.genre_id)  return showToast('Please select a genre', 'error');

    const res = await apiFetch(id ? `/books/${id}` : '/books', {
        method: id ? 'PUT' : 'POST',
        body: JSON.stringify(body)
    });
    if (res.ok) {
        showToast(id ? 'Book updated!' : 'Book added!', 'success');
        closeModal();
        await refreshBooks();
    } else {
        showToast(res.data.error || 'Failed to save', 'error');
    }
}

async function deleteBook(id, title) {
    if (!confirm('Delete "' + title + '"?')) return;
    const res = await apiFetch(`/books/${id}`, { method: 'DELETE' });
    if (res.ok) { showToast('Book deleted', 'success'); await refreshBooks(); }
    else showToast('Failed to delete', 'error');
}

async function refreshBooks() {
    const res = await apiFetch('/books');
    if (res.ok) {
        allBooks = res.data.books;
        document.getElementById('totalBooks').textContent = allBooks.length;
        const avg = allBooks.reduce((s, b) => s + (b.rating || 0), 0) / allBooks.length;
        document.getElementById('avgRating').textContent = avg.toFixed(1);
        filterBooks();
    }
}

function showToast(msg, type = 'info') {
    const t = document.getElementById('toast');
    t.textContent = msg;
    t.className   = type;
    t.style.display = 'block';
    setTimeout(() => t.style.display = 'none', 3000);
}

document.getElementById('bookModal').addEventListener('click',   e => { if (e.target === e.currentTarget) closeModal(); });
document.getElementById('detailModal').addEventListener('click', e => { if (e.target === e.currentTarget) closeDetailModal(); });

init();
</script>
</body>
</html>
