# brawl-wheel
<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Wheel of Brawl Stars</title>
<style>
body{
    margin:0;
    background:#0f172a;
    color:white;
    font-family:sans-serif;
    display:flex;
    flex-direction:column;
    align-items:center;
    justify-content:center;
    height:100vh;
}
canvas{
    display:block;
}
button{
    margin-top:20px;
    padding:14px 28px;
    font-size:18px;
    border:none;
    border-radius:12px;
    background:#facc15;
    color:#000;
}
.center{
    position:absolute;
    text-align:center;
    pointer-events:none;
}
</style>
</head>
<body>

<canvas id="wheel"></canvas>
<div class="center">
    <div style="font-size:28px;">🏆</div>
    <div id="name" style="font-size:18px;margin-top:6px;">—</div>
</div>
<button id="spin">Крутить</button>

<script>
const canvas = document.getElementById("wheel");
const ctx = canvas.getContext("2d");
const DPR = window.devicePixelRatio || 1;

function resize(){
    const size = Math.min(window.innerWidth, window.innerHeight)*0.9;
    canvas.width = size * DPR;
    canvas.height = size * DPR;
    canvas.style.width = size+"px";
    canvas.style.height = size+"px";
    ctx.setTransform(DPR,0,0,DPR,0,0);
}
window.addEventListener("resize", resize);
resize();

const center = () => canvas.width/(2*DPR);

const colors = {
    "common":"#9ca3af",
    "rare":"#3b82f6",
    "super rare":"#22c55e",
    "epic":"#a855f7",
    "mythic":"#ef4444",
    "legendary":"#facc15"
};

const brawlers = [
["Шелли","common"],

["Кольт","rare"],["Булл","rare"],["Брок","rare"],["Барли","rare"],["Нита","rare"],["Эль Примо","rare"],["Поко","rare"],["Роза","rare"],

["Рико","super rare"],["Джесси","super rare"],["Динамайк","super rare"],["Дэррил","super rare"],["Пенни","super rare"],["Тик","super rare"],["Карл","super rare"],["8-Бит","super rare"],["Джеки","super rare"],["Гас","super rare"],

["Бо","epic"],["Пайпер","epic"],["Пэм","epic"],["Фрэнк","epic"],["Биби","epic"],["Беа","epic"],["Эмз","epic"],["Гейл","epic"],["Нани","epic"],["Колетт","epic"],["Эдгар","epic"],["Сту","epic"],["Белль","epic"],["Гром","epic"],["Грифф","epic"],["Эш","epic"],["Лола","epic"],["Бонни","epic"],["Сэм","epic"],["Мэнди","epic"],["Мэйси","epic"],["Хэнк","epic"],["Перл","epic"],["Ларри и Лори","epic"],["Анджело","epic"],["Берри","epic"],["Шейд","epic"],["Мипл","epic"],["Транк","epic"],

["Мортис","mythic"],["Тара","mythic"],["Джин","mythic"],["Макс","mythic"],["Мистер П","mythic"],["Спраут","mythic"],["Байрон","mythic"],["Скуик","mythic"],["Грей","mythic"],["Р-Т","mythic"],["Луми","mythic"],["Джэ-Ён","mythic"],["Алли","mythic"],["Мина","mythic"],["Фэнг","mythic"],["Базз","mythic"],["Гавс","mythic"],["Ева","mythic"],["Джанет","mythic"],["Отис","mythic"],["Виллоу","mythic"],["Чак","mythic"],["Мико","mythic"],["Мелоди","mythic"],["Лили","mythic"],["Джуджу","mythic"],["Мо","mythic"],["Финкс","mythic"],["Олли","mythic"],["Зигги","mythic"],["Джиджи","mythic"],["Клэнси","mythic"],["Чарли","mythic"],["Бастер","mythic"],["Лу","mythic"],["Даг","mythic"],["Глоуи","mythic"],["Наджия","mythic"],

["Спайк","legendary"],["Ворон","legendary"],["Леон","legendary"],["Сэнди","legendary"],["Амбер","legendary"],["Мэг","legendary"],["Честер","legendary"],["Корделиус","legendary"],["Кит","legendary"],["Драко","legendary"],["Кэндзи","legendary"],["Пирс","legendary"],["Вольт","legendary"],

["Сириус","ultra"],["Кадзэ","ultra"]
];

const total = brawlers.length;
const anglePer = Math.PI*2 / total;

let rotation = 0;
let spinning = false;
let startTime = 0;
let duration = 6000;
let startRot = 0;
let targetRot = 0;

function easeOutCubic(t){ return 1 - Math.pow(1 - t, 3); }
function easeIn(t){ return t*t; }

function draw(){
    const c = center();
    const r = c - 10;

    ctx.clearRect(0,0,canvas.width,canvas.height);

    for(let i=0;i<total;i++){
        const a1 = rotation + i*anglePer;
        const a2 = a1 + anglePer;

        ctx.beginPath();
        ctx.moveTo(c,c);
        ctx.arc(c,c,r,a1,a2);
        ctx.closePath();

        let rarity = brawlers[i][1];
        if(rarity==="ultra"){
            let grad = ctx.createLinearGradient(0,0,canvas.width,canvas.height);
            grad.addColorStop(0,"red");
            grad.addColorStop(0.5,"yellow");
            grad.addColorStop(1,"cyan");
            ctx.fillStyle = grad;
        } else {
            ctx.fillStyle = colors[rarity];
        }
        ctx.fill();

        ctx.save();
        ctx.translate(c,c);
        ctx.rotate(a1 + anglePer/2);
        ctx.fillStyle = "#fff";
        ctx.font = "10px sans-serif";
        ctx.fillText(brawlers[i][0], r*0.65, 3);
        ctx.restore();
    }

    // arrow
    ctx.fillStyle="#fff";
    ctx.beginPath();
    ctx.moveTo(c,10);
    ctx.lineTo(c-10,40);
    ctx.lineTo(c+10,40);
    ctx.fill();

    // center circle
    ctx.beginPath();
    ctx.arc(c,c,40,0,Math.PI*2);
    ctx.fillStyle="#111";
    ctx.fill();

    updateName();
}

function updateName(){
    let pointerAngle = (Math.PI*1.5 - rotation) % (Math.PI*2);
    if(pointerAngle<0) pointerAngle+=Math.PI*2;
    let index = Math.floor(pointerAngle / anglePer);
    document.getElementById("name").textContent = brawlers[index][0];
}

function animate(t){
    if(!spinning) return;

    let elapsed = t - startTime;
    let progress = Math.min(elapsed/duration,1);

    let eased = easeOutCubic(progress);
    rotation = startRot + (targetRot - startRot) * eased;

    draw();

    if(progress<1){
        requestAnimationFrame(animate);
    } else {
        spinning=false;
        if(navigator.vibrate) navigator.vibrate(100);
    }
}

document.getElementById("spin").onclick = ()=>{
    if(spinning) return;

    spinning=true;
    startTime = performance.now();
    startRot = rotation;

    let extra = Math.random()*Math.PI*2;
    targetRot = rotation + Math.PI*2*(5 + Math.random()*2) + extra;

    requestAnimationFrame(animate);
};

draw();
</script>
</body>
</html>
