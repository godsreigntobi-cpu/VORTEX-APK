# VORTEX-APK
<!DOCTYPE html>
<html>
<head>
<title>VORTEX</title>

<style>
body{
margin:0;
background:black;
color:white;
font-family:Arial;
text-align:center;
overflow:hidden;
}

canvas{
background:#0a0a0a;
border:2px solid white;
display:block;
margin:auto;
}

#mobile{
position:absolute;
bottom:10px;
left:50%;
transform:translateX(-50%);
}

button{
padding:12px;
margin:4px;
font-size:14px;
}
</style>
</head>

<body>

<h1>VORTEX</h1>
<canvas id="game" width="900" height="500"></canvas>

<div id="mobile">
<button onclick="attack1('punch')">Punch</button>
<button onclick="attack1('kick')">Kick</button>
<button onclick="attack1('vortex')">Vortex</button>
<button onclick="dash1()">Dash</button>
</div>

<script>

const canvas = document.getElementById("game")
const ctx = canvas.getContext("2d")

let keys={}
let particles=[]
let level=1
let enemyCount=1

document.addEventListener("keydown",e=>keys[e.key.toLowerCase()]=true)
document.addEventListener("keyup",e=>keys[e.key.toLowerCase()]=false)

class Fighter{

constructor(x,color){

this.x=x
this.y=350
this.velY=0
this.health=100
this.energy=0
this.combo=0
this.block=false
this.color=color

}

draw(){

ctx.strokeStyle=this.color
ctx.lineWidth=4

ctx.beginPath()
ctx.arc(this.x,this.y-60,10,0,Math.PI*2)
ctx.stroke()

ctx.beginPath()
ctx.moveTo(this.x,this.y-50)
ctx.lineTo(this.x,this.y)
ctx.stroke()

ctx.beginPath()
ctx.moveTo(this.x,this.y-40)
ctx.lineTo(this.x-20,this.y-10)
ctx.stroke()

ctx.beginPath()
ctx.moveTo(this.x,this.y-40)
ctx.lineTo(this.x+20,this.y-10)
ctx.stroke()

ctx.beginPath()
ctx.moveTo(this.x,this.y)
ctx.lineTo(this.x-15,this.y+30)
ctx.stroke()

ctx.beginPath()
ctx.moveTo(this.x,this.y)
ctx.lineTo(this.x+15,this.y+30)
ctx.stroke()

}

update(){

this.y+=this.velY

if(this.y<350){
this.velY+=0.5
}else{
this.y=350
this.velY=0
}

}

}

class Particle{

constructor(x,y){
this.x=x
this.y=y
this.vx=(Math.random()-0.5)*8
this.vy=(Math.random()-0.5)*8
this.life=40
}

draw(){
ctx.fillStyle="red"
ctx.fillRect(this.x,this.y,3,3)
}

update(){
this.x+=this.vx
this.y+=this.vy
this.life--
}

}

let p1 = new Fighter(200,"white")
let p2 = new Fighter(700,"red")

function blood(x,y){

for(let i=0;i<12;i++){
particles.push(new Particle(x,y))
}

}

function damage(attacker,target,amount){

if(target.block){
amount*=0.3
}

target.health-=amount
target.x+=Math.sign(target.x-attacker.x)*25
blood(target.x,target.y)

attacker.energy+=5
attacker.combo++

}

function punch(a,t){

if(Math.abs(a.x-t.x)<60){
damage(a,t,6)
}

}

function kick(a,t){

if(Math.abs(a.x-t.x)<70){
damage(a,t,10)
}

}

function vortex(a,t){

if(a.energy<40) return

if(Math.abs(a.x-t.x)<100){

for(let i=0;i<3;i++){
damage(a,t,8)
}

a.energy=0

}

}

function teleport(a,t){

if(a.energy<30) return

a.x=t.x-50
a.energy-=30

}

function dash1(){

p1.x+=80

}

function attack1(type){

if(type=="punch") punch(p1,p2)
if(type=="kick") kick(p1,p2)
if(type=="vortex") vortex(p1,p2)

}

function enemyAI(){

if(p2.x>p1.x) p2.x-=1.4
if(p2.x<p1.x) p2.x+=1.4

if(Math.random()<0.02){

if(Math.random()<0.5){
punch(p2,p1)
}else{
kick(p2,p1)
}

}

if(Math.random()<0.01){
p2.block=true
setTimeout(()=>p2.block=false,500)
}

}

function updateParticles(){

particles.forEach((p,i)=>{

p.update()
p.draw()

if(p.life<=0){
particles.splice(i,1)
}

})

}

function drawBars(){

ctx.fillStyle="green"
ctx.fillRect(20,20,p1.health*2,15)

ctx.fillStyle="cyan"
ctx.fillRect(20,40,p1.energy*2,10)

ctx.fillStyle="red"
ctx.fillRect(680,20,p2.health*2,15)

}

function nextLevel(){

level++

p2 = new Fighter(700,"red")
p2.health = 120 + level*20

}

function gameLoop(){

ctx.clearRect(0,0,900,500)

p1.update()
p2.update()

p1.block = keys["shift"]
p2.block = keys["0"]

if(keys["a"]) p1.x-=4
if(keys["d"]) p1.x+=4
if(keys["w"] && p1.y==350) p1.velY=-10

if(keys["j"]) punch(p1,p2)
if(keys["k"]) kick(p1,p2)
if(keys["l"]) vortex(p1,p2)

if(keys["arrowleft"]) p2.x-=4
if(keys["arrowright"]) p2.x+=4
if(keys["arrowup"] && p2.y==350) p2.velY=-10

if(keys["1"]) punch(p2,p1)
if(keys["2"]) kick(p2,p1)
if(keys["3"]) vortex(p2,p1)

enemyAI()

p1.draw()
p2.draw()

drawBars()
updateParticles()

ctx.fillStyle="white"
ctx.fillText("Level "+level,420,20)

if(p2.health<=0){
nextLevel()
}

if(p1.health<=0){

ctx.font="50px Arial"
ctx.fillText("GAME OVER",330,200)
return

}

requestAnimationFrame(gameLoop)

}

gameLoop()

</script>

</body>
</html>
