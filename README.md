```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>3D Saber Model</title>

<style>
* {
    box-sizing: border-box;
}

html, body {
    margin: 0;
    width: 100%;
    height: 100%;
    overflow: hidden;
    background: #101216;
    font-family: Arial, sans-serif;
    color: white;
}

#scene {
    position: fixed;
    inset: 0;
}

#ui {
    position: fixed;
    top: 20px;
    left: 20px;
    width: 230px;
    padding: 18px;
    background: rgba(15, 17, 22, 0.88);
    border: 1px solid rgba(255,255,255,0.12);
    border-radius: 14px;
    backdrop-filter: blur(10px);
    z-index: 10;
}

#ui h1 {
    margin: 0 0 4px;
    font-size: 20px;
    letter-spacing: 2px;
}

.subtitle {
    color: #999;
    font-size: 11px;
    margin-bottom: 18px;
}

.section-title {
    font-size: 11px;
    color: #888;
    margin: 14px 0 7px;
    text-transform: uppercase;
    letter-spacing: 1px;
}

button {
    width: 100%;
    margin-bottom: 7px;
    padding: 9px 10px;
    border: 1px solid rgba(255,255,255,0.13);
    border-radius: 7px;
    background: #1b1e25;
    color: #ddd;
    cursor: pointer;
    text-align: left;
    transition: 0.2s;
}

button:hover {
    background: #292d36;
}

button.active {
    background: #343944;
    color: white;
}

.controls {
    position: fixed;
    bottom: 18px;
    left: 50%;
    transform: translateX(-50%);
    padding: 9px 15px;
    border-radius: 20px;
    background: rgba(15,17,22,0.78);
    color: #aaa;
    font-size: 12px;
    z-index: 10;
    pointer-events: none;
}

#loading {
    position: fixed;
    inset: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    background: #101216;
    z-index: 100;
    font-size: 14px;
    letter-spacing: 2px;
}

@media(max-width:600px) {

    #ui {
        width: 175px;
        top: 10px;
        left: 10px;
        padding: 12px;
    }

    #ui h1 {
        font-size: 15px;
    }

    button {
        font-size: 11px;
        padding: 7px;
    }

    .controls {
        font-size: 10px;
        bottom: 10px;
    }
}
</style>
</head>

<body>

<div id="loading">LOADING 3D MODEL...</div>

<div id="scene"></div>

<div id="ui">

    <h1>SABER</h1>
    <div class="subtitle">3D COMPONENT MODEL</div>

    <div class="section-title">Assembly</div>

    <button onclick="explode()">EXPLODE</button>
    <button onclick="assemble()">ASSEMBLE</button>
    <button onclick="resetView()">RESET VIEW</button>
    <button onclick="toggleAutoRotate()">AUTO ROTATE</button>

    <div class="section-title">Components</div>

    <button onclick="togglePart('blade')">◉ Blade</button>
    <button onclick="togglePart('guard')">◉ Guard</button>
    <button onclick="togglePart('handle')">◉ Handle</button>
    <button onclick="togglePart('pommel')">◉ Pommel</button>

</div>

<div class="controls">
    左键拖拽：旋转　|　滚轮：缩放　|　右键：平移
</div>


<script type="module">

import * as THREE from
'https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js';

import { OrbitControls } from
'https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/controls/OrbitControls.js';


/* =========================================================
   SCENE
========================================================= */

const scene = new THREE.Scene();

scene.background = new THREE.Color(0x101216);

scene.fog = new THREE.Fog(
    0x101216,
    15,
    35
);


/* =========================================================
   CAMERA
========================================================= */

const camera = new THREE.PerspectiveCamera(
    45,
    window.innerWidth / window.innerHeight,
    0.1,
    100
);

camera.position.set(
    7,
    -8,
    5
);


/* =========================================================
   RENDERER
========================================================= */

const renderer = new THREE.WebGLRenderer({
    antialias: true
});

renderer.setPixelRatio(
    Math.min(window.devicePixelRatio, 2)
);

renderer.setSize(
    window.innerWidth,
    window.innerHeight
);

renderer.shadowMap.enabled = true;

renderer.shadowMap.type =
    THREE.PCFSoftShadowMap;

document
    .getElementById("scene")
    .appendChild(renderer.domElement);


/* =========================================================
   CONTROLS
========================================================= */

const controls = new OrbitControls(
    camera,
    renderer.domElement
);

controls.enableDamping = true;

controls.dampingFactor = 0.05;

controls.enablePan = true;

controls.minDistance = 3;

controls.maxDistance = 18;

controls.target.set(
    0,
    0,
    1.8
);


/* =========================================================
   LIGHT
========================================================= */

const ambient = new THREE.HemisphereLight(
    0xffffff,
    0x202020,
    2
);

scene.add(ambient);


const keyLight = new THREE.DirectionalLight(
    0xffffff,
    4
);

keyLight.position.set(
    5,
    -6,
    10
);

keyLight.castShadow = true;

scene.add(keyLight);


const fillLight = new THREE.DirectionalLight(
    0x9ab5ff,
    1.5
);

fillLight.position.set(
    -6,
    3,
    5
);

scene.add(fillLight);


/* =========================================================
   MATERIALS
========================================================= */

const bladeMaterial =
    new THREE.MeshStandardMaterial({

        color: 0xbfc7cf,

        metalness: 0.95,

        roughness: 0.18

    });


const edgeMaterial =
    new THREE.MeshStandardMaterial({

        color: 0xe7edf2,

        metalness: 1,

        roughness: 0.1

    });


const darkMetal =
    new THREE.MeshStandardMaterial({

        color: 0x20242b,

        metalness: 0.9,

        roughness: 0.25

    });


const goldMaterial =
    new THREE.MeshStandardMaterial({

        color: 0xb08a42,

        metalness: 0.9,

        roughness: 0.22

    });


const leatherMaterial =
    new THREE.MeshStandardMaterial({

        color: 0x201814,

        metalness: 0.05,

        roughness: 0.8

    });


/* =========================================================
   SABER GROUP
========================================================= */

const saber = new THREE.Group();

scene.add(saber);


/* =========================================================
   COMPONENT GROUPS
========================================================= */

const parts = {

    blade: new THREE.Group(),

    guard: new THREE.Group(),

    handle: new THREE.Group(),

    pommel: new THREE.Group()

};

Object.values(parts).forEach(
    p => saber.add(p)
);


/* =========================================================
   BLADE
========================================================= */

function createBlade() {

    const shape = new THREE.Shape();

    /*
       弯曲军刀刀身
    */

    shape.moveTo(-0.27, 0);

    shape.lineTo(0.27, 0);

    shape.quadraticCurveTo(
        0.42,
        1.4,
        0.58,
        2.8
    );

    shape.quadraticCurveTo(
        0.78,
        4.3,
        0.60,
        5.7
    );

    shape.quadraticCurveTo(
        0.45,
        6.35,
        0.0,
        6.75
    );

    shape.quadraticCurveTo(
        -0.10,
        6.25,
        -0.15,
        5.5
    );

    shape.quadraticCurveTo(
        -0.20,
        4.2,
        -0.16,
        3
    );

    shape.quadraticCurveTo(
        -0.18,
        1.3,
        -0.27,
        0
    );


    const geometry =
        new THREE.ExtrudeGeometry(
            shape,
            {
                depth: 0.12,

                bevelEnabled: true,

                bevelThickness: 0.035,

                bevelSize: 0.035,

                bevelSegments: 2,

                curveSegments: 8
            }
        );


    geometry.center();


    const blade =
        new THREE.Mesh(
            geometry,
            bladeMaterial
        );

    blade.castShadow = true;

    blade.receiveShadow = true;

    /*
       ExtrudeGeometry 默认在 XY 平面，
       我们旋转到刀身竖直方向。
    */

    blade.rotation.x =
        Math.PI / 2;

    blade.rotation.z =
        Math.PI;


    parts.blade.add(blade);


    /* =========================
       刀刃装饰线
    ========================= */

    const edgeGeometry =
        new THREE.BoxGeometry(
            0.035,
            6.0,
            0.02
        );

    const edge =
        new THREE.Mesh(
            edgeGeometry,
            edgeMaterial
        );

    edge.position.set(
        0.35,
        0,
        3.0
    );

    edge.rotation.z =
        -0.04;

    parts.blade.add(edge);


    /* =========================
       刀身中线
    ========================= */

    const grooveGeometry =
        new THREE.BoxGeometry(
            0.025,
            5.6,
            0.025
        );

    const groove =
        new THREE.Mesh(
            grooveGeometry,
            darkMetal
        );

    groove.position.set(
        0.12,
        -0.065,
        2.8
    );

    parts.blade.add(groove);
}

createBlade();


/* =========================================================
   GUARD
========================================================= */

function createGuard() {

    /*
       横向护手
    */

    const guardGeometry =
        new THREE.BoxGeometry(
            2.15,
            0.22,
            0.22
        );

    const guard =
        new THREE.Mesh(
            guardGeometry,
            goldMaterial
        );

    guard.castShadow = true;

    parts.guard.add(guard);


    /*
       两侧圆形装饰
    */

    [-1, 1].forEach(side => {

        const geo =
            new THREE.SphereGeometry(
                0.20,
                24,
                24
            );

        const sphere =
            new THREE.Mesh(
                geo,
                goldMaterial
            );

        sphere.position.x =
            side * 1.02;

        parts.guard.add(sphere);

    });


    /*
       中央护手连接
    */

    const centerGeometry =
        new THREE.CylinderGeometry(
            0.28,
            0.28,
            0.35,
            32
        );

    const center =
        new THREE.Mesh(
            centerGeometry,
            darkMetal
        );

    center.rotation.z =
        Math.PI / 2;

    parts.guard.add(center);
}

createGuard();


/* =========================================================
   HANDLE
========================================================= */

function createHandle() {

    const handleGeometry =
        new THREE.CylinderGeometry(
            0.30,
            0.25,
            1.9,
            32
        );

    const handle =
        new THREE.Mesh(
            handleGeometry,
            leatherMaterial
        );

    handle.position.z =
        -0.95;

    handle.castShadow = true;

    parts.handle.add(handle);


    /*
       皮革缠绕
    */

    for (
        let i = 0;
        i < 9;
        i++
    ) {

        const ringGeometry =
            new THREE.TorusGeometry(
                0.28,
                0.025,
                8,
                32
            );

        const ring =
            new THREE.Mesh(
                ringGeometry,
                goldMaterial
            );

        ring.rotation.x =
            Math.PI / 2;

        ring.position.z =
            -0.2 - i * 0.20;

        parts.handle.add(ring);
    }
}

createHandle();


/* =========================================================
   POMMEL
========================================================= */

function createPommel() {

    const pommelGeometry =
        new THREE.SphereGeometry(
            0.38,
            32,
            20
        );

    const pommel =
        new THREE.Mesh(
            pommelGeometry,
            goldMaterial
        );

    pommel.position.z =
        -2.05;

    pommel.scale.set(
        1,
        0.8,
        0.8
    );

    pommel.castShadow = true;

    parts.pommel.add(pommel);


    /*
       柄头中心装饰
    */

    const jewelGeometry =
        new THREE.SphereGeometry(
            0.12,
            24,
            16
        );

    const jewelMaterial =
        new THREE.MeshStandardMaterial({

            color: 0x15202d,

            metalness: 0.7,

            roughness: 0.15

        });

    const jewel =
        new THREE.Mesh(
            jewelGeometry,
            jewelMaterial
        );

    jewel.position.set(
        0,
        -0.30,
        -2.05
    );

    parts.pommel.add(jewel);
}

createPommel();


/* =========================================================
   GROUND
========================================================= */

const groundGeometry =
    new THREE.PlaneGeometry(
        30,
        30
    );

const groundMaterial =
    new THREE.MeshStandardMaterial({

        color: 0x17191d,

        roughness: 0.85,

        metalness: 0.05

    });

const ground =
    new THREE.Mesh(
        groundGeometry,
        groundMaterial
    );

ground.rotation.x =
    -Math.PI / 2;

ground.position.y =
    -2.45;

ground.receiveShadow = true;

scene.add(ground);


/* =========================================================
   ORIGINAL POSITIONS
========================================================= */

const originalPositions = {

    blade:
        parts.blade.position.clone(),

    guard:
        parts.guard.position.clone(),

    handle:
        parts.handle.position.clone(),

    pommel:
        parts.pommel.position.clone()

};


/* =========================================================
   EXPLODE POSITIONS
========================================================= */

const explodePositions = {

    blade:
        new THREE.Vector3(
            0,
            0,
            2.2
        ),

    guard:
        new THREE.Vector3(
            0,
            0,
            0.45
        ),

    handle:
        new THREE.Vector3(
            0,
            0,
            -0.7
        ),

    pommel:
        new THREE.Vector3(
            0,
            0,
            -2.7
        )

};


/* =========================================================
   ANIMATION
========================================================= */

let animationFrame = null;


function animateParts(targets) {

    if (animationFrame)
        cancelAnimationFrame(animationFrame);

    const start = {};

    Object.keys(parts).forEach(
        key => {

            start[key] =
                parts[key].position.clone();

        }
    );


    const duration = 700;

    const startTime =
        performance.now();


    function update(time) {

        const progress =
            Math.min(
                (time - startTime) /
                duration,
                1
            );


        const smooth =
            progress *
            progress *
            (3 - 2 * progress);


        Object.keys(parts).forEach(
            key => {

                parts[key].position.lerpVectors(
                    start[key],
                    targets[key],
                    smooth
                );

            }
        );


        if (progress < 1) {

            animationFrame =
                requestAnimationFrame(update);

        }

    }

    animationFrame =
        requestAnimationFrame(update);
}


/* =========================================================
   EXPLODE
========================================================= */

window.explode = function() {

    animateParts(
        explodePositions
    );

};


/* =========================================================
   ASSEMBLE
========================================================= */

window.assemble = function() {

    animateParts(
        originalPositions
    );

};


/* =========================================================
   TOGGLE PART
========================================================= */

window.togglePart = function(name) {

    parts[name].visible =
        !parts[name].visible;

};


/* =========================================================
   RESET VIEW
========================================================= */

window.resetView = function() {

    camera.position.set(
        7,
        -8,
        5
    );

    controls.target.set(
        0,
        0,
        1.8
    );

    controls.update();

    assemble();

};


/* =========================================================
   AUTO ROTATE
========================================================= */

let autoRotate = false;

window.toggleAutoRotate = function() {

    autoRotate =
        !autoRotate;

    controls.autoRotate =
        autoRotate;

    controls.autoRotateSpeed =
        1.5;

};


/* =========================================================
   CLICK HIGHLIGHT
========================================================= */

const raycaster =
    new THREE.Raycaster();

const mouse =
    new THREE.Vector2();


renderer.domElement.addEventListener(
    "click",
    event => {

        mouse.x =
            (event.clientX /
            window.innerWidth) *
            2 - 1;

        mouse.y =
            -(event.clientY /
            window.innerHeight) *
            2 + 1;


        raycaster.setFromCamera(
            mouse,
            camera
        );


        const objects = [];

        Object.values(parts)
            .forEach(group => {

                group.traverse(
                    child => {

                        if (
                            child.isMesh &&
                            child.visible
                        ) {
                            objects.push(child);
                        }

                    }
                );

            });


        const hits =
            raycaster.intersectObjects(
                objects
            );


        if (hits.length > 0) {

            const object =
                hits[0].object;

            const oldMaterial =
                object.material;

            object.material =
                object.material.clone();

            object.material.emissive =
                new THREE.Color(
                    0x333333
                );

            setTimeout(() => {

                object.material =
                    oldMaterial;

            }, 250);

        }

    }
);


/* =========================================================
   RESIZE
========================================================= */

window.addEventListener(
    "resize",
    () => {

        camera.aspect =
            window.innerWidth /
            window.innerHeight;

        camera.updateProjectionMatrix();

        renderer.setSize(
            window.innerWidth,
            window.innerHeight
        );

    }
);


/* =========================================================
   ANIMATION LOOP
========================================================= */

function animate() {

    requestAnimationFrame(
        animate
    );

    controls.update();

    renderer.render(
        scene,
        camera
    );

}

animate();


/* =========================================================
   FINISH LOADING
========================================================= */

setTimeout(() => {

    document
        .getElementById("loading")
        .style.display = "none";

}, 500);

</script>

</body>
</html>
```
