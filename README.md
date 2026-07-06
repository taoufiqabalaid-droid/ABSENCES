<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GestAbs — Gestion d'Absences</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;400;500;600;700&family=DM+Sans:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --bg: #0c0f14;
            --bg-elevated: #13171e;
            --bg-card: #1a1f2a;
            --bg-hover: #222836;
            --fg: #e8ecf4;
            --fg-muted: #7a8599;
            --fg-dim: #4a5568;
            --accent: #10b981;
            --accent-soft: rgba(16, 185, 129, 0.12);
            --accent-glow: rgba(16, 185, 129, 0.25);
            --warning: #f59e0b;
            --warning-soft: rgba(245, 158, 11, 0.12);
            --danger: #ef4444;
            --danger-soft: rgba(239, 68, 68, 0.12);
            --info: #06b6d4;
            --info-soft: rgba(6, 182, 212, 0.12);
            --border: #252d3a;
            --border-light: #2f3a4a;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; }
        body {
            font-family: 'DM Sans', sans-serif;
            background: var(--bg);
            color: var(--fg);
            min-height: 100vh;
            overflow-x: hidden;
        }
        h1, h2, h3, h4, h5 { font-family: 'Space Grotesk', sans-serif; }

        /* Fond animé */
        .bg-glow {
            position: fixed;
            top: -200px;
            right: -200px;
            width: 600px;
            height: 600px;
            background: radial-gradient(circle, var(--accent-glow) 0%, transparent 70%);
            border-radius: 50%;
            pointer-events: none;
            z-index: 0;
            animation: glowPulse 8s ease-in-out infinite;
        }
        .bg-glow-2 {
            position: fixed;
            bottom: -300px;
            left: -100px;
            width: 500px;
            height: 500px;
            background: radial-gradient(circle, rgba(6, 182, 212, 0.1) 0%, transparent 70%);
            border-radius: 50%;
            pointer-events: none;
            z-index: 0;
            animation: glowPulse 10s ease-in-out infinite reverse;
        }
        @keyframes glowPulse {
            0%, 100% { opacity: 0.4; transform: scale(1); }
            50% { opacity: 0.7; transform: scale(1.15); }
        }

        /* Sidebar */
        .sidebar {
            position: fixed;
            left: 0; top: 0;
            width: 260px;
            height: 100vh;
            background: var(--bg-elevated);
            border-right: 1px solid var(--border);
            z-index: 50;
            display: flex;
            flex-direction: column;
            transition: transform 0.3s ease;
        }
        .sidebar-logo {
            padding: 28px 24px;
            border-bottom: 1px solid var(--border);
            display: flex;
            align-items: center;
            gap: 12px;
        }
        .sidebar-logo .logo-icon {
            width: 40px; height: 40px;
            background: linear-gradient(135deg, var(--accent), #059669);
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 18px;
            color: #fff;
            box-shadow: 0 4px 15px var(--accent-glow);
        }
        .sidebar-logo span {
            font-family: 'Space Grotesk', sans-serif;
            font-weight: 700;
            font-size: 20px;
            letter-spacing: -0.5px;
        }
        .sidebar-nav { flex: 1; padding: 16px 12px; overflow-y: auto; }
        .nav-section-label {
            font-size: 10px;
            font-weight: 600;
            text-transform: uppercase;
            letter-spacing: 1.5px;
            color: var(--fg-dim);
            padding: 16px 12px 8px;
        }
        .nav-item {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 11px 14px;
            border-radius: 10px;
            cursor: pointer;
            color: var(--fg-muted);
            font-size: 14px;
            font-weight: 500;
            transition: all 0.2s;
            margin-bottom: 2px;
            position: relative;
        }
        .nav-item:hover { background: var(--bg-hover); color: var(--fg); }
        .nav-item.active {
            background: var(--accent-soft);
            color: var(--accent);
        }
        .nav-item.active::before {
            content: '';
            position: absolute;
            left: 0; top: 50%;
            transform: translateY(-50%);
            width: 3px; height: 20px;
            background: var(--accent);
            border-radius: 0 3px 3px 0;
        }
        .nav-item i { width: 20px; text-align: center; font-size: 15px; }
        .nav-badge {
            margin-left: auto;
            background: var(--danger);
            color: #fff;
            font-size: 10px;
            font-weight: 700;
            padding: 2px 7px;
            border-radius: 10px;
            min-width: 20px;
            text-align: center;
        }

        /* Contenu principal */
        .main-content {
            margin-left: 260px;
            min-height: 100vh;
            position: relative;
            z-index: 1;
        }

        /* Header */
        .top-header {
            position: sticky;
            top: 0;
            z-index: 40;
            background: rgba(12, 15, 20, 0.8);
            backdrop-filter: blur(20px);
            border-bottom: 1px solid var(--border);
            padding: 16px 32px;
            display: flex;
            align-items: center;
            justify-content: space-between;
        }
        .search-box {
            display: flex;
            align-items: center;
            gap: 10px;
            background: var(--bg-card);
            border: 1px solid var(--border);
            border-radius: 10px;
            padding: 9px 16px;
            width: 360px;
            transition: border-color 0.2s;
        }
        .search-box:focus-within { border-color: var(--accent); }
        .search-box i { color: var(--fg-dim); font-size: 14px; }
        .search-box input {
            background: none;
            border: none;
            outline: none;
            color: var(--fg);
            font-size: 14px;
            width: 100%;
            font-family: 'DM Sans', sans-serif;
        }
        .search-box input::placeholder { color: var(--fg-dim); }
        .header-actions { display: flex; align-items: center; gap: 12px; }
        .header-btn {
            width: 40px; height: 40px;
            border-radius: 10px;
            border: 1px solid var(--border);
            background: var(--bg-card);
            color: var(--fg-muted);
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.2s;
            position: relative;
        }
        .header-btn:hover { border-color: var(--accent); color: var(--accent); }
        .header-btn .notif-dot {
            position: absolute;
            top: 8px; right: 8px;
            width: 7px; height: 7px;
            background: var(--danger);
            border-radius: 50%;
            border: 2px solid var(--bg-card);
        }
        .user-avatar {
            width: 40px; height: 40px;
            border-radius: 10px;
            background: linear-gradient(135deg, var(--accent), #059669);
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: 700;
            font-size: 14px;
            color: #fff;
            cursor: pointer;
        }

        /* Pages */
        .page { display: none; padding: 32px; animation: fadeIn 0.3s ease; }
        .page.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }

        /* KPI Cards */
        .kpi-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 20px; margin-bottom: 28px; }
        .kpi-card {
            background: var(--bg-card);
            border: 1px solid var(--border);
            border-radius: 14px;
            padding: 22px;
            position: relative;
            overflow: hidden;
            transition: all 0.3s;
        }
        .kpi-card:hover { border-color: var(--border-light); transform: translateY(-2px); }
        .kpi-card .kpi-icon {
            width: 44px; height: 44px;
            border-radius: 11px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 18px;
            margin-bottom: 16px;
        }
        .kpi-card .kpi-value {
            font-family: 'Space Grotesk', sans-serif;
            font-size: 32px;
            font-weight: 700;
            letter-spacing: -1px;
            margin-bottom: 4px;
        }
        .kpi-card .kpi-label { color: var(--fg-muted); font-size: 13px; }
        .kpi-card .kpi-trend {
            font-size: 12px;
            font-weight: 600;
            margin-top: 8px;
            display: flex;
            align-items: center;
            gap: 4px;
        }
        .kpi-card .kpi-glow {
            position: absolute;
            top: -30px; right: -30px;
            width: 100px; height: 100px;
            border-radius: 50%;
            opacity: 0.15;
            pointer-events: none;
        }

        /* Tableaux */
        .data-table-wrapper {
            background: var(--bg-card);
            border: 1px solid var(--border);
            border-radius: 14px;
            overflow: hidden;
        }
        .table-header {
            padding: 20px 24px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            border-bottom: 1px solid var(--border);
        }
        .table-header h3 { font-size: 16px; font-weight: 600; }
        .table-filters { display: flex; gap: 10px; align-items: center; }
        .filter-btn {
            padding: 7px 14px;
            border-radius: 8px;
            border: 1px solid var(--border);
            background: transparent;
            color: var(--fg-muted);
            font-size: 13px;
            font-family: 'DM Sans', sans-serif;
            cursor: pointer;
            transition: all 0.2s;
        }
        .filter-btn:hover { border-color: var(--fg-dim); color: var(--fg); }
        .filter-btn.active { background: var(--accent-soft); border-color: var(--accent); color: var(--accent); }

        table { width: 100%; border-collapse: collapse; }
        thead th {
            text-align: left;
            padding: 14px 24px;
            font-size: 11px;
            font-weight: 600;
            text-transform: uppercase;
            letter-spacing: 1px;
            color: var(--fg-dim);
            border-bottom: 1px solid var(--border);
        }
        tbody tr {
            border-bottom: 1px solid var(--border);
            transition: background 0.15s;
        }
        tbody tr:last-child { border-bottom: none; }
        tbody tr:hover { background: var(--bg-hover); }
        tbody td {
            padding: 16px 24px;
            font-size: 14px;
            vertical-align: middle;
        }
        .employee-cell {
            display: flex;
            align-items: center;
            gap: 12px;
        }
        .emp-avatar {
            width: 36px; height: 36px;
            border-radius: 9px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: 700;
            font-size: 13px;
            color: #fff;
            flex-shrink: 0;
        }
        .emp-name { font-weight: 600; font-size: 14px; }
        .emp-dept { font-size: 12px; color: var(--fg-muted); }

        /* Badges statut */
        .badge {
            display: inline-flex;
            align-items: center;
            gap: 6px;
            padding: 5px 12px;
            border-radius: 8px;
            font-size: 12px;
            font-weight: 600;
        }
        .badge-pending { background: var(--warning-soft); color: var(--warning); }
        .badge-approved { background: var(--accent-soft); color: var(--accent); }
        .badge-rejected { background: var(--danger-soft); color: var(--danger); }
        .badge-type {
            background: var(--info-soft);
            color: var(--info);
        }

        /* Boutons d'action */
        .action-btn {
            width: 32px; height: 32px;
            border-radius: 8px;
            border: 1px solid var(--border);
            background: transparent;
            color: var(--fg-muted);
            display: inline-flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.2s;
            font-size: 13px;
        }
        .action-btn:hover { border-color: var(--accent); color: var(--accent); }
        .action-btn.danger:hover { border-color: var(--danger); color: var(--danger); }

        /* Bouton principal */
        .btn-primary {
            display: inline-flex;
            align-items: center;
            gap: 8px;
            padding: 10px 20px;
            background: linear-gradient(135deg, var(--accent), #059669);
            color: #fff;
            border: none;
            border-radius: 10px;
            font-size: 14px;
            font-weight: 600;
            font-family: 'DM Sans', sans-serif;
            cursor: pointer;
            transition: all 0.25s;
            box-shadow: 0 4px 15px var(--accent-glow);
        }
        .btn-primary:hover { transform: translateY(-1px); box-shadow: 0 6px 25px var(--accent-glow); }
        .btn-secondary {
            display: inline-flex;
            align-items: center;
            gap: 8px;
            padding: 10px 20px;
            background: transparent;
            color: var(--fg);
            border: 1px solid var(--border);
            border-radius: 10px;
            font-size: 14px;
            font-weight: 500;
            font-family: 'DM Sans', sans-serif;
            cursor: pointer;
            transition: all 0.2s;
        }
        .btn-secondary:hover { border-color: var(--fg-dim); background: var(--bg-hover); }

        /* Modal */
        .modal-overlay {
            position: fixed;
            inset: 0;
            background: rgba(0,0,0,0.6);
            backdrop-filter: blur(6px);
            z-index: 100;
            display: none;
            align-items: center;
            justify-content: center;
            animation: fadeIn 0.2s ease;
        }
        .modal-overlay.show { display: flex; }
        .modal {
            background: var(--bg-elevated);
            border: 1px solid var(--border);
            border-radius: 18px;
            width: 520px;
            max-width: 95vw;
            max-height: 90vh;
            overflow-y: auto;
            animation: modalIn 0.3s ease;
        }
        @keyframes modalIn { from { opacity: 0; transform: scale(0.95) translateY(10px); } to { opacity: 1; transform: scale(1) translateY(0); } }
        .modal-head {
            padding: 24px 28px 0;
            display: flex;
            align-items: center;
            justify-content: space-between;
        }
        .modal-head h3 { font-size: 18px; font-weight: 700; }
        .modal-close {
            width: 34px; height: 34px;
            border-radius: 8px;
            border: 1px solid var(--border);
            background: transparent;
            color: var(--fg-muted);
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.2s;
        }
        .modal-close:hover { border-color: var(--danger); color: var(--danger); }
        .modal-body { padding: 24px 28px; }
        .modal-footer {
            padding: 0 28px 24px;
            display: flex;
            gap: 12px;
            justify-content: flex-end;
        }

        /* Formulaires */
        .form-group { margin-bottom: 18px; }
        .form-label {
            display: block;
            font-size: 13px;
            font-weight: 600;
            color: var(--fg-muted);
            margin-bottom: 7px;
        }
        .form-input, .form-select {
            width: 100%;
            padding: 10px 14px;
            background: var(--bg-card);
            border: 1px solid var(--border);
            border-radius: 10px;
            color: var(--fg);
            font-size: 14px;
            font-family: 'DM Sans', sans-serif;
            outline: none;
            transition: border-color 0.2s;
        }
        .form-input:focus, .form-select:focus { border-color: var(--accent); }
        .form-select { appearance: none; cursor: pointer; }
        .form-row { display: grid; grid-template-columns: 1fr 1fr; gap: 14px; }
        option { background: var(--bg-card); color: var(--fg); }

        /* Calendrier */
        .calendar-container {
            background: var(--bg-card);
            border: 1px solid var(--border);
            border-radius: 14px;
            padding: 24px;
        }
        .cal-header {
            display: flex;
            align-items: center;
            justify-content: space-between;
            margin-bottom: 20px;
        }
        .cal-header h3 { font-size: 18px; font-weight: 700; }
        .cal-nav {
            display: flex;
            gap: 8px;
        }
        .cal-nav button {
            width: 36px; height: 36px;
            border-radius: 9px;
            border: 1px solid var(--border);
            background: transparent;
            color: var(--fg-muted);
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.2s;
        }
        .cal-nav button:hover { border-color: var(--accent); color: var(--accent); }
        .cal-grid {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            gap: 4px;
        }
        .cal-day-label {
            text-align: center;
            font-size: 11px;
            font-weight: 600;
            color: var(--fg-dim);
            text-transform: uppercase;
            letter-spacing: 1px;
            padding: 8px 0;
        }
        .cal-day {
            aspect-ratio: 1;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            border-radius: 10px;
            font-size: 14px;
            font-weight: 500;
            color: var(--fg-muted);
            cursor: pointer;
            transition: all 0.2s;
            position: relative;
            gap: 3px;
        }
        .cal-day:hover { background: var(--bg-hover); }
        .cal-day.today {
            background: var(--accent-soft);
            color: var(--accent);
            font-weight: 700;
        }
        .cal-day.other-month { color: var(--fg-dim); opacity: 0.4; }
        .cal-day.has-absence::after {
            content: '';
            width: 5px; height: 5px;
            border-radius: 50%;
            background: var(--danger);
            position: absolute;
            bottom: 6px;
        }
        .cal-day.has-approved::after { background: var(--accent); }
        .cal-day.has-pending::after { background: var(--warning); }

        /* Toast */
        .toast-container {
            position: fixed;
            top: 20px;
            right: 20px;
            z-index: 200;
            display: flex;
            flex-direction: column;
            gap: 10px;
        }
        .toast {
            padding: 14px 20px;
            border-radius: 12px;
            font-size: 14px;
            font-weight: 500;
            display: flex;
            align-items: center;
            gap: 10px;
            min-width: 300px;
            animation: toastIn 0.35s ease;
            border: 1px solid;
        }
        .toast-success { background: rgba(16,185,129,0.1); border-color: rgba(16,185,129,0.3); color: var(--accent); }
        .toast-error { background: rgba(239,68,68,0.1); border-color: rgba(239,68,68,0.3); color: var(--danger); }
        .toast-info { background: rgba(6,182,212,0.1); border-color: rgba(6,182,212,0.3); color: var(--info); }
        @keyframes toastIn { from { opacity: 0; transform: translateX(40px); } to { opacity: 1; transform: translateX(0); } }
        .toast-exit { animation: toastOut 0.3s ease forwards; }
        @keyframes toastOut { to { opacity: 0; transform: translateX(40px); } }

        /* Stats graphiques simples */
        .chart-bar-container { display: flex; align-items: flex-end; gap: 8px; height: 140px; padding-top: 10px; }
        .chart-bar-wrap { flex: 1; display: flex; flex-direction: column; align-items: center; gap: 6px; height: 100%; justify-content: flex-end; }
        .chart-bar {
            width: 100%;
            max-width: 40px;
            border-radius: 6px 6px 2px 2px;
            transition: height 0.6s ease;
            min-height: 4px;
        }
        .chart-bar-label { font-size: 11px; color: var(--fg-dim); }

        /* Composant grille employés */
        .emp-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(280px, 1fr)); gap: 16px; }
        .emp-card {
            background: var(--bg-card);
            border: 1px solid var(--border);
            border-radius: 14px;
            padding: 22px;
            transition: all 0.25s;
            cursor: pointer;
        }
        .emp-card:hover { border-color: var(--border-light); transform: translateY(-2px); }
        .emp-card-top { display: flex; align-items: center; gap: 14px; margin-bottom: 16px; }
        .emp-card-avatar {
            width: 48px; height: 48px;
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: 700;
            font-size: 16px;
            color: #fff;
        }
        .emp-card-info h4 { font-size: 15px; font-weight: 600; margin-bottom: 2px; }
        .emp-card-info p { font-size: 12px; color: var(--fg-muted); }
        .emp-card-stats { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 8px; }
        .emp-stat {
            text-align: center;
            padding: 8px 4px;
            background: var(--bg);
            border-radius: 8px;
        }
        .emp-stat-val { font-family: 'Space Grotesk', sans-serif; font-weight: 700; font-size: 18px; }
        .emp-stat-label { font-size: 10px; color: var(--fg-dim); text-transform: uppercase; letter-spacing: 0.5px; }

        /* Confirm dialog */
        .confirm-dialog {
            text-align: center;
            padding: 12px 0;
        }
        .confirm-icon {
            width: 60px; height: 60px;
            border-radius: 50%;
            background: var(--danger-soft);
            color: var(--danger);
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
            margin: 0 auto 16px;
        }
        .confirm-dialog h4 { font-size: 18px; font-weight: 700; margin-bottom: 8px; }
        .confirm-dialog p { color: var(--fg-muted); font-size: 14px; }
        .btn-danger {
            display: inline-flex;
            align-items: center;
            gap: 8px;
            padding: 10px 20px;
            background: var(--danger);
            color: #fff;
            border: none;
            border-radius: 10px;
            font-size: 14px;
            font-weight: 600;
            font-family: 'DM Sans', sans-serif;
            cursor: pointer;
            transition: all 0.2s;
        }
        .btn-danger:hover { background: #dc2626; }

        /* Scrollbar */
        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: var(--border-light); border-radius: 3px; }
        ::-webkit-scrollbar-thumb:hover { background: var(--fg-dim); }

        /* Mobile */
        .mobile-toggle {
            display: none;
            width: 40px; height: 40px;
            border-radius: 10px;
            border: 1px solid var(--border);
            background: var(--bg-card);
            color: var(--fg);
            cursor: pointer;
            align-items: center;
            justify-content: center;
            font-size: 18px;
        }
        @media (max-width: 1024px) {
            .kpi-grid { grid-template-columns: repeat(2, 1fr); }
        }
        @media (max-width: 768px) {
            .sidebar { transform: translateX(-100%); }
            .sidebar.open { transform: translateX(0); }
            .main-content { margin-left: 0; }
            .mobile-toggle { display: flex; }
            .kpi-grid { grid-template-columns: 1fr; }
            .search-box { width: 200px; }
            .table-filters { flex-wrap: wrap; }
            .form-row { grid-template-columns: 1fr; }
            .page { padding: 20px 16px; }
            .top-header { padding: 12px 16px; }
        }

        /* Animation d'entrée des lignes */
        .row-animate {
            animation: rowIn 0.3s ease forwards;
            opacity: 0;
        }
        @keyframes rowIn { from { opacity: 0; transform: translateX(-10px); } to { opacity: 1; transform: translateX(0); } }

        /* Solde de congés */
        .leave-balance {
            display: flex;
            align-items: center;
            gap: 12px;
            margin-top: 4px;
        }
        .balance-bar-bg {
            flex: 1;
            height: 6px;
            background: var(--bg);
            border-radius: 3px;
            overflow: hidden;
        }
        .balance-bar-fill {
            height: 100%;
            border-radius: 3px;
            transition: width 0.6s ease;
        }
        .balance-text { font-size: 12px; color: var(--fg-muted); white-space: nowrap; }

        /* Pagination */
        .pagination {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 6px;
            padding: 20px;
            border-top: 1px solid var(--border);
        }
        .page-btn {
            width: 36px; height: 36px;
            border-radius: 8px;
            border: 1px solid var(--border);
            background: transparent;
            color: var(--fg-muted);
            font-size: 13px;
            font-weight: 600;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.2s;
            font-family: 'DM Sans', sans-serif;
        }
        .page-btn:hover { border-color: var(--fg-dim); color: var(--fg); }
        .page-btn.active { background: var(--accent); border-color: var(--accent); color: #fff; }
    </style>
</head>
<body>

<!-- Fond lumineux animé -->
<div class="bg-glow"></div>
<div class="bg-glow-2"></div>

<!-- Conteneur de toasts -->
<div class="toast-container" id="toastContainer"></div>

<!-- Sidebar -->
<aside class="sidebar" id="sidebar">
    <div class="sidebar-logo">
        <div class="logo-icon"><i class="fa-solid fa-calendar-check"></i></div>
        <span>GestAbs</span>
    </div>
    <nav class="sidebar-nav">
        <div class="nav-section-label">Principal</div>
        <div class="nav-item active" data-page="dashboard" onclick="switchPage('dashboard')">
            <i class="fa-solid fa-chart-pie"></i> Tableau de bord
        </div>
        <div class="nav-item" data-page="absences" onclick="switchPage('absences')">
            <i class="fa-solid fa-list-check"></i> Absences
            <span class="nav-badge" id="pendingBadge">3</span>
        </div>
        <div class="nav-item" data-page="calendar" onclick="switchPage('calendar')">
            <i class="fa-solid fa-calendar-days"></i> Calendrier
        </div>
        <div class="nav-item" data-page="employees" onclick="switchPage('employees')">
            <i class="fa-solid fa-users"></i> Employes
        </div>
        <div class="nav-section-label">Parametres</div>
        <div class="nav-item" data-page="types" onclick="switchPage('types')">
            <i class="fa-solid fa-tags"></i> Types d'absence
        </div>
        <div class="nav-item" data-page="settings" onclick="switchPage('settings')">
            <i class="fa-solid fa-gear"></i> Configuration
        </div>
    </nav>
    <div style="padding: 16px 12px; border-top: 1px solid var(--border);">
        <div style="display: flex; align-items: center; gap: 12px; padding: 8px 12px;">
            <div class="user-avatar" style="width: 36px; height: 36px; font-size: 12px;">AD</div>
            <div>
                <div style="font-size: 13px; font-weight: 600;">Admin RH</div>
                <div style="font-size: 11px; color: var(--fg-dim);">admin@gestabs.fr</div>
            </div>
        </div>
    </div>
</aside>

<!-- Contenu principal -->
<div class="main-content">
    <!-- Header -->
    <header class="top-header">
        <div style="display: flex; align-items: center; gap: 16px;">
            <button class="mobile-toggle" onclick="toggleSidebar()"><i class="fa-solid fa-bars"></i></button>
            <div class="search-box">
                <i class="fa-solid fa-magnifying-glass"></i>
                <input type="text" placeholder="Rechercher un employe, une absence..." id="globalSearch" oninput="handleGlobalSearch(this.value)">
            </div>
        </div>
        <div class="header-actions">
            <button class="header-btn" onclick="showToast('Aucune nouvelle notification', 'info')">
                <i class="fa-solid fa-bell"></i>
                <span class="notif-dot" id="notifDot"></span>
            </button>
            <button class="header-btn" onclick="exportData()">
                <i class="fa-solid fa-download"></i>
            </button>
            <div class="user-avatar">AD</div>
        </div>
    </header>

    <!-- PAGE: Tableau de bord -->
    <section class="page active" id="page-dashboard">
        <div style="margin-bottom: 28px;">
            <h1 style="font-size: 28px; font-weight: 700; letter-spacing: -0.5px; margin-bottom: 4px;">Tableau de bord</h1>
            <p style="color: var(--fg-muted); font-size: 14px;">Vue d'ensemble des absences — <span id="currentDate"></span></p>
        </div>

        <!-- KPIs -->
        <div class="kpi-grid">
            <div class="kpi-card">
                <div class="kpi-glow" style="background: var(--info);"></div>
                <div class="kpi-icon" style="background: var(--info-soft); color: var(--info);"><i class="fa-solid fa-calendar-xmark"></i></div>
                <div class="kpi-value" id="kpiTotal">0</div>
                <div class="kpi-label">Total absences</div>
                <div class="kpi-trend" style="color: var(--info);"><i class="fa-solid fa-arrow-up" style="font-size: 10px;"></i> Ce mois</div>
            </div>
            <div class="kpi-card">
                <div class="kpi-glow" style="background: var(--warning);"></div>
                <div class="kpi-icon" style="background: var(--warning-soft); color: var(--warning);"><i class="fa-solid fa-clock"></i></div>
                <div class="kpi-value" id="kpiPending">0</div>
                <div class="kpi-label">En attente</div>
                <div class="kpi-trend" style="color: var(--warning);"><i class="fa-solid fa-exclamation" style="font-size: 10px;"></i> A traiter</div>
            </div>
            <div class="kpi-card">
                <div class="kpi-glow" style="background: var(--accent);"></div>
                <div class="kpi-icon" style="background: var(--accent-soft); color: var(--accent);"><i class="fa-solid fa-circle-check"></i></div>
                <div class="kpi-value" id="kpiApproved">0</div>
                <div class="kpi-label">Approuvees</div>
                <div class="kpi-trend" style="color: var(--accent);"><i class="fa-solid fa-arrow-up" style="font-size: 10px;"></i> +12%</div>
            </div>
            <div class="kpi-card">
                <div class="kpi-glow" style="background: var(--danger);"></div>
                <div class="kpi-icon" style="background: var(--danger-soft); color: var(--danger);"><i class="fa-solid fa-circle-xmark"></i></div>
                <div class="kpi-value" id="kpiRejected">0</div>
                <div class="kpi-label">Refusees</div>
                <div class="kpi-trend" style="color: var(--danger);"><i class="fa-solid fa-arrow-down" style="font-size: 10px;"></i> -5%</div>
            </div>
        </div>

        <!-- Graphique + Dernières absences -->
        <div style="display: grid; grid-template-columns: 1fr 1.5fr; gap: 20px;">
            <!-- Graphique barres -->
            <div class="calendar-container">
                <h3 style="font-size: 16px; font-weight: 600; margin-bottom: 20px;">Absences par mois</h3>
                <div class="chart-bar-container" id="chartBars"></div>
            </div>
            <!-- Dernières absences -->
            <div class="data-table-wrapper">
                <div class="table-header">
                    <h3>Dernieres demandes</h3>
                    <button class="btn-secondary" onclick="switchPage('absences')" style="padding: 7px 14px; font-size: 13px;">
                        Voir tout <i class="fa-solid fa-arrow-right" style="font-size: 11px;"></i>
                    </button>
                </div>
                <table>
                    <thead>
                        <tr>
                            <th>Employe</th>
                            <th>Type</th>
                            <th>Dates</th>
                            <th>Statut</th>
                        </tr>
                    </thead>
                    <tbody id="dashboardTableBody"></tbody>
                </table>
            </div>
        </div>

        <!-- Solde congés équipe -->
        <div style="margin-top: 20px;">
            <div class="data-table-wrapper">
                <div class="table-header">
                    <h3>Solde des conges</h3>
                </div>
                <div style="padding: 20px 24px; display: grid; grid-template-columns: repeat(auto-fill, minmax(220px, 1fr)); gap: 16px;" id="balanceCards"></div>
            </div>
        </div>
    </section>

    <!-- PAGE: Absences -->
    <section class="page" id="page-absences">
        <div style="display: flex; align-items: center; justify-content: space-between; margin-bottom: 28px;">
            <div>
                <h1 style="font-size: 28px; font-weight: 700; letter-spacing: -0.5px; margin-bottom: 4px;">Gestion des absences</h1>
                <p style="color: var(--fg-muted); font-size: 14px;">Consultez, approuvez ou refusez les demandes</p>
            </div>
            <button class="btn-primary" onclick="openAbsenceModal()">
                <i class="fa-solid fa-plus"></i> Nouvelle absence
            </button>
        </div>

        <div class="data-table-wrapper">
            <div class="table-header" style="flex-wrap: wrap; gap: 12px;">
                <div class="table-filters">
                    <button class="filter-btn active" data-filter="all" onclick="filterAbsences('all', this)">Toutes</button>
                    <button class="filter-btn" data-filter="pending" onclick="filterAbsences('pending', this)">En attente</button>
                    <button class="filter-btn" data-filter="approved" onclick="filterAbsences('approved', this)">Approuvees</button>
                    <button class="filter-btn" data-filter="rejected" onclick="filterAbsences('rejected', this)">Refusees</button>
                </div>
                <div class="table-filters">
                    <select class="form-select" style="width: 180px; padding: 7px 12px; font-size: 13px;" id="typeFilter" onchange="applyFilters()">
                        <option value="all">Tous les types</option>
                    </select>
                    <select class="form-select" style="width: 180px; padding: 7px 12px; font-size: 13px;" id="empFilter" onchange="applyFilters()">
                        <option value="all">Tous les employes</option>
                    </select>
                </div>
            </div>
            <div style="overflow-x: auto;">
                <table>
                    <thead>
                        <tr>
                            <th>Employe</th>
                            <th>Type</th>
                            <th>Date debut</th>
                            <th>Date fin</th>
                            <th>Duree</th>
                            <th>Motif</th>
                            <th>Statut</th>
                            <th>Actions</th>
                        </tr>
                    </thead>
                    <tbody id="absencesTableBody"></tbody>
                </table>
            </div>
            <div class="pagination" id="pagination"></div>
        </div>
    </section>

    <!-- PAGE: Calendrier -->
    <section class="page" id="page-calendar">
        <div style="margin-bottom: 28px;">
            <h1 style="font-size: 28px; font-weight: 700; letter-spacing: -0.5px; margin-bottom: 4px;">Calendrier</h1>
            <p style="color: var(--fg-muted); font-size: 14px;">Visualisez les absences sur le mois</p>
        </div>
        <div style="display: grid; grid-template-columns: 1fr 340px; gap: 20px;">
            <div class="calendar-container" id="calendarMain"></div>
            <div>
                <div class="calendar-container" style="margin-bottom: 16px;">
                    <h3 style="font-size: 15px; font-weight: 600; margin-bottom: 16px;">Legendes</h3>
                    <div style="display: flex; flex-direction: column; gap: 10px;">
                        <div style="display: flex; align-items: center; gap: 10px;">
                            <span style="width: 10px; height: 10px; border-radius: 50%; background: var(--accent);"></span>
                            <span style="font-size: 13px; color: var(--fg-muted);">Absence approuvee</span>
                        </div>
                        <div style="display: flex; align-items: center; gap: 10px;">
                            <span style="width: 10px; height: 10px; border-radius: 50%; background: var(--warning);"></span>
                            <span style="font-size: 13px; color: var(--fg-muted);">En attente</span>
                        </div>
                        <div style="display: flex; align-items: center; gap: 10px;">
                            <span style="width: 10px; height: 10px; border-radius: 50%; background: var(--danger);"></span>
                            <span style="font-size: 13px; color: var(--fg-muted);">Absence refusee</span>
                        </div>
                    </div>
                </div>
                <div class="calendar-container">
                    <h3 style="font-size: 15px; font-weight: 600; margin-bottom: 16px;">Absences du jour</h3>
                    <div id="calDayDetail" style="display: flex; flex-direction: column; gap: 10px;">
                        <p style="color: var(--fg-dim); font-size: 13px; text-align: center; padding: 20px 0;">Cliquez sur un jour pour voir les details</p>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- PAGE: Employés -->
    <section class="page" id="page-employees">
        <div style="display: flex; align-items: center; justify-content: space-between; margin-bottom: 28px;">
            <div>
                <h1 style="font-size: 28px; font-weight: 700; letter-spacing: -0.5px; margin-bottom: 4px;">Equipe</h1>
                <p style="color: var(--fg-muted); font-size: 14px;"><span id="empCount">0</span> employes enregistrés</p>
            </div>
            <button class="btn-primary" onclick="openEmployeeModal()">
                <i class="fa-solid fa-user-plus"></i> Ajouter un employe
            </button>
        </div>
        <div class="emp-grid" id="employeeGrid"></div>
    </section>

    <!-- PAGE: Types d'absence -->
    <section class="page" id="page-types">
        <div style="display: flex; align-items: center; justify-content: space-between; margin-bottom: 28px;">
            <div>
                <h1 style="font-size: 28px; font-weight: 700; letter-spacing: -0.5px; margin-bottom: 4px;">Types d'absence</h1>
                <p style="color: var(--fg-muted); font-size: 14px;">Gerez les categories d'absences</p>
            </div>
            <button class="btn-primary" onclick="openTypeModal()">
                <i class="fa-solid fa-plus"></i> Nouveau type
            </button>
        </div>
        <div class="data-table-wrapper">
            <table>
                <thead>
                    <tr>
                        <th>Type</th>
                        <th>Couleur</th>
                        <th>Deduction conges</th>
                        <th>Nombre d'absences</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody id="typesTableBody"></tbody>
            </table>
        </div>
    </section>

    <!-- PAGE: Configuration -->
    <section class="page" id="page-settings">
        <div style="margin-bottom: 28px;">
            <h1 style="font-size: 28px; font-weight: 700; letter-spacing: -0.5px; margin-bottom: 4px;">Configuration</h1>
            <p style="color: var(--fg-muted); font-size: 14px;">Parametres de la plateforme</p>
        </div>
        <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px;">
            <div class="calendar-container">
                <h3 style="font-size: 16px; font-weight: 600; margin-bottom: 20px;">Conges annuels</h3>
                <div class="form-group">
                    <label class="form-label">Nombre de jours par an</label>
                    <input type="number" class="form-input" id="settingDays" value="25" min="1" max="60">
                </div>
                <div class="form-group">
                    <label class="form-label">Jours ouvrables par semaine</label>
                    <select class="form-select" id="settingWorkDays">
                        <option value="5" selected>5 jours (Lun-Ven)</option>
                        <option value="6">6 jours (Lun-Sam)</option>
                    </select>
                </div>
                <button class="btn-primary" onclick="saveSettings()" style="margin-top: 8px;">
                    <i class="fa-solid fa-floppy-disk"></i> Enregistrer
                </button>
            </div>
            <div class="calendar-container">
                <h3 style="font-size: 16px; font-weight: 600; margin-bottom: 20px;">Notifications</h3>
                <div class="form-group">
                    <label class="form-label">Email de notification</label>
                    <input type="email" class="form-input" id="settingEmail" value="rh@entreprise.fr">
                </div>
                <div style="display: flex; flex-direction: column; gap: 14px; margin-top: 10px;">
                    <label style="display: flex; align-items: center; gap: 12px; cursor: pointer;">
                        <input type="checkbox" checked id="notifNew" style="accent-color: var(--accent); width: 16px; height: 16px;">
                        <span style="font-size: 14px;">Nouvelle demande d'absence</span>
                    </label>
                    <label style="display: flex; align-items: center; gap: 12px; cursor: pointer;">
                        <input type="checkbox" checked id="notifStatus" style="accent-color: var(--accent); width: 16px; height: 16px;">
                        <span style="font-size: 14px;">Changement de statut</span>
                    </label>
                    <label style="display: flex; align-items: center; gap: 12px; cursor: pointer;">
                        <input type="checkbox" id="notifReminder" style="accent-color: var(--accent); width: 16px; height: 16px;">
                        <span style="font-size: 14px;">Rappel solde conges faible</span>
                    </label>
                </div>
                <button class="btn-primary" onclick="saveSettings()" style="margin-top: 20px;">
                    <i class="fa-solid fa-floppy-disk"></i> Enregistrer
                </button>
            </div>
        </div>
        <div class="calendar-container" style="margin-top: 20px;">
            <h3 style="font-size: 16px; font-weight: 600; margin-bottom: 20px;">Donnees</h3>
            <div style="display: flex; gap: 12px;">
                <button class="btn-secondary" onclick="exportData()"><i class="fa-solid fa-file-csv"></i> Exporter CSV</button>
                <button class="btn-secondary" onclick="resetData()" style="border-color: var(--danger); color: var(--danger);">
                    <i class="fa-solid fa-rotate-left"></i> Reinitialiser les donnees
                </button>
            </div>
        </div>
    </section>
</div>

<!-- MODAL: Absence -->
<div class="modal-overlay" id="absenceModal">
    <div class="modal">
        <div class="modal-head">
            <h3 id="absenceModalTitle">Nouvelle absence</h3>
            <button class="modal-close" onclick="closeModal('absenceModal')"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
            <input type="hidden" id="absenceId">
            <div class="form-group">
                <label class="form-label">Employe</label>
                <select class="form-select" id="absenceEmployee"></select>
            </div>
            <div class="form-row">
                <div class="form-group">
                    <label class="form-label">Date de debut</label>
                    <input type="date" class="form-input" id="absenceStart">
                </div>
                <div class="form-group">
                    <label class="form-label">Date de fin</label>
                    <input type="date" class="form-input" id="absenceEnd">
                </div>
            </div>
            <div class="form-row">
                <div class="form-group">
                    <label class="form-label">Type d'absence</label>
                    <select class="form-select" id="absenceType"></select>
                </div>
                <div class="form-group">
                    <label class="form-label">Statut</label>
                    <select class="form-select" id="absenceStatus">
                        <option value="pending">En attente</option>
                        <option value="approved">Approuvee</option>
                        <option value="rejected">Refusee</option>
                    </select>
                </div>
            </div>
            <div class="form-group">
                <label class="form-label">Motif</label>
                <textarea class="form-input" id="absenceReason" rows="3" placeholder="Raison de l'absence..." style="resize: vertical;"></textarea>
            </div>
        </div>
        <div class="modal-footer">
            <button class="btn-secondary" onclick="closeModal('absenceModal')">Annuler</button>
            <button class="btn-primary" onclick="saveAbsence()"><i class="fa-solid fa-check"></i> Enregistrer</button>
        </div>
    </div>
</div>

<!-- MODAL: Employé -->
<div class="modal-overlay" id="employeeModal">
    <div class="modal">
        <div class="modal-head">
            <h3 id="empModalTitle">Nouvel employe</h3>
            <button class="modal-close" onclick="closeModal('employeeModal')"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
            <input type="hidden" id="empId">
            <div class="form-row">
                <div class="form-group">
                    <label class="form-label">Prenom</label>
                    <input type="text" class="form-input" id="empFirstName" placeholder="Prenom">
                </div>
                <div class="form-group">
                    <label class="form-label">Nom</label>
                    <input type="text" class="form-input" id="empLastName" placeholder="Nom">
                </div>
            </div>
            <div class="form-group">
                <label class="form-label">Departement</label>
                <select class="form-select" id="empDept">
                    <option value="Developpement">Developpement</option>
                    <option value="Marketing">Marketing</option>
                    <option value="Ressources Humaines">Ressources Humaines</option>
                    <option value="Finance">Finance</option>
                    <option value="Design">Design</option>
                    <option value="Support">Support</option>
                    <option value="Direction">Direction</option>
                </select>
            </div>
            <div class="form-row">
                <div class="form-group">
                    <label class="form-label">Email</label>
                    <input type="email" class="form-input" id="empEmail" placeholder="email@entreprise.fr">
                </div>
                <div class="form-group">
                    <label class="form-label">Solde conges (jours)</label>
                    <input type="number" class="form-input" id="empBalance" value="25" min="0" max="60">
                </div>
            </div>
        </div>
        <div class="modal-footer">
            <button class="btn-secondary" onclick="closeModal('employeeModal')">Annuler</button>
            <button class="btn-primary" onclick="saveEmployee()"><i class="fa-solid fa-check"></i> Enregistrer</button>
        </div>
    </div>
</div>

<!-- MODAL: Type -->
<div class="modal-overlay" id="typeModal">
    <div class="modal">
        <div class="modal-head">
            <h3 id="typeModalTitle">Nouveau type</h3>
            <button class="modal-close" onclick="closeModal('typeModal')"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <div class="modal-body">
            <input type="hidden" id="typeId">
            <div class="form-group">
                <label class="form-label">Nom du type</label>
                <input type="text" class="form-input" id="typeName" placeholder="Ex: Conge paternite">
            </div>
            <div class="form-group">
                <label class="form-label">Couleur</label>
                <input type="color" class="form-input" id="typeColor" value="#10b981" style="height: 44px; padding: 4px 8px; cursor: pointer;">
            </div>
            <div class="form-group">
                <label style="display: flex; align-items: center; gap: 10px; cursor: pointer;">
                    <input type="checkbox" checked id="typeDeduct" style="accent-color: var(--accent); width: 16px; height: 16px;">
                    <span style="font-size: 14px;">Déduire du solde de conges</span>
                </label>
            </div>
        </div>
        <div class="modal-footer">
            <button class="btn-secondary" onclick="closeModal('typeModal')">Annuler</button>
            <button class="btn-primary" onclick="saveType()"><i class="fa-solid fa-check"></i> Enregistrer</button>
        </div>
    </div>
</div>

<!-- MODAL: Confirmation -->
<div class="modal-overlay" id="confirmModal">
    <div class="modal" style="width: 400px;">
        <div class="modal-body">
            <div class="confirm-dialog">
                <div class="confirm-icon"><i class="fa-solid fa-trash-can"></i></div>
                <h4 id="confirmTitle">Confirmer la suppression</h4>
                <p id="confirmText">Cette action est irreversible.</p>
            </div>
        </div>
        <div class="modal-footer" style="justify-content: center;">
            <button class="btn-secondary" onclick="closeModal('confirmModal')">Annuler</button>
            <button class="btn-danger" id="confirmBtn" onclick="confirmAction()"><i class="fa-solid fa-trash-can"></i> Supprimer</button>
        </div>
    </div>
</div>

<script>
// ============================================================
// DONNÉES INITIALES
// ============================================================
const COLORS_AVATAR = [
    '#10b981', '#06b6d4', '#8b5cf6', '#f59e0b', '#ef4444',
    '#ec4899', '#14b8a6', '#f97316', '#6366f1', '#84cc16'
];

// Types d'absence
let absenceTypes = [
    { id: 1, name: 'Conge annuel', color: '#10b981', deduct: true },
    { id: 2, name: 'Maladie', color: '#ef4444', deduct: false },
    { id: 3, name: 'Conge sans solde', color: '#f59e0b', deduct: false },
    { id: 4, name: 'Teletravail', color: '#06b6d4', deduct: false },
    { id: 5, name: 'Conge materinite', color: '#ec4899', deduct: false },
    { id: 6, name: 'Formation', color: '#8b5cf6', deduct: false },
];

// Employés
let employees = [
    { id: 1, firstName: 'Sophie', lastName: 'Martin', dept: 'Developpement', email: 's.martin@entreprise.fr', balance: 22 },
    { id: 2, firstName: 'Lucas', lastName: 'Bernard', dept: 'Marketing', email: 'l.bernard@entreprise.fr', balance: 18 },
    { id: 3, firstName: 'Emma', lastName: 'Dubois', dept: 'Design', email: 'e.dubois@entreprise.fr', balance: 25 },
    { id: 4, firstName: 'Thomas', lastName: 'Robert', dept: 'Finance', email: 't.robert@entreprise.fr', balance: 20 },
    { id: 5, firstName: 'Lea', lastName: 'Richard', dept: 'Ressources Humaines', email: 'l.richard@entreprise.fr', balance: 15 },
    { id: 6, firstName: 'Hugo', lastName: 'Petit', dept: 'Support', email: 'h.petit@entreprise.fr', balance: 23 },
    { id: 7, firstName: 'Chloe', lastName: 'Durand', dept: 'Developpement', email: 'c.durand@entreprise.fr', balance: 12 },
    { id: 8, firstName: 'Nathan', lastName: 'Leroy', dept: 'Direction', email: 'n.leroy@entreprise.fr', balance: 25 },
];

// Absences
let absences = [
    { id: 1, empId: 1, type: 1, start: '2025-01-06', end: '2025-01-10', reason: 'Vacances famille', status: 'approved' },
    { id: 2, empId: 2, type: 2, start: '2025-01-15', end: '2025-01-16', reason: 'Grippe', status: 'approved' },
    { id: 3, empId: 3, type: 4, start: '2025-01-20', end: '2025-01-22', reason: 'Travaux domicile', status: 'approved' },
    { id: 4, empId: 4, type: 1, start: '2025-01-27', end: '2025-01-31', reason: 'Voyage', status: 'pending' },
    { id: 5, empId: 5, type: 6, start: '2025-02-03', end: '2025-02-07', reason: 'Formation management', status: 'approved' },
    { id: 6, empId: 1, type: 2, start: '2025-02-12', end: '2025-02-13', reason: 'Consultation medicale', status: 'rejected' },
    { id: 7, empId: 6, type: 1, start: '2025-02-17', end: '2025-02-21', reason: 'Semaine ski', status: 'pending' },
    { id: 8, empId: 7, type: 3, start: '2025-02-24', end: '2025-02-28', reason: 'Deménagement', status: 'pending' },
    { id: 9, empId: 8, type: 1, start: '2025-03-03', end: '2025-03-07', reason: 'Conges annuels', status: 'approved' },
    { id: 10, empId: 3, type: 1, start: '2025-03-17', end: '2025-03-21', reason: 'Voyage maroc', status: 'approved' },
    { id: 11, empId: 2, type: 4, start: '2025-03-24', end: '2025-03-25', reason: 'Teletravail', status: 'approved' },
    { id: 12, empId: 5, type: 2, start: '2025-03-28', end: '2025-03-28', reason: 'Mal de dos', status: 'rejected' },
];

// ============================================================
// ÉTAT
// ============================================================
let currentFilter = 'all';
let currentPage = 1;
const ITEMS_PER_PAGE = 8;
let pendingConfirmAction = null;
let calMonth = new Date().getMonth();
let calYear = new Date().getFullYear();
let nextId = 20;

// ============================================================
// UTILITAIRES
// ============================================================
function getInitials(first, last) {
    return (first[0] + last[0]).toUpperCase();
}

function getAvatarColor(id) {
    return COLORS_AVATAR[(id - 1) % COLORS_AVATAR.length];
}

function formatDate(dateStr) {
    const d = new Date(dateStr + 'T00:00:00');
    return d.toLocaleDateString('fr-FR', { day: '2-digit', month: 'short', year: 'numeric' });
}

function getDayCount(start, end) {
    const s = new Date(start + 'T00:00:00');
    const e = new Date(end + 'T00:00:00');
    const diff = Math.ceil((e - s) / (1000 * 60 * 60 * 24)) + 1;
    return Math.max(1, diff);
}

function getEmpById(id) {
    return employees.find(e => e.id === id);
}

function getTypeById(id) {
    return absenceTypes.find(t => t.id === id);
}

function getStatusLabel(status) {
    const map = { pending: 'En attente', approved: 'Approuvee', rejected: 'Refusee' };
    return map[status] || status;
}

function getStatusBadge(status) {
    const cls = { pending: 'badge-pending', approved: 'badge-approved', rejected: 'badge-rejected' };
    const icon = { pending: 'fa-clock', approved: 'fa-circle-check', rejected: 'fa-circle-xmark' };
    return `<span class="badge ${cls[status]}"><i class="fa-solid ${icon[status]}" style="font-size: 10px;"></i> ${getStatusLabel(status)}</span>`;
}

// ============================================================
// TOASTS
// ============================================================
function showToast(message, type = 'success') {
    const container = document.getElementById('toastContainer');
    const toast = document.createElement('div');
    const icon = { success: 'fa-circle-check', error: 'fa-circle-xmark', info: 'fa-circle-info' };
    toast.className = `toast toast-${type}`;
    toast.innerHTML = `<i class="fa-solid ${icon[type]}"></i> ${message}`;
    container.appendChild(toast);
    setTimeout(() => {
        toast.classList.add('toast-exit');
        setTimeout(() => toast.remove(), 300);
    }, 3000);
}

// ============================================================
// NAVIGATION
// ============================================================
function switchPage(pageName) {
    // Masquer toutes les pages
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));

    // Afficher la page sélectionnée
    const page = document.getElementById('page-' + pageName);
    if (page) page.classList.add('active');

    const nav = document.querySelector(`.nav-item[data-page="${pageName}"]`);
    if (nav) nav.classList.add('active');

    // Fermer la sidebar sur mobile
    document.getElementById('sidebar').classList.remove('open');

    // Rafraîchir le contenu de la page
    if (pageName === 'dashboard') renderDashboard();
    if (pageName === 'absences') renderAbsences();
    if (pageName === 'calendar') renderCalendar();
    if (pageName === 'employees') renderEmployees();
    if (pageName === 'types') renderTypes();
}

