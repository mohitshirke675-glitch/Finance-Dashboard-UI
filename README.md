<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>FinFlow — Finance Dashboard</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>

<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --bg: #0d0f14;
    --bg2: #141720;
    --bg3: #1c2030;
    --bg4: #232840;
    --border: rgba(255,255,255,0.07);
    --border2: rgba(255,255,255,0.13);
    --text: #e8eaf2;
    --text2: #8b90a8;
    --text3: #555d78;
    --accent: #6c63ff;
    --accent2: #8b83ff;
    --green: #2dd4a0;
    --green-bg: rgba(45,212,160,0.12);
    --red: #f0556a;
    --red-bg: rgba(240,85,106,0.12);
    --amber: #f5a623;
    --amber-bg: rgba(245,166,35,0.1);
    --blue: #4da6ff;
    --blue-bg: rgba(77,166,255,0.1);
    --purple: #b46eff;
    --pink: #ff6eb4;
    --teal: #2dd4bf;
    --sidebar-w: 240px;
    --radius: 14px;
    --radius-sm: 8px;
  }

  [data-theme="light"] {
    --bg: #f4f5fa;
    --bg2: #ffffff;
    --bg3: #eceef5;
    --bg4: #e2e4ef;
    --border: rgba(0,0,0,0.06);
    --border2: rgba(0,0,0,0.12);
    --text: #1a1d2e;
    --text2: #555d78;
    --text3: #9099b5;
    --accent: #5b52f5;
    --accent2: #7b74f8;
    --green: #12b07a;
    --green-bg: rgba(18,176,122,0.1);
    --red: #e03353;
    --red-bg: rgba(224,51,83,0.1);
    --amber: #d4820a;
    --amber-bg: rgba(212,130,10,0.1);
    --blue: #1a7fe8;
    --blue-bg: rgba(26,127,232,0.1);
    --bg3: #eef0f8;
    --bg4: #e4e6f2;
  }

  body {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif;
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    display: flex;
    font-size: 14px;
    line-height: 1.6;
    transition: background 0.3s, color 0.3s;
  }

  /* SIDEBAR */
  .sidebar {
    width: var(--sidebar-w);
    background: var(--bg2);
    border-right: 1px solid var(--border);
    display: flex;
    flex-direction: column;
    position: fixed;
    top: 0; left: 0; bottom: 0;
    z-index: 100;
    transition: transform 0.3s;
  }

  .sidebar-logo {
    padding: 24px 20px 20px;
    border-bottom: 1px solid var(--border);
  }

  .logo-mark {
    display: flex; align-items: center; gap: 10px;
  }

  .logo-icon {
    width: 34px; height: 34px;
    background: var(--accent);
    border-radius: 9px;
    display: flex; align-items: center; justify-content: center;
    font-family: 'Courier New', monospace;
    font-size: 13px; font-weight: 500;
    color: #fff;
  }

  .logo-text {
    font-size: 17px; font-weight: 600;
    letter-spacing: -0.3px;
  }

  .logo-text span { color: var(--accent2); }

  .sidebar-nav { flex: 1; padding: 16px 12px; overflow-y: auto; }

  .nav-section-label {
    font-size: 10px; font-weight: 500;
    text-transform: uppercase;
    letter-spacing: 0.1em;
    color: var(--text3);
    padding: 0 8px 8px;
    margin-top: 12px;
  }

  .nav-item {
    display: flex; align-items: center; gap: 10px;
    padding: 9px 10px;
    border-radius: var(--radius-sm);
    cursor: pointer;
    color: var(--text2);
    font-size: 13.5px;
    font-weight: 400;
    transition: all 0.18s;
    margin-bottom: 2px;
    border: none; background: none; width: 100%; text-align: left;
  }

  .nav-item:hover { background: var(--bg3); color: var(--text); }

  .nav-item.active {
    background: rgba(108,99,255,0.15);
    color: var(--accent2);
    font-weight: 500;
  }

  .nav-item .nav-icon {
    width: 32px; height: 32px;
    border-radius: 7px;
    display: flex; align-items: center; justify-content: center;
    font-size: 15px;
    flex-shrink: 0;
  }

  .nav-item.active .nav-icon { background: rgba(108,99,255,0.2); }

  .nav-badge {
    margin-left: auto;
    background: var(--accent);
    color: #fff;
    font-size: 10px;
    font-weight: 600;
    padding: 1px 6px;
    border-radius: 20px;
    font-family: 'Courier New', monospace;
  }

  .sidebar-footer {
    padding: 16px 12px;
    border-top: 1px solid var(--border);
  }

  .user-block {
    display: flex; align-items: center; gap: 10px;
    padding: 8px 10px;
    border-radius: var(--radius-sm);
    background: var(--bg3);
  }

  .user-avatar {
    width: 32px; height: 32px; border-radius: 50%;
    background: linear-gradient(135deg, var(--accent), var(--purple));
    display: flex; align-items: center; justify-content: center;
    font-size: 12px; font-weight: 600; color: #fff;
    flex-shrink: 0;
  }

  .user-info { flex: 1; min-width: 0; }
  .user-name { font-size: 12.5px; font-weight: 500; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
  .user-role-label { font-size: 10.5px; color: var(--text3); }

  /* MAIN CONTENT */
  .main {
    margin-left: var(--sidebar-w);
    flex: 1;
    display: flex;
    flex-direction: column;
    min-height: 100vh;
  }

  /* TOP BAR */
  .topbar {
    background: var(--bg2);
    border-bottom: 1px solid var(--border);
    padding: 14px 28px;
    display: flex;
    align-items: center;
    gap: 16px;
    position: sticky; top: 0; z-index: 50;
  }

  .topbar-title { font-size: 16px; font-weight: 600; flex: 1; }

  .topbar-controls { display: flex; align-items: center; gap: 10px; }

  .role-select {
    background: var(--bg3);
    border: 1px solid var(--border2);
    color: var(--text);
    padding: 7px 12px;
    border-radius: var(--radius-sm);
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif;
    font-size: 12.5px;
    cursor: pointer;
    outline: none;
  }

  .theme-btn {
    width: 34px; height: 34px;
    background: var(--bg3);
    border: 1px solid var(--border2);
    border-radius: var(--radius-sm);
    cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    font-size: 15px;
    color: var(--text2);
    transition: all 0.18s;
  }

  .theme-btn:hover { background: var(--bg4); color: var(--text); }

  .export-btn {
    background: var(--accent);
    border: none;
    color: #fff;
    padding: 7px 14px;
    border-radius: var(--radius-sm);
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif;
    font-size: 12.5px; font-weight: 500;
    cursor: pointer;
    display: flex; align-items: center; gap: 6px;
    transition: opacity 0.18s;
  }

  .export-btn:hover { opacity: 0.88; }

  .hamburger {
    display: none;
    background: none; border: none; cursor: pointer;
    color: var(--text); font-size: 20px;
  }

  /* PAGE CONTENT */
  .page-content {
    padding: 28px;
    flex: 1;
  }

  .page { display: none; }
  .page.active { display: block; }

  /* SECTION HEADING */
  .section-header {
    display: flex; align-items: center; justify-content: space-between;
    margin-bottom: 20px;
  }

  .section-title { font-size: 18px; font-weight: 600; }
  .section-sub { font-size: 12.5px; color: var(--text3); margin-top: 2px; }

  /* METRIC CARDS */
  .metrics-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 16px;
    margin-bottom: 24px;
  }

  .metric-card {
    background: var(--bg2);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 20px 22px;
    position: relative;
    overflow: hidden;
    transition: border-color 0.2s, transform 0.18s;
    cursor: default;
  }

  .metric-card:hover { border-color: var(--border2); transform: translateY(-1px); }

  .metric-card::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 2px;
  }

  .metric-card.balance::before { background: var(--accent); }
  .metric-card.income::before { background: var(--green); }
  .metric-card.expense::before { background: var(--red); }
  .metric-card.savings::before { background: var(--amber); }

  .metric-label {
    font-size: 11px; font-weight: 500;
    text-transform: uppercase;
    letter-spacing: 0.08em;
    color: var(--text3);
    margin-bottom: 10px;
    display: flex; align-items: center; gap: 6px;
  }

  .metric-icon { font-size: 13px; }

  .metric-value {
    font-family: 'Courier New', monospace;
    font-size: 26px; font-weight: 500;
    letter-spacing: -0.5px;
    margin-bottom: 8px;
  }

  .metric-change {
    font-size: 11.5px;
    display: flex; align-items: center; gap: 4px;
  }

  .change-up { color: var(--green); }
  .change-down { color: var(--red); }

  /* CHARTS GRID */
  .charts-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 16px;
    margin-bottom: 24px;
  }

  .chart-card {
    background: var(--bg2);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 20px 22px;
  }

  .chart-title {
    font-size: 13.5px; font-weight: 600;
    margin-bottom: 4px;
  }

  .chart-sub { font-size: 11.5px; color: var(--text3); margin-bottom: 16px; }

  .chart-container { position: relative; }

  .chart-legend {
    display: flex; flex-wrap: wrap; gap: 12px;
    margin-bottom: 12px;
    font-size: 11.5px; color: var(--text2);
  }

  .legend-item { display: flex; align-items: center; gap: 5px; }
  .legend-dot { width: 9px; height: 9px; border-radius: 2px; flex-shrink: 0; }

  /* TRANSACTIONS */
  .trans-controls {
    display: flex; gap: 10px; flex-wrap: wrap;
    margin-bottom: 16px;
    align-items: center;
  }

  .search-input {
    flex: 1; min-width: 200px;
    background: var(--bg2);
    border: 1px solid var(--border2);
    color: var(--text);
    padding: 8px 14px 8px 36px;
    border-radius: var(--radius-sm);
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif;
    font-size: 13px;
    outline: none;
    position: relative;
    transition: border-color 0.18s;
  }

  .search-input:focus { border-color: var(--accent); }

  .search-wrap { position: relative; flex: 1; min-width: 200px; }
  .search-icon {
    position: absolute; left: 11px; top: 50%; transform: translateY(-50%);
    color: var(--text3); font-size: 14px; pointer-events: none;
  }

  .filter-select {
    background: var(--bg2);
    border: 1px solid var(--border2);
    color: var(--text);
    padding: 8px 12px;
    border-radius: var(--radius-sm);
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif;
    font-size: 12.5px;
    cursor: pointer; outline: none;
  }

  .sort-btn {
    background: var(--bg2);
    border: 1px solid var(--border2);
    color: var(--text2);
    padding: 8px 12px;
    border-radius: var(--radius-sm);
    cursor: pointer;
    font-size: 12.5px;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif;
    display: flex; align-items: center; gap: 5px;
    transition: all 0.18s;
  }

  .sort-btn:hover { background: var(--bg3); color: var(--text); border-color: var(--accent); }

  .trans-table-wrap {
    background: var(--bg2);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    overflow: hidden;
  }

  .trans-table {
    width: 100%; border-collapse: collapse;
  }

  .trans-table th {
    padding: 12px 16px;
    text-align: left;
    font-size: 11px; font-weight: 500;
    text-transform: uppercase;
    letter-spacing: 0.07em;
    color: var(--text3);
    background: var(--bg3);
    border-bottom: 1px solid var(--border);
    cursor: pointer;
    user-select: none;
    white-space: nowrap;
  }

  .trans-table th:hover { color: var(--text); }

  .trans-table td {
    padding: 13px 16px;
    font-size: 13px;
    border-bottom: 1px solid var(--border);
    vertical-align: middle;
  }

  .trans-table tr:last-child td { border-bottom: none; }

  .trans-table tr:hover td { background: var(--bg3); }

  .trans-amount { font-family: 'Courier New', monospace; font-size: 13px; font-weight: 500; }
  .trans-amount.income { color: var(--green); }
  .trans-amount.expense { color: var(--red); }

  .category-pill {
    display: inline-flex; align-items: center; gap: 5px;
    padding: 3px 9px;
    border-radius: 20px;
    font-size: 11px; font-weight: 500;
    white-space: nowrap;
  }

  .pill-food { background: rgba(245,166,35,0.15); color: var(--amber); }
  .pill-transport { background: rgba(77,166,255,0.15); color: var(--blue); }
  .pill-shopping { background: rgba(180,110,255,0.15); color: var(--purple); }
  .pill-health { background: rgba(45,212,160,0.15); color: var(--green); }
  .pill-entertainment { background: rgba(255,110,180,0.15); color: var(--pink); }
  .pill-utilities { background: rgba(45,212,191,0.15); color: var(--teal); }
  .pill-salary { background: rgba(108,99,255,0.2); color: var(--accent2); }
  .pill-freelance { background: rgba(45,212,160,0.15); color: var(--green); }
  .pill-rent { background: rgba(240,85,106,0.15); color: var(--red); }
  .pill-other { background: var(--bg4); color: var(--text2); }

  .type-badge {
    font-size: 10px; padding: 2px 7px; border-radius: 4px; font-weight: 500;
  }

  .type-income { background: var(--green-bg); color: var(--green); }
  .type-expense { background: var(--red-bg); color: var(--red); }

  .action-btn {
    background: none; border: 1px solid var(--border2);
    color: var(--text2); padding: 5px 10px;
    border-radius: var(--radius-sm);
    cursor: pointer; font-size: 11px;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif;
    transition: all 0.15s;
    margin-right: 4px;
  }

  .action-btn:hover { background: var(--bg4); color: var(--text); }
  .action-btn.del:hover { background: var(--red-bg); color: var(--red); border-color: var(--red); }

  .empty-state {
    text-align: center; padding: 48px 20px;
    color: var(--text3);
  }

  .empty-icon { font-size: 36px; margin-bottom: 12px; }
  .empty-text { font-size: 14px; }

  /* INSIGHTS */
  .insights-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 16px;
    margin-bottom: 24px;
  }

  .insight-card {
    background: var(--bg2);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 20px 22px;
  }

  .insight-header {
    display: flex; align-items: center; gap: 10px;
    margin-bottom: 14px;
  }

  .insight-icon {
    width: 38px; height: 38px; border-radius: 10px;
    display: flex; align-items: center; justify-content: center;
    font-size: 18px;
  }

  .insight-card-title { font-size: 13px; font-weight: 600; }
  .insight-card-sub { font-size: 11px; color: var(--text3); margin-top: 1px; }

  .insight-value { font-family: 'Courier New', monospace; font-size: 22px; font-weight: 500; margin-bottom: 4px; }
  .insight-desc { font-size: 12px; color: var(--text2); }

  .progress-bar-wrap { margin-top: 14px; }

  .progress-label {
    display: flex; justify-content: space-between;
    font-size: 11.5px; margin-bottom: 5px;
    color: var(--text2);
  }

  .progress-track {
    background: var(--bg4);
    border-radius: 20px;
    height: 6px;
    overflow: hidden;
  }

  .progress-fill {
    height: 100%;
    border-radius: 20px;
    transition: width 0.8s cubic-bezier(.4,0,.2,1);
  }

  .monthly-compare {
    display: grid; grid-template-columns: 1fr 1fr;
    gap: 12px; margin-top: 12px;
  }

  .month-block {
    background: var(--bg3);
    border-radius: var(--radius-sm);
    padding: 12px;
  }

  .month-label { font-size: 11px; color: var(--text3); margin-bottom: 4px; }
  .month-val { font-family: 'Courier New', monospace; font-size: 17px; font-weight: 500; }

  /* MODAL */
  .modal-overlay {
    display: none;
    position: fixed; inset: 0;
    background: rgba(0,0,0,0.6);
    z-index: 200;
    align-items: center;
    justify-content: center;
    padding: 16px;
  }

  .modal-overlay.open { display: flex; }

  .modal {
    background: var(--bg2);
    border: 1px solid var(--border2);
    border-radius: var(--radius);
    padding: 28px;
    width: 100%; max-width: 460px;
    animation: fadeUp 0.22s ease;
  }

  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(12px); }
    to { opacity: 1; transform: translateY(0); }
  }

  .modal-title { font-size: 16px; font-weight: 600; margin-bottom: 20px; }

  .form-group { margin-bottom: 16px; }
  .form-label { font-size: 12px; font-weight: 500; color: var(--text2); margin-bottom: 6px; display: block; }

  .form-input, .form-select {
    width: 100%;
    background: var(--bg3);
    border: 1px solid var(--border2);
    color: var(--text);
    padding: 9px 13px;
    border-radius: var(--radius-sm);
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif;
    font-size: 13px;
    outline: none;
    transition: border-color 0.18s;
  }

  .form-input:focus, .form-select:focus { border-color: var(--accent); }

  .form-row { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }

  .modal-actions { display: flex; justify-content: flex-end; gap: 10px; margin-top: 22px; }

  .btn-cancel {
    background: var(--bg3); border: 1px solid var(--border2);
    color: var(--text2); padding: 9px 18px;
    border-radius: var(--radius-sm);
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif;
    font-size: 13px; cursor: pointer;
    transition: all 0.15s;
  }

  .btn-cancel:hover { background: var(--bg4); color: var(--text); }

  .btn-primary {
    background: var(--accent); border: none;
    color: #fff; padding: 9px 18px;
    border-radius: var(--radius-sm);
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif;
    font-size: 13px; font-weight: 500; cursor: pointer;
    transition: opacity 0.15s;
  }

  .btn-primary:hover { opacity: 0.88; }

  /* TOAST */
  .toast {
    position: fixed; bottom: 24px; right: 24px;
    background: var(--bg2);
    border: 1px solid var(--border2);
    border-radius: var(--radius-sm);
    padding: 12px 18px;
    font-size: 13px;
    box-shadow: 0 8px 32px rgba(0,0,0,0.4);
    z-index: 300;
    animation: slideIn 0.22s ease;
    display: flex; align-items: center; gap: 8px;
  }

  .toast.hidden { display: none; }

  @keyframes slideIn {
    from { opacity: 0; transform: translateX(24px); }
    to { opacity: 1; transform: translateX(0); }
  }

  /* ADMIN BADGE */
  .admin-only { display: none; }
  body.is-admin .admin-only { display: flex; }
  body.is-admin .admin-only-inline { display: inline-flex; }
  .admin-only-inline { display: none; }

  /* ROLE INDICATOR */
  .role-indicator {
    display: inline-flex; align-items: center; gap: 5px;
    font-size: 11px; font-weight: 500;
    padding: 3px 9px; border-radius: 20px;
  }

  .role-admin { background: rgba(108,99,255,0.2); color: var(--accent2); }
  .role-viewer { background: var(--bg3); color: var(--text3); }

  /* MOBILE */
  .mobile-overlay {
    display: none;
    position: fixed; inset: 0;
    background: rgba(0,0,0,0.5);
    z-index: 99;
  }

  @media (max-width: 768px) {
    .sidebar {
      transform: translateX(-100%);
    }

    .sidebar.open { transform: translateX(0); }

    .mobile-overlay.open { display: block; }

    .main { margin-left: 0; }

    .hamburger { display: flex; }

    .charts-grid { grid-template-columns: 1fr; }

    .page-content { padding: 16px; }

    .topbar { padding: 12px 16px; }

    .trans-table th:nth-child(4),
    .trans-table td:nth-child(4) { display: none; }

    .form-row { grid-template-columns: 1fr; }

    .monthly-compare { grid-template-columns: 1fr; }

    .export-btn span { display: none; }
  }

  @media (max-width: 500px) {
    .metrics-grid { grid-template-columns: 1fr 1fr; }
    .metric-value { font-size: 20px; }
    .trans-table th:nth-child(5),
    .trans-table td:nth-child(5) { display: none; }
  }

  /* SCROLL */
  ::-webkit-scrollbar { width: 5px; height: 5px; }
  ::-webkit-scrollbar-track { background: transparent; }
  ::-webkit-scrollbar-thumb { background: var(--border2); border-radius: 4px; }
