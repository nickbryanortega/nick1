<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Readable Tiny Number Mosaic</title>
<style>
body{
  margin:0;
  height:100vh;
  display:flex;
  justify-content:center;
  align-items:center;
  background:linear-gradient(135deg,#f0f7ff,#d0e8ff);
  perspective:1000px;
  overflow:hidden;
}

.scene{
  animation:rotateScene 12s infinite ease-in-out;
}

canvas{
  background:#fff;
  box-shadow:0 20px 60px rgba(0,0,0,.2);
  border-radius:10px;
}

@keyframes rotateScene{
  0%{transform:rotateY(-15deg) rotateX(5deg);}
  50%{transform:rotateY(15deg) rotateX(-5deg);}
  100%{transform:rotateY(-15deg) rotateX(5deg);}
}
</style>
</head>
<body>

<div class="scene">
<canvas id="canvas"></canvas>
</div>

<script>
// ==================== SETTINGS ====================
const text = "maureen";      // letters to form
const fontSize = 200;         // letter size
const density = 6;            // spacing between numbers (increase to reduce overlap)
const numberSize = 10;        // smaller but still readable
const numbers = "0123456789"; // numbers to use

// ==================== CANVAS SETUP ====================
const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");
canvas.width = 900;
canvas.height = 320;

// ==================== STEP 1: Draw hidden text mask ====================
ctx.fillStyle = "#000";
ctx.font = `bold ${fontSize}px Arial`;
ctx.textAlign = "center";
ctx.textBaseline = "middle";
ctx.fillText(text, canvas.width/2, canvas.height/2);

// Get pixel data
const data = ctx.getImageData(0,0,canvas.width,canvas.height).data;
ctx.clearRect(0,0,canvas.width,canvas.height);

// ==================== STEP 2: Collect points inside text ====================
let points = [];
for(let y=0;y<canvas.height;y+=density){
  for(let x=0;x<canvas.width;x+=density){
    let i=(y*canvas.width+x)*4;
    if(data[i+3]>128){ // alpha > 128 = inside letter
      points.push({x,y});
    }
  }
}

// Shuffle points for natural bloom
points.sort(()=>Math.random()-0.5);

// ==================== STEP 3: Draw number function ====================
function drawNumber(x,y){
  const num = numbers[Math.floor(Math.random()*numbers.length)];
  ctx.fillStyle = "#1f3c88"; // dark blue for high contrast
  ctx.font = `bold ${numberSize}px Arial`; // bold + small
  ctx.fillText(num, x, y+numberSize/2);
}

// ==================== STEP 4: Bloom animation ====================
let i=0;
function bloom(){
  for(let k=0;k<50 && i<points.length;k++,i++){
    const p = points[i];
    drawNumber(p.x, p.y);
  }
  if(i<points.length){
    requestAnimationFrame(bloom);   
  }
}

bloom();
</script>

</body>
</html>
