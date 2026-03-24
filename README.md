<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Books Library</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f0f0f0;
        }

        header {
            background-color: #003366;
            color: white;
            padding: 15px 20px;
        }

        header h1 {
            margin: 0;
            font-size: 24px;
        }

        header p {
            margin: 4px 0 0 0;
            font-size: 13px;
        }

        #statusText {
            font-size: 13px;
            color: #ffdd57;
        }

        .container {
            max-width: 1100px;
            margin: 20px auto;
            padding: 0 15px;
        }

        /* Stats */
        .stats {
            display: flex;
            gap: 15px;
            margin-bottom: 20px;
            flex-wrap: wrap;
        }

        .stat-box {
            background: white;
            border: 1px solid #ccc;
            padding: 12px 20px;
            text-align: center;
            width: 130px;
        }

        .stat-box h2 {
            margin: 0;
            font-size: 28px;
            color: #003366;
        }

        .stat-box p {
            margin: 4px 0 0;
            font-size: 12px;
            color: #555;
        }

        /* Controls */
        .controls {
            background: white;
            border: 1px solid #ccc;
            padding: 12px 15px;
            margin-bottom: 20px;
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            align-items: center;
        }

        .controls input[type="text"],
        .controls select {
            padding: 7px 10px;
            border: 1px solid #aaa;
            font-size: 14px;
        }

        .controls input[type="text"] {
            width: 250px;
        }

        button {
            padding: 7px 14px;
            font-size: 14px;
            cursor: pointer;
        }

        .btn-add {
            background-color: #003366;
            color: white;
            border: none;
        }

        .btn-edit {
            background-color: #0066cc;
            color: white;
            border: none;
            font-size: 12px;
            padding: 5px 10px;
        }

        .btn-delete {
            background-color: #cc0000;
            color: white;
            border: none;
            font-size: 12px;
            padding: 5px 10px;
        }

        .btn-close {
            background-color: #888;
            color: white;
            border: none;
        }

        .btn-save {
            background-color: #006600;
            color: white;
            border: none;
        }

        /* Book table */
        table {
            width: 100%;
            border-collapse: collapse;
            background: white;
        }

        table th {
            background-color: #003366;
            color: white;
            padding: 10px;
            text-align: left;
            font-size: 14px;
        }

        table td {
            padding: 9px 10px;
            border-bottom: 1px solid #ddd;
            font-size: 13px;
            vertical-align: middle;
        }

        table tr:hover {
            background-color: #f5f5f5;
            cursor: pointer;
        }

        .badge-available {
            background: green;
            color: white;
            padding: 2px 7px;
            font-size: 11px;
            border-radius: 3px;
        }

        .badge-unavailable {
            background: red;
            color: white;
            padding: 2px 7px;
            font-size: 11px;
            border-radius: 3px;
        }

        .empty-msg {
            text-align: center;
            padding: 40px;
            color: #777;
            background: white;
        }

        /* Modal */
        .modal-bg {
            display: none;
            position: fixed;
            top: 0; left: 0;
            width: 100%; height: 100%;
            background: rgba(0,0,0,0.5);
            z-index: 999;
            justify-content: center;
            align-items: flex-start;
            padding-top: 50px;
        }

        .modal-bg.open {
            display: flex;
        }

        .modal-box {
            background: white;
            border: 2px solid #003366;
            padding: 25px;
            width: 480px;
            max-height: 90vh;
            overflow-y: auto;
        }

        .modal-box h2 {
            margin-top: 0;
            font-size: 18px;
            color: #003366;
            border-bottom: 1px solid #ccc;
            padding-bottom: 10px;
        }

        .form-row {
            display: flex;
            gap: 15px;
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
        }

        .form-group input,
        .form-group select,
        .form-group textarea {
            width: 100%;
            padding: 7px 9px;
            border: 1px solid #aaa;
            font-size: 13px;
            box-sizing: border-box;
            font-family: Arial, sans-serif;
        }

        .modal-actions {
            margin-top: 15px;
            text-align: right;
            display: flex;
            gap: 8px;
            justify-content: flex-end;
        }

        /* Detail modal */
        .detail-table {
            width: 100%;
            border-collapse: collapse;
            margin-bottom: 12px;
        }

        .detail-table td {
            padding: 7px 10px;
            border: 1px solid #ddd;
            font-size: 13px;
        }

        .detail-table td:first-child {
            font-weight: bold;
            background: #f0f0f0;
            width: 110px;
        }

        .synopsis-box {
            background: #f9f9f9;
            border: 1px solid #ddd;
            padding: 10px;
            font-size: 13px;
            line-height: 1.5;
            margin-bottom: 12px;
        }

        /* Toast */
        #toast {
            position: fixed;
            bottom: 20px;
            right: 20px;
            padding: 12px 20px;
            color: white;
            font-size: 14px;
            display: none;
            z-index: 9999;
            border-radius: 3px;
        }

        #toast.success { background: #006600; }
        #toast.error { background: #cc0000; }
        #toast.info { background: #003366; }
    </style>
