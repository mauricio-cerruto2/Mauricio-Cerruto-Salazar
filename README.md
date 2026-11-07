# Mauricio-Cerruto-Salazar   C.I: 10954648


<!doctype html><html lang="es"><head><meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1"><title>Patitas de vuelta a casa | Simulador EDO</title>
<style>
body{margin:0;font-family:Arial,sans-serif;color:#1d2935;background:#f4f7fb}header{background:#174a70;color:white;padding:26px max(20px,calc((100% - 1100px)/2))}main{max-width:1100px;margin:24px auto;padding:0 18px}.card{background:white;border-radius:12px;padding:20px;margin:16px 0;box-shadow:0 2px 10px #152b4012}h2{color:#174a70}.controls,.stats{display:grid;grid-template-columns:repeat(auto-fit,minmax(170px,1fr));gap:14px}label{font-size:14px;font-weight:bold}input{display:block;width:100%;box-sizing:border-box;padding:10px;border:1px solid #bdc9d5;border-radius:7px;margin-top:6px}button{background:#174a70;color:white;border:0;border-radius:8px;padding:12px 20px;font-weight:bold;cursor:pointer;margin-top:16px}.stat{background:#eef4f9;padding:14px;border-radius:8px}.stat b{display:block;font-size:22px;margin-top:5px}canvas{width:100%;height:360px;border:1px solid #e1e7ed;border-radius:8px}.note{color:#526273;font-size:14px}.error{color:#b42318;font-weight:bold}table{border-collapse:collapse;width:100%;font-size:13px}th,td{padding:8px;border:1px solid #d8e0e8;text-align:right}th{background:#174a70;color:white}tr:nth-child(even){background:#f2f6fa}.scroll{overflow:auto}
</style></head><body><header><h1>🐾 Patitas de vuelta a casa</h1><div>Simulador EDO: solución analítica, Heun y RK4</div></header><main>
<section class="card"><h2>Problema estudiado</h2><p>Se modela la cantidad de mascotas reportadas que todavía no han regresado con sus familias. Se supone que la rapidez de reencuentro es proporcional a los casos pendientes.</p><p><b>Modelo:</b> dy/dt = −k·y | <b>Condición inicial:</b> y(0)=y₀ | <b>Solución exacta:</b> y(t)=y₀·e<sup>−kt</sup>.</p><p class="note">Parámetros hipotéticos; no es una predicción validada.</p></section>
<section class="card"><h2>Parámetros de simulación</h2><div class="controls">
<label>Mascotas iniciales, y₀<input id="y0" type="number" min="1" max="100000" step="1" value="100"></label><label>Tasa k (día⁻¹)<input id="k" type="number" min="0.0001" max="10" step="0.01" value="0.18"></label><label>Tiempo final (días)<input id="T" type="number" min="0.5" max="365" step="0.5" value="14"></label><label>Paso h (días)<input id="h" type="number" min="0.01" max="10" step="0.01" value="1"></label></div><button onclick="run()">Ejecutar simulación</button><p id="msg" class="error"></p></section>
<section class="card"><h2>Resultados principales</h2><div class="stats"><div class="stat">Mascotas pendientes (exacta)<b id="remain">—</b></div><div class="stat">Porcentaje reunido<b id="reunited">—</b></div><div class="stat">Tiempo para reunir 80 %<b id="goal">—</b></div><div class="stat">Error máximo RK4<b id="errrk">—</b></div></div><p id="interpret" class="note"></p></section>
<section class="card"><h2>Gráfica comparativa</h2><canvas id="chart" width="1000" height="420"></canvas><p class="note">Curva analítica continua y puntos de aproximación numérica.</p></section>
<section class="card"><h2>Tabla de iteraciones y errores</h2><div class="scroll"><table id="table"><thead><tr><th>n</th><th>t (días)</th><th>Exacta</th><th>Heun</th><th>RK4</th><th>Error abs. Heun</th><th>Error % Heun</th><th>Error abs. RK4</th><th>Error % RK4</th></tr></thead><tbody></tbody></table></div></section>
<section class="card"><h2>Fórmulas</h2><p><b>Heun:</b> y*=yₙ+h·f(tₙ,yₙ); yₙ₊₁=yₙ+(h/2)[f(tₙ,yₙ)+f(tₙ₊₁,y*)].</p><p><b>RK4:</b> yₙ₊₁=yₙ+(h/6)(k₁+2k₂+2k₃+k₄), con k₁=f(t,y), k₂=f(t+h/2,y+hk₁/2), k₃=f(t+h/2,y+hk₂/2), k₄=f(t+h,y+hk₃).</p></section></main>
<script>
const f=(t,y,k)=>-k*y, exact=(t,y0,k)=>y0*Math.exp(-k*t);
function run(){const y0=+y0El.value,k=+kEl.value,T=+tEl.value,h=+hEl.value,msg=document.getElementById('msg');
if(!(y0>0&&k>0&&T>0&&h>0)||![y0,k,T,h].every(Number.isFinite)){msg.textContent='Ingrese valores numéricos positivos.';return}
const N=Math.ceil(T/h);if(N>2000){msg.textContent='Use un paso mayor: máximo 2000 iteraciones.';return}msg.textContent='';
let he=[y0],rk=[y0],times=[0],yh=y0,yr=y0;
for(let i=0;i<N;i++){let dt=Math.min(h,T-i*h),t=i*h,a=f(t,yh,k),yp=yh+dt*a,b=f(t+dt,yp,k);yh+=dt*(a+b)/2;
let q1=f(t,yr,k),q2=f(t+dt/2,yr+dt*q1/2,k),q3=f(t+dt/2,yr+dt*q2/2,k),q4=f(t+dt,yr+dt*q3,k);yr+=dt*(q1+2*q2+2*q3+q4)/6;
times.push(Math.min(T,(i+1)*h));he.push(yh);rk.push(yr)}
let body=document.querySelector('#table tbody');body.innerHTML='';let maxRK=0;
times.forEach((t,i)=>{let ex=exact(t,y0,k),eh=Math.abs(he[i]-ex),er=Math.abs(rk[i]-ex);maxRK=Math.max(maxRK,er);let vals=[i,t.toFixed(3),ex.toFixed(6),he[i].toFixed(6),rk[i].toFixed(6),eh.toExponential(3),(eh/ex*100).toFixed(5)+'%',er.toExponential(3),(er/ex*100).toFixed(7)+'%'];let tr=document.createElement('tr');vals.forEach(v=>{let td=document.createElement('td');td.textContent=v;tr.appendChild(td)});body.appendChild(tr)});
let end=exact(T,y0,k),pct=(1-end/y0)*100,goal=Math.log(5)/k;
document.getElementById('remain').textContent=end.toFixed(4)+' mascotas';document.getElementById('reunited').textContent=pct.toFixed(2)+' %';document.getElementById('goal').textContent=goal<=T?goal.toFixed(2)+' días':'Más de '+T+' días';document.getElementById('errrk').textContent=maxRK.toExponential(3);document.getElementById('interpret').textContent=`Al día ${T}, el modelo estima ${pct.toFixed(2)} % de reencuentros. ${pct>=80?'Se alcanza':'No se alcanza'} la meta del 80 % dentro del intervalo. Parámetros supuestos; contrastar con datos reales.`;draw(y0,k,T,times,he,rk)}
const y0El=document.getElementById('y0'),kEl=document.getElementById('k'),tEl=document.getElementById('T'),hEl=document.getElementById('h');
function draw(y0,k,T,times,he,rk){let c=document.getElementById('chart'),ctx=c.getContext('2d'),W=c.width,H=c.height,L=70,R=25,TOP=25,B=55,ymax=y0*1.08;ctx.clearRect(0,0,W,H);let X=t=>L+(W-L-R)*t/T,Y=v=>TOP+(H-TOP-B)*(1-v/ymax);ctx.strokeStyle='#dce3ea';ctx.fillStyle='#344454';ctx.font='13px Arial';
for(let j=0;j<=5;j++){let v=ymax*j/5,yy=Y(v);ctx.beginPath();ctx.moveTo(L,yy);ctx.lineTo(W-R,yy);ctx.stroke();ctx.fillText(v.toFixed(0),20,yy+4)}
for(let j=0;j<=7;j++){let t=T*j/7,xx=X(t);ctx.beginPath();ctx.moveTo(xx,TOP);ctx.lineTo(xx,H-B);ctx.stroke();ctx.fillText(t.toFixed(1),xx-10,H-25)}
ctx.fillText('Mascotas pendientes',L,15);ctx.fillText('Tiempo (días)',W/2,H-5);
ctx.beginPath();for(let i=0;i<=250;i++){let t=T*i/250,x=X(t),y=Y(exact(t,y0,k));if(!i)ctx.moveTo(x,y);else ctx.lineTo(x,y)}ctx.strokeStyle='#174a70';ctx.lineWidth=3;ctx.stroke();
[[he,'#d97706'],[rk,'#16835d']].forEach(([arr,col])=>{ctx.fillStyle=col;arr.forEach((v,i)=>{ctx.beginPath();ctx.arc(X(times[i]),Y(v),4,0,Math.PI*2);ctx.fill()})});
ctx.font='14px Arial';ctx.fillStyle='#174a70';ctx.fillText('— Analítica',L+100,18);ctx.fillStyle='#d97706';ctx.fillText('● Heun',L+220,18);ctx.fillStyle='#16835d';ctx.fillText('● RK4',L+300,18)}
run();
</script></body></html>