function toggleSidebar() {
    document.getElementById('sidebar').classList.toggle('open');
}

// ============================================================
// MODALS
// ============================================================
function openModal(id) {
    document.getElementById(id).classList.add('show');
}
function closeModal(id) {
    document.getElementById(id).classList.remove('show');
}

// Fermer les modals en cliquant à l'extérieur
document.querySelectorAll('.modal-overlay').forEach(overlay => {
    overlay.addEventListener('click', (e) => {
        if (e.target === overlay) overlay.classList.remove('show');
    });
});

// ============================================================
// TABLEAU DE BORD
// ============================================================
function renderDashboard() {
    const total = absences.length;
    const pending = absences.filter(a => a.status === 'pending').length;
    const approved = absences.filter(a => a.status === 'approved').length;
    const rejected = absences.filter(a => a.status === 'rejected').length;

    // Animer les compteurs
    animateCounter('kpiTotal', total);
    animateCounter('kpiPending', pending);
    animateCounter('kpiApproved', approved);
    animateCounter('kpiRejected', rejected);

    // Badge sidebar
    const badge = document.getElementById('pendingBadge');
    badge.textContent = pending;
    badge.style.display = pending > 0 ? 'inline' : 'none';
    const dot = document.getElementById('notifDot');
    dot.style.display = pending > 0 ? 'block' : 'none';

    // Date courante
    document.getElementById('currentDate').textContent = new Date().toLocaleDateString('fr-FR', {
        weekday: 'long', day: 'numeric', month: 'long', year: 'numeric'
    });

    // Graphique barres mensuel
    renderChartBars();

    // Dernières absences (5 dernières)
    const tbody = document.getElementById('dashboardTableBody');
    const recent = [...absences].sort((a, b) => new Date(b.start) - new Date(a.start)).slice(0, 5);
    tbody.innerHTML = recent.map(a => {
        const emp = getEmpById(a.empId);
        const type = getTypeById(a.type);
        if (!emp || !type) return '';
        return `<tr class="row-animate">
            <td>
                <div class="employee-cell">
                    <div class="emp-avatar" style="background: ${getAvatarColor(emp.id)};">${getInitials(emp.firstName, emp.lastName)}</div>
                    <div>
                        <div class="emp-name">${emp.firstName} ${emp.lastName}</div>
                        <div class="emp-dept">${emp.dept}</div>
                    </div>
                </div>
            </td>
            <td><span class="badge badge-type" style="background: ${type.color}18; color: ${type.color};">${type.name}</span></td>
            <td style="font-size: 13px; color: var(--fg-muted);">${formatDate(a.start)}</td>
            <td>${getStatusBadge(a.status)}</td>
        </tr>`;
    }).join('');

    // Solde congés
    renderBalanceCards();
}