</head>
<body>

<header>
    <h1>Books Library</h1>
    <p>CET252 — Books REST API Client &nbsp;|&nbsp; <span id="statusText">Connecting to API...</span></p>
</header>

<div class="container">

    <!-- Stats -->
    <div class="stats">
        <div class="stat-box">
            <h2 id="totalBooks">—</h2>
            <p>Total Books</p>
        </div>
        <div class="stat-box">
            <h2 id="totalAuthors">—</h2>
            <p>Authors</p>
        </div>
        <div class="stat-box">
            <h2 id="totalGenres">—</h2>
            <p>Genres</p>
        </div>
        <div class="stat-box">
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

    <!-- Books Table -->
    <div id="booksContainer">
        <p style="padding:20px; background:white;">Loading books...</p>
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
            <button class="btn-close" onclick="closeModal()">Cancel</button>
            <button class="btn-save" onclick="saveBook()">Save Book</button>
        </div>
    </div>
</div>

<!-- Detail Modal -->
<div class="modal-bg" id="detailModal">
    <div class="modal-box" style="width: 540px;">
        <h2>Book Details</h2>
        <div id="detailContent"></div>
        <div class="modal-actions">
            <button class="btn-close" onclick="closeDetailModal()">Close</button>
            <button class="btn-edit" id="detailEditBtn">Edit</button>
            <button class="btn-delete" id="detailDeleteBtn">Delete</button>
        </div>
    </div>
</div>

<!-- Toast -->
<div id="toast"></div>

<script>
const API = 'http://localhost:3001/api';
let allBooks = [];
let allAuthors = [];
let allGenres = [];

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

        if (!booksRes.ok) throw new Error('API not reachable');

        allBooks   = booksRes.data.books    || [];
        allAuthors = authorsRes.data.authors || [];
        allGenres  = genresRes.data.genres   || [];

        document.getElementById('statusText').textContent = 'API Connected';
        document.getElementById('statusText').style.color = '#90ee90';

        document.getElementById('totalBooks').textContent   = allBooks.length;
        document.getElementById('totalAuthors').textContent = allAuthors.length;
        document.getElementById('totalGenres').textContent  = allGenres.length;
        const avg = allBooks.reduce((s, b) => s + (b.rating || 0), 0) / allBooks.length;
        document.getElementById('avgRating').textContent    = avg.toFixed(1);

        const gf = document.getElementById('genreFilter');
        allGenres.forEach(g => gf.innerHTML += `<option value="${g.name}">${g.name}</option>`);

        const authorSel = document.getElementById('bookAuthor');
        allAuthors.forEach(a => authorSel.innerHTML += `<option value="${a.id}">${a.name}</option>`);

        const genreSel = document.getElementById('bookGenre');
        allGenres.forEach(g => genreSel.innerHTML += `<option value="${g.id}">${g.name}</option>`);

        renderBooks(allBooks);
    } catch (err) {
        document.getElementById('statusText').textContent = 'API Offline - Start the API server first';
        document.getElementById('booksContainer').innerHTML = `<p class="empty-msg">Cannot connect to API at localhost:3001. Run npm start in the API folder.</p>`;
    }
}