</style>
</head>
<body>

<!-- SIDEBAR -->
<nav class="sidebar" id="sidebar">
  <div class="sidebar-logo">
    <div class="logo-mark">
      <div class="logo-icon">FF</div>
      <div class="logo-text">Fin<span>Flow</span></div>
    </div>
  </div>
  <div class="sidebar-nav">
    <div class="nav-section-label">Overview</div>
    <button class="nav-item active" data-page="dashboard" onclick="navigate('dashboard')">
      <div class="nav-icon">📊</div>
      Dashboard
    </button>
    <button class="nav-item" data-page="transactions" onclick="navigate('transactions')">
      <div class="nav-icon">💳</div>
      Transactions
      <span class="nav-badge" id="trans-badge">0</span>
    </button>
    <div class="nav-section-label">Analysis</div>
    <button class="nav-item" data-page="insights" onclick="navigate('insights')">
      <div class="nav-icon">💡</div>
      Insights
    </button>
  </div>
  <div class="sidebar-footer">
    <div class="user-block">
      <div class="user-avatar" id="user-avatar">A</div>
      <div class="user-info">
        <div class="user-name" id="user-name">Admin User</div>
        <div class="user-role-label" id="user-role-label">Administrator</div>
      </div>
    </div>
  </div>
</nav>

