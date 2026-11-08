<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Страж</title>
<style>
body{font-family:-apple-system,Arial,sans-serif;background:#f2f5f9;margin:0;color:#1e293b}
h1{text-align:center;margin:16px 0 4px;color:#2563eb}
.sub{text-align:center;color:#64748b;font-size:13px;margin-bottom:12px}
.wrap{max-width:640px;margin:0 auto;padding:0 12px 40px}
.counts{display:flex;gap:8px;flex-wrap:wrap;margin-bottom:12px}
.cnt{flex:1;min-width:70px;background:#fff;border-radius:10px;padding:8px;text-align:center;box-shadow:0 1px 2px rgba(0,0,0,.06)}
.cnt b{display:block;font-size:20px}
.lbl{font-size:11px;color:#64748b}
.red{color:#dc2626}.yellow{color:#d97706}.blue{color:#2563eb}.green{color:#16a34a}
.card{background:#fff;border-radius:12px;padding:12px;box-shadow:0 1px 3px rgba(0,0,0,.06);margin-bottom:10px}
input{width:100%;padding:10px;border:1px solid #e2e8f0;border-radius:8px;margin:4px 0 8px;box-sizing:border-box}
.btn{background:#2563eb;color:#fff;border:none;padding:10px;border-radius:8px;width:100%;font-size:15px;cursor:pointer}
.ghost{background:none;border:none;color:#64748b;cursor:pointer;font-size:14px}
.task{display:flex;align-items:center;gap:10px;padding:10px 0;border-bottom:1px solid #eef2f7}
.dot{width:12px;height:12px;border-radius:50%;flex-shrink:0}
.tag{margin-left:auto;font-size:11px;padding:3px 8px;border-radius:6px;white-space:nowrap}
.ok{color:#16a34a;cursor:pointer}.del{color:#dc2626;cursor:pointer}
h3{margin:8px 0 8px}
</style>
</head>
<body>
<h1>Страж</h1>
<div class="sub">Отдел ПЭО · задачи и дедлайны</div>
<div class="wrap">
  <div class="counts">
    <div class="cnt"><b id="cAll">0</b><span class="lbl">всего</span></div>
    <div class="cnt"><b class="blue" id="cWork">0</b><span class="lbl">в работе</span></div>
    <div class="cnt"><b class="yellow" id="cSoon">0</b><span class="lbl">скоро дедлайн</span></div>
    <div class="cnt"><b class="red" id="cLate">0</b><span class="lbl">просрочено</span></div>
    <div class="cnt"><b class="green" id="cDone">0</b><span class="lbl">закрыто</span></div>
  </div>
  <div class="card">
    <h3>Новая задача</h3>
    <input id="tTitle" placeholder="Что сделать?">
    <input id="tWho" placeholder="Исполнитель (экономист)">
    <input id="tDate" type="date">
    <button class="btn" onclick="addTask()">Добавить задачу</button>
  </div>
  <div class="card" id="list"></div>
</div>
<script>
function load(){try{return JSON.parse(localStorage.getItem('strazh_tasks')||'[]')}catch(e){return[]}}
function save(t){localStorage.setItem('strazh_tasks',JSON.stringify(t))}
function statusOf(t){
  if(t.done)return['green','#16a34a','готово'];
  const d=new Date(t.date),now=new Date();now.setHours(0,0,0,0);d.setHours(0,0,0,0);
  const days=Math.round((d-now)/86400000);
  if(days<0)return['red','#dc2626','просрочено '+(−days)+' дн.'];
  if(days<=3)return['yellow','#d97706','скоро дедлайн'];
  return['blue','#2563eb','в работе'];
}
function addTask(){
  const title=document.getElementById('tTitle').value.trim();
  const who=document.getElementById('tWho').value.trim();
  const date=document.getElementById('tDate').value;
  if(!title||!who||!date){alert('Заполните все три поля');return}
  const t=load();t.unshift({title,who,date,done:false});save(t);
  document.getElementById('tTitle').value='';document.getElementById('tWho').value='';
  render();
}
function toggle(i){const t=load();t[i].done=!t[i].done;save(t);render()}
function del(i){const t=load();t.splice(i,1);save(t);render()}
function render(){
  const t=load(),list=document.getElementById('list');
  let cw=0,cs=0,cl=0,cd=0;
  t.forEach(x=>{const s=statusOf(x);if(s[0]==='blue')cw++;if(s[0]==='yellow')cs++;if(s[0]==='red')cl++;if(s[0]==='green')cd++});
  document.getElementById('cAll').textContent=t.length;
  document.getElementById('cWork').textContent=cw;
  document.getElementById('cSoon').textContent=cs;
  document.getElementById('cLate').textContent=cl;
  document.getElementById('cDone').textContent=cd;
  list.innerHTML=t.length?'<h3>Задачи ('+t.length+')</h3>':'<h3>Пока нет задач — добавьте первую выше</h3>';
  t.forEach((x,i)=>{
    const s=statusOf(x);
    const div=document.createElement('div');div.className='task';
    div.innerHTML='<span class="dot" style="background:'+s[1]+'"></span><span><b>'+x.title+'</b><br><small>'+x.who+' · до '+x.date+'</small></span><span class="tag" style="background:'+s[1]+'22;color:'+s[1]+'">'+s[2]+'</span><span class="ok" onclick="toggle('+i+')">✓</span><span class="del" onclick="del('+i+')">✕</span>';
    list.appendChild(div);
  });
}
render();
</script>
</body>
</html>
