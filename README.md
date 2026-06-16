<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Flashcard Quiz App</title>
  <style>
    * {
      box-sizing: border-box;
      font-family: Arial, sans-serif;
    }

    body {
      background: #f4f4f8;
      display: flex;
      justify-content: center;
      padding: 30px 15px;
      margin: 0;
    }

    .app {
      width: 100%;
      max-width: 480px;
      background: #fff;
      border-radius: 12px;
      padding: 24px;
      box-shadow: 0 2px 10px rgba(0, 0, 0, 0.08);
    }

    h1 {
      text-align: center;
      font-size: 22px;
      margin: 0 0 16px;
      color: #222;
    }

    .meta {
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 13px;
      color: #666;
      margin-bottom: 12px;
    }

    .card {
      border: 1px solid #ddd;
      border-radius: 10px;
      min-height: 150px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      text-align: center;
      padding: 24px 16px;
      cursor: pointer;
      background: #fafafa;
      transition: background 0.2s;
    }

    .card:hover {
      background: #f0f0f5;
    }

    .card .label {
      font-size: 11px;
      text-transform: uppercase;
      letter-spacing: 1px;
      color: #999;
      margin-bottom: 8px;
    }

    .card .text {
      font-size: 18px;
      font-weight: 600;
      color: #222;
    }

    .controls {
      display: flex;
      justify-content: center;
      gap: 10px;
      margin: 16px 0;
    }

    .nav-row {
      display: flex;
      gap: 10px;
      margin-bottom: 16px;
    }

    button {
      border: 1px solid #ccc;
      background: #fff;
      border-radius: 8px;
      padding: 10px 16px;
      font-size: 14px;
      cursor: pointer;
      transition: background 0.15s;
    }

    button:hover {
      background: #f0f0f5;
    }

    button.primary {
      background: #4a4ae0;
      color: #fff;
      border: none;
    }

    button.primary:hover {
      background: #3838c0;
    }

    .nav-row button {
      flex: 1;
    }

    .manage {
      border-top: 1px solid #eee;
      padding-top: 16px;
      display: none;
    }

    .manage.open {
      display: block;
    }

    .manage h2 {
      font-size: 15px;
      margin: 0 0 10px;
    }

    .card-list {
      display: flex;
      flex-direction: column;
      gap: 8px;
      max-height: 220px;
      overflow-y: auto;
      margin-bottom: 14px;
    }

    .card-row {
      display: flex;
      align-items: center;
      gap: 8px;
      border: 1px solid #eee;
      border-radius: 8px;
      padding: 8px 10px;
      background: #fff;
    }

    .card-row .q {
      flex: 1;
      font-size: 13px;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }

    .card-row button {
      padding: 4px 8px;
      font-size: 13px;
    }

    .form-box {
      background: #f7f7fb;
      border-radius: 8px;
      padding: 14px;
    }

    .form-box p {
      margin: 0 0 8px;
      font-size: 13px;
      font-weight: 600;
    }

    .form-box input {
      width: 100%;
      padding: 8px 10px;
      margin-bottom: 8px;
      border: 1px solid #ccc;
      border-radius: 6px;
      font-size: 14px;
    }

    .form-actions {
      display: flex;
      gap: 8px;
    }

    .form-actions button {
      flex: 1;
    }

    .cancel-btn {
      display: none;
    }
  </style>
</head>