<!-- MOBILE OVERLAY -->
<div class="mobile-overlay" id="mobile-overlay" onclick="closeSidebar()"></div>

<!-- MAIN -->
<div class="main">
  <header class="topbar">
    <button class="hamburger" onclick="toggleSidebar()">☰</button>
    <span class="topbar-title" id="topbar-title">Dashboard</span>
    <div class="topbar-controls">
      <span class="role-indicator role-admin" id="role-indicator">● Admin</span>
      <select class="role-select" id="role-select" onchange="switchRole(this.value)">
        <option value="admin">Admin</option>
        <option value="viewer">Viewer</option>
      </select>
      <button class="theme-btn" onclick="toggleTheme()" title="Toggle theme">🌙</button>
      <button class="export-btn" onclick="exportData()">⬇ <span>Export</span></button>
      <button class="export-btn admin-only" style="background:var(--green);" onclick="openModal()">+ <span>Add</span></button>
    </div>
  </header>

  <!-- DASHBOARD PAGE -->
  <div class="page active" id="page-dashboard">
    <div class="page-content">
      <div class="section-header">
        <div>
          <div class="section-title">Overview</div>
          <div class="section-sub">Your financial summary at a glance</div>
        </div>
        <select class="filter-select" id="period-filter" onchange="updateDashboard()">
          <option value="all">All Time</option>
          <option value="month">This Month</option>
          <option value="last3">Last 3 Months</option>
        </select>
      </div>

      <div class="metrics-grid">
        <div class="metric-card balance">
          <div class="metric-label"><span class="metric-icon">💰</span> Total Balance</div>
          <div class="metric-value" id="m-balance">$0.00</div>
          <div class="metric-change change-up" id="m-balance-change">—</div>
        </div>
        <div class="metric-card income">
          <div class="metric-label"><span class="metric-icon">📈</span> Total Income</div>
          <div class="metric-value" id="m-income">$0.00</div>
          <div class="metric-change change-up" id="m-income-change">—</div>
        </div>
        <div class="metric-card expense">
          <div class="metric-label"><span class="metric-icon">📉</span> Total Expenses</div>
          <div class="metric-value" id="m-expense">$0.00</div>
          <div class="metric-change change-down" id="m-expense-change">—</div>
        </div>
        <div class="metric-card savings">
          <div class="metric-label"><span class="metric-icon">🏦</span> Savings Rate</div>
          <div class="metric-value" id="m-savings">0%</div>
          <div class="metric-change" id="m-savings-change">—</div>
        </div>
      </div>

      <div class="charts-grid">
        <div class="chart-card" style="grid-column: 1 / -1;">
          <div class="chart-title">Balance Trend</div>
          <div class="chart-sub">Monthly income vs expenses over time</div>
          <div class="chart-legend" id="trend-legend">
            <span class="legend-item"><span class="legend-dot" style="background:#6c63ff;"></span>Income</span>
            <span class="legend-item"><span class="legend-dot" style="background:#f0556a;"></span>Expenses</span>
            <span class="legend-item"><span class="legend-dot" style="background:#2dd4a0;"></span>Net Balance</span>
          </div>
          <div class="chart-container" style="height:240px;">
            <canvas id="trendChart"></canvas>
          </div>
        </div>
        <div class="chart-card">
          <div class="chart-title">Spending Breakdown</div>
          <div class="chart-sub">By category</div>
          <div class="chart-legend" id="pie-legend"></div>
          <div class="chart-container" style="height:200px;">
            <canvas id="pieChart"></canvas>
          </div>
        </div>
        <div class="chart-card">
          <div class="chart-title">Weekly Spending</div>
          <div class="chart-sub">Current week daily expenses</div>
          <div class="chart-container" style="height:220px;">
            <canvas id="barChart"></canvas>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- TRANSACTIONS PAGE -->
  <div class="page" id="page-transactions">
    <div class="page-content">
      <div class="section-header">
        <div>
          <div class="section-title">Transactions</div>
          <div class="section-sub" id="trans-count-label">Loading…</div>
        </div>
        <button class="export-btn admin-only" style="background:var(--green);" onclick="openModal()">+ Add Transaction</button>
      </div>

      <div class="trans-controls">
        <div class="search-wrap">
          <span class="search-icon">🔍</span>
          <input class="search-input" type="text" id="search-input" placeholder="Search transactions…" oninput="renderTransactions()">
        </div>
        <select class="filter-select" id="type-filter" onchange="renderTransactions()">
          <option value="all">All Types</option>
          <option value="income">Income</option>
          <option value="expense">Expense</option>
        </select>
        <select class="filter-select" id="cat-filter" onchange="renderTransactions()">
          <option value="all">All Categories</option>
        </select>
        <button class="sort-btn" onclick="toggleSort('date')">📅 Date <span id="sort-date-icon"></span></button>
        <button class="sort-btn" onclick="toggleSort('amount')">💲 Amount <span id="sort-amount-icon"></span></button>
      </div>

      <div class="trans-table-wrap">
        <table class="trans-table">
          <thead>
            <tr>
              <th onclick="toggleSort('date')">Date ↕</th>
              <th>Description</th>
              <th>Category</th>
              <th>Type</th>
              <th onclick="toggleSort('amount')">Amount ↕</th>
              <th class="admin-only-inline" style="display:none;">Actions</th>
            </tr>
          </thead>
          <tbody id="trans-tbody"></tbody>
        </table>
        <div class="empty-state" id="empty-state" style="display:none;">
          <div class="empty-icon">📭</div>
          <div class="empty-text">No transactions found</div>
        </div>
      </div>
    </div>
  </div>

  <!-- INSIGHTS PAGE -->
  <div class="page" id="page-insights">
    <div class="page-content">
      <div class="section-header">
        <div>
          <div class="section-title">Insights</div>
          <div class="section-sub">Patterns and observations from your data</div>
        </div>
      </div>

      <div class="insights-grid">
        <div class="insight-card">
          <div class="insight-header">
            <div class="insight-icon" style="background:var(--amber-bg);">🔥</div>
            <div>
              <div class="insight-card-title">Highest Spending Category</div>
              <div class="insight-card-sub">All time</div>
            </div>
          </div>
          <div class="insight-value" id="ins-top-cat">—</div>
          <div class="insight-desc" id="ins-top-cat-desc">—</div>
          <div class="progress-bar-wrap" id="ins-cat-bars"></div>
        </div>

        <div class="insight-card">
          <div class="insight-header">
            <div class="insight-icon" style="background:var(--blue-bg);">📅</div>
            <div>
              <div class="insight-card-title">Monthly Comparison</div>
              <div class="insight-card-sub">This month vs last month</div>
            </div>
          </div>
          <div class="monthly-compare" id="ins-monthly"></div>
          <div class="insight-desc" id="ins-monthly-desc" style="margin-top:12px;">—</div>
        </div>

        <div class="insight-card">
          <div class="insight-header">
            <div class="insight-icon" style="background:var(--green-bg);">💹</div>
            <div>
              <div class="insight-card-title">Savings Analysis</div>
              <div class="insight-card-sub">Income vs spending ratio</div>
            </div>
          </div>
          <div class="insight-value" id="ins-savings">—</div>
          <div class="insight-desc" id="ins-savings-desc">—</div>
          <div class="progress-bar-wrap">
            <div class="progress-label">
              <span>Spent</span>
              <span id="ins-savings-pct">0%</span>
            </div>
            <div class="progress-track">
              <div class="progress-fill" id="ins-savings-bar" style="background:var(--green); width:0%;"></div>
            </div>
          </div>
        </div>

        <div class="insight-card">
          <div class="insight-header">
            <div class="insight-icon" style="background:rgba(180,110,255,0.15);">🎯</div>
            <div>
              <div class="insight-card-title">Smart Observations</div>
              <div class="insight-card-sub">Key takeaways</div>
            </div>
          </div>
          <div id="ins-observations" style="display:flex;flex-direction:column;gap:10px;"></div>
        </div>
      </div>

      <div class="chart-card">
        <div class="chart-title">Category Spend Over Months</div>
        <div class="chart-sub">How your top categories evolve</div>
        <div class="chart-container" style="height:260px;">
          <canvas id="insightChart"></canvas>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- ADD/EDIT MODAL -->
