<html lang="ja" data-theme="light">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <!-- DotGothic16 フォント読み込み -->
  <link href="https://fonts.googleapis.com/css2?family=DotGothic16&display=swap" rel="stylesheet" />
  <title>買い物リスト</title>
  <style>
    :root {
      --bg: #ffffff;
      --fg: #333333;
      --card-bg: #f8f8f8;
      --primary: #f7de40;
      --danger: #e74c3c;
      --transition-speed: 0.3s;
    }
    /* ダークテーマ変数 */
    [data-theme="dark"] {
      --bg: #2b2b2b;
      --fg: #e0e0e0;
      --card-bg: #3a3a3a;
    }
    * {
      box-sizing: border-box;
      font-family: 'DotGothic16', sans-serif;
      transition: background var(--transition-speed), color var(--transition-speed);
    }
    body {
      margin: 0;
      padding: 1rem;
      background: var(--bg);
      color: var(--fg);
      display: flex;
      flex-direction: column;
      align-items: center;
      min-height: 100vh;
    }
    h1 {
      margin-bottom: 0.5rem;
      font-size: 1.5rem;
      display: flex;
      align-items: center;
      gap: 0.5rem;
    }
    /* 絵文字切り替え */
    .emoji.light { display: inline; }
    .emoji.dark { display: none; }
    [data-theme="dark"] .emoji.light { display: none; }
    [data-theme="dark"] .emoji.dark { display: inline; }
    /* テーマ切替ボタン */
    #theme-toggle {
      font-family: 'DotGothic16', sans-serif;
      margin-bottom: 1rem;
      padding: 0.25rem 0.75rem;
      background: var(--card-bg);
      border: 1px solid var(--fg);
      border-radius: 4px;
      cursor: pointer;
      color: var(--fg);
      transition: background var(--transition-speed), color var(--transition-speed), border-color var(--transition-speed);
    }
    .budget {
      font-size: 1.2rem;
      margin-bottom: 1rem;
      font-weight: bold;
    }
    .controls {
      display: flex;
      flex-direction: column;
      gap: 0.5rem;
      width: 100%;
      max-width: 800px;
      margin-bottom: 1rem;
    }
    .controls > * {
      padding: 0.25rem 0.5rem;
      border: 1px solid var(--fg);
      border-radius: 4px;
      background: var(--card-bg);
      color: var(--fg);
      font-size: 1rem;
      transition: background var(--transition-speed), color var(--transition-speed), border-color var(--transition-speed);
    }
    #add-item {
      padding: 0.5rem 1.5rem;
      background: var(--primary);
      color: #fff;
      border: none;
      border-radius: 4px;
      cursor: pointer;
      display: block;
      margin: 0.5rem auto;
      font-size: 1rem;
      transition: background var(--transition-speed), color var(--transition-speed);
    }
    .sections {
      width: 100%;
      max-width: 1400px;
      display: flex;
      flex-direction: column;
      gap: 2rem;
    }
    .section { width: 100%; margin-bottom: 2rem; }
    .section h2 { margin: 0 0 0.5rem; font-size: 1.1rem; }
    .box {
      padding: 0.75rem;
      background: var(--card-bg);
      border-radius: 6px;
      min-height: 2rem;
      transition: background var(--transition-speed);
    }
    .box ul { list-style: none; padding: 0; margin: 0; }
    .box li {
      display: grid;
      grid-template-columns: 1fr auto auto;
      align-items: center;
      gap: 0.5rem;
      padding: 0.5rem 0;
      border-bottom: 1px solid var(--fg);
      transition: border-color var(--transition-speed);
    }
    .box li:last-child { border-bottom: none; }
    .info { font-size: 0.875rem; line-height: 1.3; }
    .toggle {
      padding: 0.25rem 0.5rem;
      border: none;
      border-radius: 4px;
      cursor: pointer;
      background: var(--primary);
      color: #fff;
      font-size: 0.875rem;
      transition: background var(--transition-speed);
    }
    .toggle.delete { background: var(--danger); }
    /* ダークモード専用ボタン色 */
    [data-theme="dark"] .toggle { background: #6aade3; }
    [data-theme="dark"] .toggle.delete { background: #1515b0; }
    /* ダークモード専用追加ボタン色 */
    [data-theme="dark"] #add-item { background: #6aade3; color: #000; }
    /* PC版レスポンシブ */
    @media (min-width: 768px) {
      body { padding: 2rem; }
      .controls { flex-direction: row; flex-wrap: wrap; gap: 1rem; }
      .controls > #item-name { flex: 2 1 350px; }
      .controls > input:not(#item-name) { flex: 1 1 180px; }
      #add-item { align-self: flex-end; margin: 0; }
      .sections { flex-direction: row; }
      .section { width: 50%; margin-bottom: 0; }
    }
  </style>
</head>
<body>
  <h1>
    <span class="emoji light">🐣</span>
    <span class="emoji dark">🐟</span>
    買い物リスト
    <span class="emoji light">🐣</span>
    <span class="emoji dark">🐟</span>
  </h1>
  <button id="theme-toggle">ダークモード</button>
  <div class="budget" id="budget">必要予算: ¥0</div>

  <div class="controls">
    <input type="text" id="item-name" placeholder="買うもの" />
    <input type="number" id="item-qty" placeholder="数量" min="1" />
    <input type="text" id="item-store" placeholder="どこで買える？" />
    <input type="number" id="item-price" placeholder="単価（¥）" min="0" />
    <button id="add-item">追加</button>
  </div>

  <div class="sections">
    <section class="section">
      <h2 id="pending-header">未購入リスト</h2>
      <div class="box"><ul id="pending-list"></ul></div>
    </section>
    <section class="section">
      <h2>購入済みリスト</h2>
      <div class="box"><ul id="done-list"></ul></div>
    </section>
  </div>

  <script>
    const root = document.documentElement;
    const themeToggle = document.getElementById('theme-toggle');
    // 保存されたテーマを適用
    const savedTheme = localStorage.getItem('theme') || 'light';
    root.dataset.theme = savedTheme;
    themeToggle.textContent = savedTheme === 'light' ? 'ダークモード' : 'ライトモード';
    themeToggle.addEventListener('click', () => {
      const newTheme = root.dataset.theme === 'light' ? 'dark' : 'light';
      root.dataset.theme = newTheme;
      localStorage.setItem('theme', newTheme);
      themeToggle.textContent = newTheme === 'light' ? 'ダークモード' : 'ライトモード';
    });

    // 以下、既存の買い物リストロジック
    const pendingEl = document.getElementById('pending-list');
    const doneEl    = document.getElementById('done-list');
    const budgetEl  = document.getElementById('budget');
    const pendingHeaderEl = document.getElementById('pending-header');
    const nameIn = document.getElementById('item-name');
    const qtyIn  = document.getElementById('item-qty');
    const storeIn= document.getElementById('item-store');
    const priceIn= document.getElementById('item-price');
    const addBtn = document.getElementById('add-item');

    let list = JSON.parse(localStorage.getItem('shoppingList') || '[]');

    function updateTotals() {
      const pendingSum = list.filter(i => !i.done)
        .reduce((acc, i) => acc + i.qty * i.price, 0);
      pendingHeaderEl.textContent = `未購入リスト（合計: ¥${pendingSum.toLocaleString()}）`;
      budgetEl.textContent = `必要予算: ¥${pendingSum.toLocaleString()}`;
    }

    function render() {
      pendingEl.innerHTML = ''; 
      doneEl.innerHTML    = '';

      list.forEach((item, idx) => {
        const li = document.createElement('li');
        if (!item.done) {
          li.innerHTML = `
            <div class="info">
              <strong>${item.name}</strong> × ${item.qty}<br>
              店舗: ${item.store} | 単価: ¥${item.price.toLocaleString()} | 小計: ¥${(item.qty*item.price).toLocaleString()}
            </div>
            <button class="toggle">完了</button>
          `;
          li.querySelector('button').addEventListener('click', () => {
            list[idx].done = true; saveAndRender();
          });
          pendingEl.appendChild(li);
        } else {
          li.innerHTML = `
            <div class="info">
              <strong>${item.name}</strong> × ${item.qty}<br>
              店舗: ${item.store} | 単価: ¥${item.price.toLocaleString()} | 小計: ¥${(item.qty*item.price).toLocaleString()}
            </div>
            <button class="toggle" data-action="undo">未完了</button>
            <button class="toggle delete" data-action="delete">削除</button>
          `;
          li.querySelector('[data-action="undo"]').addEventListener('click', () => { list[idx].done = false; saveAndRender(); });
          li.querySelector('[data-action="delete"]').addEventListener('click', () => { list.splice(idx, 1); saveAndRender(); });
          doneEl.appendChild(li);
        }
      });
      updateTotals();
    }

    function saveAndRender() { localStorage.setItem('shoppingList', JSON.stringify(list)); render(); }

    addBtn.addEventListener('click', () => {
      const name  = nameIn.value.trim();
      const qty   = parseInt(qtyIn.value, 10);
      const store = storeIn.value.trim();
      const price = parseInt(priceIn.value, 10);
      if (!name || !qty || !store || !price) {
        alert('すべての項目を正しく入力してください'); return;
      }
      list.push({ name, qty, store, price, done: false });
      nameIn.value = '';
      qtyIn.value   = '';
      storeIn.value = '';
      priceIn.value = '';
      saveAndRender();
    });

    render();
  </script>
</body>
</html>