function animateCounter(id, target) {
    const el = document.getElementById(id);
    let current = 0;
    const step = Math.max(1, Math.ceil(target / 20));
    const interval = setInterval(() => {
        current += step;
        if (current >= target) {
            current = target;
            clearInterval(interval);
        }
        el.textContent = current;
    }, 40);
}

function renderChartBars() {
    const months = ['Jan', 'Fev', 'Mar', 'Avr', 'Mai', 'Jun', 'Jul', 'Aou', 'Sep', 'Oct', 'Nov', 'Dec'];
    const counts = new Array(12).fill(0);
    absences.forEach(a => {
        const m = new Date(a.start + 'T00:00:00').getMonth();
        counts[m]++;
    });
    const max = Math.max(...counts, 1);
    const container = document.getElementById('chartBars');
    container.innerHTML = months.map((m, i) => {
        const h = (counts[i] / max) * 120;
        const color = counts[i] > 0 ? 'var(--accent)' : 'var(--border)';
        return `<div class="chart-bar-wrap">
            <div class="chart-bar" style="height: ${Math.max(4, h)}px; background: ${color};"></div>
            <span class="chart-bar-label">${m}</span>
        </div>`;
    }).join('');
}

function renderBalanceCards() {
    const container = document.getElementById('balanceCards');
    container.innerHTML = employees.map(emp => {
        const used = 25 - emp.balance;
        const pct = Math.max(0, Math.min(100, (emp.balance / 25) * 100));
        const barColor = pct > 40 ? 'var(--accent)' : pct > 15 ? 'var(--warning)' : 'var(--danger)';
        return `<div style="background: var(--bg); border-radius: 10px; padding: 16px;">
            <div style="display: flex; align-items: center; gap: 10px; margin-bottom: 10px;">
                <div class="emp-avatar" style="width: 32px; height: 32px; font-size: 11px; border-radius: 8px; background: ${getAvatarColor(emp.id)};">${getInitials(emp.firstName, emp.lastName)}</div>
                <span style="font-size: 13px; font-weight: 600;">${emp.firstName} ${emp.lastName}</span>
            </div>
            <div class="leave-balance">
                <div class="balance-bar-bg">
                    <div class="balance-bar-fill" style="width: ${pct}%; background: ${barColor};"></div>
                </div>
                <span class="balance-text">${emp.balance}j</span>
            </div>
        </div>`;
    }).join('');
}