<div class="modal-overlay" id="modal-overlay">
  <div class="modal">
    <div class="modal-title" id="modal-title">Add Transaction</div>
    <div class="form-row">
      <div class="form-group">
        <label class="form-label">Date</label>
        <input class="form-input" type="date" id="f-date">
      </div>
      <div class="form-group">
        <label class="form-label">Type</label>
        <select class="form-select" id="f-type">
          <option value="expense">Expense</option>
          <option value="income">Income</option>
        </select>
      </div>
    </div>
    <div class="form-group">
      <label class="form-label">Description</label>
      <input class="form-input" type="text" id="f-desc" placeholder="e.g. Monthly Salary">
    </div>
    <div class="form-row">
      <div class="form-group">
        <label class="form-label">Amount ($)</label>
        <input class="form-input" type="number" id="f-amount" placeholder="0.00" min="0" step="0.01">
      </div>
      <div class="form-group">
        <label class="form-label">Category</label>
        <select class="form-select" id="f-category">
          <option value="Food">Food</option>
          <option value="Transport">Transport</option>
          <option value="Shopping">Shopping</option>
          <option value="Health">Health</option>
          <option value="Entertainment">Entertainment</option>
          <option value="Utilities">Utilities</option>
          <option value="Salary">Salary</option>
          <option value="Freelance">Freelance</option>
          <option value="Rent">Rent</option>
          <option value="Other">Other</option>
        </select>
      </div>
    </div>
    <div class="modal-actions">
      <button class="btn-cancel" onclick="closeModal()">Cancel</button>
      <button class="btn-primary" onclick="saveTransaction()">Save Transaction</button>
    </div>
  </div>
</div>

<!-- TOAST -->
<div class="toast hidden" id="toast"></div>

<script>
// ─── STATE ───────────────────────────────────────────────────────────────────

const STORAGE_KEY = 'finflow_data_v1';
const ROLE_KEY = 'finflow_role_v1';
const THEME_KEY = 'finflow_theme_v1';

let state = {
  transactions: [],
  role: 'admin',
  sortField: 'date',
  sortDir: 'desc',
  editId: null
};

