<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>CityEye · Smart City Road Safety</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"/>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=Space+Grotesk:wght@500;600;700&display=swap" rel="stylesheet">
<style>
  * { margin:0; padding:0; box-sizing:border-box; }
  body { font-family:'Inter',sans-serif; background:#f8fafc; color:#0f172a; }
  h1,h2,h3 { font-family:'Space Grotesk',sans-serif; }

  /* NAV */
  .nav {
    display:flex; justify-content:space-between; align-items:center;
    padding:18px 40px; background:white; position:sticky; top:0; z-index:1000;
    border-bottom:1px solid #e2eef9;
  }
  .logo { font-size:1.6rem; font-weight:700; color:#0b2e4b; }
  .logo span { background:linear-gradient(135deg,#1e6b9b,#0f4c75); -webkit-background-clip:text; -webkit-text-fill-color:transparent; }
  .nav-links { display:flex; gap:24px; list-style:none; }
  .nav-links a { color:#334155; text-decoration:none; font-weight:500; font-size:.95rem; }
  .nav-links a:hover { color:#1e6b9b; }

  /* HERO */
  .hero {
    background:linear-gradient(135deg,#0b2e4b 0%,#0f4c75 60%,#1e6b9b 100%);
    color:white; padding:70px 40px; text-align:center;
  }
  .hero h1 { font-size:2.8rem; margin-bottom:16px; }
  .hero p { max-width:750px; margin:0 auto 24px; opacity:.92; font-size:1.1rem; }
  .hero .cta {
    display:inline-block; background:white; color:#0f4c75; padding:12px 30px;
    border-radius:50px; text-decoration:none; font-weight:600;
    transition:.2s;
  }
  .hero .cta:hover { transform:scale(1.05); }

  /* SECTION */
  .container { max-width:1280px; margin:0 auto; padding:50px 24px; }
  .section-title { font-size:1.8rem; margin-bottom:8px; display:flex; align-items:center; gap:10px; color:#0b2e4b; }
  .section-sub { color:#475569; margin-bottom:28px; }
  .section-title i { color:#1e6b9b; }

  /* STATS */
  .stats-grid { display:grid; grid-template-columns:repeat(auto-fit,minmax(180px,1fr)); gap:18px; margin-bottom:50px; }
  .stat-card {
    background:white; padding:22px; border-radius:20px; text-align:center;
    border:1px solid #e2eef9; box-shadow:0 6px 16px -8px rgba(0,50,80,.1);
    transition:.2s;
  }
  .stat-card:hover { transform:translateY(-4px); }
  .stat-card i { font-size:1.8rem; color:#1e6b9b; margin-bottom:10px; }
  .stat-card .num { font-size:1.9rem; font-weight:700; color:#0b2e4b; }
  .stat-card .lbl { color:#475569; font-size:.85rem; }

  /* FORM */
  .form-card {
    background:white; padding:28px; border-radius:24px;
    border:1px solid #e2eef9; box-shadow:0 10px 30px -12px rgba(0,50,80,.1);
    margin-bottom:32px;
  }
  .form-grid { display:grid; grid-template-columns:repeat(auto-fit,minmax(220px,1fr)); gap:14px; }
  .form-grid input, .form-grid select, .form-grid textarea {
    padding:11px 14px; border:1px solid #cbd5e1; border-radius:12px;
    font-family:inherit; font-size:.92rem; width:100%;
    transition:.15s;
  }
  .form-grid input:focus, .form-grid select:focus, .form-grid textarea:focus {
    outline:none; border-color:#1e6b9b; box-shadow:0 0 0 3px rgba(30,107,155,.15);
  }
  .form-grid textarea { grid-column:1/-1; resize:vertical; min-height:70px; }
  .btn {
    background:#0f4c75; color:white; padding:11px 26px; border:none;
    border-radius:50px; font-weight:600; cursor:pointer;
    font-family:inherit; font-size:.92rem; transition:.2s;
    display:inline-flex; align-items:center; gap:8px;
  }
  .btn:hover { background:#0b3e5c; transform:translateY(-1px); }
  .btn.ghost { background:transparent; color:#0f4c75; border:1.5px solid #0f4c75; }
  .btn.danger { background:#dc2626; }
  .btn-row { display:flex; gap:12px; margin-top:16px; flex-wrap:wrap; }

  /* MAP */
  #map { height:420px; border-radius:24px; box-shadow:0 10px 30px -12px rgba(0,50,80,.15); z-index:1; }

  /* EVENT LIST */
  .event-list { display:grid; grid-template-columns:repeat(auto-fit,minmax(300px,1fr)); gap:16px; }
  .event-item {
    background:white; padding:18px 20px; border-radius:18px;
    border-left:6px solid #1e6b9b; border-top:1px solid #e2eef9;
    border-right:1px solid #e2eef9; border-bottom:1px solid #e2eef9;
    box-shadow:0 4px 14px -8px rgba(0,50,80,.1);
    transition:.2s;
  }
  .event-item:hover { transform:translateY(-3px); }
  .event-item.high { border-left-color:#dc2626; }
  .event-item.moderate { border-left-color:#f59e0b; }
  .event-item.low { border-left-color:#10b981; }
  .event-head { display:flex; justify-content:space-between; align-items:center; margin-bottom:6px; }
  .event-type { font-weight:600; color:#0b2e4b; text-transform:capitalize; }
  .badge {
    display:inline-block; padding:3px 10px; border-radius:20px;
    font-size:.7rem; font-weight:600; text-transform:uppercase;
  }
  .badge.high { background:#fee2e2; color:#b91c1c; }
  .badge.moderate { background:#fef3c7; color:#92400e; }
  .badge.low { background:#d1fae5; color:#065f46; }
  .badge.verified { background:#dbeafe; color:#1e40af; }
  .badge.incident { background:#fce7f3; color:#9d174d; }
  .event-meta { font-size:.82rem; color:#64748b; margin-top:4px; }
  .event-desc { font-size:.9rem; color:#334155; margin-top:8px; }

  /* ROUTE SCORING */
  .route-grid { display:grid; grid-template-columns:repeat(auto-fit,minmax(280px,1fr)); gap:16px; margin-top:20px; }
  .route-card {
    background:white; padding:20px; border-radius:20px;
    border:1px solid #e2eef9; box-shadow:0 6px 16px -8px rgba(0,50,80,.1);
  }
  .route-card h4 { color:#0b2e4b; margin-bottom:8px; font-size:1.05rem; }
  .route-score { font-size:1.6rem; font-weight:700; margin:8px 0; }
  .route-score.low { color:#10b981; }
  .route-score.moderate { color:#f59e0b; }
  .route-score.high { color:#dc2626; }

  .filters { display:flex; gap:12px; margin-bottom:20px; flex-wrap:wrap; align-items:center; }
  .filters select { padding:9px 14px; border-radius:12px; border:1px solid #cbd5e1; font-family:inherit; }

  .toast {
    position:fixed; bottom:24px; right:24px; background:#0b2e4b; color:white;
    padding:14px 22px; border-radius:12px; box-shadow:0 10px 30px -10px rgba(0,0,0,.3);
    opacity:0; transform:translateY(20px); transition:.3s; z-index:9999;
  }
  .toast.show { opacity:1; transform:translateY(0); }

  footer { background:#0b2e4b; color:white; text-align:center; padding:28px; margin-top:40px; font-size:.9rem; }
  footer i { color:#5fa7d6; }

  @media(max-width:640px){
    .hero h1 { font-size:2rem; }
    .nav-links { display:none; }
    .nav { padding:14px 20px; }
    .container { padding:30px 16px; }
  }
</style>
</head>
<body>

<!-- NAV -->
<nav class="nav">
  <div class="logo"><i class="fas fa-bus"></i> <span>City</span>Eye</div>
  <ul class="nav-links">
    <li><a href="#dashboard"><i class="fas fa-chart-line"></i> Dashboard</a></li>
    <li><a href="#report"><i class="fas fa-plus-circle"></i> Report</a></li>
    <li><a href="#map-section"><i class="fas fa-map"></i> Map</a></li>
    <li><a href="#routes"><i class="fas fa-route"></i> Routes</a></li>
  </ul>
</nav>

<!-- HERO -->
<header class="hero">
  <h1><i class="fas fa-shield-alt"></i> Smart City Road Safety</h1>
  <p>An AI-powered urban intelligence platform that turns public transport buses into continuous mobile sensing units — detecting potholes, waterlogging, hazards and incidents in real time.</p>
  <a href="#dashboard" class="cta"><i class="fas fa-rocket"></i> Explore Dashboard</a>
</header>

<!-- DASHBOARD / STATS -->
<div class="container" id="dashboard">
  <h2 class="section-title"><i class="fas fa-chart-line"></i> Live Dashboard</h2>
  <p class="section-sub">Real-time overview of road events detected across the city by the bus fleet.</p>

  <div class="stats-grid" id="statsGrid">
    <div class="stat-card"><i class="fas fa-map-marker-alt"></i><div class="num" id="statTotal">–</div><div class="lbl">Total Events</div></div>
    <div class="stat-card"><i class="fas fa-road"></i><div class="num" id="statPotholes">–</div><div class="lbl">Potholes</div></div>
    <div class="stat-card"><i class="fas fa-water"></i><div class="num" id="statWater">–</div><div class="lbl">Waterlogging</div></div>
    <div class="stat-card"><i class="fas fa-car-crash"></i><div class="num" id="statIncidents">–</div><div class="lbl">Incidents</div></div>
    <div class="stat-card"><i class="fas fa-check-circle"></i><div class="num" id="statVerified">–</div><div class="lbl">Verified</div></div>
    <div class="stat-card"><i class="fas fa-exclamation-triangle"></i><div class="num" id="statHigh">–</div><div class="lbl">High Severity</div></div>
  </div>
</div>

<!-- REPORT FORM -->
<div class="container" id="report">
  <h2 class="section-title"><i class="fas fa-plus-circle"></i> Report New Event</h2>
  <p class="section-sub">Simulate a bus detection. Duplicate events within ~50m are auto-merged by the Unique Event Engine.</p>

  <div class="form-card">
    <form id="eventForm" class="form-grid">
      <select id="event_type" required>
        <option value="">Select event type…</option>
        <option value="pothole">Pothole / Damaged Road</option>
        <option value="waterlogging">Waterlogging</option>
        <option value="divider_missing">Divider Missing/Damaged</option>
        <option value="zebra_missing">Zebra Crossing Missing</option>
        <option value="traffic_sign_damaged">Traffic Sign Damaged</option>
        <option value="obstacle">Roadside Obstacle</option>
        <option value="traffic_jam">Traffic Jam</option>
        <option value="accident">Accident</option>
        <option value="hit_and_run">Hit and Run</option>
      </select>

      <select id="severity">
        <option value="low">Low severity</option>
        <option value="moderate" selected>Moderate severity</option>
        <option value="high">High severity</option>
      </select>

      <input type="number" step="any" id="latitude" placeholder="Latitude (e.g. 28.6139)" required>
      <input type="number" step="any" id="longitude" placeholder="Longitude (e.g. 77.2090)" required>
      <input type="text" id="bus_id" placeholder="Bus ID (e.g. BUS-101)" value="BUS-101">
      <input type="text" id="number_plate" placeholder="Vehicle number (if incident)">
      <textarea id="description" placeholder="Short description / evidence notes…"></textarea>

      <label style="display:flex; align-items:center; gap:8px; font-size:.9rem; color:#334155; grid-column:1/-1;">
        <input type="checkbox" id="is_incident" style="width:auto;"> Mark as safety incident (accident / hit-and-run)
      </label>

      <div class="btn-row" style="grid-column:1/-1;">
        <button type="submit" class="btn"><i class="fas fa-paper-plane"></i> Submit Detection</button>
        <button type="button" class="btn ghost" id="seedBtn"><i class="fas fa-database"></i> Load Sample Data</button>
        <button type="reset" class="btn ghost"><i class="fas fa-undo"></i> Reset</button>
      </div>
    </form>
  </div>
</div>

<!-- MAP -->
<div class="container" id="map-section">
  <h2 class="section-title"><i class="fas fa-map-marked-alt"></i> Live City Map</h2>
  <p class="section-sub">Every event pinned with GPS coordinates, colour-coded by severity.</p>
  <div id="map"></div>
</div>

<!-- EVENT LIST -->
<div class="container">
  <h2 class="section-title"><i class="fas fa-list"></i> Detected Events</h2>
  <div class="filters">
    <label style="font-size:.9rem; color:#475569;">Filter by type:</label>
    <select id="filterType">
      <option value="">All types</option>
      <option value="pothole">Pothole</option>
      <option value="waterlogging">Waterlogging</option>
      <option value="divider_missing">Divider</option>
      <option value="accident">Accident</option>
    </select>
    <label style="font-size:.9rem; color:#475569;">Severity:</label>
    <select id="filterSeverity">
      <option value="">All</option>
      <option value="high">High</option>
      <option value="moderate">Moderate</option>
      <option value="low">Low</option>
    </select>
    <button class="btn ghost" id="refreshBtn"><i class="fas fa-sync-alt"></i> Refresh</button>
  </div>
  <div class="event-list" id="eventList"></div>
</div>

<!-- ROUTE SCORING -->
<div class="container" id="routes">
  <h2 class="section-title"><i class="fas fa-route"></i> Consequence-Aware Route Scoring</h2>
  <p class="section-sub">Compares route conditions and recommends the safest & most efficient path.</p>

  <div class="form-card">
    <form id="routeForm" class="form-grid">
      <input type="text" id="origin" placeholder="Origin" required>
      <input type="text" id="destination" placeholder="Destination" required>
      <input type="number" step="any" id="distance_km" placeholder="Distance (km)" required>
      <input type="number" step="any" id="traffic_score" placeholder="Traffic score (0-10)" required>
      <input type="number" step="any" id="road_condition" placeholder="Road condition score (0-10)" required>
      <input type="number" step="any" id="waterlogging" placeholder="Waterlogging score (0-10)" required>
      <input type="number" step="any" id="hazards" placeholder="Hazards score (0-10)" required>
      <div class="btn-row" style="grid-column:1/-1;">
        <button type="submit" class="btn"><i class="fas fa-calculator"></i> Calculate Route Score</button>
        <button type="button" class="btn ghost" id="refreshRoutesBtn"><i class="fas fa-sync-alt"></i> Refresh Routes</button>
      </div>
    </form>
  </div>

  <div class="route-grid" id="routeList"></div>
</div>

<!-- FOOTER -->
<footer>
  <i class="fas fa-bus"></i> CityEye · UrbanSense AI — Smart Mobility & Transportation
  <br><small style="opacity:.7;">Existing buses + AI = Continuous, scalable urban monitoring.</small>
</footer>

<div class="toast" id="toast">Saved!</div>

<!-- Leaflet -->
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script>
/* ================= CONFIG ================= */
const API = "http://127.0.0.1:8000";

/* ================= MAP SETUP ================= */
const map = L.map('map').setView([28.6139, 77.2090], 12);
L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
  attribution: '© OpenStreetMap contributors'
}).addTo(map);

let markersLayer = L.layerGroup().addTo(map);

/* ================= HELPERS ================= */
function toast(msg) {
  const t = document.getElementById('toast');
  t.textContent = msg;
  t.classList.add('show');
  setTimeout(() => t.classList.remove('show'), 2200);
}

function severityColor(sev) {
  return sev === 'high' ? '#dc2626' : sev === 'moderate' ? '#f59e0b' : '#10b981';
}

/* ================= STATS ================= */
async function loadStats() {
  try {
    const r = await fetch(`${API}/api/stats`);
    const d = await r.json();
    document.getElementById('statTotal').textContent = d.total_events;
    document.getElementById('statPotholes').textContent = d.potholes;
    document.getElementById('statWater').textContent = d.waterlogging;
    document.getElementById('statIncidents').textContent = d.incidents;
    document.getElementById('statVerified').textContent = d.verified;
    document.getElementById('statHigh').textContent = d.high_severity;
  } catch (e) { console.error(e); }
}

/* ================= EVENTS ================= */
async function loadEvents() {
  const type = document.getElementById('filterType').value;
  const sev = document.getElementById('filterSeverity').value;
  const params = new URLSearchParams();
  if (type) params.append('event_type', type);
  if (sev) params.append('severity', sev);

  try {
    const r = await fetch(`${API}/api/events?${params.toString()}`);
    const events = await r.json();

    // Render list
    const list = document.getElementById('eventList');
    if (!events.length) {
      list.innerHTML = `<p style="color:#64748b;">No events yet. Report one above 👆</p>`;
    } else {
      list.innerHTML = events.map(ev => `
        <div class="event-item ${ev.severity}">
          <div class="event-head">
            <span class="event-type">${ev.event_type.replace(/_/g,' ')}</span>
            <span class="badge ${ev.severity}">${ev.severity}</span>
          </div>
          <div class="event-meta">
            <i class="fas fa-fingerprint"></i> ${ev.event_uid} ·
            <i class="fas fa-bus"></i> ${ev.bus_id} ·
            <i class="fas fa-eye"></i> ${ev.detection_count} detection(s)
          </div>
          ${ev.description ? `<div class="event-desc">${ev.description}</div>` : ''}
          <div class="event-meta" style="margin-top:8px;">
            <i class="fas fa-map-pin"></i> ${ev.latitude.toFixed(4)}, ${ev.longitude.toFixed(4)}
            ${ev.verified ? '<span class="badge verified" style="margin-left:6px;">Verified</span>' : ''}
            ${ev.is_incident ? '<span class="badge incident" style="margin-left:6px;">Incident</span>' : ''}
            ${ev.number_plate ? `<br><i class="fas fa-car"></i> ${ev.number_plate}` : ''}
          </div>
        </div>
      `).join('');
    }

    // Render map markers
    markersLayer.clearLayers();
    events.forEach(ev => {
      const color = severityColor(ev.severity);
      const circle = L.circleMarker([ev.latitude, ev.longitude], {
        radius: 9, color, fillColor: color, fillOpacity: .8, weight: 2
      }).bindPopup(`
        <b>${ev.event_type.replace(/_/g,' ')}</b><br>
        <small>UID: ${ev.event_uid}</small><br>
        Severity: <b style="color:${color}">${ev.severity}</b><br>
        Detections: ${ev.detection_count}<br>
        ${ev.description || ''}
      `);
      markersLayer.addLayer(circle);
    });
  } catch (e) { console.error(e); }
}

/* ================= SUBMIT EVENT ================= */
document.getElementById('eventForm').addEventListener('submit', async (e) => {
  e.preventDefault();
  const payload = {
    event_type: document.getElementById('event_type').value,
    severity: document.getElementById('severity').value,
    latitude: parseFloat(document.getElementById('latitude').value),
    longitude: parseFloat(document.getElementById('longitude').value),
    bus_id: document.getElementById('bus_id').value || "BUS-001",
    description: document.getElementById('description').value,
    evidence_url: "",
    is_incident: document.getElementById('is_incident').checked,
    number_plate: document.getElementById('number_plate').value,
  };

  try {
    const r = await fetch(`${API}/api/events`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload)
    });
    if (!r.ok) throw new Error(await r.text());
    const data = await r.json();
    toast(`Event ${data.event_uid} saved (count: ${data.detection_count})`);
    document.getElementById('eventForm').reset();
    loadEvents(); loadStats();
  } catch (err) { alert("Error: " + err.message); }
});

/* ================= SEED ================= */
document.getElementById('seedBtn').addEventListener('click', async () => {
  try {
    const r = await fetch(`${API}/api/seed`, { method: 'POST' });
    const d = await r.json();
    toast(d.message);
    loadEvents(); loadStats(); loadRoutes();
  } catch (e) { alert("Error seeding"); }
});

/* ================= ROUTES ================= */
async function loadRoutes() {
  try {
    const r = await fetch(`${API}/api/routes`);
    const routes = await r.json();
    const list = document.getElementById('routeList');
    if (!routes.length) {
      list.innerHTML = `<p style="color:#64748b;">No routes scored yet.</p>`;
      return;
    }
    list.innerHTML = routes.map(rt => `
      <div class="route-card">
        <h4><i class="fas fa-route"></i> ${rt.origin} → ${rt.destination}</h4>
        <div class="route-score ${rt.recommendation}">${rt.consequence_score} <small style="font-size:.9rem;font-weight:500;">/ 10</small></div>
        <div class="badge ${rt.recommendation}">${rt.recommendation.toUpperCase()} RISK</div>
        <div class="event-meta" style="margin-top:10px;">
          Distance: ${rt.distance_km} km · Traffic: ${rt.traffic_score} · Road: ${rt.road_condition}<br>
          Waterlogging: ${rt.waterlogging} · Hazards: ${rt.hazards}
        </div>
      </div>
    `).join('');
  } catch (e) { console.error(e); }
}

document.getElementById('routeForm').addEventListener('submit', async (e) => {
  e.preventDefault();
  const payload = {
    origin: document.getElementById('origin').value,
    destination: document.getElementById('destination').value,
    distance_km: parseFloat(document.getElementById('distance_km').value),
    traffic_score: parseFloat(document.getElementById('traffic_score').value),
    road_condition: parseFloat(document.getElementById('road_condition').value),
    waterlogging: parseFloat(document.getElementById('waterlogging').value),
    hazards: parseFloat(document.getElementById('hazards').value),
  };
  try {
    const r = await fetch(`${API}/api/routes/score`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload)
    });
    const data = await r.json();
    toast(`Route scored: ${data.consequence_score} (${data.recommendation})`);
    document.getElementById('routeForm').reset();
    loadRoutes();
  } catch (err) { alert("Error: " + err.message); }
});

document.getElementById('refreshRoutesBtn').addEventListener('click', loadRoutes);

/* ================= FILTERS & REFRESH ================= */
document.getElementById('refreshBtn').addEventListener('click', loadEvents);
document.getElementById('filterType').addEventListener('change', loadEvents);
document.getElementById('filterSeverity').addEventListener('change', loadEvents);

/* ================= INIT ================= */
loadStats();
loadEvents();
loadRoutes();

// Auto-refresh every 15s
setInterval(() => { loadStats(); loadEvents(); }, 15000);
</script>
</body>
</html>
