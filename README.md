
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Bitácora de Flota</title>
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #F5F3EE; --surface: #FFFFFF;
    --border: #E2DDD6; --border-strong: #C8C2B8;
    --text: #1C1917; --text-muted: #78716C; --text-faint: #A8A29E;
    --accent: #1D4ED8; --accent-light: #EFF3FF; --accent-border: #BFCFFF;
    --green: #166534; --green-light: #DCFCE7;
    --amber: #92400E; --amber-light: #FEF3C7;
    --red: #991B1B; --red-light: #FEE2E2;
    --radius: 10px; --radius-sm: 6px;
    --shadow: 0 1px 3px rgba(0,0,0,0.08);
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body { font-family: 'DM Sans', sans-serif; background: var(--bg); color: var(--text); min-height: 100vh; font-size: 15px; line-height: 1.5; }

  .top-bar { background: var(--surface); border-bottom: 1px solid var(--border); padding: 0 1.25rem; display: flex; align-items: center; justify-content: space-between; height: 54px; position: sticky; top: 0; z-index: 100; }
  .logo { font-size: 15px; font-weight: 600; display: flex; align-items: center; gap: 8px; }
  .logo-icon { width: 28px; height: 28px; background: var(--text); border-radius: 6px; display: flex; align-items: center; justify-content: center; }
  .logo-icon svg { width: 16px; height: 16px; fill: white; }
  .driver-pill { background: var(--accent-light); border: 1px solid var(--accent-border); color: var(--accent); border-radius: 20px; padding: 5px 14px; font-size: 13px; font-weight: 500; cursor: pointer; display: flex; align-items: center; gap: 6px; }

  .nav-bar { background: var(--surface); border-bottom: 1px solid var(--border); display: flex; padding: 0 1rem; overflow-x: auto; }
  .nav-bar::-webkit-scrollbar { display: none; }
  .nav-item { padding: 12px 14px; font-size: 13px; font-weight: 500; color: var(--text-muted); cursor: pointer; border-bottom: 2px solid transparent; transition: all 0.15s; white-space: nowrap; }
  .nav-item.active { color: var(--text); border-bottom-color: var(--text); }
  .nav-item:hover:not(.active) { color: var(--text); }

  .main { max-width: 680px; margin: 0 auto; padding: 1.25rem; }
  .section { display: none; } .section.active { display: block; }

  /* OVERLAYS */
  .overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.45); z-index: 300; display: flex; align-items: center; justify-content: center; padding: 1rem; }
  .overlay.hidden { display: none; }
  .modal { background: var(--surface); border-radius: var(--radius); padding: 1.75rem; width: 100%; max-width: 420px; box-shadow: 0 20px 60px rgba(0,0,0,0.2); max-height: 90vh; overflow-y: auto; }
  .modal-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 1rem; }
  .modal-header h2 { font-size: 17px; font-weight: 600; }
  .modal-close { background: none; border: none; font-size: 20px; cursor: pointer; color: var(--text-muted); padding: 2px 6px; line-height: 1; }
  .modal p { color: var(--text-muted); font-size: 14px; margin-bottom: 1.25rem; }
  .driver-list { display: flex; flex-direction: column; gap: 8px; }
  .driver-option { padding: 12px 14px; border: 1px solid var(--border); border-radius: var(--radius-sm); cursor: pointer; display: flex; align-items: center; gap: 12px; transition: all 0.12s; }
  .driver-option:hover { border-color: var(--accent); background: var(--accent-light); }
  .d-avatar { width: 36px; height: 36px; border-radius: 50%; background: var(--bg); border: 1px solid var(--border); display: flex; align-items: center; justify-content: center; font-weight: 600; font-size: 13px; flex-shrink: 0; }
  .d-name { font-weight: 500; font-size: 14px; }
  .d-sub { font-size: 12px; color: var(--text-muted); }

  .card { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius); padding: 1.25rem; margin-bottom: 12px; box-shadow: var(--shadow); }
  .card-title { font-size: 12px; font-weight: 600; text-transform: uppercase; letter-spacing: 0.5px; color: var(--text-muted); margin-bottom: 1rem; }

  .stats-row { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin-bottom: 1.25rem; }
  .stat-box { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius); padding: 1rem; text-align: center; box-shadow: var(--shadow); }
  .stat-num { font-size: 22px; font-weight: 600; font-family: 'DM Mono', monospace; }
  .stat-lbl { font-size: 11px; color: var(--text-muted); margin-top: 4px; text-transform: uppercase; letter-spacing: 0.4px; }

  .type-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; margin-bottom: 1rem; }
  .type-opt { border: 1.5px solid var(--border); border-radius: var(--radius-sm); padding: 10px 6px; text-align: center; cursor: pointer; transition: all 0.12s; background: var(--surface); }
  .type-opt.sel { border-color: var(--accent); background: var(--accent-light); }
  .type-opt .ico { font-size: 18px; margin-bottom: 3px; }
  .type-opt .lbl { font-size: 11px; font-weight: 500; color: var(--text-muted); }
  .type-opt.sel .lbl { color: var(--accent); }

  .fg { display: flex; flex-direction: column; gap: 5px; margin-bottom: 12px; }
  .fg label { font-size: 12px; font-weight: 500; color: var(--text-muted); }
  .fg input, .fg select, .fg textarea { font-family: 'DM Sans', sans-serif; font-size: 14px; border: 1px solid var(--border); border-radius: var(--radius-sm); padding: 9px 12px; background: var(--surface); color: var(--text); outline: none; transition: border-color 0.12s; width: 100%; }
  .fg input:focus, .fg select:focus, .fg textarea:focus { border-color: var(--accent); }
  .fg textarea { resize: vertical; min-height: 68px; }
  .grid2 { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
  .grid3 { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 12px; }

  .km-hint { background: var(--green-light); border: 1px solid #86EFAC; border-radius: var(--radius-sm); padding: 7px 12px; font-size: 13px; color: var(--green); font-family: 'DM Mono', monospace; font-weight: 500; margin-bottom: 12px; display: none; }

  .btn-primary { width: 100%; padding: 11px; background: var(--text); color: white; border: none; border-radius: var(--radius-sm); font-family: 'DM Sans', sans-serif; font-size: 14px; font-weight: 600; cursor: pointer; transition: all 0.15s; display: flex; align-items: center; justify-content: center; gap: 8px; }
  .btn-primary:hover { background: #374151; }
  .btn-primary:disabled { background: var(--text-faint); cursor: not-allowed; }
  .btn-sm { padding: 5px 12px; background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius-sm); font-family: 'DM Sans', sans-serif; font-size: 12px; font-weight: 500; cursor: pointer; color: var(--text); transition: all 0.12s; }
  .btn-sm:hover { border-color: var(--border-strong); background: var(--bg); }
  .btn-sm.danger { color: var(--red); border-color: #FECACA; }
  .btn-sm.danger:hover { background: var(--red-light); }
  .btn-sm.edit { color: var(--accent); border-color: var(--accent-border); }
  .btn-sm.edit:hover { background: var(--accent-light); }

  .entity-row { display: flex; align-items: center; gap: 12px; padding: 12px 0; border-bottom: 1px solid var(--border); }
  .entity-row:last-child { border-bottom: none; }
  .entity-avatar { width: 40px; height: 40px; border-radius: 50%; background: var(--bg); border: 1px solid var(--border); display: flex; align-items: center; justify-content: center; font-weight: 600; font-size: 13px; flex-shrink: 0; }
  .entity-avatar.vehicle { border-radius: 8px; font-size: 20px; }
  .entity-info { flex: 1; min-width: 0; }
  .entity-name { font-weight: 600; font-size: 14px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
  .entity-sub { font-size: 12px; color: var(--text-muted); font-family: 'DM Mono', monospace; margin-top: 2px; }
  .entity-actions { display: flex; gap: 6px; flex-shrink: 0; }
  .entity-stat { text-align: right; flex-shrink: 0; margin-right: 6px; }
  .entity-stat-val { font-size: 13px; font-weight: 600; font-family: 'DM Mono', monospace; }
  .entity-stat-lbl { font-size: 11px; color: var(--text-muted); }

  .rec-card { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius); padding: 1rem 1.25rem; margin-bottom: 10px; box-shadow: var(--shadow); }
  .rec-head { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 8px; }
  .rec-name { font-weight: 600; font-size: 14px; }
  .rec-plate { font-size: 12px; color: var(--text-muted); font-family: 'DM Mono', monospace; }
  .rec-date { font-size: 12px; color: var(--text-muted); font-family: 'DM Mono', monospace; white-space: nowrap; }
  .badge { display: inline-flex; align-items: center; gap: 4px; font-size: 11px; font-weight: 600; padding: 3px 10px; border-radius: 20px; margin-bottom: 6px; }
  .badge-trip { background: var(--green-light); color: var(--green); }
  .badge-fuel { background: var(--amber-light); color: var(--amber); }
  .badge-both { background: var(--accent-light); color: var(--accent); }
  .rec-route { font-size: 13px; color: var(--text-muted); margin-bottom: 4px; }
  .rec-pills { display: flex; flex-wrap: wrap; gap: 6px; margin-top: 8px; }
  .pill { background: var(--bg); border: 1px solid var(--border); border-radius: 20px; padding: 3px 10px; font-size: 12px; font-family: 'DM Mono', monospace; color: var(--text-muted); }
  .pill-rendimiento { background: var(--green-light); border: 1px solid #86EFAC; border-radius: 20px; padding: 4px 12px; font-size: 12px; font-family: 'DM Mono', monospace; color: var(--green); font-weight: 600; display: inline-flex; align-items: center; gap: 4px; }
  .rec-notes { font-size: 12px; color: var(--text-faint); margin-top: 8px; border-top: 1px solid var(--border); padding-top: 8px; font-style: italic; }

  .filter-row { display: flex; gap: 8px; margin-bottom: 1rem; flex-wrap: wrap; }
  .filter-row select { flex: 1; min-width: 130px; font-family: 'DM Sans', sans-serif; font-size: 13px; border: 1px solid var(--border); border-radius: var(--radius-sm); padding: 7px 10px; background: var(--surface); color: var(--text); outline: none; }

  .sub-tabs { display: flex; background: var(--bg); border-radius: var(--radius-sm); padding: 3px; margin-bottom: 1rem; gap: 3px; }
  .sub-tab { flex: 1; padding: 7px; text-align: center; font-size: 13px; cursor: pointer; border-radius: 5px; border: none; background: transparent; color: var(--text-muted); font-family: 'DM Sans', sans-serif; transition: all 0.12s; font-weight: 500; }
  .sub-tab.active { background: var(--surface); color: var(--text); border: 1px solid var(--border); }

  .info-box { background: var(--accent-light); border: 1px solid var(--accent-border); border-radius: var(--radius); padding: 1rem 1.25rem; margin-bottom: 1rem; display: flex; gap: 12px; }
  .info-box-icon { font-size: 22px; flex-shrink: 0; }
  .info-box h3 { font-size: 14px; font-weight: 600; color: var(--accent); margin-bottom: 3px; }
  .info-box p { font-size: 13px; color: var(--text-muted); }

  .empty { text-align: center; padding: 2rem 1rem; color: var(--text-muted); }
  .empty .eico { font-size: 30px; margin-bottom: 10px; }
  .empty p { font-size: 14px; }

  .toast { position: fixed; bottom: 1.5rem; left: 50%; transform: translateX(-50%); background: var(--text); color: white; padding: 10px 20px; border-radius: 20px; font-size: 13px; font-weight: 500; box-shadow: 0 4px 20px rgba(0,0,0,0.2); transition: opacity 0.3s; opacity: 0; pointer-events: none; white-space: nowrap; z-index: 999; }
  .toast.show { opacity: 1; }

  .spinner { width: 16px; height: 16px; border: 2px solid rgba(255,255,255,0.3); border-top-color: white; border-radius: 50%; animation: spin 0.7s linear infinite; }
  @keyframes spin { to { transform: rotate(360deg); } }

  .pin-row { display: flex; gap: 10px; justify-content: center; margin: 1.25rem 0; }
  .pin-digit { width: 52px; height: 60px; font-size: 24px; font-weight: 700; text-align: center; border: 1.5px solid var(--border); border-radius: var(--radius-sm); font-family: 'DM Mono', monospace; background: var(--bg); color: var(--text); outline: none; transition: border-color 0.12s; }
  .pin-digit:focus { border-color: var(--accent); background: var(--accent-light); }
  .pin-error { color: var(--red); font-size: 13px; text-align: center; margin-top: -8px; margin-bottom: 8px; display: none; }
  .admin-badge { background: #1C1917; color: white; border-radius: 20px; padding: 4px 12px; font-size: 12px; font-weight: 600; letter-spacing: 0.3px; display: none; align-items: center; gap: 5px; }
  .admin-badge.visible { display: flex; }
  .admin-logout { background: none; border: none; color: rgba(255,255,255,0.6); cursor: pointer; font-size: 14px; margin-left: 4px; padding: 0; line-height: 1; }
  .admin-logout:hover { color: white; }

  /* FUEL GAUGE */
  .fuel-gauge-wrap { margin-bottom: 12px; }
  .fuel-gauge-wrap label { font-size: 12px; font-weight: 500; color: var(--text-muted); display: block; margin-bottom: 8px; }
  .fuel-levels { display: grid; grid-template-columns: repeat(6, 1fr); gap: 6px; }
  .fuel-level-btn { border: 1.5px solid var(--border); border-radius: var(--radius-sm); padding: 8px 4px; text-align: center; cursor: pointer; background: var(--surface); transition: all 0.12s; }
  .fuel-level-btn:hover { border-color: var(--amber); background: var(--amber-light); }
  .fuel-level-btn.sel { border-color: var(--amber); background: var(--amber-light); }
  .fuel-level-btn .fill-icon { font-size: 18px; display: block; margin-bottom: 2px; }
  .fuel-level-btn .fill-lbl { font-size: 10px; font-weight: 600; color: var(--text-muted); }
  .fuel-level-btn.sel .fill-lbl { color: var(--amber); }

  /* PHOTO UPLOAD */
  .photo-upload-area { border: 1.5px dashed var(--border); border-radius: var(--radius-sm); padding: 1rem; text-align: center; cursor: pointer; transition: all 0.12s; background: var(--bg); margin-bottom: 12px; }
  .photo-upload-area:hover { border-color: var(--accent); background: var(--accent-light); }
  .photo-upload-area.has-photo { border-color: var(--green); border-style: solid; background: var(--green-light); }
  .photo-upload-area .upload-icon { font-size: 24px; margin-bottom: 4px; }
  .photo-upload-area p { font-size: 13px; color: var(--text-muted); margin: 0; }
  .photo-preview { width: 100%; max-height: 200px; object-fit: cover; border-radius: var(--radius-sm); margin-top: 8px; display: none; }
  .photo-uploading { font-size: 12px; color: var(--accent); margin-top: 6px; display: none; }

  @media (max-width: 480px) {
    .stats-row { grid-template-columns: 1fr 1fr; }
    .grid2 { grid-template-columns: 1fr; }
    .grid3 { grid-template-columns: 1fr 1fr; }
    .type-grid { grid-template-columns: 1fr; }
    .entity-stat { display: none; }
    .fuel-levels { grid-template-columns: repeat(3, 1fr); }
  }
</style>
</head>
<body>

<!-- SELECCIONAR CONDUCTOR -->
<div class="overlay" id="overlay-driver">
  <div class="modal">
    <div class="modal-header"><h2>👋 ¿Quién eres?</h2></div>
    <p>Selecciona tu nombre para llenar la bitácora</p>
    <div class="driver-list" id="driver-list-modal"></div>
  </div>
</div>

<!-- PIN ADMIN -->
<div class="overlay hidden" id="overlay-pin">
  <div class="modal" style="max-width:340px;text-align:center">
    <div style="font-size:32px;margin-bottom:8px">🔐</div>
    <h2 style="margin-bottom:6px">Acceso administrador</h2>
    <p>Ingresa tu PIN de 4 dígitos</p>
    <div class="pin-row">
      <input class="pin-digit" type="password" maxlength="1" id="pin1" inputmode="numeric" oninput="pinNext(this,'pin2')">
      <input class="pin-digit" type="password" maxlength="1" id="pin2" inputmode="numeric" oninput="pinNext(this,'pin3')" onkeydown="pinBack(event,this,'pin1')">
      <input class="pin-digit" type="password" maxlength="1" id="pin3" inputmode="numeric" oninput="pinNext(this,'pin4')" onkeydown="pinBack(event,this,'pin2')">
      <input class="pin-digit" type="password" maxlength="1" id="pin4" inputmode="numeric" oninput="checkPin()" onkeydown="pinBack(event,this,'pin3')">
    </div>
    <div class="pin-error" id="pin-error">PIN incorrecto. Intenta de nuevo.</div>
    <button class="btn-sm" onclick="closeModal('overlay-pin')" style="margin:0 auto">Cancelar</button>
  </div>
</div>

<!-- EDITAR CONDUCTOR -->
<div class="overlay hidden" id="overlay-edit-driver">
  <div class="modal">
    <div class="modal-header">
      <h2>✏️ Editar conductor</h2>
      <button class="modal-close" onclick="closeModal('overlay-edit-driver')">✕</button>
    </div>
    <div class="fg"><label>Nombre completo</label><input type="text" id="ed-nombre"></div>
    <div class="fg"><label>No. de licencia</label><input type="text" id="ed-licencia"></div>
    <div class="fg" style="margin-bottom:1rem"><label>Teléfono (opcional)</label><input type="text" id="ed-telefono" placeholder="Ej: 9999-9999"></div>
    <input type="hidden" id="ed-index">
    <button class="btn-primary" onclick="saveEditDriver()">Guardar cambios</button>
  </div>
</div>

<!-- EDITAR VEHÍCULO -->
<div class="overlay hidden" id="overlay-edit-vehicle">
  <div class="modal">
    <div class="modal-header">
      <h2>✏️ Editar vehículo</h2>
      <button class="modal-close" onclick="closeModal('overlay-edit-vehicle')">✕</button>
    </div>
    <div class="fg"><label>Placa</label><input type="text" id="ev-placa"></div>
    <div class="fg"><label>Marca y modelo</label><input type="text" id="ev-modelo" placeholder="Ej: Toyota Hilux"></div>
    <div class="grid2">
      <div class="fg"><label>Año</label><input type="number" id="ev-anio" placeholder="2020"></div>
      <div class="fg"><label>Color</label><input type="text" id="ev-color" placeholder="Ej: Blanco"></div>
    </div>
    <div class="fg" style="margin-bottom:1rem"><label>Tipo</label>
      <select id="ev-tipo">
        <option>Camión</option><option>Panel / Furgón</option><option>Camioneta</option>
        <option>Cisterna</option><option>Pickup</option><option>Otro</option>
      </select>
    </div>
    <input type="hidden" id="ev-index">
    <button class="btn-primary" onclick="saveEditVehicle()">Guardar cambios</button>
  </div>
</div>

<!-- TOP BAR -->
<div class="top-bar">
  <div class="logo">
    <div class="logo-icon">
      <svg viewBox="0 0 24 24"><path d="M1 3h15v13H1zM16 8h6l3 3v5h-9V8zM5 19a2 2 0 100-4 2 2 0 000 4zm14 0a2 2 0 100-4 2 2 0 000 4z"/></svg>
    </div>
    Bitácora de Flota
  </div>
  <div class="driver-pill" onclick="showDriverModal()">
    <svg width="14" height="14" viewBox="0 0 24 24" fill="currentColor"><path d="M12 12c2.7 0 4.8-2.1 4.8-4.8S14.7 2.4 12 2.4 7.2 4.5 7.2 7.2 9.3 12 12 12zm0 2.4c-3.2 0-9.6 1.6-9.6 4.8v2.4h19.2v-2.4c0-3.2-6.4-4.8-9.6-4.8z"/></svg>
    <span id="driver-pill-name">Seleccionar conductor</span>
  </div>
  <div class="admin-badge" id="admin-badge">
    ⚙️ Admin <button class="admin-logout" onclick="logoutAdmin()" title="Salir de admin">✕</button>
  </div>
</div>

<!-- NAV -->
<div class="nav-bar">
  <div class="nav-item active" onclick="goTo('nuevo')"     id="nav-nuevo">📝 Nuevo</div>
  <div class="nav-item admin-only" onclick="goTo('historial')" id="nav-historial" style="display:none">📋 Historial</div>
  <div class="nav-item admin-only" onclick="goTo('flota')"     id="nav-flota"     style="display:none">👥 Flota</div>
  <div class="nav-item admin-only" onclick="goTo('admin')"     id="nav-admin"     style="display:none">⚙️ Admin</div>
  <div class="nav-item" onclick="showPinModal()" id="nav-admin-lock" style="color:var(--text-faint)">🔐</div>
</div>

<div class="main">

  <!-- NUEVO REGISTRO -->
  <div class="section active" id="sec-nuevo">
    <div class="card">
      <div class="card-title">Tipo de registro</div>
      <div class="type-grid">
        <div class="type-opt sel" onclick="setType('viaje')"      id="t-viaje">      <div class="ico">🚚</div><div class="lbl">Viaje / Ruta</div></div>
        <div class="type-opt"     onclick="setType('combustible')" id="t-combustible"><div class="ico">⛽</div><div class="lbl">Combustible</div></div>
        <div class="type-opt"     onclick="setType('ambos')"       id="t-ambos">      <div class="ico">📋</div><div class="lbl">Viaje + Comb.</div></div>
      </div>
    </div>

    <div class="card">
      <div class="card-title">Información general</div>
      <div class="grid2">
        <div class="fg"><label>Conductor</label>
          <select id="f-conductor"><option value="">Seleccionar conductor...</option></select>
        </div>
        <div class="fg"><label>Vehículo / Placa</label>
          <select id="f-vehiculo"><option value="">Seleccionar vehículo...</option></select>
        </div>
      </div>
      <div class="grid2">
        <div class="fg"><label>Fecha</label><input type="date" id="f-fecha"></div>
        <div class="fg"><label>Hora de salida</label><input type="time" id="f-hora"></div>
      </div>
    </div>

    <div class="card" id="card-trip">
      <div class="card-title">Datos del viaje</div>
      <div class="grid2">
        <div class="fg"><label>Origen</label><input type="text" id="f-origen" placeholder="Ej: Tegucigalpa, Central"></div>
        <div class="fg"><label>Destino</label><input type="text" id="f-destino" placeholder="Ej: San Pedro Sula"></div>
      </div>
      <div class="grid2">
        <div class="fg"><label>Km inicial (odómetro)</label><input type="number" id="f-km-ini" placeholder="0" oninput="calcKm()"></div>
        <div class="fg"><label>Km final (odómetro)</label><input type="number" id="f-km-fin" placeholder="0" oninput="calcKm()"></div>
      </div>
      <div class="km-hint" id="km-hint">📏 Recorrido: <span id="km-calc">0</span> km</div>
      <div class="fg" style="margin-bottom:0"><label>Propósito / Carga transportada</label>
        <input type="text" id="f-proposito" placeholder="Ej: Entrega de mercancía, reparto zona norte...">
      </div>
    </div>

    <div class="card" id="card-fuel" style="display:none">
      <div class="card-title">Carga de combustible</div>
      <div class="grid3">
        <div class="fg"><label>Litros cargados</label><input type="number" id="f-litros" placeholder="0.0" step="0.1"></div>
        <div class="fg"><label>Costo total (L)</label><input type="number" id="f-costo" placeholder="0.00" step="0.01"></div>
        <div class="fg"><label>Tipo</label>
          <select id="f-tipo-comb"><option>Diesel</option><option>Regular</option><option>Premium</option></select>
        </div>
      </div>
      <div class="fg"><label>Gasolinera / Estación</label>
        <input type="text" id="f-estacion" placeholder="Ej: Delta, UNO, Texaco, Puma...">
      </div>

      <div class="fuel-gauge-wrap">
        <label>Nivel de combustible al cargar</label>
        <div class="fuel-levels">
          <div class="fuel-level-btn" onclick="setFuelLevel('Vacío')"      id="fl-vacio">  <span class="fill-icon">⬜</span><span class="fill-lbl">Vacío</span></div>
          <div class="fuel-level-btn" onclick="setFuelLevel('1/4')"        id="fl-1/4">    <span class="fill-icon">🟥</span><span class="fill-lbl">1/4</span></div>
          <div class="fuel-level-btn" onclick="setFuelLevel('1/2')"        id="fl-1/2">    <span class="fill-icon">🟧</span><span class="fill-lbl">1/2</span></div>
          <div class="fuel-level-btn" onclick="setFuelLevel('3/4')"        id="fl-3/4">    <span class="fill-icon">🟨</span><span class="fill-lbl">3/4</span></div>
          <div class="fuel-level-btn" onclick="setFuelLevel('Lleno')"      id="fl-lleno">  <span class="fill-icon">🟩</span><span class="fill-lbl">Lleno</span></div>
          <div class="fuel-level-btn" onclick="setFuelLevel('Reserva')"    id="fl-reserva"><span class="fill-icon">⚠️</span><span class="fill-lbl">Reserva</span></div>
        </div>
      </div>

      <div class="fuel-gauge-wrap" style="margin-bottom:0">
        <label>Foto del comprobante / ticket</label>
        <div class="photo-upload-area" id="photo-drop" onclick="document.getElementById('f-photo').click()">
          <div class="upload-icon">📷</div>
          <p id="photo-label">Toca para tomar foto o seleccionar imagen</p>
          <img id="photo-preview" class="photo-preview" alt="Vista previa">
          <div class="photo-uploading" id="photo-uploading">⏳ Subiendo a Google Drive...</div>
        </div>
        <input type="file" id="f-photo" accept="image/*" capture="environment" style="display:none" onchange="handlePhoto(this)">
      </div>
    </div>

    <div class="card">
      <div class="fg" style="margin-bottom:0"><label>Notas / Observaciones</label>
        <textarea id="f-notas" placeholder="Incidencias, estado del vehículo, entregas realizadas..."></textarea>
      </div>
    </div>

    <button class="btn-primary" onclick="saveRecord()" id="btn-save">
      <span id="btn-save-text">Guardar registro</span>
    </button>
    <p style="text-align:center;font-size:12px;color:var(--text-faint);margin-top:8px">Se sincroniza automáticamente con Google Sheets</p>
  </div>

  <!-- HISTORIAL -->
  <div class="section" id="sec-historial">
    <div class="stats-row" style="grid-template-columns:repeat(4,1fr)">
      <div class="stat-box"><div class="stat-num" id="s-km">—</div><div class="stat-lbl">Km este mes</div></div>
      <div class="stat-box"><div class="stat-num" id="s-fuel">—</div><div class="stat-lbl">Litros cargados</div></div>
      <div class="stat-box"><div class="stat-num" id="s-rendimiento" style="color:var(--green)">—</div><div class="stat-lbl">Km / Galón prom.</div></div>
      <div class="stat-box"><div class="stat-num" id="s-total">—</div><div class="stat-lbl">Registros totales</div></div>
    </div>
    <div class="filter-row">
      <select id="filt-tipo" onchange="renderHistory()">
        <option value="">Todos los tipos</option>
        <option value="Viaje">Viajes</option>
        <option value="Combustible">Combustible</option>
        <option value="Viaje + Combustible">Viaje + Combustible</option>
      </select>
      <select id="filt-conductor" onchange="renderHistory()"><option value="">Todos los conductores</option></select>
      <select id="filt-vehiculo"  onchange="renderHistory()"><option value="">Todos los vehículos</option></select>
    </div>
    <div id="history-container"></div>
  </div>

  <!-- FLOTA -->
  <div class="section" id="sec-flota">
    <div class="sub-tabs">
      <button class="sub-tab active" onclick="switchSubTab('conductores')" id="subtab-conductores">👤 Conductores</button>
      <button class="sub-tab"        onclick="switchSubTab('vehiculos')"   id="subtab-vehiculos">🚗 Vehículos</button>
    </div>

    <!-- SUB: CONDUCTORES -->
    <div id="sub-conductores">
      <div class="card">
        <div class="card-title">Agregar conductor</div>
        <div class="grid2">
          <div class="fg"><label>Nombre completo</label><input type="text" id="nc-nombre" placeholder="Nombre del conductor"></div>
          <div class="fg"><label>No. de licencia</label><input type="text" id="nc-licencia" placeholder="Ej: TN-0001-2020"></div>
        </div>
        <div class="fg"><label>Teléfono (opcional)</label><input type="text" id="nc-telefono" placeholder="Ej: 9999-9999"></div>
        <button class="btn-primary" onclick="addDriver()">Agregar conductor</button>
      </div>
      <div class="card">
        <div class="card-title">Conductores registrados</div>
        <div id="drivers-list"></div>
      </div>
    </div>

    <!-- SUB: VEHÍCULOS -->
    <div id="sub-vehiculos" style="display:none">
      <div class="card">
        <div class="card-title">Agregar vehículo</div>
        <div class="grid2">
          <div class="fg"><label>Placa</label><input type="text" id="nv-placa" placeholder="Ej: AAA-1234"></div>
          <div class="fg"><label>Marca y modelo</label><input type="text" id="nv-modelo" placeholder="Ej: Toyota Hilux"></div>
        </div>
        <div class="grid3">
          <div class="fg"><label>Año</label><input type="number" id="nv-anio" placeholder="2020"></div>
          <div class="fg"><label>Tipo</label>
            <select id="nv-tipo"><option>Camión</option><option>Panel / Furgón</option><option>Camioneta</option><option>Cisterna</option><option>Pickup</option><option>Otro</option></select>
          </div>
          <div class="fg"><label>Color</label><input type="text" id="nv-color" placeholder="Ej: Blanco"></div>
        </div>
        <button class="btn-primary" onclick="addVehicle()">Agregar vehículo</button>
      </div>
      <div class="card">
        <div class="card-title">Vehículos registrados</div>
        <div id="vehicles-list"></div>
      </div>
    </div>
  </div>

  <!-- ADMIN -->
  <div class="section" id="sec-admin">
    <div class="info-box">
      <div class="info-box-icon">📊</div>
      <div>
        <h3>Exportar datos</h3>
        <p>Descarga todos los registros como CSV compatible con Excel, o ábrelos en tu Google Sheet.</p>
      </div>
    </div>
    <div style="display:flex;gap:10px;margin-bottom:1.5rem;flex-wrap:wrap">
      <button class="btn-sm" onclick="exportCSV()" style="padding:9px 16px;font-size:13px">⬇️ Descargar CSV (Excel)</button>
      <button class="btn-sm" onclick="openSheet()" style="padding:9px 16px;font-size:13px">📄 Ver Google Sheet</button>
      <button class="btn-sm" onclick="syncAll()" style="padding:9px 16px;font-size:13px">🔄 Sincronizar ahora</button>
    </div>

    <div class="card">
      <div class="card-title">Configuración de Google Sheets</div>
      <div class="fg"><label>URL del Google Apps Script (Web App)</label>
        <input type="text" id="cfg-script-url" placeholder="https://script.google.com/macros/s/..." oninput="saveConfig()">
      </div>
      <div class="fg"><label>URL del Google Sheet</label>
        <input type="text" id="cfg-sheet-url" placeholder="https://docs.google.com/spreadsheets/d/..." oninput="saveConfig()">
      </div>
      <div class="fg" style="margin-bottom:0"><label>Correo para reportes semanales</label>
        <input type="email" id="cfg-email" placeholder="tuempresa@gmail.com" oninput="saveConfig()">
      </div>
    </div>

    <div class="card">
      <div class="card-title">Zona de peligro</div>
      <p style="font-size:13px;color:var(--text-muted);margin-bottom:12px">Borrar registros locales no afecta los datos ya en Google Sheets.</p>
      <button class="btn-sm danger" style="padding:9px 16px;font-size:13px" onclick="clearData()">🗑️ Borrar registros locales</button>
    </div>
  </div>

</div>
<div class="toast" id="toast"></div>

<script>
// ── STATE ──────────────────────────────────────────────
const S = {
  records: [], drivers: [], vehicles: [],
  isAdmin: false,
  currentDriver: null, currentType: 'viaje',
  fuelLevel: '', photoUrl: '',
  scriptUrl: '', sheetUrl: '', email: ''
};

// ── INIT ───────────────────────────────────────────────
async function init() {
  // Default config — hardcoded so all devices work automatically
  const DEFAULT_SCRIPT = 'https://script.google.com/macros/s/AKfycbzm6dP6E0azPXi84P15f8CIyW2w2ngzKDVjIwoiqPNDy6v6fGD6D0iV_LkQKrz5aFju/exec';
  const DEFAULT_SHEET  = 'https://docs.google.com/spreadsheets/d/1tZiAT8Xtfomgzu8nqAa5FK8InXxSsjvlQHZxXHIslsU/edit';
  const DEFAULT_EMAIL  = 'compras@geotecnica.hn';

  S.scriptUrl = localStorage.getItem('bfl_script') || DEFAULT_SCRIPT;
  S.sheetUrl  = localStorage.getItem('bfl_sheet')  || DEFAULT_SHEET;
  S.email     = localStorage.getItem('bfl_email')  || DEFAULT_EMAIL;

  // Save defaults to localStorage so they persist
  if (!localStorage.getItem('bfl_script')) localStorage.setItem('bfl_script', DEFAULT_SCRIPT);
  if (!localStorage.getItem('bfl_sheet'))  localStorage.setItem('bfl_sheet',  DEFAULT_SHEET);
  if (!localStorage.getItem('bfl_email'))  localStorage.setItem('bfl_email',  DEFAULT_EMAIL);

  document.getElementById('cfg-script-url').value = S.scriptUrl;
  document.getElementById('cfg-sheet-url').value  = S.sheetUrl;
  document.getElementById('cfg-email').value      = S.email;

  setDefaultDateTime();

  // Load from Sheets if configured, otherwise fallback to localStorage
  loadFromLocal(); // always load cache first for fast startup

  if (S.sheetUrl) {
    // Sync from Sheets in background after rendering
    setTimeout(() => syncAll(), 100);
  }

  // Always ensure Alejandro Fonseca exists as admin driver
  if (!S.drivers.find(d => d.name === 'Alejandro Fonseca')) {
    const adminDriver = { id: uid(), name: 'Alejandro Fonseca', license: '', phone: '' };
    S.drivers.push(adminDriver);
    persist();
    if (S.scriptUrl) apiPost({ action: 'saveDriver', ...adminDriver });
  }
  // Ensure Dennis Acosta exists if no drivers at all
  if (S.drivers.length === 1 && S.drivers[0].name === 'Alejandro Fonseca') {
    const d = { id: uid(), name: 'Dennis Acosta', license: '', phone: '' };
    S.drivers.unshift(d);
    persist();
    if (S.scriptUrl) apiPost({ action: 'saveDriver', ...d });
  }

  populateFormSelects();
  buildDriverModal();

  S.isAdmin = sessionStorage.getItem('bfl_admin') === '1';
  applyRole();

  const saved = localStorage.getItem('bfl_current_driver');
  if (saved) {
    const d = S.drivers.find(x => x.name === saved);
    if (d) selectDriver(d, false); else showDriverModal();
  } else { showDriverModal(); }
}

function loadFromLocal() {
  try { S.records  = JSON.parse(localStorage.getItem('bfl_records')  || '[]'); } catch(e) { S.records  = []; }
  try { S.drivers  = JSON.parse(localStorage.getItem('bfl_drivers')  || '[]'); } catch(e) { S.drivers  = []; }
  try { S.vehicles = JSON.parse(localStorage.getItem('bfl_vehicles') || '[]'); } catch(e) { S.vehicles = []; }
}

// Generic API POST helper
async function apiPost(data) {
  if (!S.scriptUrl) return;
  try {
    await fetch(S.scriptUrl, { method: 'POST', mode: 'no-cors', headers: { 'Content-Type': 'application/json' }, body: JSON.stringify(data) });
  } catch(e) { console.warn('API error:', e); }
}

// Sync banner
function showSyncBanner(msg) {
  let b = document.getElementById('sync-banner');
  if (!b) {
    b = document.createElement('div');
    b.id = 'sync-banner';
    b.style.cssText = 'position:fixed;top:54px;left:0;right:0;background:#1D4ED8;color:white;text-align:center;padding:8px;font-size:13px;z-index:200;';
    document.body.appendChild(b);
  }
  b.textContent = '🔄 ' + msg;
  b.style.display = 'block';
}
function hideSyncBanner() {
  const b = document.getElementById('sync-banner');
  if (b) b.style.display = 'none';
}

function persist() {
  localStorage.setItem('bfl_records',  JSON.stringify(S.records));
  localStorage.setItem('bfl_drivers',  JSON.stringify(S.drivers));
  localStorage.setItem('bfl_vehicles', JSON.stringify(S.vehicles));
}

function saveConfig() {
  S.scriptUrl = document.getElementById('cfg-script-url').value;
  S.sheetUrl  = document.getElementById('cfg-sheet-url').value;
  S.email     = document.getElementById('cfg-email').value;
  localStorage.setItem('bfl_script', S.scriptUrl);
  localStorage.setItem('bfl_sheet',  S.sheetUrl);
  localStorage.setItem('bfl_email',  S.email);
}

function setDefaultDateTime() {
  const now = new Date();
  document.getElementById('f-fecha').value = now.toISOString().slice(0,10);
  document.getElementById('f-hora').value  = `${pad(now.getHours())}:${pad(now.getMinutes())}`;
}

function pad(n)   { return String(n).padStart(2,'0'); }
function uid()    { return Date.now().toString(36) + Math.random().toString(36).slice(2,6); }
function initials(n) { return (n||'?').trim().split(' ').slice(0,2).map(w=>w[0]||'').join('').toUpperCase(); }

// ── DRIVER MODAL ───────────────────────────────────────
const ADMIN_DRIVER = 'Alejandro Fonseca';

function buildDriverModal() {
  const list = document.getElementById('driver-list-modal');
  if (S.drivers.length === 0) {
    list.innerHTML = '<div class="empty"><div class="eico">👤</div><p>No hay conductores.<br>Ve a <strong>Flota → Conductores</strong> para agregar.</p></div>';
    return;
  }
  list.innerHTML = S.drivers.map(d => {
    const safe = JSON.stringify(d).replace(/'/g, "&#39;");
    const isAdminUser = d.name === ADMIN_DRIVER;
    return `<div class="driver-option" onclick='${isAdminUser ? "pickAdminDriver(" + safe + ")" : "selectDriver(" + safe + ")"}'>
      <div class="d-avatar">${initials(d.name)}</div>
      <div style="flex:1">
        <div class="d-name">${d.name}</div>
        <div class="d-sub">${isAdminUser ? '🔐 Administrador' : (d.license || 'Conductor')}</div>
      </div>
      ${isAdminUser ? '<span style="font-size:18px">🔐</span>' : ''}
    </div>`;
  }).join('');
}

function pickAdminDriver(d) {
  // Store pending admin driver, then show PIN
  S._pendingAdminDriver = d;
  closeModal('overlay-driver');
  ['pin1','pin2','pin3','pin4'].forEach(id => document.getElementById(id).value = '');
  document.getElementById('pin-error').style.display = 'none';
  // Override checkPin to also set driver on success
  S._adminFromLogin = true;
  document.getElementById('overlay-pin').classList.remove('hidden');
  setTimeout(() => document.getElementById('pin1').focus(), 100);
}

function showDriverModal() { document.getElementById('overlay-driver').classList.remove('hidden'); }
function closeModal(id)    { document.getElementById(id).classList.add('hidden'); }

function selectDriver(d, showToast = true) {
  S.currentDriver = d;
  localStorage.setItem('bfl_current_driver', d.name);
  document.getElementById('driver-pill-name').textContent = d.name;
  document.getElementById('f-conductor').value = d.name;
  closeModal('overlay-driver');
  if (showToast) toast(`Hola, ${d.name.split(' ')[0]} 👋`);
}

// ── FORM SELECTS ───────────────────────────────────────
function populateFormSelects() {
  const cSel = document.getElementById('f-conductor');
  const vSel = document.getElementById('f-vehiculo');
  const fcSel = document.getElementById('filt-conductor');
  const fvSel = document.getElementById('filt-vehiculo');

  [cSel, fcSel].forEach(s => { while(s.options.length > 1) s.remove(1); });
  S.drivers.forEach(d => {
    cSel.add(new Option(d.name, d.name));
    fcSel.add(new Option(d.name, d.name));
  });
  if (S.currentDriver) cSel.value = S.currentDriver.name;

  [vSel, fvSel].forEach(s => { while(s.options.length > 1) s.remove(1); });
  S.vehicles.forEach(v => {
    const label = `${v.plate}${v.model ? ' — ' + v.model : ''}`;
    vSel.add(new Option(label, v.plate));
    fvSel.add(new Option(v.plate, v.plate));
  });
}

// ── TYPE ───────────────────────────────────────────────
function setType(type) {
  S.currentType = type;
  ['viaje','combustible','ambos'].forEach(t => document.getElementById('t-'+t).classList.toggle('sel', t===type));
  document.getElementById('card-trip').style.display = type === 'combustible' ? 'none' : 'block';
  document.getElementById('card-fuel').style.display = type === 'viaje'       ? 'none' : 'block';
}

function calcKm() {
  const ini = parseInt(document.getElementById('f-km-ini').value) || 0;
  const fin = parseInt(document.getElementById('f-km-fin').value) || 0;
  const hint = document.getElementById('km-hint');
  if (ini > 0 && fin > ini) {
    document.getElementById('km-calc').textContent = (fin - ini).toLocaleString();
    hint.style.display = 'block';
  } else { hint.style.display = 'none'; }
}

// ── FUEL LEVEL ─────────────────────────────────────────
function setFuelLevel(level) {
  S.fuelLevel = level;
  const ids = { 'Vacío':'fl-vacio', '1/4':'fl-1/4', '1/2':'fl-1/2', '3/4':'fl-3/4', 'Lleno':'fl-lleno', 'Reserva':'fl-reserva' };
  Object.values(ids).forEach(id => { const el = document.getElementById(id); if(el) el.classList.remove('sel'); });
  const selId = ids[level];
  if (selId) { const el = document.getElementById(selId); if(el) el.classList.add('sel'); }
}

// ── PHOTO UPLOAD ────────────────────────────────────────
async function handlePhoto(input) {
  if (!input.files || !input.files[0]) return;
  const file    = input.files[0];
  const preview = document.getElementById('photo-preview');
  const label   = document.getElementById('photo-label');
  const uploading = document.getElementById('photo-uploading');
  const dropArea  = document.getElementById('photo-drop');

  // Show local preview immediately
  const reader = new FileReader();
  reader.onload = e => { preview.src = e.target.result; preview.style.display = 'block'; };
  reader.readAsDataURL(file);

  if (!S.scriptUrl) {
    S.photoUrl = '';
    dropArea.classList.add('has-photo');
    label.textContent = '✅ Foto seleccionada';
    return;
  }

  uploading.style.display = 'block';
  label.textContent = 'Subiendo a Google Drive...';

  try {
    const base64 = await fileToBase64(file);
    // Use fetch with cors mode — Apps Script with doPost supports it when deployed as "anyone"
    const res = await fetch(S.scriptUrl, {
      method: 'POST',
      headers: { 'Content-Type': 'text/plain' }, // text/plain avoids preflight
      body: JSON.stringify({
        action: 'uploadPhoto',
        filename: `foto_combustible_${Date.now()}.jpg`,
        mimeType: file.type,
        base64Data: base64,
        conductor: S.currentDriver ? S.currentDriver.name : 'conductor'
      })
    });
    const json = await res.json();
    if (json.success && json.url) {
      S.photoUrl = json.url;
      dropArea.classList.add('has-photo');
      label.textContent = '✅ Foto subida a Google Drive';
    } else {
      S.photoUrl = '';
      label.textContent = '⚠️ No se pudo subir: ' + (json.error || 'error');
    }
  } catch(e) {
    // Fallback: save indicator only (no-cors path)
    S.photoUrl = '';
    dropArea.classList.add('has-photo');
    label.textContent = '✅ Foto lista (se subirá al guardar)';
  }
  uploading.style.display = 'none';
}

function fileToBase64(file) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload  = () => resolve(reader.result.split(',')[1]);
    reader.onerror = reject;
    reader.readAsDataURL(file);
  });
}

// ── SAVE RECORD ────────────────────────────────────────
async function saveRecord() {
  const conductor = document.getElementById('f-conductor').value;
  const vehiculo  = document.getElementById('f-vehiculo').value;
  const fecha     = document.getElementById('f-fecha').value;
  if (!conductor) { toast('⚠️ Selecciona un conductor'); return; }
  if (!fecha)     { toast('⚠️ Ingresa la fecha'); return; }

  const ini = parseInt(document.getElementById('f-km-ini').value) || 0;
  const fin = parseInt(document.getElementById('f-km-fin').value) || 0;
  const veh = S.vehicles.find(v => v.plate === vehiculo);
  const typeLabel = { viaje:'Viaje', combustible:'Combustible', ambos:'Viaje + Combustible' }[S.currentType];

  const rec = {
    id: uid(), fecha, hora: document.getElementById('f-hora').value,
    tipo: typeLabel, conductor, vehiculo,
    modeloVehiculo: veh ? (veh.model || veh.type) : '',
    origen:    document.getElementById('f-origen').value,
    destino:   document.getElementById('f-destino').value,
    kmInicial: ini || '', kmFinal: fin || '',
    kmRecorridos: (fin > ini) ? fin - ini : '',
    proposito: document.getElementById('f-proposito').value,
    litros:       parseFloat(document.getElementById('f-litros').value) || '',
    costoLempiras:parseFloat(document.getElementById('f-costo').value)  || '',
    estacion:     document.getElementById('f-estacion').value,
    tipoCombustible: document.getElementById('f-tipo-comb').value,
    notas: document.getElementById('f-notas').value,
    nivelCombustible: S.fuelLevel || '',
    fotoUrl: S.photoUrl || '',
    timestamp: new Date().toISOString()
  };

  S.records.unshift(rec);
  persist();

  const btn = document.getElementById('btn-save');
  const btnText = document.getElementById('btn-save-text');
  btn.disabled = true;
  btnText.innerHTML = '<div class="spinner"></div>';

  if (S.scriptUrl) {
    try {
      await fetch(S.scriptUrl, { method:'POST', mode:'no-cors', headers:{'Content-Type':'application/json'}, body: JSON.stringify({ action: 'saveRecord', ...rec }) });
      toast('✅ Guardado en Google Sheets');
    } catch(e) { toast('⚠️ Guardado localmente (sin conexión)'); }
  } else {
    toast('✅ Registro guardado');
  }

  btn.disabled = false;
  btnText.textContent = 'Guardar registro';
  ['f-origen','f-destino','f-km-ini','f-km-fin','f-proposito','f-litros','f-costo','f-estacion','f-notas'].forEach(id => {
    const el = document.getElementById(id); if (el) el.value = '';
  });
  // Reset fuel level
  S.fuelLevel = '';
  Object.values({v:'fl-vacio',a:'fl-1/4',b:'fl-1/2',c:'fl-3/4',d:'fl-lleno',e:'fl-reserva'}).forEach(id => {
    const el = document.getElementById(id); if(el) el.classList.remove('sel');
  });
  // Reset photo
  S.photoUrl = '';
  document.getElementById('f-photo').value = '';
  document.getElementById('photo-preview').style.display = 'none';
  document.getElementById('photo-preview').src = '';
  document.getElementById('photo-label').textContent = 'Toca para tomar foto o seleccionar imagen';
  document.getElementById('photo-drop').classList.remove('has-photo');
  document.getElementById('km-hint').style.display = 'none';
  setDefaultDateTime();
}

// ── HISTORY ────────────────────────────────────────────
// ── RENDIMIENTO (km/galón) ──────────────────────────────
// 1 galón = 3.785 litros
const LITROS_POR_GALON = 3.785;

function calcRendimiento(kmRecorridos, litros) {
  const km  = Number(kmRecorridos) || 0;
  const lit = Number(litros)       || 0;
  if (km <= 0 || lit <= 0) return null;
  const kmPorLitro = km / lit;
  const kmPorGalon = kmPorLitro * LITROS_POR_GALON;
  return { kmPorLitro, kmPorGalon };
}

// For a given vehicle, find km driven since last fuel-up before this record
function calcRendimientoContextual(record, allRecords) {
  const lit = Number(record.litros) || 0;
  if (lit <= 0) return null;

  // Find all records for same vehicle, sorted oldest→newest
  const vehRecs = allRecords
    .filter(r => r.vehiculo === record.vehiculo && r.vehiculo)
    .sort((a,b) => new Date(a.fecha+' '+(a.hora||'00:00')) - new Date(b.fecha+' '+(b.hora||'00:00')));

  const idx = vehRecs.findIndex(r => r.id === record.id);
  if (idx < 0) return null;

  // Sum km from last fuel record (or beginning) up to this one
  let kmAcum = 0;
  for (let i = idx - 1; i >= 0; i--) {
    const r = vehRecs[i];
    kmAcum += Number(r.kmRecorridos) || 0;
    // If this previous record also had fuel, stop here (that was the last fill-up)
    if (Number(r.litros) > 0) break;
  }
  // Also include km in this same record if it's a viaje+combustible
  kmAcum += Number(record.kmRecorridos) || 0;

  return calcRendimiento(kmAcum, lit);
}

function rendimientoLabel(rend) {
  if (!rend) return '';
  const rating = rend.kmPorGalon >= 12 ? '🟢' : rend.kmPorGalon >= 8 ? '🟡' : '🔴';
  return `${rating} ${rend.kmPorGalon.toFixed(1)} km/gal · ${rend.kmPorLitro.toFixed(2)} km/L`;
}

function renderHistory() {
  const ft = document.getElementById('filt-tipo').value;
  const fc = document.getElementById('filt-conductor').value;
  const fv = document.getElementById('filt-vehiculo').value;
  const filtered = S.records.filter(r => (!ft||r.tipo===ft) && (!fc||r.conductor===fc) && (!fv||r.vehiculo===fv));

  const now = new Date();
  const thisMonth = S.records.filter(r => { const d=new Date(r.fecha); return d.getMonth()===now.getMonth()&&d.getFullYear()===now.getFullYear(); });
  const totalKm   = thisMonth.reduce((a,r)=>a+(Number(r.kmRecorridos)||0),0);
  const totalLit  = thisMonth.reduce((a,r)=>a+(Number(r.litros)||0),0);

  // Overall km/galón this month
  const rendMes = calcRendimiento(totalKm, totalLit);
  document.getElementById('s-km').textContent          = totalKm.toLocaleString();
  document.getElementById('s-fuel').textContent        = totalLit.toFixed(0)+' L';
  document.getElementById('s-rendimiento').textContent = rendMes ? rendMes.kmPorGalon.toFixed(1) : '—';
  document.getElementById('s-total').textContent       = S.records.length;

  const cont = document.getElementById('history-container');
  if (!filtered.length) {
    cont.innerHTML = '<div class="empty"><div class="eico">📋</div><p>No hay registros que mostrar</p></div>';
    return;
  }
  cont.innerHTML = filtered.map(r => {
    const bc = r.tipo==='Viaje'?'badge-trip':r.tipo==='Combustible'?'badge-fuel':'badge-both';
    const pills = [];
    if (r.kmRecorridos)      pills.push(`📏 ${Number(r.kmRecorridos).toLocaleString()} km`);
    if (r.litros)            pills.push(`⛽ ${Number(r.litros).toFixed(1)} L${r.tipoCombustible?' ('+r.tipoCombustible+')':''}`);
    if (r.costoLempiras)     pills.push(`💰 L ${Number(r.costoLempiras).toFixed(2)}`);
    if (r.estacion)          pills.push(`🏪 ${r.estacion}`);
    if (r.nivelCombustible)  pills.push(`🔋 Nivel: ${r.nivelCombustible}`);
    if (r.fotoUrl && r.fotoUrl.startsWith('http')) pills.push(`📷 <a href="${r.fotoUrl}" target="_blank" style="color:var(--accent)">Ver foto</a>`);

    // Rendimiento: only show on records that have fuel
    const rend = (Number(r.litros) > 0) ? calcRendimientoContextual(r, S.records) : null;
    const rendHTML = rend
      ? `<div style="margin-top:8px"><span class="pill-rendimiento">⚡ ${rendimientoLabel(rend)}</span></div>`
      : '';

    return `<div class="rec-card">
      <div class="rec-head">
        <div>
          <div class="rec-name">${r.conductor}</div>
          <div class="rec-plate">${r.vehiculo||'Sin vehículo'}${r.modeloVehiculo?' · '+r.modeloVehiculo:''}</div>
        </div>
        <div class="rec-date">${r.fecha}${r.hora?' '+r.hora:''}</div>
      </div>
      <span class="badge ${bc}">${r.tipo}</span>
      ${r.origen?`<div class="rec-route">📍 ${r.origen} → ${r.destino}</div>`:''}
      ${r.proposito?`<div style="font-size:12px;color:var(--text-muted)">${r.proposito}</div>`:''}
      ${pills.length?`<div class="rec-pills">${pills.map(p=>`<span class="pill">${p}</span>`).join('')}</div>`:''}
      ${rendHTML}
      ${r.notas?`<div class="rec-notes">${r.notas}</div>`:''}
    </div>`;
  }).join('');
}

// ── DRIVERS ────────────────────────────────────────────
function addDriver() {
  const name    = document.getElementById('nc-nombre').value.trim();
  const license = document.getElementById('nc-licencia').value.trim();
  const phone   = document.getElementById('nc-telefono').value.trim();
  if (!name) { toast('⚠️ El nombre es requerido'); return; }
  if (S.drivers.find(d => d.name.toLowerCase() === name.toLowerCase())) { toast('⚠️ Ya existe ese conductor'); return; }
  const driver = { id: uid(), name, license, phone };
  S.drivers.push(driver);
  persist();
  apiPost({ action: 'saveDriver', ...driver });
  document.getElementById('nc-nombre').value    = '';
  document.getElementById('nc-licencia').value  = '';
  document.getElementById('nc-telefono').value  = '';
  populateFormSelects();
  buildDriverModal();
  renderDriversList();
  toast(`✅ ${name} agregado`);
}

function renderDriversList() {
  const cont = document.getElementById('drivers-list');
  if (!S.drivers.length) {
    cont.innerHTML = '<div class="empty" style="padding:1.5rem 0"><div class="eico">👤</div><p>Aún no hay conductores</p></div>';
    return;
  }
  cont.innerHTML = S.drivers.map((d, i) => {
    const recs = S.records.filter(r => r.conductor === d.name);
    const km   = recs.reduce((a,r) => a+(Number(r.kmRecorridos)||0), 0);
    return `<div class="entity-row">
      <div class="entity-avatar">${initials(d.name)}</div>
      <div class="entity-info">
        <div class="entity-name">${d.name}</div>
        <div class="entity-sub">${d.license||'Sin licencia'}${d.phone?' · '+d.phone:''}</div>
      </div>
      <div class="entity-stat">
        <div class="entity-stat-val">${km.toLocaleString()} km</div>
        <div class="entity-stat-lbl">${recs.length} viaje${recs.length!==1?'s':''}</div>
      </div>
      <div class="entity-actions">
        <button class="btn-sm edit"   onclick="openEditDriver(${i})">Editar</button>
        <button class="btn-sm danger" onclick="removeDriver(${i})">Eliminar</button>
      </div>
    </div>`;
  }).join('');
}

function openEditDriver(i) {
  const d = S.drivers[i];
  document.getElementById('ed-nombre').value   = d.name;
  document.getElementById('ed-licencia').value = d.license || '';
  document.getElementById('ed-telefono').value = d.phone   || '';
  document.getElementById('ed-index').value    = i;
  document.getElementById('overlay-edit-driver').classList.remove('hidden');
}

function saveEditDriver() {
  const i    = parseInt(document.getElementById('ed-index').value);
  const name = document.getElementById('ed-nombre').value.trim();
  if (!name) { toast('⚠️ El nombre no puede estar vacío'); return; }
  const oldName = S.drivers[i].name;
  S.drivers[i] = { ...S.drivers[i], name, license: document.getElementById('ed-licencia').value.trim(), phone: document.getElementById('ed-telefono').value.trim() };
  if (oldName !== name) {
    S.records.forEach(r => { if (r.conductor === oldName) r.conductor = name; });
    if (S.currentDriver?.name === oldName) {
      S.currentDriver.name = name;
      localStorage.setItem('bfl_current_driver', name);
      document.getElementById('driver-pill-name').textContent = name;
    }
  }
  persist();
  apiPost({ action: 'saveDriver', ...S.drivers[i] });
  populateFormSelects(); buildDriverModal(); renderDriversList();
  closeModal('overlay-edit-driver');
  toast('✅ Conductor actualizado');
}

function removeDriver(i) {
  const d = S.drivers[i];
  if (!confirm(`¿Eliminar a "${d.name}"?`)) return;
  S.drivers.splice(i, 1);
  persist();
  apiPost({ action: 'deleteDriver', id: d.id });
  populateFormSelects(); buildDriverModal(); renderDriversList();
  if (S.currentDriver?.name === d.name) {
    S.currentDriver = null;
    localStorage.removeItem('bfl_current_driver');
    document.getElementById('driver-pill-name').textContent = 'Seleccionar conductor';
    document.getElementById('f-conductor').value = '';
    showDriverModal();
  }
  toast(`${d.name} eliminado`);
}

// ── VEHICLES ───────────────────────────────────────────
const V_ICONS = { 'Camión':'🚛','Panel / Furgón':'🚐','Camioneta':'🚗','Cisterna':'🚒','Pickup':'🛻','Otro':'🚙' };

function addVehicle() {
  const plate = document.getElementById('nv-placa').value.trim().toUpperCase();
  const model = document.getElementById('nv-modelo').value.trim();
  const year  = document.getElementById('nv-anio').value.trim();
  const type  = document.getElementById('nv-tipo').value;
  const color = document.getElementById('nv-color').value.trim();
  if (!plate) { toast('⚠️ La placa es requerida'); return; }
  if (S.vehicles.find(v => v.plate === plate)) { toast('⚠️ Ya existe un vehículo con esa placa'); return; }
  const vehicle = { id: uid(), plate, model, year, type, color };
  S.vehicles.push(vehicle);
  persist();
  apiPost({ action: 'saveVehicle', ...vehicle });
  document.getElementById('nv-placa').value  = '';
  document.getElementById('nv-modelo').value = '';
  document.getElementById('nv-anio').value   = '';
  document.getElementById('nv-color').value  = '';
  populateFormSelects(); renderVehiclesList();
  toast(`✅ ${plate} agregado`);
}

function renderVehiclesList() {
  const cont = document.getElementById('vehicles-list');
  if (!S.vehicles.length) {
    cont.innerHTML = '<div class="empty" style="padding:1.5rem 0"><div class="eico">🚗</div><p>Aún no hay vehículos registrados</p></div>';
    return;
  }
  cont.innerHTML = S.vehicles.map((v, i) => {
    const recs = S.records.filter(r => r.vehiculo === v.plate);
    const km   = recs.reduce((a,r) => a+(Number(r.kmRecorridos)||0), 0);
    return `<div class="entity-row">
      <div class="entity-avatar vehicle">${V_ICONS[v.type]||'🚙'}</div>
      <div class="entity-info">
        <div class="entity-name">${v.plate}${v.model?' — '+v.model:''}</div>
        <div class="entity-sub">${v.type}${v.year?' · '+v.year:''}${v.color?' · '+v.color:''}</div>
      </div>
      <div class="entity-stat">
        <div class="entity-stat-val">${km.toLocaleString()} km</div>
        <div class="entity-stat-lbl">${recs.length} viaje${recs.length!==1?'s':''}</div>
      </div>
      <div class="entity-actions">
        <button class="btn-sm edit"   onclick="openEditVehicle(${i})">Editar</button>
        <button class="btn-sm danger" onclick="removeVehicle(${i})">Eliminar</button>
      </div>
    </div>`;
  }).join('');
}

function openEditVehicle(i) {
  const v = S.vehicles[i];
  document.getElementById('ev-placa').value  = v.plate;
  document.getElementById('ev-modelo').value = v.model || '';
  document.getElementById('ev-anio').value   = v.year  || '';
  document.getElementById('ev-tipo').value   = v.type;
  document.getElementById('ev-color').value  = v.color || '';
  document.getElementById('ev-index').value  = i;
  document.getElementById('overlay-edit-vehicle').classList.remove('hidden');
}

function saveEditVehicle() {
  const i     = parseInt(document.getElementById('ev-index').value);
  const plate = document.getElementById('ev-placa').value.trim().toUpperCase();
  if (!plate) { toast('⚠️ La placa no puede estar vacía'); return; }
  const oldPlate = S.vehicles[i].plate;
  S.vehicles[i] = { ...S.vehicles[i], plate, model: document.getElementById('ev-modelo').value.trim(), year: document.getElementById('ev-anio').value.trim(), type: document.getElementById('ev-tipo').value, color: document.getElementById('ev-color').value.trim() };
  if (oldPlate !== plate) S.records.forEach(r => { if (r.vehiculo === oldPlate) r.vehiculo = plate; });
  persist();
  apiPost({ action: 'saveVehicle', ...S.vehicles[i] });
  populateFormSelects(); renderVehiclesList();
  closeModal('overlay-edit-vehicle');
  toast('✅ Vehículo actualizado');
}

function removeVehicle(i) {
  const v = S.vehicles[i];
  if (!confirm(`¿Eliminar el vehículo "${v.plate}"?`)) return;
  S.vehicles.splice(i, 1);
  persist();
  apiPost({ action: 'deleteVehicle', id: v.id });
  populateFormSelects(); renderVehiclesList();
  toast(`${v.plate} eliminado`);
}

// ── NAV ────────────────────────────────────────────────
function goTo(sec) {
  document.querySelectorAll('.section').forEach(s => s.classList.remove('active'));
  document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
  document.getElementById('sec-'+sec).classList.add('active');
  document.getElementById('nav-'+sec).classList.add('active');
  if (sec === 'historial') renderHistory();
  if (sec === 'flota')     { renderDriversList(); renderVehiclesList(); }
}

function switchSubTab(tab) {
  document.getElementById('sub-conductores').style.display = tab==='conductores'?'block':'none';
  document.getElementById('sub-vehiculos').style.display   = tab==='vehiculos'?'block':'none';
  document.getElementById('subtab-conductores').classList.toggle('active', tab==='conductores');
  document.getElementById('subtab-vehiculos').classList.toggle('active',   tab==='vehiculos');
  if (tab==='conductores') renderDriversList();
  if (tab==='vehiculos')   renderVehiclesList();
}

// ── EXPORT ─────────────────────────────────────────────
function exportCSV() {
  if (!S.records.length) { toast('No hay registros para exportar'); return; }
  const headers = ['Fecha','Hora','Tipo','Conductor','Placa','Vehículo','Origen','Destino','Km Inicial','Km Final','Km Recorridos','Propósito','Litros','Costo (L)','Gasolinera','Tipo Combustible','Notas'];
  const rows = S.records.map(r => [r.fecha,r.hora,r.tipo,r.conductor,r.vehiculo,r.modeloVehiculo,r.origen,r.destino,r.kmInicial,r.kmFinal,r.kmRecorridos,r.proposito,r.litros,r.costoLempiras,r.estacion,r.tipoCombustible,r.notas].map(v=>`"${(v||'').toString().replace(/"/g,'""')}"`).join(','));
  const csv = '\uFEFF' + [headers.join(','), ...rows].join('\n');
  const a = Object.assign(document.createElement('a'), { href: URL.createObjectURL(new Blob([csv],{type:'text/csv;charset=utf-8;'})), download:`bitacora_${new Date().toISOString().slice(0,10)}.csv` });
  a.click();
  toast('✅ CSV descargado');
}

function openSheet() {
  if (!S.sheetUrl) { toast('Configura la URL del Google Sheet en Admin'); return; }
  window.open(S.sheetUrl, '_blank');
}

function clearData() {
  if (!confirm('¿Borrar todos los registros locales?')) return;
  S.records = []; persist(); renderHistory();
  toast('Registros borrados');
}

// ── ADMIN / PIN ────────────────────────────────────────
const ADMIN_PIN = '8901';

function showPinModal() {
  ['pin1','pin2','pin3','pin4'].forEach(id => document.getElementById(id).value = '');
  document.getElementById('pin-error').style.display = 'none';
  document.getElementById('overlay-pin').classList.remove('hidden');
  setTimeout(() => document.getElementById('pin1').focus(), 100);
}

function pinNext(el, nextId) {
  if (el.value.length === 1) document.getElementById(nextId).focus();
}

function pinBack(e, el, prevId) {
  if (e.key === 'Backspace' && el.value === '') document.getElementById(prevId).focus();
}

function checkPin() {
  const pin = ['pin1','pin2','pin3','pin4'].map(id => document.getElementById(id).value).join('');
  if (pin.length < 4) return;
  if (pin === ADMIN_PIN) {
    S.isAdmin = true;
    sessionStorage.setItem('bfl_admin', '1');
    closeModal('overlay-pin');
    applyRole();
    if (S._adminFromLogin && S._pendingAdminDriver) {
      selectDriver(S._pendingAdminDriver, false);
      S._adminFromLogin = false;
      S._pendingAdminDriver = null;
    }
    goTo('historial');
    toast('✅ Bienvenido, Alejandro 👋');
  } else {
    document.getElementById('pin-error').style.display = 'block';
    ['pin1','pin2','pin3','pin4'].forEach(id => document.getElementById(id).value = '');
    document.getElementById('pin1').focus();
  }
}

function logoutAdmin() {
  if (!confirm('¿Salir del modo administrador?')) return;
  S.isAdmin = false;
  sessionStorage.removeItem('bfl_admin');
  applyRole();
  goTo('nuevo');
  toast('Sesión de admin cerrada');
}

function applyRole() {
  const isAdmin = S.isAdmin;
  // Show/hide admin nav items
  document.querySelectorAll('.admin-only').forEach(el => el.style.display = isAdmin ? '' : 'none');
  // Show/hide lock icon
  document.getElementById('nav-admin-lock').style.display = isAdmin ? 'none' : '';
  // Show/hide admin badge
  document.getElementById('admin-badge').classList.toggle('visible', isAdmin);
}

// ── SYNC ALL ───────────────────────────────────────────
// Reads data from published Google Sheet CSV tabs
async function syncAll() {
  if (!S.sheetUrl) { toast('Configura la URL del Google Sheet en Admin primero'); return; }
  
  const sheetId = extractSheetId(S.sheetUrl);
  if (!sheetId) { toast('⚠️ URL del Google Sheet inválida'); return; }

  showSyncBanner('Sincronizando datos...');
  try {
    const [drivers, vehicles, records] = await Promise.all([
      fetchSheetCSV(sheetId, 'Conductores'),
      fetchSheetCSV(sheetId, 'Vehículos'),
      fetchSheetCSV(sheetId, 'Bitácora')
    ]);

    if (drivers  !== null) S.drivers  = drivers;
    if (vehicles !== null) S.vehicles = vehicles;
    if (records  !== null) S.records  = records;

    persist();
    populateFormSelects();
    buildDriverModal();
    renderDriversList();
    renderVehiclesList();
    renderHistory();
    toast('✅ Datos sincronizados');
  } catch(e) {
    console.error(e);
    toast('⚠️ Error al sincronizar. Verifica que el Sheet sea público.');
  }
  hideSyncBanner();
}

function extractSheetId(url) {
  const m = url.match(/\/spreadsheets\/d\/([a-zA-Z0-9-_]+)/);
  return m ? m[1] : null;
}

async function fetchSheetCSV(sheetId, tabName) {
  const url = `https://docs.google.com/spreadsheets/d/${sheetId}/gviz/tq?tqx=out:csv&sheet=${encodeURIComponent(tabName)}`;
  const res = await fetch(url);
  if (!res.ok) return null;
  const text = await res.text();
  return parseCSV(text, tabName);
}

function parseCSV(text, tabName) {
  const lines = text.trim().split('\n');
  if (lines.length <= 1) return [];
  // Skip header row (index 0)
  const rows = lines.slice(1).map(line => {
    // Parse CSV properly handling quoted fields
    const fields = [];
    let cur = '', inQ = false;
    for (let i = 0; i < line.length; i++) {
      const c = line[i];
      if (c === '"') { inQ = !inQ; }
      else if (c === ',' && !inQ) { fields.push(cur.trim()); cur = ''; }
      else { cur += c; }
    }
    fields.push(cur.trim());
    return fields;
  }).filter(r => r[0] && r[0] !== '""' && r[0] !== '');

  if (tabName === 'Conductores') {
    return rows.map(r => ({ id: r[0], name: r[1], license: r[2], phone: r[3] }));
  }
  if (tabName === 'Vehículos') {
    return rows.map(r => ({ id: r[0], plate: r[1], model: r[2], year: r[3], type: r[4], color: r[5] }));
  }
  if (tabName === 'Bitácora') {
    return rows.map(r => ({
      id: r[0], fecha: r[1], hora: r[2], tipo: r[3], conductor: r[4],
      vehiculo: r[5], modeloVehiculo: r[6], origen: r[7], destino: r[8],
      kmInicial: r[9], kmFinal: r[10], kmRecorridos: r[11], proposito: r[12],
      litros: r[13], costoLempiras: r[14], estacion: r[15],
      tipoCombustible: r[16], notas: r[17], timestamp: r[18]
    }));
  }
  return [];
}

// ── TOAST ──────────────────────────────────────────────
function toast(msg) {
  const el = document.getElementById('toast');
  el.textContent = msg; el.classList.add('show');
  clearTimeout(el._t);
  el._t = setTimeout(() => el.classList.remove('show'), 3000);
}

window.onload = init;
</script>
</body>
</html>