const SEED_TRANSACTIONS = [
  { id: 1, date: '2024-11-01', desc: 'Monthly Salary', category: 'Salary', type: 'income', amount: 5200 },
  { id: 2, date: '2024-11-03', desc: 'Grocery Store', category: 'Food', type: 'expense', amount: 142.5 },
  { id: 3, date: '2024-11-05', desc: 'Uber Ride', category: 'Transport', type: 'expense', amount: 18.3 },
  { id: 4, date: '2024-11-07', desc: 'Netflix Subscription', category: 'Entertainment', type: 'expense', amount: 15.99 },
  { id: 5, date: '2024-11-09', desc: 'Freelance Project', category: 'Freelance', type: 'income', amount: 850 },
  { id: 6, date: '2024-11-11', desc: 'Electricity Bill', category: 'Utilities', type: 'expense', amount: 89 },
  { id: 7, date: '2024-11-13', desc: 'Amazon Shopping', category: 'Shopping', type: 'expense', amount: 234.6 },
  { id: 8, date: '2024-11-15', desc: 'Gym Membership', category: 'Health', type: 'expense', amount: 45 },
  { id: 9, date: '2024-11-17', desc: 'Restaurant Dinner', category: 'Food', type: 'expense', amount: 76.4 },
  { id: 10, date: '2024-11-20', desc: 'Apartment Rent', category: 'Rent', type: 'expense', amount: 1400 },
  { id: 11, date: '2024-11-22', desc: 'Bus Pass', category: 'Transport', type: 'expense', amount: 32 },
  { id: 12, date: '2024-11-25', desc: 'Doctor Visit', category: 'Health', type: 'expense', amount: 120 },
  { id: 13, date: '2024-11-28', desc: 'Coffee & Snacks', category: 'Food', type: 'expense', amount: 28.5 },
  { id: 14, date: '2024-12-01', desc: 'Monthly Salary', category: 'Salary', type: 'income', amount: 5200 },
  { id: 15, date: '2024-12-03', desc: 'Weekend Groceries', category: 'Food', type: 'expense', amount: 165.2 },
  { id: 16, date: '2024-12-05', desc: 'Spotify Premium', category: 'Entertainment', type: 'expense', amount: 9.99 },
  { id: 17, date: '2024-12-08', desc: 'Freelance Design', category: 'Freelance', type: 'income', amount: 1200 },
  { id: 18, date: '2024-12-10', desc: 'Pharmacy', category: 'Health', type: 'expense', amount: 34.5 },
  { id: 19, date: '2024-12-13', desc: 'Holiday Shopping', category: 'Shopping', type: 'expense', amount: 412 },
  { id: 20, date: '2024-12-15', desc: 'Water Bill', category: 'Utilities', type: 'expense', amount: 55 },
  { id: 21, date: '2024-12-18', desc: 'Movie Tickets', category: 'Entertainment', type: 'expense', amount: 42 },
  { id: 22, date: '2024-12-20', desc: 'Apartment Rent', category: 'Rent', type: 'expense', amount: 1400 },
  { id: 23, date: '2024-12-23', desc: 'Christmas Dinner', category: 'Food', type: 'expense', amount: 118 },
  { id: 24, date: '2024-12-27', desc: 'Metro Card', category: 'Transport', type: 'expense', amount: 40 },
  { id: 25, date: '2025-01-01', desc: 'Monthly Salary', category: 'Salary', type: 'income', amount: 5500 },
  { id: 26, date: '2025-01-04', desc: 'Grocery Run', category: 'Food', type: 'expense', amount: 138 },
  { id: 27, date: '2025-01-06', desc: 'Yoga Class', category: 'Health', type: 'expense', amount: 60 },
  { id: 28, date: '2025-01-09', desc: 'Freelance Consulting', category: 'Freelance', type: 'income', amount: 650 },
  { id: 29, date: '2025-01-12', desc: 'New Headphones', category: 'Shopping', type: 'expense', amount: 185 },
  { id: 30, date: '2025-01-15', desc: 'Internet Bill', category: 'Utilities', type: 'expense', amount: 65 },
  { id: 31, date: '2025-01-18', desc: 'Sushi Restaurant', category: 'Food', type: 'expense', amount: 92 },
  { id: 32, date: '2025-01-20', desc: 'Apartment Rent', category: 'Rent', type: 'expense', amount: 1400 },
  { id: 33, date: '2025-01-23', desc: 'Taxi Fare', category: 'Transport', type: 'expense', amount: 24.5 },
  { id: 34, date: '2025-01-26', desc: 'Concert Tickets', category: 'Entertainment', type: 'expense', amount: 95 },
  { id: 35, date: '2025-01-29', desc: 'Side Project Bonus', category: 'Freelance', type: 'income', amount: 400 },
];

// ─── INIT ────────────────────────────────────────────────────────────────────

function init() {
  const saved = localStorage.getItem(STORAGE_KEY);
  state.transactions = saved ? JSON.parse(saved) : SEED_TRANSACTIONS;
  state.role = localStorage.getItem(ROLE_KEY) || 'admin';
  const theme = localStorage.getItem(THEME_KEY) || 'dark';
  if (theme === 'light') document.documentElement.setAttribute('data-theme', 'light');

  document.getElementById('role-select').value = state.role;
  applyRole(state.role);
  populateCategoryFilter();
  renderTransactions();
  updateDashboard();
  renderInsights();
  updateBadge();
}

function saveData() {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(state.transactions));
}

// ─── NAVIGATION ──────────────────────────────────────────────────────────────

function navigate(page) {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
  document.getElementById('page-' + page).classList.add('active');
  document.querySelector(`[data-page="${page}"]`).classList.add('active');
  const titles = { dashboard: 'Dashboard', transactions: 'Transactions', insights: 'Insights' };
  document.getElementById('topbar-title').textContent = titles[page] || page;
  if (page === 'insights') renderInsights();
  closeSidebar();
}

// ─── SIDEBAR MOBILE ───────────────────────────────────────────────────────────

function toggleSidebar() {
  document.getElementById('sidebar').classList.toggle('open');
  document.getElementById('mobile-overlay').classList.toggle('open');
}

function closeSidebar() {
  document.getElementById('sidebar').classList.remove('open');
  document.getElementById('mobile-overlay').classList.remove('open');
}

// ─── ROLE ─────────────────────────────────────────────────────────────────────

function switchRole(role) {
  state.role = role;
  localStorage.setItem(ROLE_KEY, role);
  applyRole(role);
  renderTransactions();
}

function applyRole(role) {
  const isAdmin = role === 'admin';
  document.body.classList.toggle('is-admin', isAdmin);
  const ind = document.getElementById('role-indicator');
  if (isAdmin) {
    ind.className = 'role-indicator role-admin';
    ind.textContent = '● Admin';
    document.getElementById('user-name').textContent = 'Admin User';
    document.getElementById('user-role-label').textContent = 'Administrator';
    document.getElementById('user-avatar').textContent = 'A';
  } else {
    ind.className = 'role-indicator role-viewer';
    ind.textContent = '○ Viewer';
    document.getElementById('user-name').textContent = 'View Only';
    document.getElementById('user-role-label').textContent = 'Viewer';
    document.getElementById('user-avatar').textContent = 'V';
  }
  // Show/hide admin action column header
  const actionTh = document.querySelector('.trans-table thead tr th:last-child');
  if (actionTh) actionTh.style.display = isAdmin ? '' : 'none';
}

// ─── THEME ────────────────────────────────────────────────────────────────────

function toggleTheme() {
  const isLight = document.documentElement.getAttribute('data-theme') === 'light';
  if (isLight) {
    document.documentElement.removeAttribute('data-theme');
    localStorage.setItem(THEME_KEY, 'dark');
    document.querySelector('.theme-btn').textContent = '🌙';
  } else {
    document.documentElement.setAttribute('data-theme', 'light');
    localStorage.setItem(THEME_KEY, 'light');
    document.querySelector('.theme-btn').textContent = '☀️';
  }
  rebuildCharts();
}