<body>
  <div class="app">
    <h1>Flashcard Quiz</h1>

    <div class="meta">
      <span id="counter">Card 1 of 1</span>
      <button id="manage-btn">Manage cards</button>
    </div>

    <div class="card" id="card">
      <div class="label" id="card-label">Question</div>
      <div class="text" id="card-text"></div>
    </div>

    <div class="controls">
      <button class="primary" id="show-answer-btn">Show answer</button>
    </div>

    <div class="nav-row">
      <button id="prev-btn">&larr; Previous</button>
      <button id="next-btn">Next &rarr;</button>
    </div>

    <div class="manage" id="manage-panel">
      <h2>Manage flashcards</h2>
      <div class="card-list" id="card-list"></div>

      <div class="form-box">
        <p id="form-title">Add new flashcard</p>
        <input type="text" id="question-input" placeholder="Question">
        <input type="text" id="answer-input" placeholder="Answer">
        <div class="form-actions">
          <button class="primary" id="save-btn">Save</button>
          <button class="cancel-btn" id="cancel-edit-btn">Cancel</button>
        </div>
      </div>
    </div>
  </div>

  <script>
    let cards = [
      {
        q: "what is the entrance exam for \"MCA\"", a: "NIMCET / CUET"
      },
      { q: "What is the full form of HTML?", a: "HyperText Markup Language" },
      { q: "Who invented the c++ programming language?", a: "Bjarne Stroustrup" },
      { q: "What is the extension of a Python file?", a: ".py" }
    ];
    let current = 0;
    let showingAnswer = false;
    let editingIndex = null;

    const cardEl = document.getElementById('card');
    const cardLabel = document.getElementById('card-label');
    const cardText = document.getElementById('card-text');
    const counter = document.getElementById('counter');
    const showAnswerBtn = document.getElementById('show-answer-btn');
    const prevBtn = document.getElementById('prev-btn');
    const nextBtn = document.getElementById('next-btn');
    const manageBtn = document.getElementById('manage-btn');
    const managePanel = document.getElementById('manage-panel');
    const cardList = document.getElementById('card-list');
    const questionInput = document.getElementById('question-input');
    const answerInput = document.getElementById('answer-input');
    const saveBtn = document.getElementById('save-btn');
    const cancelEditBtn = document.getElementById('cancel-edit-btn');
    const formTitle = document.getElementById('form-title');

    function renderCard() {
      if (cards.length === 0) {
        cardLabel.textContent = '';
        cardText.textContent = 'No flashcards yet. Add one below.';
        counter.textContent = 'Card 0 of 0';
        showAnswerBtn.style.display = 'none';
        return;
      }
      showAnswerBtn.style.display = 'inline-block';
      const c = cards[current];
      if (showingAnswer) {
        cardLabel.textContent = 'Answer';
        cardText.textContent = c.a;
        showAnswerBtn.textContent = 'Show question';
      } else {
        cardLabel.textContent = 'Question';
        cardText.textContent = c.q;
        showAnswerBtn.textContent = 'Show answer';
      }
      counter.textContent = 'Card ' + (current + 1) + ' of ' + cards.length;
    }

    function renderList() {
      cardList.innerHTML = '';
      cards.forEach((c, i) => {
        const row = document.createElement('div');
        row.className = 'card-row';

        const text = document.createElement('div');
        text.className = 'q';
        text.textContent = c.q;
        row.appendChild(text);

        const editBtn = document.createElement('button');
        editBtn.textContent = 'Edit';
        editBtn.onclick = () => startEdit(i);
        row.appendChild(editBtn);

        const delBtn = document.createElement('button');
        delBtn.textContent = 'Delete';
        delBtn.onclick = () => deleteCard(i);
        row.appendChild(delBtn);

        cardList.appendChild(row);
      });
    }

    function startEdit(i) {
      editingIndex = i;
      questionInput.value = cards[i].q;
      answerInput.value = cards[i].a;
      formTitle.textContent = 'Edit flashcard';
      cancelEditBtn.style.display = 'inline-block';
    }

    function deleteCard(i) {
      cards.splice(i, 1);
      if (current >= cards.length) current = Math.max(0, cards.length - 1);
      showingAnswer = false;
      renderList();
      renderCard();
    }

    saveBtn.onclick = () => {
      const q = questionInput.value.trim();
      const a = answerInput.value.trim();
      if (!q || !a) return;
      if (editingIndex !== null) {
        cards[editingIndex] = { q, a };
        editingIndex = null;
        formTitle.textContent = 'Add new flashcard';
        cancelEditBtn.style.display = 'none';
      } else {
        cards.push({ q, a });
        current = cards.length - 1;
      }
      questionInput.value = '';
      answerInput.value = '';
      showingAnswer = false;
      renderList();
      renderCard();
    };

    cancelEditBtn.onclick = () => {
      editingIndex = null;
      questionInput.value = '';
      answerInput.value = '';
      formTitle.textContent = 'Add new flashcard';
      cancelEditBtn.style.display = 'none';
    };

    showAnswerBtn.onclick = () => {
      showingAnswer = !showingAnswer;
      renderCard();
    };

    cardEl.onclick = () => {
      if (cards.length === 0) return;
      showingAnswer = !showingAnswer;
      renderCard();
    };

    prevBtn.onclick = () => {
      if (cards.length === 0) return;
      current = (current - 1 + cards.length) % cards.length;
      showingAnswer = false;
      renderCard();
    };

    nextBtn.onclick = () => {
      if (cards.length === 0) return;
      current = (current + 1) % cards.length;
      showingAnswer = false;
      renderCard();
    };

    manageBtn.onclick = () => {
      managePanel.classList.toggle('open');
      if (managePanel.classList.contains('open')) renderList();
    };

    renderCard();
  </script>
</body>

</html>