// ============================================================
// ABSENCES — CRUD
// ============================================================
function renderAbsences() {
    populateFilters();
    applyFilters();
}

function populateFilters() {
    // Filtre type
    const typeSelect = document.getElementById('typeFilter');
    typeSelect.innerHTML = '<option value="all">Tous les types</option>' +
        absenceTypes.map(t => `<option value="${t.id}">${t.name}</option>`).join('');

    // Filtre employé
    const empSelect = document.getElementById('empFilter');
    empSelect.innerHTML = '<option value="all">Tous les employes</option>' +
        employees.map(e => `<option value="${e.id}">${e.firstName} ${e.lastName}</option>`).join('');
}

function filterAbsences(status, btn) {
    currentFilter = status;
    currentPage = 1;
    document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
    if (btn) btn.classList.add('active');
    applyFilters();
}

function applyFilters() {
    const typeVal = document.getElementById('typeFilter').value;
    const empVal = document.getElementById('empFilter').value;

    let filtered = absences.filter(a => {
        if (currentFilter !== 'all' && a.status !== currentFilter) return false;
        if (typeVal !== 'all' && a.type !== parseInt(typeVal)) return false;
        if (empVal !== 'all' && a.empId !== parseInt(empVal)) return false;
        return true;
    });

    // Tri par date décroissante
    filtered.sort((a, b) => new Date(b.start) - new Date(a.start));

    // Pagination
    const totalPages = Math.max(1, Math.ceil(filtered.length / ITEMS_PER_PAGE));
    if (currentPage > totalPages) currentPage = totalPages;
    const start = (currentPage - 1) * ITEMS_PER_PAGE;
    const pageItems = filtered.slice(start, start + ITEMS_PER_PAGE);

    // Rendu du tableau
    const tbody = document.getElementById('absencesTableBody');
    if (pageItems.length === 0) {
        tbody.innerHTML = `<tr><td colspan="8" style="text-align: center; padding: 40px; color: var(--fg-dim);">
            <i class="fa-solid fa-inbox" style="font-size: 28px; display: block; margin-bottom: 12px;"></i>
            Aucune absence trouvee
        </td></tr>`;
    } else {
        tbody.innerHTML = pageItems.map((a, idx) => {
            const emp = getEmpById(a.empId);
            const type = getTypeById(a.type);
            if (!emp || !type) return '';
            const days = getDayCount(a.start, a.end);
            return `<tr class="row-animate" style="animation-delay: ${idx * 0.05}s;">
                <td>
                    <div class="employee-cell">
                        <div class="emp-avatar" style="background: ${getAvatarColor(emp.id)};">${getInitials(emp.firstName, emp.lastName)}</div>
                        <div>
                            <div class="emp-name">${emp.firstName} ${emp.lastName}</div>
                            <div class="emp-dept">${emp.dept}</div>
                        </div>
                    </div>
                </td>
                <td><span class="badge badge-type" style="background: ${type.color}18; color: ${type.color};">${type.name}</span></td>
                <td style="font-size: 13px;">${formatDate(a.start)}</td>
                <td style="font-size: 13px;">${formatDate(a.end)}</td>
                <td style="font-size: 13px; font-weight: 600;">${days}j</td>
                <td style="font-size: 13px; color: var(--fg-muted); max-width: 160px; overflow: hidden; text-overflow: ellipsis; white-space: nowrap;">${a.reason}</td>
                <td>${getStatusBadge(a.status)}</td>
                <td>
                    <div style="display: flex; gap: 6px;">
                        ${a.status === 'pending' ? `
                            <button class="action-btn" title="Approuver" onclick="changeStatus(${a.id}, 'approved')"><i class="fa-solid fa-check"></i></button>
                            <button class="action-btn danger" title="Refuser" onclick="changeStatus(${a.id}, 'rejected')"><i class="fa-solid fa-xmark"></i></button>
                        ` : ''}
                        <button class="action-btn" title="Modifier" onclick="editAbsence(${a.id})"><i class="fa-solid fa-pen"></i></button>
                        <button class="action-btn danger" title="Supprimer" onclick="deleteAbsence(${a.id})"><i class="fa-solid fa-trash-can"></i></button>
                    </div>
                </td>
            </tr>`;
        }).join('');
    }

    // Pagination
    renderPagination(totalPages);
}