// ─── HELPERS ──────────────────────────────────────────────────────────────────

function fmt(n) {
  return '$' + Math.abs(n).toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: 2 });
}

function fmtShort(n) {
  if (n >= 1000) return '$' + (n / 1000).toFixed(1) + 'k';
  return '$' + Math.round(n);
}

function getCatClass(cat) {
  const map = {
    Food: 'pill-food', Transport: 'pill-transport', Shopping: 'pill-shopping',
    Health: 'pill-health', Entertainment: 'pill-entertainment', Utilities: 'pill-utilities',
    Salary: 'pill-salary', Freelance: 'pill-freelance', Rent: 'pill-rent', Other: 'pill-other'
  };
  return map[cat] || 'pill-other';
}

function getFilteredByPeriod(period) {
  const now = new Date('2025-01-31');
  return state.transactions.filter(t => {
    const d = new Date(t.date);
    if (period === 'month') {
      return d.getFullYear() === now.getFullYear() && d.getMonth() === now.getMonth();
    }
    if (period === 'last3') {
      const cutoff = new Date(now); cutoff.setMonth(cutoff.getMonth() - 3);
      return d >= cutoff;
    }
    return true;
  });
}

function updateBadge() {
  document.getElementById('trans-badge').textContent = state.transactions.length;
}

// ─── SORT ─────────────────────────────────────────────────────────────────────

function toggleSort(field) {
  if (state.sortField === field) {
    state.sortDir = state.sortDir === 'asc' ? 'desc' : 'asc';
  } else {
    state.sortField = field;
    state.sortDir = 'desc';
  }
  document.getElementById('sort-date-icon').textContent = state.sortField === 'date' ? (state.sortDir === 'asc' ? '↑' : '↓') : '';
  document.getElementById('sort-amount-icon').textContent = state.sortField === 'amount' ? (state.sortDir === 'asc' ? '↑' : '↓') : '';
  renderTransactions();
}

function sortTransactions(arr) {
  return [...arr].sort((a, b) => {
    let va = a[state.sortField], vb = b[state.sortField];
    if (state.sortField === 'date') { va = new Date(va); vb = new Date(vb); }
    if (va < vb) return state.sortDir === 'asc' ? -1 : 1;
    if (va > vb) return state.sortDir === 'asc' ? 1 : -1;
    return 0;
  });
}

// ─── CATEGORY FILTER POPULATE ─────────────────────────────────────────────────

function populateCategoryFilter() {
  const cats = [...new Set(state.transactions.map(t => t.category))].sort();
  const sel = document.getElementById('cat-filter');
  sel.innerHTML = '<option value="all">All Categories</option>';
  cats.forEach(c => {
    const o = document.createElement('option');
    o.value = c; o.textContent = c;
    sel.appendChild(o);
  });
}

// ─── RENDER TRANSACTIONS ──────────────────────────────────────────────────────

function renderTransactions() {
  const search = document.getElementById('search-input').value.toLowerCase();
  const typeF = document.getElementById('type-filter').value;
  const catF = document.getElementById('cat-filter').value;
  const isAdmin = state.role === 'admin';

  let data = state.transactions.filter(t => {
    const matchSearch = t.desc.toLowerCase().includes(search) || t.category.toLowerCase().includes(search);
    const matchType = typeF === 'all' || t.type === typeF;
    const matchCat = catF === 'all' || t.category === catF;
    return matchSearch && matchType && matchCat;
  });

  data = sortTransactions(data);

  const tbody = document.getElementById('trans-tbody');
  const empty = document.getElementById('empty-state');

  document.getElementById('trans-count-label').textContent =
    `Showing ${data.length} of ${state.transactions.length} transactions`;

  if (data.length === 0) {
    tbody.innerHTML = '';
    empty.style.display = 'block';
    return;
  }

  empty.style.display = 'none';
  tbody.innerHTML = data.map(t => `
    <tr>
      <td style="color:var(--text2);font-size:12px;font-family:'Courier New',monospace;">${t.date}</td>
      <td style="font-weight:500;">${t.desc}</td>
      <td><span class="category-pill ${getCatClass(t.category)}">${t.category}</span></td>
      <td><span class="type-badge type-${t.type}">${t.type}</span></td>
      <td class="trans-amount ${t.type}">${t.type === 'income' ? '+' : '-'}${fmt(t.amount)}</td>
      <td style="display:${isAdmin ? '' : 'none'};">
        <button class="action-btn" onclick="editTransaction(${t.id})">✎ Edit</button>
        <button class="action-btn del" onclick="deleteTransaction(${t.id})">✕</button>
      </td>
    </tr>
  `).join('');
}

// ─── DASHBOARD ────────────────────────────────────────────────────────────────

let trendChart, pieChart, barChart;

function updateDashboard() {
  const period = document.getElementById('period-filter').value;
  const data = getFilteredByPeriod(period);

  const income = data.filter(t => t.type === 'income').reduce((s, t) => s + t.amount, 0);
  const expense = data.filter(t => t.type === 'expense').reduce((s, t) => s + t.amount, 0);
  const balance = income - expense;
  const savingsRate = income > 0 ? ((income - expense) / income * 100) : 0;

  document.getElementById('m-balance').textContent = fmt(balance);
  document.getElementById('m-income').textContent = fmt(income);
  document.getElementById('m-expense').textContent = fmt(expense);
  document.getElementById('m-savings').textContent = savingsRate.toFixed(1) + '%';

  document.getElementById('m-balance-change').textContent = balance >= 0 ? '▲ Positive cashflow' : '▼ Negative cashflow';
  document.getElementById('m-balance-change').className = 'metric-change ' + (balance >= 0 ? 'change-up' : 'change-down');
  document.getElementById('m-income-change').textContent = `${data.filter(t=>t.type==='income').length} income transactions`;
  document.getElementById('m-expense-change').textContent = `${data.filter(t=>t.type==='expense').length} expense transactions`;
  document.getElementById('m-savings-change').textContent = savingsRate >= 20 ? '▲ Healthy savings' : savingsRate >= 0 ? '◐ Moderate savings' : '▼ Spending over income';
  document.getElementById('m-savings-change').className = 'metric-change ' + (savingsRate >= 20 ? 'change-up' : savingsRate >= 0 ? '' : 'change-down');

  buildTrendChart(data);
  buildPieChart(data);
  buildBarChart(data);
}

function getMonthlyData(data) {
  const months = {};
  data.forEach(t => {
    const key = t.date.slice(0, 7);
    if (!months[key]) months[key] = { income: 0, expense: 0 };
    if (t.type === 'income') months[key].income += t.amount;
    else months[key].expense += t.amount;
  });
  return months;
}

function buildTrendChart(data) {
  const monthly = getMonthlyData(data);
  const labels = Object.keys(monthly).sort();
  const incomes = labels.map(l => Math.round(monthly[l].income));
  const expenses = labels.map(l => Math.round(monthly[l].expense));
  const nets = labels.map((l, i) => incomes[i] - expenses[i]);
  const lblFmt = labels.map(l => {
    const [y, m] = l.split('-');
    return ['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'][+m-1] + ' ' + y.slice(2);
  });

  if (trendChart) trendChart.destroy();
  const ctx = document.getElementById('trendChart').getContext('2d');
  trendChart = new Chart(ctx, {
    type: 'line',
    data: {
      labels: lblFmt,
      datasets: [
        { label: 'Income', data: incomes, borderColor: '#6c63ff', backgroundColor: 'rgba(108,99,255,0.08)', fill: true, tension: 0.4, pointRadius: 4, pointBackgroundColor: '#6c63ff' },
        { label: 'Expenses', data: expenses, borderColor: '#f0556a', backgroundColor: 'rgba(240,85,106,0.06)', fill: true, tension: 0.4, pointRadius: 4, pointBackgroundColor: '#f0556a' },
        { label: 'Net', data: nets, borderColor: '#2dd4a0', backgroundColor: 'transparent', fill: false, tension: 0.4, borderDash: [4,3], pointRadius: 3, pointBackgroundColor: '#2dd4a0' }
      ]
    },
    options: {
      responsive: true, maintainAspectRatio: false,
      plugins: { legend: { display: false }, tooltip: { mode: 'index', intersect: false, callbacks: { label: ctx => ' ' + ctx.dataset.label + ': $' + ctx.parsed.y.toLocaleString() } } },
      scales: {
        x: { grid: { color: 'rgba(255,255,255,0.04)' }, ticks: { color: '#8b90a8', font: { size: 11 } } },
        y: { grid: { color: 'rgba(255,255,255,0.04)' }, ticks: { color: '#8b90a8', font: { size: 11 }, callback: v => fmtShort(v) } }
      }
    }
  });
}

