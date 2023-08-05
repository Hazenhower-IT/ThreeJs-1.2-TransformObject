<template>
  <canvas ref="canvasRef"></canvas>
</template>

<script setup>
import { ref, onMounted} from "vue"
import * as THREE from "three"

let canvasRef = ref();

const sizes = {
  width: 800,
  height: 600
}

// Per iniziare con threejs abbiamo bisogno di 4 elementi, una scena, una camera, un renderer, e degli oggetti da mostrare
//la Scena e la camera vengono inizializzate nel setup mentre il renderer nel onMounted

// La scena è come un container al cui interno posizioniamo i nostri oggetti, luci, modelli ecc.
let scene = new THREE.Scene()

// il renderer ci serve per dire a threeJs di renderizzare questa scena
let renderer

/* 
  All'interno della scena reindirizzeremo i nostri oggetti 
*/



// LA CAMERA
// 1) La camera ci serve per avere appunto il "punto di vista" sulla scena
//    Il renderer è come se facesse una foto alla nostra scena, ma da dove la sta facendo questa foto? 
//    modificando la posizione della camera, ed i suoi parametri noi cambiamo questo punto di vista. 
let camera = new THREE.PerspectiveCamera(
  75, //vertical field of view
  sizes.width / sizes.height, //aspect ratio
  /*Optional
  0.1, //near plane
  100 //far plane
  */
);

camera.position.set(0,0,3)

scene.add(camera);

// LookAt
//Un altra interessante funzione è LookAt, in sostanza possiamo dire a un 
//qualsiasi Object3D di "guardare" o "puntare" a qualcosa.
//per esempio possiamo dire alla camera di guardare il centro della scena
camera.lookAt(new THREE.Vector3(0,0,0))
//o di guardare al cubo per esempio, ma in questo caso dovremmo scrivere
//questo metodo dopo aver aggiunto il box alla scena altrimenti avremo un errore
//camera.lookAt(box.position)


//AXES HELPER
const axesHelper = new THREE.AxesHelper()
scene.add(axesHelper)

// BOX
const boxGeometry = new THREE.BoxGeometry(1,1,1)
const boxMaterial = new THREE.MeshBasicMaterial({color: 0xff0000}) 

const box = new THREE.Mesh(boxGeometry,boxMaterial)

// Posizione
//box.position.x = 0.7
//box.position.y = -0.6
//box.position.z = 1
box.position.set(0.7 , -0.6, 1)

// Scala
//box.scale.x = 2
//box.scale.y = 0.5
//box.scale.z = 0.5 
box.scale.set(2, 0.5, 0.5)

// Rotazione
//N.B.:Durante le rotazioni è possibile che si crei un errore che blocca 
//gli assi, è possibile risolverlo utilizzando questo metodo 
//per riorganizzare l'ordine degli assi. p.s. l impostazione seguente 
//si usa per avere la corretta visuale in un gioco fps
//box.rotation.reorder("YXZ")
box.rotation.x = Math.PI * 0.25
//box.rotation.y = Math.PI * 0.25

// Quaternion
//Un altro metodo efficace per risolvere il problema delle rotazioni
// è utilizzare il quaternion. Il quaternion è una rappresentazione della
//rotazione ma usando una forma piu matematica. Puoi quindi ottenere lo stesso
//risultato della rotazione ma senza incappare in quei problemi.
//Il problema con i quaternion è che sono difficili da immaginare,
//ma possiamo risolvere questo problema in quanto modificando la rotazione,
//viene in automatico aggiornato il valore dei quaternion, quindi possiamo
//letteralmente modificare la rotazione e incollare poi il valore in quaternion

//aggiungiamo la mesh alla scena
//scene.add(box)


// I Gruppi di Oggetti
// In ThreeJS possiamo creare gruppi di oggetti, questo ci consente ad esempio
// di muoverli come fossero un solo oggetto
const group = new THREE.Group()
scene.add(group)
group.position.y = 1
group.scale.y = 2
group.rotation.y = 1

const cube1 = new THREE.Mesh(
  new THREE.BoxGeometry(),
  new THREE.MeshBasicMaterial({color: 0xff0000})
)
 
const cube2 = new THREE.Mesh(
  new THREE.BoxGeometry(),
  new THREE.MeshBasicMaterial({color: 0x00ff00})
)
cube2.position.x = -2
const cube3 = new THREE.Mesh(
  new THREE.BoxGeometry(),
  new THREE.MeshBasicMaterial({color: 0x0000ff})
)
cube3.position.x = 2

group.add(cube1,cube2,cube3)

onMounted(() => {
  // Il renderer renderizza la scena dal punto di vista della telecamera
  // e ne scrive i risultati sul canvas
  // Un canvas è un elemento html in cui abbiamo la possibilità di disegnare cose,
  // e threeJs usa WebGL per disegnare il render all'interno di questo canvas
  renderer = new THREE.WebGLRenderer({
    canvas: canvasRef.value
  });

 
  renderer.setSize(sizes.width, sizes.height);
  renderer.setPixelRatio(window.devicePixelRatio);
  renderer.render(scene, camera);

});

</script>

<style>
canvas {
  width: 100%;
  height: 100%;
  display: block;
}
</style>