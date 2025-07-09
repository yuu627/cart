<html lang="ja">
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
    }
    * {
      box-sizing: border-box;
      font-family: 'DotGothic16', sans-serif;
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
      max-width: 600px;
      margin-bottom: 1rem;
    }
    .controls > * {
      padding: 0.25rem 0.5rem;
      border: 1px solid var(--fg);
      border-radius: 4px;
      background: var(--card-bg);
      color: var(--fg);
      font-size: 1rem;
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
    }
    .section {
      width: 100%;
      max-width: 600px;
      margin-bottom: 2rem;
    }
    .section h2 {
      margin: 0 0 0.5rem;
      font-size: 1.1rem;
    }
    .box {
      padding: 0.75rem;
      background: var(--card-bg);
      border-radius: 6px;
      min-height: 2rem;
    }
    .box ul {
      list-style: none;
      padding: 0;
      margin: 0;
    }
    .box li {
      display: grid;
      grid-template-columns: 1fr auto;
      align-items: center;
      gap: 0.5rem;
      padding: 0.5rem 0;
      border-bottom: 1px solid var(--fg);
    }
    .box li:last-child {
      border-bottom: none;
    }
    .info {
      font-size: 0.875rem;
      line-height: 1.3;
    }
    .toggle {
      padding: 0.25rem 0.5rem;
      border: none;
      border-radius: 4px;
      cursor: pointer;
      background: var(--primary);
      color: #fff;
      font-size: 0.875rem;
    }
  </style>
</head>
<body>
  <h1>🐣買い物リスト🐣</h1>
  <div class="budget" id="budget">必要予算: ¥0</div>

  <div class="controls">
    <input type="text" id="item-name" placeholder="買うもの" />
    <input type="number" id="item-qty" placeholder="数量" min="1" />
    <input type="text" id="item-store" placeholder="どこで買える？" />
    <input type="number" id="item-price" placeholder="単価（¥）" min="0" />
    <button id="add-item">追加</button>
  </div>

  <section class="section">
    <!-- idを付与 -->
    <h2 id="pending-header">未購入リスト</h2>
    <div class="box">
      <ul id="pending-list"></ul>
    </div>
  </section>

  <section class="section">
    <h2>購入済みリスト</h2>
    <div class="box">
      <ul id="done-list"></ul>
    </div>
  </section>

  <script>
    const pendingEl       = document.getElementById('pending-list');
    const doneEl          = document.getElementById('done-list');
    const budgetEl        = document.getElementById('budget');
    const pendingHeaderEl = document.getElementById('pending-header');
    const nameIn          = document.getElementById('item-name');
    const qtyIn           = document.getElementById('item-qty');
    const storeIn         = document.getElementById('item-store');
    const priceIn         = document.getElementById('item-price');
    const addBtn          = document.getElementById('add-item');

    let list = JSON.parse(localStorage.getItem('shoppingList') || '[]');

    // 未購入合計を計算して見出しと予算を更新
    function updateTotals() {
      const pendingSum = list
        .filter(i => !i.done)
        .reduce((acc, i) => acc + i.qty * i.price, 0);
      pendingHeaderEl.textContent = `未購入リスト（合計: ¥${pendingSum.toLocaleString()}）`;
      const totalSum = list
        .filter(i => !i.done)
        .reduce((acc, i) => acc + i.qty * i.price, 0);
      budgetEl.textContent = `必要予算: ¥${totalSum.toLocaleString()}`;
    }

    function render() {
      pendingEl.innerHTML = '';
      doneEl.innerHTML    = '';

      list.forEach((item, idx) => {
        const li = document.createElement('li');
        li.innerHTML = `
          <div class="info">
            <strong>${item.name}</strong> × ${item.qty}<br>
            店舗: ${item.store} | 単価: ¥${item.price.toLocaleString()} | 小計: ¥${(item.qty*item.price).toLocaleString()}
          </div>
          <button class="toggle">${item.done ? '削除' : '完了'}</button>
        `;
        li.querySelector('button').addEventListener('click', () => {
          if (!item.done) {
            list[idx].done = true;
          } else {
            list.splice(idx, 1);
          }
          saveAndRender();
        });
        (item.done ? doneEl : pendingEl).appendChild(li);
      });

      updateTotals();
    }

    function saveAndRender() {
      localStorage.setItem('shoppingList', JSON.stringify(list));
      render();
    }

    addBtn.addEventListener('click', () => {
      const name  = nameIn.value.trim();
      const qty   = parseInt(qtyIn.value, 10);
      const store = storeIn.value.trim();
      const price = parseInt(priceIn.value, 10);
      if (!name || !qty || !store || !price) {
        alert('すべての項目を正しく入力してください');
        return;
      }
      list.push({ name, qty, store, price, done: false });
      nameIn.value = '';
      qtyIn.value = '';
      storeIn.value = '';
      priceIn.value = '';
      saveAndRender();
    });

    // 初期描画
    render();
  </script>
</body>
</html>