function buildPieChart(data) {
  const catTotals = {};
  data.filter(t => t.type === 'expense').forEach(t => {
    catTotals[t.category] = (catTotals[t.category] || 0) + t.amount;
  });
  const sorted = Object.entries(catTotals).sort((a,b) => b[1]-a[1]);
  const labels = sorted.map(e => e[0]);
  const values = sorted.map(e => Math.round(e[1]));
  const colors = ['#6c63ff','#f0556a','#2dd4a0','#f5a623','#4da6ff','#b46eff','#ff6eb4','#2dd4bf','#aaa','#555'];
  const total = values.reduce((s,v) => s+v, 0);

  const legend = document.getElementById('pie-legend');
  legend.innerHTML = labels.map((l,i) => `<span class="legend-item"><span class="legend-dot" style="background:${colors[i]||'#888'};"></span>${l} ${total ? Math.round(values[i]/total*100) : 0}%</span>`).join('');

  if (pieChart) pieChart.destroy();
  if (!labels.length) return;
  const ctx = document.getElementById('pieChart').getContext('2d');
  pieChart = new Chart(ctx, {
    type: 'doughnut',
    data: { labels, datasets: [{ data: values, backgroundColor: colors, borderWidth: 2, borderColor: 'transparent', hoverOffset: 6 }] },
    options: {
      responsive: true, maintainAspectRatio: false, cutout: '65%',
      plugins: { legend: { display: false }, tooltip: { callbacks: { label: ctx => ' ' + ctx.label + ': $' + ctx.parsed.toLocaleString() } } }
    }
  });
}

function buildBarChart(data) {
  const days = ['Mon','Tue','Wed','Thu','Fri','Sat','Sun'];
  const spending = [0,0,0,0,0,0,0];
  data.filter(t => t.type === 'expense').forEach(t => {
    const d = new Date(t.date);
    const dow = (d.getDay() + 6) % 7;
    spending[dow] += t.amount;
  });
  const norm = spending.map(v => Math.round(v / Math.max(...spending.map((_,i) => i)) || 1));

  if (barChart) barChart.destroy();
  const ctx = document.getElementById('barChart').getContext('2d');
  barChart = new Chart(ctx, {
    type: 'bar',
    data: {
      labels: days,
      datasets: [{
        label: 'Spending',
        data: spending.map(v => Math.round(v)),
        backgroundColor: spending.map((v,i) => v === Math.max(...spending) ? '#6c63ff' : 'rgba(108,99,255,0.3)'),
        borderRadius: 6, borderSkipped: false
      }]
    },
    options: {
      responsive: true, maintainAspectRatio: false,
      plugins: { legend: { display: false }, tooltip: { callbacks: { label: ctx => ' $' + ctx.parsed.y.toLocaleString() } } },
      scales: {
        x: { grid: { display: false }, ticks: { color: '#8b90a8', font: { size: 11 } } },
        y: { grid: { color: 'rgba(255,255,255,0.04)' }, ticks: { color: '#8b90a8', font: { size: 11 }, callback: v => fmtShort(v) } }
      }
    }
  });
}

function rebuildCharts() {
  updateDashboard();
  renderInsights();
}

// ─── INSIGHTS ─────────────────────────────────────────────────────────────────

let insightChart;

function renderInsights() {
  const data = state.transactions;
  const expenses = data.filter(t => t.type === 'expense');

  // Top category
  const catTotals = {};
  expenses.forEach(t => { catTotals[t.category] = (catTotals[t.category] || 0) + t.amount; });
  const sorted = Object.entries(catTotals).sort((a,b) => b[1]-a[1]);
  if (sorted.length) {
    const [topCat, topAmt] = sorted[0];
    const totalExp = expenses.reduce((s,t) => s+t.amount, 0);
    document.getElementById('ins-top-cat').textContent = topCat;
    document.getElementById('ins-top-cat-desc').textContent = fmt(topAmt) + ' spent · ' + Math.round(topAmt/totalExp*100) + '% of all expenses';
    const barsHtml = sorted.slice(0,5).map(([cat, amt]) => `
      <div class="progress-bar-wrap" style="margin-bottom:10px;">
        <div class="progress-label"><span>${cat}</span><span>${fmt(amt)}</span></div>
        <div class="progress-track"><div class="progress-fill" style="background:var(--accent);width:${Math.round(amt/sorted[0][1]*100)}%;"></div></div>
      </div>
    `).join('');
    document.getElementById('ins-cat-bars').innerHTML = barsHtml;
  }

  // Monthly comparison
  const now = new Date('2025-01-31');
  const thisMonth = data.filter(t => { const d = new Date(t.date); return d.getMonth() === now.getMonth() && d.getFullYear() === now.getFullYear(); });
  const prevDate = new Date(now); prevDate.setMonth(prevDate.getMonth() - 1);
  const lastMonth = data.filter(t => { const d = new Date(t.date); return d.getMonth() === prevDate.getMonth() && d.getFullYear() === prevDate.getFullYear(); });

  const thisInc = thisMonth.filter(t=>t.type==='income').reduce((s,t)=>s+t.amount,0);
  const thisExp = thisMonth.filter(t=>t.type==='expense').reduce((s,t)=>s+t.amount,0);
  const lastInc = lastMonth.filter(t=>t.type==='income').reduce((s,t)=>s+t.amount,0);
  const lastExp = lastMonth.filter(t=>t.type==='expense').reduce((s,t)=>s+t.amount,0);

  document.getElementById('ins-monthly').innerHTML = `
    <div class="month-block">
      <div class="month-label">This Month Income</div>
      <div class="month-val" style="color:var(--green);">${fmt(thisInc)}</div>
    </div>
    <div class="month-block">
      <div class="month-label">Last Month Income</div>
      <div class="month-val" style="color:var(--green);">${fmt(lastInc)}</div>
    </div>
    <div class="month-block">
      <div class="month-label">This Month Expenses</div>
      <div class="month-val" style="color:var(--red);">${fmt(thisExp)}</div>
    </div>
    <div class="month-block">
      <div class="month-label">Last Month Expenses</div>
      <div class="month-val" style="color:var(--red);">${fmt(lastExp)}</div>
    </div>
  `;

  const expChange = lastExp > 0 ? ((thisExp - lastExp) / lastExp * 100) : 0;
  const dir = expChange > 0 ? '▲ up' : '▼ down';
  document.getElementById('ins-monthly-desc').textContent =
    `Expenses ${dir} ${Math.abs(expChange).toFixed(1)}% vs last month`;

  // Savings
  const totalInc = data.filter(t=>t.type==='income').reduce((s,t)=>s+t.amount,0);
  const totalExp = data.filter(t=>t.type==='expense').reduce((s,t)=>s+t.amount,0);
  const saved = totalInc - totalExp;
  const pct = totalInc > 0 ? Math.round(totalExp/totalInc*100) : 0;
  document.getElementById('ins-savings').textContent = fmt(saved);
  document.getElementById('ins-savings-desc').textContent = 'Total net savings across all time';
  document.getElementById('ins-savings-pct').textContent = pct + '% of income spent';
  document.getElementById('ins-savings-bar').style.width = Math.min(pct, 100) + '%';
  document.getElementById('ins-savings-bar').style.background = pct > 90 ? 'var(--red)' : pct > 70 ? 'var(--amber)' : 'var(--green)';

  // Observations
  const obs = [];
  if (sorted.length) {
    const [top] = sorted[0];
    obs.push({ icon: '🔥', text: `<strong>${top}</strong> is your biggest spending category` });
  }
  if (thisExp > lastExp) obs.push({ icon: '📊', text: `Spending increased this month vs last month` });
  else if (thisExp < lastExp) obs.push({ icon: '✅', text: `Spending decreased this month — great job!` });
  if (pct < 60) obs.push({ icon: '🏆', text: `You're saving over 40% of your income — excellent!` });
  else if (pct > 90) obs.push({ icon: '⚠️', text: `Spending is very close to income — consider reviewing` });
  const freelance = data.filter(t=>t.category==='Freelance' && t.type==='income').reduce((s,t)=>s+t.amount,0);
  if (freelance > 0) obs.push({ icon: '💼', text: `Freelance income contributes ${fmt(freelance)} total` });

  document.getElementById('ins-observations').innerHTML = obs.map(o => `
    <div style="display:flex;align-items:flex-start;gap:10px;padding:10px;background:var(--bg3);border-radius:var(--radius-sm);">
      <span style="font-size:16px;">${o.icon}</span>
      <span style="font-size:12.5px;color:var(--text2);line-height:1.5;">${o.text}</span>
    </div>
  `).join('');

  // Insight chart: top 3 categories over months
  buildInsightChart();
}