function renderBooks(books) {
    const container = document.getElementById('booksContainer');
    if (books.length === 0) {
        container.innerHTML = `<p class="empty-msg">No books found. Try changing the search or filters.</p>`;
        return;
    }

    let html = `
        <table>
            <thead>
                <tr>
                    <th>#</th>
                    <th>Title</th>
                    <th>Author</th>
                    <th>Genre</th>
                    <th>Year</th>
                    <th>Rating</th>
                    <th>Status</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody>
    `;

    books.forEach((b, i) => {
        html += `
            <tr onclick="showDetail(${b.id})">
                <td>${i + 1}</td>
                <td>${b.title}</td>
                <td>${b.author}</td>
                <td>${b.genre}</td>
                <td>${b.published_year || 'N/A'}</td>
                <td>${b.rating ? b.rating.toFixed(1) : 'N/A'}</td>
                <td><span class="${b.available ? 'badge-available' : 'badge-unavailable'}">${b.available ? 'Available' : 'Borrowed'}</span></td>
                <td onclick="event.stopPropagation()">
                    <button class="btn-edit" onclick="editBook(${b.id})">Edit</button>
                    <button class="btn-delete" onclick="deleteBook(${b.id}, '${b.title.replace(/'/g, "\\'")}')">Delete</button>
                </td>
            </tr>
        `;
    });

    html += `</tbody></table>`;
    container.innerHTML = html;
}

function filterBooks() {
    const search = document.getElementById('searchInput').value.toLowerCase();
    const genre  = document.getElementById('genreFilter').value;
    const rating = parseFloat(document.getElementById('ratingFilter').value) || 0;

    const filtered = allBooks.filter(b => {
        const matchSearch = !search || b.title.toLowerCase().includes(search) || b.author.toLowerCase().includes(search);
        const matchGenre  = !genre  || b.genre === genre;
        const matchRating = !rating || b.rating >= rating;
        return matchSearch && matchGenre && matchRating;
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
        ${b.synopsis ? `<div class="synopsis-box"><strong>Synopsis:</strong><br>${b.synopsis}</div>` : ''}
    `;

    document.getElementById('detailEditBtn').onclick   = () => { closeDetailModal(); editBook(id); };
    document.getElementById('detailDeleteBtn').onclick = () => { closeDetailModal(); deleteBook(id, b.title); };
    document.getElementById('detailModal').classList.add('open');
}

function closeDetailModal() {
    document.getElementById('detailModal').classList.remove('open');
}

function openAddModal() {
    document.getElementById('modalTitle').textContent = 'Add New Book';
    document.getElementById('editingBookId').value = '';
    document.getElementById('bookTitle').value    = '';
    document.getElementById('bookAuthor').value   = '';
    document.getElementById('bookGenre').value    = '';
    document.getElementById('bookIsbn').value     = '';
    document.getElementById('bookYear').value     = '';
    document.getElementById('bookRating').value   = '';
    document.getElementById('bookPages').value    = '';
    document.getElementById('bookSynopsis').value = '';
    document.getElementById('bookAvailable').checked = true;
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

function closeModal() {
    document.getElementById('bookModal').classList.remove('open');
}

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

    const method = id ? 'PUT' : 'POST';
    const path   = id ? `/books/${id}` : '/books';
    const res    = await apiFetch(path, { method, body: JSON.stringify(body) });

    if (res.ok) {
        showToast(id ? 'Book updated successfully' : 'Book added successfully', 'success');
        closeModal();
        await refreshBooks();
    } else {
        showToast(res.data.error || 'Failed to save book', 'error');
    }
}

async function deleteBook(id, title) {
    if (!confirm('Are you sure you want to delete "' + title + '"?')) return;
    const res = await apiFetch(`/books/${id}`, { method: 'DELETE' });
    if (res.ok) {
        showToast('Book deleted', 'success');
        await refreshBooks();
    } else {
        showToast('Failed to delete book', 'error');
    }
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
    t.textContent  = msg;
    t.className    = type;
    t.style.display = 'block';
    setTimeout(() => { t.style.display = 'none'; }, 3000);
}

init();
</script>
</body>
</html>
