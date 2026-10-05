# prova-experimental
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Prova de Ecologia - 6º Ano</title>
<style>
:root{--bg:#f1f8f1;--card:#fff;--txt:#1f2d1f;--main:#2e7d32;--ok:#2e7d32;--bad:#c62828;--line:#cfe3cf}
@media (prefers-color-scheme:dark){:root{--bg:#142014;--card:#1d2e1d;--txt:#e6f2e6;--main:#66bb6a;--ok:#81c784;--bad:#ef9a9a;--line:#355035}}
*{box-sizing:border-box}
body{margin:0;font-family:system-ui,Arial,sans-serif;background:var(--bg);color:var(--txt);line-height:1.5}
main{max-width:720px;margin:0 auto;padding:20px}
h1{color:var(--main);text-align:center;margin-bottom:4px}
.sub{text-align:center;margin-top:0;opacity:.8}
.card{background:var(--card);border:1px solid var(--line);border-radius:12px;padding:16px;margin:16px 0}
.card h3{margin-top:0}
label.op{display:block;padding:8px 10px;margin:6px 0;border:1px solid var(--line);border-radius:8px;cursor:pointer}
label.op:hover{border-color:var(--main)}
label.op.certa{background:rgba(46,125,50,.2);border-color:var(--ok)}
label.op.errada{background:rgba(198,40,40,.18);border-color:var(--bad)}
input[type=text]{width:100%;padding:10px;border:1px solid var(--line);border-radius:8px;font-size:1rem;background:var(--card);color:var(--txt)}
button{background:var(--main);color:#fff;border:0;border-radius:8px;padding:12px 24px;font-size:1rem;cursor:pointer}
button:disabled{opacity:.5;cursor:default}
.exp{margin-top:10px;font-size:.95rem;padding:8px 10px;border-left:4px solid var(--main)}
#resultado{text-align:center;font-size:1.2rem}
#resultado strong{font-size:2rem;color:var(--main)}
.centro{text-align:center}
</style>
</head>
<body>
<main>
<h1>🌳 Prova de Ecologia</h1>
<p class="sub">6º Ano - Ensino Fundamental</p>

<div class="card">
  <label for="nome"><strong>Nome do aluno:</strong></label>
  <input type="text" id="nome" placeholder="Digite seu nome completo">
</div>

<div id="prova"></div>

<div class="centro"><button id="enviar">Enviar prova</button></div>
<div id="resultado" class="card" hidden></div>
</main>

<script>
// ===== EDITE AS QUESTÕES AQUI =====
// resposta = número da alternativa correta (começa em 0)
const questoes = [
  {p:"O que é um ecossistema?",
   o:["Apenas os animais de uma floresta","O conjunto dos seres vivos e do ambiente onde vivem, e as relações entre eles","Somente as plantas de uma região","Um tipo de lixo reciclável"],
   resposta:1,
   exp:"Um ecossistema reúne os seres vivos (bióticos) e os elementos não vivos (abióticos), como água, solo, luz e ar."},
  {p:"Qual destes é um fator ABIÓTICO?",
   o:["Cobra","Capim","Luz do sol","Fungo"],
   resposta:2,
   exp:"Fatores abióticos são os componentes não vivos do ambiente, como luz, água, temperatura e solo."},
  {p:"Na cadeia alimentar, os seres que produzem o próprio alimento por fotossíntese são chamados de:",
   o:["Consumidores","Decompositores","Produtores","Predadores"],
   resposta:2,
   exp:"As plantas e as algas são produtoras: usam luz solar para fabricar seu alimento."},
  {p:"Qual é a função dos decompositores, como fungos e bactérias?",
   o:["Produzir oxigênio","Caçar outros animais","Decompor restos de seres vivos e devolver nutrientes ao solo","Fazer fotossíntese"],
   resposta:2,
   exp:"Os decompositores degradam a matéria orgânica e reciclam os nutrientes no ambiente."},
  {p:"Na cadeia alimentar: capim → gafanhoto → sapo → cobra, quem é o consumidor primário?",
   o:["Capim","Gafanhoto","Sapo","Cobra"],
   resposta:1,
   exp:"O consumidor primário se alimenta diretamente do produtor. O gafanhoto come o capim."},
  {p:"O lugar onde uma espécie vive e encontra o que precisa para sobreviver chama-se:",
   o:["Habitat","População","Comunidade","Predador"],
   resposta:0,
   exp:"Habitat é o ambiente onde uma espécie vive, como o rio para o peixe ou a mata para a onça."},
  {p:"Qual atitude ajuda a preservar o meio ambiente?",
   o:["Jogar lixo nos rios","Queimar plástico","Separar o lixo para a reciclagem","Desmatar para fazer pasto"],
   resposta:2,
   exp:"A reciclagem reduz o lixo, economiza recursos naturais e diminui a poluição."},
  {p:"O que significa biodiversidade?",
   o:["A quantidade de água de um rio","A variedade de seres vivos em um ambiente","O número de pessoas em uma cidade","A temperatura média de uma região"],
   resposta:1,
   exp:"Biodiversidade é a variedade de espécies de plantas, animais e outros seres vivos em um local."}
];
// ==================================

const div = document.getElementById('prova');
questoes.forEach((q,i)=>{
  const c = document.createElement('div');
  c.className='card';
  c.innerHTML = `<h3>${i+1}. ${q.p}</h3>` +
    q.o.map((t,j)=>`<label class="op"><input type="radio" name="q${i}" value="${j}"> ${t}</label>`).join('') +
    `<div class="exp" id="exp${i}" hidden></div>`;
  div.appendChild(c);
});

document.getElementById('enviar').onclick = function(){
  const nome = document.getElementById('nome').value.trim();
  if(!nome){alert('Digite seu nome antes de enviar.');return;}
  const faltam = questoes.filter((q,i)=>!document.querySelector(`input[name=q${i}]:checked`)).length;
  if(faltam && !confirm(`Você deixou ${faltam} questão(ões) em branco. Enviar mesmo assim?`)) return;

  let acertos = 0;
  questoes.forEach((q,i)=>{
    const marcada = document.querySelector(`input[name=q${i}]:checked`);
    const labels = document.querySelectorAll(`input[name=q${i}]`);
    labels.forEach(inp=>{
      inp.disabled = true;
      const lb = inp.parentElement;
      if(+inp.value === q.resposta) lb.classList.add('certa');
      else if(inp.checked) lb.classList.add('errada');
    });
    if(marcada && +marcada.value === q.resposta) acertos++;
    const e = document.getElementById('exp'+i);
    e.hidden = false;
    e.textContent = '💡 ' + q.exp;
  });

  const nota = (acertos / questoes.length * 10).toFixed(1).replace('.',',');
  const r = document.getElementById('resultado');
  r.hidden = false;
  r.innerHTML = `${nome}, você acertou <strong>${acertos}</strong> de ${questoes.length} questões.<br>Nota: <strong>${nota}</strong><br><small>Tire um print desta tela e envie ao professor.</small>`;
  this.disabled = true;
  r.scrollIntoView({behavior:'smooth'});
};
</script>
</body>
</html>
