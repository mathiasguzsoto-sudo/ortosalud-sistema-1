<!doctype html>
<html lang="es">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>OrtoSalud — Sistema</title>
<style>
  :root{--blue:#0b72d1;--soft:#f4f7fb;--card:#fff}
  *{box-sizing:border-box}
  body{margin:0;font-family:Inter,Segoe UI,Arial;background:var(--soft);color:#1b1b1b}
  header{background:var(--blue);color:white;padding:14px 18px;font-size:20px;font-weight:700}
  .wrap{display:flex;min-height:calc(100vh - 56px)}
  aside{width:260px;background:linear-gradient(180deg,#0b72d1,#0a63b8);color:white;padding:18px}
  aside h2{margin:0 0 8px 0;font-size:18px}
  .nav-btn{display:block;width:100%;padding:10px;border-radius:8px;margin:8px 0;background:rgba(255,255,255,0.06);color:white;border:0;text-align:left;cursor:pointer}
  .nav-btn.active{background:rgba(255,255,255,0.12)}
  main{flex:1;padding:18px}
  .card{background:var(--card);padding:16px;border-radius:10px;box-shadow:0 6px 18px rgba(12,34,63,0.06);margin-bottom:16px}
  input,select,textarea,button{width:100%;padding:10px;margin:8px 0;border:1px solid #ddd;border-radius:8px;font-size:15px}
  button{background:var(--blue);color:white;border:0;cursor:pointer}
  .grid{display:grid;grid-template-columns:repeat(2,1fr);gap:12px}
  .menu-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(140px,1fr));gap:12px}
  .box{background:#fff;padding:12px;border-radius:10px;text-align:center;cursor:pointer;border:1px solid #eee}
  .small{font-size:13px;color:#666}
  .list .item{background:#f7fbff;padding:10px;border-radius:8px;margin-bottom:8px;border-left:4px solid var(--blue)}
  .flex{display:flex;gap:8px;align-items:center}
  .right{margin-left:auto}
  @media (max-width:880px){
    aside{display:none}
    .grid{grid-template-columns:1fr}
    .wrap{flex-direction:column}
  }
</style>
</head>
<body>
<header>OrtoSalud — Sistema de Registro</header>

<div class="wrap">
  <aside>
    <h2>OrtoSalud</h2>
    <div class="small">Panel principal</div>
    <button class="nav-btn active" data-view="dashboard">Panel</button>
    <button class="nav-btn" data-view="patients">Pacientes</button>
    <button class="nav-btn" data-view="orders">Pedidos</button>
    <button class="nav-btn" data-view="invoices">Facturas</button>
    <button class="nav-btn" data-view="stock">Stock</button>
    <button class="nav-btn" data-view="calendar">Agenda</button>
    <button class="nav-btn" data-view="search">Buscar</button>
    <button class="nav-btn" data-view="lost">Me perdí</button>
    <hr style="border:none;height:1px;background:rgba(255,255,255,0.08);margin:12px 0">
    <button id="btn-export" class="nav-btn" style="background:rgba(255,255,255,0.06)">Exportar datos (JSON)</button>
    <button id="btn-clear" class="nav-btn" style="background:rgba(255,255,255,0.06)">Borrar datos (pruebas)</button>
  </aside>

  <main>
    <div id="view-root"></div>
  </main>
</div>

<script>
/* ------------------ DB (localStorage) ------------------ */
const STORAGE_KEY = 'ortosalud_db_v2';
const DEFAULT_DB = {
  patients: [],
  orders: [],
  products: [
    {id:'P-PL-001', name:'Plantilla personalizada', stock:20, price:12000},
    {id:'P-ZP-001', name:'Zapato ortopédico', stock:8, price:45000},
    {id:'P-FX-001', name:'Faja post-operatoria', stock:10, price:25000}
  ],
  invoices: [],
  appointments: []
};
let DB = load();

function save(){ localStorage.setItem(STORAGE_KEY, JSON.stringify(DB)); }
function load(){
  try{
    const raw = localStorage.getItem(STORAGE_KEY);
    if(!raw){ localStorage.setItem(STORAGE_KEY, JSON.stringify(DEFAULT_DB)); return JSON.parse(JSON.stringify(DEFAULT_DB)); }
    return JSON.parse(raw);
  }catch(e){ console.error(e); localStorage.removeItem(STORAGE_KEY); localStorage.setItem(STORAGE_KEY, JSON.stringify(DEFAULT_DB)); return JSON.parse(JSON.stringify(DEFAULT_DB)); }
}
function uid(){ return Date.now().toString(36)+Math.random().toString(36).slice(2,6); }

/* ------------------ RENDER / NAV ------------------ */
const root = document.getElementById('view-root');
document.querySelectorAll('.nav-btn[data-view]').forEach(b=>{
  b.addEventListener('click', ()=> {
    document.querySelectorAll('.nav-btn').forEach(x=>x.classList.remove('active'));
    b.classList.add('active');
    showView(b.dataset.view);
  });
});

showView('dashboard');

function showView(v){
  if(v==='dashboard') return renderDashboard();
  if(v==='patients') return renderPatients();
  if(v==='orders') return renderOrders();
  if(v==='invoices') return renderInvoices();
  if(v==='stock') return renderStock();
  if(v==='calendar') return renderCalendar();
  if(v==='search') return renderSearch();
  if(v==='lost') return renderLost();
}

/* ------------------ DASHBOARD ------------------ */
function renderDashboard(){
  const html = `
    <div class="card">
      <h2>Panel</h2>
      <div class="grid">
        <div class="card">
          <h3>Resumen</h3>
          <div class="small">Pacientes: ${DB.patients.length}</div>
          <div class="small">Pedidos: ${DB.orders.length}</div>
          <div class="small">Productos: ${DB.products.length}</div>
        </div>
        <div class="card">
          <h3>Top productos (stock)</h3>
          ${DB.products.map(p=>`<div class="small">${p.name} — ${p.stock}</div>`).join('')}
        </div>
      </div>
    </div>
  `;
  root.innerHTML = html;
}

/* ------------------ PATIENTS ------------------ */
function renderPatients(){
  root.innerHTML = `
    <div class="card">
      <h2>Pacientes</h2>
      <div class="grid">
        <div>
          <input id="pt-name" placeholder="Nombre completo">
          <input id="pt-phone1" placeholder="Teléfono">
          <input id="pt-phone2" placeholder="Segundo teléfono (opcional)">
          <input id="pt-cedula" placeholder="Cédula">
          <textarea id="pt-notes" placeholder="Notas..."></textarea>
          <button id="pt-add">Guardar paciente</button>
        </div>
        <div>
          <h3>Buscar</h3>
          <input id="pt-search" placeholder="Buscar por nombre, cédula o teléfono">
          <div id="pt-list" style="margin-top:12px"></div>
        </div>
      </div>
    </div>
  `;
  document.getElementById('pt-add').addEventListener('click', ()=>{
    const p = {
      id: uid(),
      name: document.getElementById('pt-name').value.trim(),
      phone1: document.getElementById('pt-phone1').value.trim(),
      phone2: document.getElementById('pt-phone2').value.trim(),
      cedula: document.getElementById('pt-cedula').value.trim(),
      notes: document.getElementById('pt-notes').value.trim(),
      createdAt: Date.now()
    };
    if(!p.name || !p.phone1){ alert('Nombre y teléfono son requeridos'); return; }
    DB.patients.unshift(p); save();
    renderPatients();
    alert('Paciente guardado');
  });
  document.getElementById('pt-search').addEventListener('input', ()=> drawPatients(document.getElementById('pt-search').value));
  drawPatients('');
}

function drawPatients(filter){
  const node = document.getElementById('pt-list');
  const f = (filter||'').toLowerCase();
  const rows = DB.patients.filter(p => !f || (p.name+p.cedula+(p.phone1||'')+(p.phone2||'')).toLowerCase().includes(f));
  if(!rows.length) return node.innerHTML = '<div class="small">No hay pacientes</div>';
  node.innerHTML = rows.map(p=>`
    <div class="item">
      <strong>${p.name}</strong><div class="small">Tel: ${p.phone1}${p.phone2? ' / '+p.phone2:''} • Cédula: ${p.cedula||'-'}</div>
      <div class="small">${p.notes||''}</div>
      <div style="margin-top:8px">
        <button data-id="${p.id}" class="btn btn-edit">Editar</button>
        <button data-id="${p.id}" class="btn btn-order" style="background:#2a9d8f;margin-left:8px">Nuevo pedido</button>
        <button data-id="${p.id}" class="btn btn-del" style="background:#d62828;margin-left:8px">Eliminar</button>
      </div>
    </div>
  `).join('');
  node.querySelectorAll('.btn-edit').forEach(b=> b.addEventListener('click', ()=> editPatient(b.dataset.id)));
  node.querySelectorAll('.btn-order').forEach(b=> b.addEventListener('click', ()=> openOrderForm(b.dataset.id)));
  node.querySelectorAll('.btn-del').forEach(b=> b.addEventListener('click', ()=> {
    if(confirm('Borrar paciente?')){ DB.patients = DB.patients.filter(x=>x.id!==b.dataset.id); save(); renderPatients(); }
  }));
}

function editPatient(id){
  const p = DB.patients.find(x=>x.id===id);
  if(!p) return alert('Paciente no encontrado');
  const name = prompt('Nombre', p.name);
  if(!name) return;
  p.name = name;
  p.phone1 = prompt('Teléfono', p.phone1) || p.phone1;
  p.phone2 = prompt('Segundo teléfono', p.phone2) || p.phone2;
  p.cedula = prompt('Cédula', p.cedula) || p.cedula;
  p.notes = prompt('Notas', p.notes) || p.notes;
  save(); renderPatients();
}

/* ------------------ ORDERS (Pedidos / Plantillas) ------------------ */
function renderOrders(){
  root.innerHTML = `
    <div class="card">
      <h2>Pedidos / Plantillas</h2>
      <div class="grid">
        <div>
          <select id="o-patient"><option value="">-- Seleccionar paciente (o dejar vacío) --</option>${DB.patients.map(p=>`<option value="${p.id}">${p.name}</option>`).join('')}</select>
          <select id="o-type"><option>Plantillas</option><option>Zapatos</option><option>Pedidos especiales</option></select>
          <div id="o-items-root"></div>
          <button id="o-add-item" style="background:#6c757d">+ Agregar producto</button>
          <input id="o-abono" type="number" placeholder="Abono">
          <select id="o-pay"><option>Efectivo</option><option>Tarjeta</option><option>SINPE</option></select>
          <textarea id="o-notes" placeholder="Notas"></textarea>
          <button id="o-save">Registrar pedido</button>
        </div>
        <div>
          <h3>Pedidos existentes</h3>
          <div id="orders-list"></div>
        </div>
      </div>
    </div>
  `;
  // items
  let items = [];
  function redrawItems(){
    document.getElementById('o-items-root').innerHTML = items.map((it,idx)=>`
      <div style="display:flex;gap:8px;margin-bottom:8px">
        <select data-idx="${idx}" class="it-prod">${DB.products.map(p=>`<option value="${p.id}" ${p.id===it.productId?'selected':''}>${p.name}</option>`).join('')}</select>
        <input data-idx="${idx}" class="it-qty" type="number" value="${it.qty||1}" style="width:100px">
        <input data-idx="${idx}" class="it-price" type="number" value="${it.price||0}" style="width:140px">
        <button data-idx="${idx}" class="it-rm" style="background:#d11;width:40px">X</button>
      </div>
    `).join('');
    document.querySelectorAll('.it-rm').forEach(b=> b.addEventListener('click', ()=> { items.splice(b.dataset.idx,1); redrawItems(); }));
    document.querySelectorAll('.it-qty').forEach(inp=> inp.addEventListener('input', ()=> { items[inp.dataset.idx].qty = parseInt(inp.value)||1; }));
    document.querySelectorAll('.it-price').forEach(inp=> inp.addEventListener('input', ()=> { items[inp.dataset.idx].price = parseInt(inp.value)||0; }));
  }
  document.getElementById('o-add-item').addEventListener('click', ()=> { items.push({ productId:DB.products[0].id, qty:1, price:DB.products[0].price }); redrawItems(); });
  document.getElementById('o-save').addEventListener('click', ()=> {
    const order = {
      id: uid(),
      patientId: document.getElementById('o-patient').value || null,
      type: document.getElementById('o-type').value,
      items: items.slice(),
      abono: parseInt(document.getElementById('o-abono').value)||0,
      payMethod: document.getElementById('o-pay').value,
      notes: document.getElementById('o-notes').value,
      delivered: false,
      createdAt: Date.now()
    };
    order.total = order.items.reduce((s,it)=> s + (it.price||0)*(it.qty||1),0);
    order.saldo = Math.max(0, order.total - order.abono);
    DB.orders.unshift(order);
    // actualizar stock
    order.items.forEach(it=>{
      const prod = DB.products.find(p=>p.id===it.productId);
      if(prod) prod.stock = Math.max(0, prod.stock - (it.qty||0));
    });
    save();
    alert('Pedido registrado');
    renderOrders();
  });
  redrawItems();
  drawOrdersList();
}

function drawOrdersList(){
  const listNode = document.getElementById('orders-list');
  if(!listNode) return;
  listNode.innerHTML = DB.orders.map(o=>`
    <div class="item">
      <strong>${o.type}</strong><div class="small">Cliente: ${o.patientId ? (DB.patients.find(p=>p.id===o.patientId)||{name:'-' }).name : 'Sin cliente'}</div>
      <div class="small">Total: ₡${o.total || 0} — Abono: ₡${o.abono || 0} — Saldo: ₡${o.saldo || 0}</div>
      <div style="margin-top:8px">
        <button data-id="${o.id}" class="btn-mark">Marcar entregado</button>
        <button data-id="${o.id}" class="btn-del" style="background:#d11;margin-left:8px">Eliminar</button>
      </div>
    </div>
  `).join('');
  listNode.querySelectorAll('.btn-mark').forEach(b=> b.addEventListener('click', ()=> {
    const o = DB.orders.find(x=>x.id===b.dataset.id);
    if(o){ o.delivered = true; save(); drawOrdersList(); alert('Marcado entregado'); }
  }));
  listNode.querySelectorAll('.btn-del').forEach(b=> b.addEventListener('click', ()=> {
    if(confirm('Eliminar pedido?')){ DB.orders = DB.orders.filter(x=>x.id!==b.dataset.id); save(); drawOrdersList(); }
  }));
}

/* ------------------ INVOICES ------------------ */
function renderInvoices(){
  root.innerHTML = `
    <div class="card">
      <h2>Facturas</h2>
      <div class="grid">
        <div>
          <input id="inv-name" placeholder="Nombre completo">
          <input id="inv-tel1" placeholder="Teléfono">
          <input id="inv-ced" placeholder="Cédula">
          <input id="inv-email" placeholder="Correo">
          <input id="inv-product" placeholder="Producto">
          <input id="inv-price" type="number" placeholder="Precio">
          <select id="inv-method"><option>Efectivo</option><option>Tarjeta</option><option>SINPE</option></select>
          <button id="inv-save">Guardar factura</button>
        </div>
        <div>
          <h3>Facturas guardadas</h3>
          <div id="inv-list"></div>
        </div>
      </div>
    </div>
  `;
  document.getElementById('inv-save').addEventListener('click', ()=>{
    const f = {
      id: uid(),
      name: document.getElementById('inv-name').value.trim(),
      tel: document.getElementById('inv-tel1').value.trim(),
      ced: document.getElementById('inv-ced').value.trim(),
      email: document.getElementById('inv-email').value.trim(),
      product: document.getElementById('inv-product').value.trim(),
      price: parseInt(document.getElementById('inv-price').value)||0,
      method: document.getElementById('inv-method').value,
      createdAt: Date.now()
    };
    if(!f.name || !f.product){ alert('Nombre y producto requeridos'); return; }
    DB.invoices.unshift(f); save(); renderInvoices();
  });
  drawInvoices();
}

function drawInvoices(){
  const node = document.getElementById('inv-list');
  if(!node) return;
  node.innerHTML = DB.invoices.map(i=>`
    <div class="item">
      <strong>${i.name}</strong><div class="small">${i.product} — ₡${i.price}</div>
      <div style="margin-top:8px"><button data-id="${i.id}" class="btn-del-inv" style="background:#d11">Eliminar</button></div>
    </div>
  `).join('');
  node.querySelectorAll('.btn-del-inv').forEach(b=> b.addEventListener('click', ()=> { DB.invoices = DB.invoices.filter(x=>x.id!==b.dataset.id); save(); drawInvoices(); }));
}

/* ------------------ STOCK ------------------ */
function renderStock(){
  root.innerHTML = `
    <div class="card">
      <h2>Inventario</h2>
      <div class="grid">
        <div>
          <input id="prod-name" placeholder="Nombre producto">
          <input id="prod-stock" type="number" placeholder="Stock inicial">
          <input id="prod-price" type="number" placeholder="Precio">
          <button id="prod-add">Agregar producto</button>
        </div>
        <div>
          <h3>Productos</h3>
          <div id="prod-list"></div>
        </div>
      </div>
    </div>
  `;
  document.getElementById('prod-add').addEventListener('click', ()=>{
    const p = { id: uid(), name: document.getElementById('prod-name').value.trim(), stock: parseInt(document.getElementById('prod-stock').value)||0, price: parseInt(document.getElementById('prod-price').value)||0 };
    if(!p.name){ alert('Nombre requerido'); return; }
    DB.products.unshift(p); save(); renderStock();
  });
  drawProducts();
}

function drawProducts(){
  const node = document.getElementById('prod-list');
  if(!node) return;
  node.innerHTML = DB.products.map(p=>`
    <div class="item">
      <strong>${p.name}</strong><div class="small">Stock: ${p.stock} — Precio: ₡${p.price}</div>
      <div style="margin-top:8px">
        <button data-id="${p.id}" class="btn-edit-prod">Editar</button>
        <button data-id="${p.id}" class="btn-del-prod" style="background:#d11;margin-left:8px">Eliminar</button>
      </div>
    </div>
  `).join('');
  node.querySelectorAll('.btn-edit-prod').forEach(b=> b.addEventListener('click', ()=> {
    const p = DB.products.find(x=>x.id===b.dataset.id);
    if(!p) return;
    p.name = prompt('Nombre', p.name) || p.name;
    p.stock = parseInt(prompt('Stock', p.stock)||p.stock);
    p.price = parseInt(prompt('Precio', p.price)||p.price);
    save(); drawProducts();
  }));
  node.querySelectorAll('.btn-del-prod').forEach(b=> b.addEventListener('click', ()=> { DB.products = DB.products.filter(x=>x.id!==b.dataset.id); save(); drawProducts(); }));
}

/* ------------------ CALENDAR / AGENDA ------------------ */
function renderCalendar(){
  root.innerHTML = `
    <div class="card">
      <h2>Agenda</h2>
      <div class="grid">
        <div>
          <input id="ap-name" placeholder="Nombre paciente">
          <input id="ap-date" type="date">
          <input id="ap-time" type="time">
          <textarea id="ap-notes" placeholder="Notas"></textarea>
          <button id="ap-add">Agregar cita</button>
        </div>
        <div>
          <h3>Citas</h3>
          <div id="ap-list"></div>
        </div>
      </div>
    </div>
  `;
  document.getElementById('ap-add').addEventListener('click', ()=>{
    const c = { id: uid(), name: document.getElementById('ap-name').value.trim(), date: document.getElementById('ap-date').value, time: document.getElementById('ap-time').value, notes: document.getElementById('ap-notes').value, status:'Pendiente' };
    if(!c.name || !c.date){ alert('Nombre y fecha requeridos'); return; }
    DB.appointments.unshift(c); save(); renderCalendar();
  });
  drawAppointments();
}

function drawAppointments(){
  const node = document.getElementById('ap-list'); if(!node) return;
  node.innerHTML = DB.appointments.map(a=>`
    <div class="item">
      <strong>${a.name}</strong><div class="small">${a.date} ${a.time || ''} — ${a.status}</div>
      <div class="small">${a.notes||''}</div>
      <div style="margin-top:8px">
        <button data-id="${a.id}" class="btn-done">Visto</button>
        <button data-id="${a.id}" class="btn-del" style="background:#d11;margin-left:8px">Eliminar</button>
      </div>
    </div>
  `).join('');
  node.querySelectorAll('.btn-done').forEach(b=> b.addEventListener('click', ()=> { const a = DB.appointments.find(x=>x.id===b.dataset.id); if(a){ a.status='Visto'; save(); drawAppointments(); }}));
  node.querySelectorAll('.btn-del').forEach(b=> b.addEventListener('click', ()=> { DB.appointments = DB.appointments.filter(x=>x.id!==b.dataset.id); save(); drawAppointments(); }));
}

/* ------------------ SEARCH ------------------ */
function renderSearch(){
  root.innerHTML = `
    <div class="card">
      <h2>Buscar Personas</h2>
      <div class="flex"><input id="s-txt" placeholder="Escriba nombre, cédula o teléfono"><button id="s-btn" style="width:150px;margin-left:8px">Buscar</button></div>
      <div id="s-res" style="margin-top:12px"></div>
    </div>
  `;
  document.getElementById('s-btn').addEventListener('click', buscar);
  document.getElementById('s-txt').addEventListener('keydown', (e)=>{ if(e.key==='Enter') buscar(); });
  function buscar(){
    const q = document.getElementById('s-txt').value.toLowerCase();
    const res = DB.patients.filter(p=> (p.name+p.cedula+(p.phone1||'')+(p.phone2||'')).toLowerCase().includes(q));
    const node = document.getElementById('s-res');
    if(!res.length) return node.innerHTML = '<div class="small">No encontrado</div>';
    node.innerHTML = res.map(p=>`<div class="item"><strong>${p.name}</strong><div class="small">Tel: ${p.phone1} • Cédula: ${p.cedula||'-'}</div></div>`).join('');
  }
}

/* ------------------ ME PERDÍ ------------------ */
function renderLost(){
  root.innerHTML = `
    <div class="card">
      <h2>Me perdí</h2>
      <input id="lost-txt" placeholder="Escribe lo que quieres hacer (ej: plantillas, agenda, facturas)">
      <button id="lost-btn">Ir</button>
      <div id="lost-res" style="margin-top:12px" class="small"></div>
    </div>
  `;
  document.getElementById('lost-btn').addEventListener('click', ()=>{
    const t = document.getElementById('lost-txt').value.toLowerCase();
    const map = [['plant', 'plantillas'], ['plantillas','plantillas'], ['zapato','zapatos'], ['factura','facturas'], ['stock','stock'], ['agenda','calendar'], ['cita','calendar'], ['buscar','search']];
    for(const [k,v] of map) if(t.includes(k)){ // switch to view
      document.querySelectorAll('.nav-btn').forEach(x=>x.classList.remove('active'));
      const btn = Array.from(document.querySelectorAll('.nav-btn')).find(x=>x.dataset.view===v);
      if(btn) btn.classList.add('active');
      showView(v); document.getElementById('lost-res').innerText = 'Listo — te llevé a: ' + v; return;
    }
    document.getElementById('lost-res').innerText = 'No entendí. Prueba escribir: plantillas, zapatos, facturas, agenda o stock';
  });
}

/* ------------------ EXPORT / CLEAR ------------------ */
document.getElementById('btn-export').addEventListener('click', ()=>{
  const data = JSON.stringify(DB, null, 2);
  const blob = new Blob([data], {type:'application/json'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a'); a.href = url; a.download = 'ortosalud_export.json'; a.click();
  URL.revokeObjectURL(url);
});

document.getElementById('btn-clear').addEventListener('click', ()=>{
  if(!confirm('Borrar todos los datos guardados en este navegador?')) return;
  localStorage.removeItem(STORAGE_KEY);
  DB = load();
  alert('Datos eliminados (solo en este navegador).');
  showView('dashboard');
});

/* ------------------ INIT ------------------ */
// ensure initial rendering of lists when switching views that depend on DB content
const originalShow = showView;
function showView(v){ originalShow(v); /* no-op: kept for older references */ }
save(); // ensure DB saved
</script>
</body>
</html>
