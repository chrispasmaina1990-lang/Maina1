<!DOCTYPE html>
<html lang="en"><head><meta charset="UTF-8"><meta name="viewport" content="width=device-width,initial-scale=1">
<title>Maina Accessories - POS</title>
<style>
*{box-sizing:border-box;font-family:Arial}body{margin:0;background:#f3f3f3}
header{background:#111;color:#fff;padding:14px;text-align:center;position:sticky;top:0;z-index:10}
header h1{margin:0;font-size:22px}header p{margin:2px;color:#ffd700;font-size:13px}
.wrap{display:flex;flex-wrap:wrap;gap:12px;padding:12px;max-width:1100px;margin:auto}
.products{flex:2;min-width:280px;display:grid;grid-template-columns:repeat(auto-fill,minmax(140px,1fr));gap:10px}
.card{background:#fff;border-radius:12px;padding:10px;text-align:center;box-shadow:0 2px 6px #0001}
.card .em{font-size:36px}.card b{display:block;margin:6px 0 2px}.card span{color:#666;font-size:13px}
.card button{width:100%;margin-top:8px;padding:9px;border:0;border-radius:8px;background:#111;color:#fff;font-weight:bold}
.cart{flex:1;min-width:280px;background:#fff;border-radius:12px;padding:14px;height:fit-content;position:sticky;top:70px}
.item{display:flex;justify-content:space-between;align-items:center;padding:7px 0;border-bottom:1px solid #eee;font-size:14px}
.item button{border:0;background:#ffe0e0;border-radius:6px;padding:4px 8px}
.total{font-size:20px;font-weight:bold;display:flex;justify-content:space-between;margin:12px 0}
input,select{width:100%;padding:10px;border:1px solid #ddd;border-radius:8px;margin:5px 0}
.pay{width:100%;padding:13px;border:0;border-radius:10px;background:#25D366;color:#fff;font-weight:bold;font-size:16px}
.clear{width:100%;padding:10px;border:0;background:#eee;border-radius:10px;margin-top:8px}
.badge{display:inline-block;background:#ffd700;padding:3px 8px;border-radius:20px;font-size:12px;color:#000}
.login{max-width:380px;margin:40px auto;background:#fff;padding:20px;border-radius:14px;text-align:center}
.login h2{margin:0}.roleBtn{width:100%;padding:12px;margin:6px 0;border:2px solid #111;border-radius:10px;background:#fff;font-weight:bold}
.roleBtn.active{background:#111;color:#fff}.hidden{display:none}
table{width:100%;border-collapse:collapse;font-size:13px}td,th{border-bottom:1px solid #eee;padding:6px;text-align:left}
@media(max-width:700px){.cart{position:static}}
</style></head>
<body>
<div id="loginScreen" class="login">
<h2>✨ MAINA ACCESSORIES ✨</h2><p>Mombasa • Chagua kazi yako</p>
<button class="roleBtn" onclick="setRole('Owner',this)">👑 OWNER</button>
<button class="roleBtn" onclick="setRole('Cashier',this)">💰 CASHIER</button>
<button class="roleBtn" onclick="setRole('Storekeeper',this)">📦 STOREKEEPER</button>
<input id="pin" type="password" inputmode="numeric" placeholder="Weka PIN - Owner 1234 / Cashier 1111 / Store 2222">
<button class="pay" onclick="doLogin()">INGIA →</button>
<p style="font-size:12px;color:#888">Owner anaona kila kitu • Cashier anauza tu • Storekeeper anasimamia stoo tu</p>
</div>
<div id="main" class="hidden">
<header><h1>✨ MAINA ACCESSORIES ✨</h1><p>Mombasa • <span id="roleShow"></span> • <span id="who"></span> • <a href="#" onclick="logout()" style="color:#ffd700">Toka / Logout</a></p><span class="badge" id="todaySales">Mauzo Leo: KSh 0</span></header>

<div id="ownerPanel" class="wrap hidden"><div style="background:#fff;padding:14px;border-radius:12px;width:100%">
<h3>👑 Owner Dashboard - Ripoti Kamili</h3>
<div id="ownerStats"></div>
<h4>Ongeza Bidhaa Mpya</h4>
<input id="newName" placeholder="Jina la bidhaa - mf. Choker"><input id="newPrice" type="number" placeholder="Bei - mf. 450"><input id="newEmoji" placeholder="Emoji - mf. 💎"><input id="newStock" type="number" placeholder="Stock - mf. 20">
<button class="pay" onclick="addProduct()">+ ONGEZA BIDHAA</button>
<h4>Badilisha Bei / Stock</h4><div id="ownerEdit"></div>
<hr><h4>📊 Historia Kamili ya Mauzo</h4><div id="ownerHistory"></div>
<button class="clear" onclick="clearHistory()">Futa Historia Yote (Owner tu)</button>
</div></div>

<div id="storePanel" class="wrap hidden"><div style="background:#fff;padding:14px;border-radius:12px;width:100%">
<h3>📦 Storekeeper - Stoo</h3><p>Unaweza kuongeza stock na bidhaa. Huoni pesa za mauzo.</p>
<input id="sName" placeholder="Jina la bidhaa"><input id="sPrice" type="number" placeholder="Bei"><input id="sStock" type="number" placeholder="Stock">
<button class="pay" onclick="addProductStore()">+ ONGEZA BIDHAA STOO</button>
<table><tr><th>Bidhaa</th><th>Bei</th><th>Stock</th><th>+Stock</th></tr><tbody id="storeTable"></tbody></table>
</div></div>

<div id="posArea" class="wrap">
<div class="products" id="products"></div>
<div class="cart">
<h3>🛒 Kikapu <span id="count">0</span></h3>
<div id="cartList"></div>
<div class="total"><span>Jumla:</span><span id="total">KSh 0</span></div>
<input id="cust" placeholder="Jina la mteja (hiari)">
<select id="payMethod"><option>M-Pesa</option><option>Cash</option><option>Card</option></select>
<input id="paid" type="number" placeholder="Pesa aliyolipa">
<button class="pay" onclick="checkout()">✅ LIPIA • RISITI</button>
<button class="clear" onclick="clearCart()">Futa Kikapu</button>
<div id="cashierHistory"></div>
</div></div>
</div>
<script>
let products=JSON.parse(localStorage.getItem('maina_products')||'null')||[
{name:"Sunglasses",price:800,em:"🕶️",stock:20},{name:"Wrist Watch",price:1500,em:"⌚",stock:15},
{name:"Necklace",price:600,em:"📿",stock:30},{name:"Earrings",price:350,em:"💎",stock:40},
{name:"Handbag",price:1800,em:"👜",stock:10},{name:"Cap",price:700,em:"🧢",stock:25},
{name:"Belt",price:500,em:"➰",stock:20},{name:"Bracelet",price:400,em:"🔗",stock:30},
{name:"Ring",price:300,em:"💍",stock:50},{name:"Wallet",price:900,em:"👛",stock:15},
{name:"Hair Clip",price:250,em:"🎀",stock:40},{name:"Scarf",price:650,em:"🧣",stock:18},
{name:"Perfume",price:1200,em:"🧴",stock:12},{name:"Phone Cover",price:550,em:"📱",stock:25},
{name:"AirPods / Earbuds",price:2200,em:"🎧",stock:8},{name:"Hair Band",price:200,em:"👑",stock:30},
];
let cart={},sales=JSON.parse(localStorage.getItem('maina_sales')||'[]'),role='',pins={Owner:'1234',Cashier:'1111',Storekeeper:'2222'};
function saveP(){localStorage.setItem('maina_products',JSON.stringify(products))}
function setRole(r,btn){role=r;document.querySelectorAll('.roleBtn').forEach(b=>b.classList.remove('active'));btn.classList.add('active')}
function doLogin(){if(!role)return alert('Chagua kazi kwanza - Owner / Cashier / Storekeeper');if(document.getElementById('pin').value!==pins[role])return alert('PIN sio sahihi!');document.getElementById('loginScreen').classList.add('hidden');document.getElementById('main').classList.remove('hidden');document.getElementById('roleShow').textContent=role;document.getElementById('who').textContent='Karibu '+role;if(role==='Owner'){document.getElementById('ownerPanel').classList.remove('hidden')}if(role==='Storekeeper'){document.getElementById('storePanel').classList.remove('hidden');document.getElementById('posArea').classList.add('hidden')}render()}
function logout(){location.reload()}
function render(){
document.getElementById('products').innerHTML=products.map((p,i)=>`<div class="card"><div class="em">${p.em}</div><b>${p.name}</b><span>KSh ${p.price} • Stock: ${p.stock}</span><button onclick="add(${i})">+ Ongeza</button></div>`).join('');
let html='',total=0,count=0;for(let i in cart){let q=cart[i],p=products[i],sub=p.price*q;total+=q;count+=q;html+=`<div class="item"><span>${p.em} ${p.name} x ${q}</span><span>KSh ${sub} <button onclick="remove(${i})">✕</button></span></div>`}
document.getElementById('cartList').innerHTML=html||'Hakuna bidhaa bado.';document.getElementById('total').textContent='KSh '+total;document.getElementById('count').textContent=count;
let today=new Date().toDateString(),t=sales.filter(s=>new Date(s.time).toDateString()===today).reduce((a,s)=>a+s.total,0);
document.getElementById('todaySales').textContent='Mauzo Leo: KSh '+t;
if(role==='Owner'){document.getElementById('ownerStats').innerHTML=`<b>Mauzo Leo:</b> KSh ${t} | <b>Jumla ya mauzo yote:</b> KSh ${sales.reduce((a,s)=>a+s.total,0)} | <b>Mauzo yaliyofanyika:</b> ${sales.length}`;document.getElementById('ownerEdit').innerHTML=products.map((p,i)=>`<div style="display:flex;gap:5px;margin:4px 0"><span style="flex:1">${p.em} ${p.name}</span><input style="width:80px" value="${p.price}" onchange="editPrice(${i},this.value)"><input style="width:70px" value="${p.stock}" onchange="editStock(${i},this.value)"></div>`).join('');document.getElementById('ownerHistory').innerHTML=sales.slice(-20).reverse().map(s=>`• ${new Date(s.time).toLocaleString()} - KSh ${s.total} (${s.method}) na ${s.seller||''} - ${s.cust||''}`).join('<br>')||'Hakuna mauzo';}
if(role==='Storekeeper'){document.getElementById('storeTable').innerHTML=products.map((p,i)=>`<tr><td>${p.em} ${p.name}</td><td>${p.price}</td><td>${p.stock}</td><td><button onclick="addStock(${i})">+10</button></td></tr>`).join('')}
if(role==='Cashier'){document.getElementById('cashierHistory').innerHTML='<hr><b>Mauzo yako leo:</b><br>'+sales.filter(s=>s.seller==='Cashier'&&new Date(s.time).toDateString()===today).map(s=>`• KSh ${s.total} - ${new Date(s.time).toLocaleTimeString()}`).join('<br>')}
}
function add(i){if(products[i].stock<=0)return alert('Stock imeisha!');cart[i]=(cart[i]||0)+1;render()}
function remove(i){delete cart[i];render()}function clearCart(){cart={};render()}
function clearHistory(){if(confirm('Futa historia yote?')){sales=[];localStorage.removeItem('maina_sales');render()}}
function editPrice(i,v){products[i].price=parseInt(v)||0;saveP();render()}
function editStock(i,v){products[i].stock=parseInt(v)||0;saveP();render()}
function addStock(i){products[i].stock+=10;saveP();render()}
function addProduct(){let n=document.getElementById('newName').value,p=parseInt(document.getElementById('newPrice').value),e=document.getElementById('newEmoji').value||'✨',s=parseInt(document.getElementById('newStock').value)||0;if(!n||!p)return alert('Jaza jina na bei');products.push({name:n,price:p,em:e,stock:s});saveP();render();alert('Bidhaa imeongezwa!')}
function addProductStore(){let n=document.getElementById('sName').value,p=parseInt(document.getElementById('sPrice').value),s=parseInt(document.getElementById('sStock').value)||0;if(!n||!p)return alert('Jaza jina na bei');products.push({name:n,price:p,em:'📦',stock:s});saveP();render();alert('Bidhaa imeongezwa kwenye stoo!')}
function checkout(){let total=0,items=[];for(let i in cart){let p=products[i];total+=p.price*cart[i];items.push(p.name+' x '+cart[i])}if(!total)return alert('Kikapu kiko tupu!');let method=document.getElementById('payMethod').value,cust=document.getElementById('cust').value,paid=parseInt(document.getElementById('paid').value||total);for(let i in cart){products[i].stock-=cart[i]}saveP();sales.push({total,method,cust,time:Date.now(),items,seller:role});localStorage.setItem('maina_sales',JSON.stringify(sales));alert(`MAINA ACCESSORIES\n${items.join('\n')}\nJumla: KSh ${total}\nMalipo: ${method}\nAlilipa: KSh ${paid}\nChange: KSh ${Math.max(0,paid-total)}\nMuuzaji: ${role}\nAsante!`);cart={};document.getElementById('paid').value='';document.getElementById('cust').value='';render()}
render();
</script></body></html>
