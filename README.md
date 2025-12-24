<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8" />
  <title>Heart Explosion Reels</title>
  <style>
    body { margin: 0; overflow: hidden; background: black; }
    canvas { display: block; }
  </style>
</head>
<body>

<script src="https://cdn.jsdelivr.net/npm/three@0.152.2/build/three.min.js"></script>

<script>
const scene = new THREE.Scene();

const camera = new THREE.PerspectiveCamera(45, 9/16, 0.1, 100);
camera.position.set(0, 0, 35);

const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(1080, 1920);
renderer.setPixelRatio(window.devicePixelRatio);
document.body.appendChild(renderer.domElement);

// LIGHT
const light = new THREE.PointLight(0xff3366, 2, 100);
light.position.set(0, 0, 30);
scene.add(light);

// PARTICLES
const COUNT = 1200;
const geometry = new THREE.BufferGeometry();
const positions = new Float32Array(COUNT * 3);
geometry.setAttribute("position", new THREE.BufferAttribute(positions, 3));

const material = new THREE.PointsMaterial({
  color: 0xff3366,
  size: 0.18,
  transparent: true,
  opacity: 0.9,
  depthWrite: false
});

const points = new THREE.Points(geometry, material);
scene.add(points);

function heartExplosion(i, total, time) {
  const t = (i / total) * Math.PI * 2;
  const pulse = 1 + Math.sin(time * 2) * 0.08;
  const scale = 2.5 * pulse;

  let x = 16 * Math.pow(Math.sin(t), 3);
  let y = 13 * Math.cos(t) - 5 * Math.cos(2*t) - 2 * Math.cos(3*t) - Math.cos(4*t);

  x *= scale;
  y *= scale;

  const explosion = Math.max(0, time - 3) * 3;
  x += Math.cos(t) * explosion;
  y += Math.sin(t) * explosion;

  const z = Math.sin(t*3 + time*4) * 1.5 + explosion * 0.4;

  const jitter = 0.15;
  const r = () => (Math.random() - 0.5) * jitter;

  return { x: x+r(), y: y+r(), z: z+r() };
}

let time = 0;
function animate() {
  requestAnimationFrame(animate);
  time += 0.016;

  camera.position.z = 35 + Math.sin(time*0.8)*1.5;
  camera.position.x = Math.sin(time*0.3)*0.8;
  camera.lookAt(0,0,0);

  const pos = geometry.attributes.position.array;
  for(let i=0;i<COUNT;i++){
    const p = heartExplosion(i, COUNT, time);
    pos[i*3] = p.x;
    pos[i*3+1] = p.y;
    pos[i*3+2] = p.z;
  }
  geometry.attributes.position.needsUpdate = true;

  renderer.render(scene, camera);
}
animate();
</script>

</body>
</html>