function renderPagination(totalPages) {
    const container = document.getElementById('pagination');
    if (totalPages <= 1) { container.innerHTML = ''; return; }
    let html = `<button class="page-btn" onclick="goPage(${currentPage - 1})" ${currentPage === 1 ? 'disabled style="opacity:0.3;pointer-events:none;"' : ''}><i class="fa-solid fa-chevron-left" style="font-size: 11px;"></i></button>`;
    for (let i = 1; i <= totalPages; i++) {
        html += `<button class="page-btn ${i === currentPage ? 'active' : ''}" onclick="goPage(${i})">${i}</button>`;
    }
    html += `<button class="page-btn" onclick="goPage(${currentPage + 1})" ${currentPage === totalPages ? 'disabled style="opacity:0.3;pointer-events:none;"' : ''}><i class="fa-solid fa-chevron-right" style="font-size: 11px;"></i></button>`;
    container.innerHTML = html;
}

function goPage(p) {
    currentPage = p;
    applyFilters();
}

function changeStatus(id, status) {
    const a = absences.find(x => x.id === id);
    if (!a) return;

    // Si on approuve un type qui déduit, mettre à jour le solde
    if (status === 'approved' && a.status !== 'approved') {
        const type = getTypeById(a.type);
        if (type && type.deduct) {
            const emp = getEmpById(a.empId);
            if (emp) {
                const days = getDayCount(a.start, a.end);
                emp.balance = Math.max(0, emp.balance - days);
            }
        }
    }
    // Si on retire l'approbation, remettre le solde
    if (a.status === 'approved' && status !== 'approved') {
        const type = getTypeById(a.type);
        if (type && type.deduct) {
            const emp = getEmpById(a.empId);
            if (emp) {
                const days = getDayCount(a.start, a.end);
                emp.balance = Math.min(25, emp.balance + days);
            }
        }
    }

    a.status = status;
    showToast(`Absence ${getStatusLabel(status).toLowerCase()}`, status === 'approved' ? 'success' : status === 'rejected' ? 'error' : 'info');
    applyFilters();
}