function buildInsightChart() {
  const data = state.transactions.filter(t => t.type === 'expense');
  const catTotals = {};
  data.forEach(t => { catTotals[t.category] = (catTotals[t.category] || 0) + t.amount; });
  const top3 = Object.entries(catTotals).sort((a,b)=>b[1]-a[1]).slice(0,3).map(e=>e[0]);

  const months = {};
  data.forEach(t => {
    const key = t.date.slice(0,7);
    if (!months[key]) months[key] = {};
    top3.forEach(c => { if (!months[key][c]) months[key][c] = 0; });
    if (top3.includes(t.category)) months[key][t.category] += t.amount;
  });

  const labels = Object.keys(months).sort().map(l => {
    const [y,m] = l.split('-');
    return ['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'][+m-1];
  });
  const monthKeys = Object.keys(months).sort();
  const colors = ['#6c63ff','#f0556a','#2dd4a0'];

  if (insightChart) insightChart.destroy();
  const ctx = document.getElementById('insightChart').getContext('2d');
  insightChart = new Chart(ctx, {
    type: 'bar',
    data: {
      labels,
      datasets: top3.map((cat, i) => ({
        label: cat,
        data: monthKeys.map(k => Math.round(months[k][cat] || 0)),
        backgroundColor: colors[i],
        borderRadius: 4
      }))
    },
    options: {
      responsive: true, maintainAspectRatio: false,
      plugins: {
        legend: { display: false },
        tooltip: { callbacks: { label: ctx => ' ' + ctx.dataset.label + ': $' + ctx.parsed.y.toLocaleString() } }
      },
      scales: {
        x: { stacked: false, grid: { display: false }, ticks: { color: '#8b90a8', font: { size: 11 } } },
        y: { stacked: false, grid: { color: 'rgba(255,255,255,0.04)' }, ticks: { color: '#8b90a8', font: { size: 11 }, callback: v => fmtShort(v) } }
      }
    }
  });

  // Custom legend below
  const canvas = document.getElementById('insightChart');
  let legWrap = document.getElementById('insight-cat-legend');
  if (!legWrap) {
    legWrap = document.createElement('div');
    legWrap.id = 'insight-cat-legend';
    legWrap.className = 'chart-legend';
    legWrap.style.marginTop = '10px';
    canvas.parentElement.parentElement.appendChild(legWrap);
  }
  legWrap.innerHTML = top3.map((cat, i) => `<span class="legend-item"><span class="legend-dot" style="background:${colors[i]};"></span>${cat}</span>`).join('');
}

// ─── MODAL / ADD-EDIT ─────────────────────────────────────────────────────────

function openModal(id) {
  state.editId = id || null;
  document.getElementById('modal-title').textContent = id ? 'Edit Transaction' : 'Add Transaction';
  if (id) {
    const t = state.transactions.find(x => x.id === id);
    if (!t) return;
    document.getElementById('f-date').value = t.date;
    document.getElementById('f-type').value = t.type;
    document.getElementById('f-desc').value = t.desc;
    document.getElementById('f-amount').value = t.amount;
    document.getElementById('f-category').value = t.category;
  } else {
    document.getElementById('f-date').value = new Date().toISOString().slice(0,10);
    document.getElementById('f-type').value = 'expense';
    document.getElementById('f-desc').value = '';
    document.getElementById('f-amount').value = '';
    document.getElementById('f-category').value = 'Food';
  }
  document.getElementById('modal-overlay').classList.add('open');
}

function closeModal() {
  document.getElementById('modal-overlay').classList.remove('open');
  state.editId = null;
}

function saveTransaction() {
  const date = document.getElementById('f-date').value;
  const type = document.getElementById('f-type').value;
  const desc = document.getElementById('f-desc').value.trim();
  const amount = parseFloat(document.getElementById('f-amount').value);
  const category = document.getElementById('f-category').value;

  if (!date || !desc || isNaN(amount) || amount <= 0) {
    showToast('⚠️ Please fill all fields correctly');
    return;
  }

  if (state.editId) {
    const idx = state.transactions.findIndex(t => t.id === state.editId);
    if (idx !== -1) state.transactions[idx] = { ...state.transactions[idx], date, type, desc, amount, category };
    showToast('✅ Transaction updated');
  } else {
    const newId = Math.max(0, ...state.transactions.map(t => t.id)) + 1;
    state.transactions.push({ id: newId, date, type, desc, amount, category });
    showToast('✅ Transaction added');
  }

  saveData();
  closeModal();
  populateCategoryFilter();
  renderTransactions();
  updateDashboard();
  updateBadge();
}

function editTransaction(id) {
  if (state.role !== 'admin') return;
  openModal(id);
}

function deleteTransaction(id) {
  if (state.role !== 'admin') return;
  if (!confirm('Delete this transaction?')) return;
  state.transactions = state.transactions.filter(t => t.id !== id);
  saveData();
  renderTransactions();
  updateDashboard();
  updateBadge();
  showToast('🗑️ Transaction deleted');
}

// ─── EXPORT ───────────────────────────────────────────────────────────────────

function exportData() {
  const headers = ['Date','Description','Category','Type','Amount'];
  const rows = state.transactions.map(t => [t.date, t.desc, t.category, t.type, t.amount]);
  const csv = [headers, ...rows].map(r => r.join(',')).join('\n');
  const blob = new Blob([csv], { type: 'text/csv' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url; a.download = 'finflow-transactions.csv';
  a.click(); URL.revokeObjectURL(url);
  showToast('📥 Export downloaded');
}

// ─── TOAST ────────────────────────────────────────────────────────────────────

let toastTimer;
function showToast(msg) {
  const t = document.getElementById('toast');
  t.textContent = msg; t.classList.remove('hidden');
  clearTimeout(toastTimer);
  toastTimer = setTimeout(() => t.classList.add('hidden'), 3000);
}

// ─── KEYBOARD ────────────────────────────────────────────────────────────────

document.addEventListener('keydown', e => {
  if (e.key === 'Escape') closeModal();
});

document.getElementById('modal-overlay').addEventListener('click', e => {
  if (e.target === document.getElementById('modal-overlay')) closeModal();
});

// ─── BOOT ────────────────────────────────────────────────────────────────────
init();
</script>
</body>
</html>