function openAbsenceModal(id = null) {
    document.getElementById('absenceModalTitle').textContent = id ? 'Modifier l\'absence' : 'Nouvelle absence';
    document.getElementById('absenceId').value = id || '';

    // Remplir les selects
    document.getElementById('absenceEmployee').innerHTML = employees.map(e =>
        `<option value="${e.id}">${e.firstName} ${e.lastName} — ${e.dept}</option>`
    ).join('');
    document.getElementById('absenceType').innerHTML = absenceTypes.map(t =>
        `<option value="${t.id}">${t.name}</option>`
    ).join('');

    if (id) {
        const a = absences.find(x => x.id === id);
        if (a) {
            document.getElementById('absenceEmployee').value = a.empId;
            document.getElementById('absenceStart').value = a.start;
            document.getElementById('absenceEnd').value = a.end;
            document.getElementById('absenceType').value = a.type;
            document.getElementById('absenceStatus').value = a.status;
            document.getElementById('absenceReason').value = a.reason;
        }
    } else {
        document.getElementById('absenceStart').value = '';
        document.getElementById('absenceEnd').value = '';
        document.getElementById('absenceStatus').value = 'pending';
        document.getElementById('absenceReason').value = '';
    }
    openModal('absenceModal');
}

function editAbsence(id) {
    openAbsenceModal(id);
}

function saveAbsence() {
    const empId = parseInt(document.getElementById('absenceEmployee').value);
    const start = document.getElementById('absenceStart').value;
    const end = document.getElementById('absenceEnd').value;
    const type = parseInt(document.getElementById('absenceType').value);
    const status = document.getElementById('absenceStatus').value;
    const reason = document.getElementById('absenceReason').value.trim();

    // Validation
    if (!empId) { showToast('Veuillez selectionner un employe', 'error'); return; }
    if (!start || !end) { showToast('Veuillez renseigner les dates', 'error'); return; }
    if (new Date(end) < new Date(start)) { showToast('La date de fin doit etre apres la date de debut', 'error'); return; }
    if (!reason) { showToast('Veuillez indiquer un motif', 'error'); return; }

    const id = document.getElementById('absenceId').value;

    if (id) {
        // Modification
        const a = absences.find(x => x.id === parseInt(id));
        if (a) {
            // Gérer le solde si le statut change
            const oldType = getTypeById(a.type);
            if (a.status === 'approved' && oldType && oldType.deduct) {
                const emp = getEmpById(a.empId);
                if (emp) emp.balance = Math.min(25, emp.balance + getDayCount(a.start, a.end));
            }

            a.empId = empId;
            a.start = start;
            a.end = end;
            a.type = type;
            a.status = status;
            a.reason = reason;

            // Appliquer le nouveau solde si approuvé
            const newType = getTypeById(type);
            if (status === 'approved' && newType && newType.deduct) {
                const emp = getEmpById(empId);
                if (emp) emp.balance = Math.max(0, emp.balance - getDayCount(start, end));
            }

            showToast('Absence modifiee avec succes', 'success');
        }
    } else {
        // Création
        absences.push({ id: ++nextId, empId, type, start, end, reason, status });

        // Déduire si approuvé directement
        const t = getTypeById(type);
        if (status === 'approved' && t && t.deduct) {
            const emp = getEmpById(empId);
            if (emp) emp.balance = Math.max(0, emp.balance - getDayCount(start, end));
        }

        showToast('Absence creee avec succes', 'success');
    }

    closeModal('absenceModal');
    applyFilters();
}

function deleteAbsence(id) {
    document.getElementById('confirmTitle').textContent = 'Supprimer cette absence';
    document.getElementById('confirmText').textContent = 'Cette action est irreversible. Voulez-vous continuer ?';
    pendingConfirmAction = () => {
        const a = absences.find(x => x.id === id);
        if (a && a.status === 'approved') {
            const type = getTypeById(a.type);
            if (type && type.deduct) {
                const emp = getEmpById(a.empId);
                if (emp) emp.balance = Math.min(25, emp.balance + getDayCount(a.start, a.end));
            }
        }
        absences = absences.filter(x => x.id !== id);
        showToast('Absence supprimee', 'error');
        applyFilters();
    };
    openModal('confirmModal');
}

// ============================================================
// CALENDRIER
// ============================================================
function renderCalendar() {
    const container = document.getElementById('calendarMain');
    const monthNames = ['Janvier', 'Fevrier', 'Mars', 'Avril', 'Mai', 'Juin', 'Juillet', 'Aout', 'Septembre', 'Octobre', 'Novembre', 'Decembre'];
    const dayLabels = ['Lun', 'Mar', 'Mer', 'Jeu', 'Ven', 'Sam', 'Dim'];

    const firstDay = new Date(calYear, calMonth, 1);
    let startDay = firstDay.getDay() - 1;
    if (startDay < 0) startDay = 6;
    const daysInMonth = new Date(calYear, calMonth + 1, 0).getDate();
    const daysInPrevMonth = new Date(calYear, calMonth, 0).getDate();

    const today = new Date();
    const todayStr = `${today.getFullYear()}-${String(today.getMonth() + 1).padStart(2, '0')}-${String(today.getDate()).padStart(2, '0')}`;

    // Construire les jours avec info d'absences
    let days = [];
    // Jours du mois précédent
    for (let i = startDay - 1; i >= 0; i--) {
        days.push({ day: daysInPrevMonth - i, other: true, dateStr: '' });
    }
    // Jours du mois
    for (let d = 1; d <= daysInMonth; d++) {
        const dateStr = `${calYear}-${String(calMonth + 1).padStart(2, '0')}-${String(d).padStart(2, '0')}`;
        const dayAbsences = absences.filter(a => a.start <= dateStr && a.end >= dateStr);
        let cls = '';
        if (dayAbsences.length > 0) {
            if (dayAbsences.some(a => a.status === 'pending')) cls = 'has-pending';
            else if (dayAbsences.some(a => a.status === 'rejected')) cls = 'has-absence';
            else cls = 'has-approved';
        }
        days.push({ day: d, other: false, dateStr, cls, today: dateStr === todayStr });
    }
    // Jours du mois suivant
    const remaining = 42 - days.length;
    for (let d = 1; d <= remaining; d++) {
        days.push({ day: d, other: true, dateStr: '' });
    }

    container.innerHTML = `
        <div class="cal-header">
            <h3>${monthNames[calMonth]} ${calYear}</h3>
            <div class="cal-nav">
                <button onclick="calNav(-1)"><i class="fa-solid fa-chevron-left"></i></button>
                <button onclick="calNav(0)" title="Aujourd'hui" style="font-size: 12px; width: auto; padding: 0 12px;">Auj.</button>
                <button onclick="calNav(1)"><i class="fa-solid fa-chevron-right"></i></button>
            </div>
        </div>
        <div class="cal-grid">
            ${dayLabels.map(l => `<div class="cal-day-label">${l}</div>`).join('')}
            ${days.map(d => `<div class="cal-day ${d.other ? 'other-month' : ''} ${d.cls || ''} ${d.today ? 'today' : ''}" ${d.dateStr ? `onclick="showDayDetail('${d.dateStr}')"` : ''}>${d.day}</div>`).join('')}
        </div>
    `;
}

function calNav(dir) {
    if (dir === 0) {
        calMonth = new Date().getMonth();
        calYear = new Date().getFullYear();
    } else {
        calMonth += dir;
        if (calMonth > 11) { calMonth = 0; calYear++; }
        if (calMonth < 0) { calMonth = 11; calYear--; }
    }
    renderCalendar();
}

function showDayDetail(dateStr) {
    const dayAbs = absences.filter(a => a.start <= dateStr && a.end >= dateStr);
    const container = document.getElementById('calDayDetail');
    const d = new Date(dateStr + 'T00:00:00');
    const label = d.toLocaleDateString('fr-FR', { weekday: 'long', day: 'numeric', month: 'long' });

    if (dayAbs.length === 0) {
        container.innerHTML = `<p style="color: var(--fg-dim); font-size: 13px; text-align: center; padding: 16px 0;">Aucune absence le ${label}</p>`;
        return;
    }

    container.innerHTML = `<p style="font-size: 12px; color: var(--fg-dim); margin-bottom: 8px; text-transform: capitalize;">${label}</p>` +
        dayAbs.map(a => {
            const emp = getEmpById(a.empId);
            const type = getTypeById(a.type);
            if (!emp || !type) return '';
            return `<div style="background: var(--bg); border-radius: 10px; padding: 12px; display: flex; align-items: center; gap: 10px;">
                <div class="emp-avatar" style="width: 30px; height: 30px; font-size: 10px; border-radius: 7px; background: ${getAvatarColor(emp.id)};">${getInitials(emp.firstName, emp.lastName)}</div>
                <div style="flex: 1; min-width: 0;">
                    <div style="font-size: 13px; font-weight: 600; white-space: nowrap; overflow: hidden; text-overflow: ellipsis;">${emp.firstName} ${emp.lastName}</div>
                    <div style="font-size: 11px; color: ${type.color};">${type.name}</div>
                </div>
                ${getStatusBadge(a.status)}
            </div>`;
        }).join('');
}

// ============================================================
// EMPLOYÉS — CRUD
// ============================================================
function renderEmployees() {
    document.getElementById('empCount').textContent = employees.length;
    const grid = document.getElementById('employeeGrid');
    grid.innerHTML = employees.map(emp => {
        const empAbsences = absences.filter(a => a.empId === emp.id);
        const approved = empAbsences.filter(a => a.status === 'approved').length;
        const pending = empAbsences.filter(a => a.status === 'pending').length;
        return `<div class="emp-card" onclick="showEmployeeAbsences(${emp.id})">
            <div class="emp-card-top">
                <div class="emp-card-avatar" style="background: ${getAvatarColor(emp.id)};">${getInitials(emp.firstName, emp.lastName)}</div>
                <div class="emp-card-info">
                    <h4>${emp.firstName} ${emp.lastName}</h4>
                    <p>${emp.dept}</p>
                </div>
                <div style="margin-left: auto; display: flex; gap: 6px;" onclick="event.stopPropagation();">
                    <button class="action-btn" title="Modifier" onclick="editEmployee(${emp.id})"><i class="fa-solid fa-pen"></i></button>
                    <button class="action-btn danger" title="Supprimer" onclick="deleteEmployee(${emp.id})"><i class="fa-solid fa-trash-can"></i></button>
                </div>
            </div>
            <div class="emp-card-stats">
                <div class="emp-stat">
                    <div class="emp-stat-val" style="color: var(--accent);">${emp.balance}</div>
                    <div class="emp-stat-label">Solde</div>
                </div>
                <div class="emp-stat">
                    <div class="emp-stat-val">${approved}</div>
                    <div class="emp-stat-label">Validees</div>
                </div>
                <div class="emp-stat">
                    <div class="emp-stat-val" style="color: ${pending > 0 ? 'var(--warning)' : 'var(--fg)'};">${pending}</div>
                    <div class="emp-stat-label">En attente</div>
                </div>
            </div>
            <div class="leave-balance" style="margin-top: 12px;">
                <div class="balance-bar-bg">
                    <div class="balance-bar-fill" style="width: ${(emp.balance / 25) * 100}%; background: ${emp.balance > 10 ? 'var(--accent)' : emp.balance > 5 ? 'var(--warning)' : 'var(--danger)'};"></div>
                </div>
                <span class="balance-text">${emp.balance}/25 jours</span>
            </div>
        </div>`;
    }).join('');
}

function showEmployeeAbsences(empId) {
    // Filtrer les absences de cet employé et aller sur la page absences
    currentFilter = 'all';
    currentPage = 1;
    document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
    document.querySelector('.filter-btn[data-filter="all"]').classList.add('active');
    switchPage('absences');
    setTimeout(() => {
        document.getElementById('empFilter').value = empId;
        applyFilters();
    }, 50);
}

function openEmployeeModal(id = null) {
    document.getElementById('empModalTitle').textContent = id ? 'Modifier l\'employe' : 'Nouvel employe';
    document.getElementById('empId').value = id || '';

    if (id) {
        const emp = employees.find(e => e.id === id);
        if (emp) {
            document.getElementById('empFirstName').value = emp.firstName;
            document.getElementById('empLastName').value = emp.lastName;
            document.getElementById('empDept').value = emp.dept;
            document.getElementById('empEmail').value = emp.email;
            document.getElementById('empBalance').value = emp.balance;
        }
    } else {
        document.getElementById('empFirstName').value = '';
        document.getElementById('empLastName').value = '';
        document.getElementById('empDept').value = 'Developpement';
        document.getElementById('empEmail').value = '';
        document.getElementById('empBalance').value = 25;
    }
    openModal('employeeModal');
}

function editEmployee(id) {
    openEmployeeModal(id);
}

function saveEmployee() {
    const firstName = document.getElementById('empFirstName').value.trim();
    const lastName = document.getElementById('empLastName').value.trim();
    const dept = document.getElementById('empDept').value;
    const email = document.getElementById('empEmail').value.trim();
    const balance = parseInt(document.getElementById('empBalance').value) || 0;

    if (!firstName || !lastName) { showToast('Le nom et le prenom sont obligatoires', 'error'); return; }
    if (!email) { showToast('L\'email est obligatoire', 'error'); return; }

    const id = document.getElementById('empId').value;

    if (id) {
        const emp = employees.find(e => e.id === parseInt(id));
        if (emp) {
            emp.firstName = firstName;
            emp.lastName = lastName;
            emp.dept = dept;
            emp.email = email;
            emp.balance = Math.max(0, Math.min(60, balance));
            showToast('Employe modifie avec succes', 'success');
        }
    } else {
        employees.push({ id: ++nextId, firstName, lastName, dept, email, balance: Math.max(0, Math.min(60, balance)) });
        showToast('Employe ajoute avec succes', 'success');
    }

    closeModal('employeeModal');
    renderEmployees();
}

function deleteEmployee(id) {
    const emp = getEmpById(id);
    const hasAbsences = absences.some(a => a.empId === id);
    document.getElementById('confirmTitle').textContent = 'Supprimer cet employe';
    document.getElementById('confirmText').textContent = hasAbsences
        ? `${emp.firstName} ${emp.lastName} a des absences enregistrees. Toutes seront supprimees.`
        : `Voulez-vous vraiment supprimer ${emp.firstName} ${emp.lastName} ?`;
    pendingConfirmAction = () => {
        employees = employees.filter(e => e.id !== id);
        absences = absences.filter(a => a.empId !== id);
        showToast('Employe supprime', 'error');
        renderEmployees();
    };
    openModal('confirmModal');
}

// ============================================================
// TYPES D'ABSENCE — CRUD
// ============================================================
function renderTypes() {
    const tbody = document.getElementById('typesTableBody');
    tbody.innerHTML = absenceTypes.map(t => {
        const count = absences.filter(a => a.type === t.id).length;
        return `<tr>
            <td style="font-weight: 600;">${t.name}</td>
            <td><span style="display: inline-block; width: 24px; height: 24px; border-radius: 6px; background: ${t.color}; vertical-align: middle;"></span></td>
            <td>${t.deduct ? '<span class="badge badge-approved"><i class="fa-solid fa-check" style="font-size:10px;"></i> Oui</span>' : '<span class="badge badge-rejected"><i class="fa-solid fa-xmark" style="font-size:10px;"></i> Non</span>'}</td>
            <td style="font-weight: 600;">${count}</td>
            <td>
                <div style="display: flex; gap: 6px;">
                    <button class="action-btn" title="Modifier" onclick="editType(${t.id})"><i class="fa-solid fa-pen"></i></button>
                    <button class="action-btn danger" title="Supprimer" onclick="deleteType(${t.id})"><i class="fa-solid fa-trash-can"></i></button>
                </div>
            </td>
        </tr>`;
    }).join('');
}

function openTypeModal(id = null) {
    document.getElementById('typeModalTitle').textContent = id ? 'Modifier le type' : 'Nouveau type';
    document.getElementById('typeId').value = id || '';

    if (id) {
        const t = absenceTypes.find(x => x.id === id);
        if (t) {
            document.getElementById('typeName').value = t.name;
            document.getElementById('typeColor').value = t.color;
            document.getElementById('typeDeduct').checked = t.deduct;
        }
    } else {
        document.getElementById('typeName').value = '';
        document.getElementById('typeColor').value = '#10b981';
        document.getElementById('typeDeduct').checked = true;
    }
    openModal('typeModal');
}

function editType(id) {
    openTypeModal(id);
}

function saveType() {
    const name = document.getElementById('typeName').value.trim();
    const color = document.getElementById('typeColor').value;
    const deduct = document.getElementById('typeDeduct').checked;

    if (!name) { showToast('Le nom du type est obligatoire', 'error'); return; }

    const id = document.getElementById('typeId').value;

    if (id) {
        const t = absenceTypes.find(x => x.id === parseInt(id));
        if (t) {
            t.name = name;
            t.color = color;
            t.deduct = deduct;
            showToast('Type modifie avec succes', 'success');
        }
    } else {
        absenceTypes.push({ id: ++nextId, name, color, deduct });
        showToast('Type ajoute avec succes', 'success');
    }

    closeModal('typeModal');
    renderTypes();
}

function deleteType(id) {
    const t = getTypeById(id);
    const used = absences.some(a => a.type === id);
    document.getElementById('confirmTitle').textContent = 'Supprimer ce type';
    document.getElementById('confirmText').textContent = used
        ? `"${t.name}" est utilise par des absences existantes.`
        : `Voulez-vous supprimer "${t.name}" ?`;
    pendingConfirmAction = () => {
        absenceTypes = absenceTypes.filter(x => x.id !== id);
        // Supprimer aussi les absences liées
        if (used) absences = absences.filter(a => a.type !== id);
        showToast('Type supprime', 'error');
        renderTypes();
    };
    openModal('confirmModal');
}

// ============================================================
// CONFIRMATION
// ============================================================
function confirmAction() {
    if (pendingConfirmAction) {
        pendingConfirmAction();
        pendingConfirmAction = null;
    }
    closeModal('confirmModal');
}

// ============================================================
// RECHERCHE GLOBALE
// ============================================================
function handleGlobalSearch(query) {
    if (!query.trim()) return;
    query = query.toLowerCase();
    // Chercher dans les employés
    const found = employees.find(e =>
        (e.firstName + ' ' + e.lastName).toLowerCase().includes(query) ||
        e.dept.toLowerCase().includes(query)
    );
    if (found) {
        showEmployeeAbsences(found.id);
        document.getElementById('globalSearch').value = '';
        return;
    }
    // Chercher dans les motifs
    const foundAbs = absences.find(a => a.reason.toLowerCase().includes(query));
    if (foundAbs) {
        switchPage('absences');
        document.getElementById('globalSearch').value = '';
    }
}

// ============================================================
// EXPORT / SETTINGS
// ============================================================
function exportData() {
    let csv = 'Employe,Type,Debut,Fin,Duree,Motif,Statut\n';
    absences.forEach(a => {
        const emp = getEmpById(a.empId);
        const type = getTypeById(a.type);
        if (!emp || !type) return;
        csv += `"${emp.firstName} ${emp.lastName}","${type.name}","${a.start}","${a.end}","${getDayCount(a.start, a.end)}","${a.reason}","${getStatusLabel(a.status)}"\n`;
    });
    const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
    const url = URL.createObjectURL(blob);
    const link = document.createElement('a');
    link.href = url;
    link.download = `absences_${new Date().toISOString().slice(0, 10)}.csv`;
    link.click();
    URL.revokeObjectURL(url);
    showToast('Fichier CSV exporte avec succes', 'success');
}

function saveSettings() {
    showToast('Parametres enregistres avec succes', 'success');
}

function resetData() {
    document.getElementById('confirmTitle').textContent = 'Reinitialiser les donnees';
    document.getElementById('confirmText').textContent = 'Toutes les donnees seront reinitialisees aux valeurs par defaut.';
    pendingConfirmAction = () => {
        location.reload();
    };
    openModal('confirmModal');
}

// ============================================================
// INITIALISATION
// ============================================================
renderDashboard();
</script>
</body>
</html>
