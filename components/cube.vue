<template>
  <div>
      <div id="container"></div>
		<div id="info">
			<a href="https://threejs.org" target="_blank" rel="noopener">three.js</a> - WebGL morph target example
		</div>

		<script type="module">

			import * as THREE from '../build/three.module.js';

			import { GUI } from '../jsm/libs/dat.gui.module.js';
			import { OrbitControls } from '../jsm/controls/OrbitControls.js';

			let camera, scene, renderer, raycaster;

			let mesh;
			const mouse = new THREE.Vector2();

			init();
			animate();

			function init() {

				const container = document.getElementById( 'container' );

				camera = new THREE.PerspectiveCamera( 45, window.innerWidth / window.innerHeight, 1, 2000 );
				camera.position.z = 500;

				scene = new THREE.Scene();
				scene.background = new THREE.Color( 0x222222 );
				scene.fog = new THREE.Fog( 0x000000, 1, 15000 );

				const light = new THREE.PointLight( 0xffffff );
				light.position.z = 500;
				camera.add( light );
				scene.add( camera );

				scene.add( new THREE.AmbientLight( 0x111111 ) );

				const geometry = new THREE.BoxGeometry( 100, 100, 100 );
				const material = new THREE.MeshLambertMaterial( { color: 0xff0000, morphTargets: true } );

				// construct 8 blend shapes

				for ( let i = 0; i < 8; i ++ ) {

					const vertices = [];

					for ( let v = 0; v < geometry.vertices.length; v ++ ) {

						vertices.push( geometry.vertices[ v ].clone() );

						if ( v === i ) {

							vertices[ vertices.length - 1 ].x *= 2;
							vertices[ vertices.length - 1 ].y *= 2;
							vertices[ vertices.length - 1 ].z *= 2;

						}

					}

					geometry.morphTargets.push( { name: "target" + i, vertices: vertices } );

				}

				mesh = new THREE.Mesh( new THREE.BufferGeometry().fromGeometry( geometry ), material );

				scene.add( mesh );

				//

				const params = {
					influence1: 0,
					influence2: 0,
					influence3: 0,
					influence4: 0,
					influence5: 0,
					influence6: 0,
					influence7: 0,
					influence8: 0
				};

				const gui = new GUI();

				const folder = gui.addFolder( 'Morph Targets' );
				folder.add( params, 'influence1', 0, 1 ).step( 0.01 ).onChange( function ( value ) {

					mesh.morphTargetInfluences[ 0 ] = value;

				} );
				folder.add( params, 'influence2', 0, 1 ).step( 0.01 ).onChange( function ( value ) {

					mesh.morphTargetInfluences[ 1 ] = value;

				} );
				folder.add( params, 'influence3', 0, 1 ).step( 0.01 ).onChange( function ( value ) {

					mesh.morphTargetInfluences[ 2 ] = value;

				} );
				folder.add( params, 'influence4', 0, 1 ).step( 0.01 ).onChange( function ( value ) {

					mesh.morphTargetInfluences[ 3 ] = value;

				} );
				folder.add( params, 'influence5', 0, 1 ).step( 0.01 ).onChange( function ( value ) {

					mesh.morphTargetInfluences[ 4 ] = value;

				} );
				folder.add( params, 'influence6', 0, 1 ).step( 0.01 ).onChange( function ( value ) {

					mesh.morphTargetInfluences[ 5 ] = value;

				} );
				folder.add( params, 'influence7', 0, 1 ).step( 0.01 ).onChange( function ( value ) {

					mesh.morphTargetInfluences[ 6 ] = value;

				} );
				folder.add( params, 'influence8', 0, 1 ).step( 0.01 ).onChange( function ( value ) {

					mesh.morphTargetInfluences[ 7 ] = value;

				} );
				folder.open();

				//

				raycaster = new THREE.Raycaster();

				renderer = new THREE.WebGLRenderer();
				renderer.setPixelRatio( window.devicePixelRatio );
				renderer.setSize( window.innerWidth, window.innerHeight );
				container.appendChild( renderer.domElement );

				//

				const controls = new OrbitControls( camera, renderer.domElement );
				controls.minDistance = 400;
				controls.maxDistance = 1000;

				//

				window.addEventListener( 'resize', onWindowResize, false );

				document.addEventListener( 'click', onClick, false );

			}

			function onClick( event ) {

				event.preventDefault();

				mouse.x = ( event.clientX / window.innerWidth ) * 2 - 1;
				mouse.y = - ( event.clientY / window.innerHeight ) * 2 + 1;

				raycaster.setFromCamera( mouse, camera );

				const intersects = raycaster.intersectObject( mesh );

				if ( intersects.length > 0 ) {

					mesh.material.color.set( Math.random() * 0xffffff );

				}

			}

			function onWindowResize() {

				camera.aspect = window.innerWidth / window.innerHeight;
				camera.updateProjectionMatrix();

				renderer.setSize( window.innerWidth, window.innerHeight );

			}

			function animate() {

				requestAnimationFrame( animate );
				render();

			}

			function render() {

				mesh.rotation.y += 0.01;

				renderer.render( scene, camera );

			}

		</script>

  </div>
</template>

<script>
export default {
    name: "cube"
};
</script>

<style>

</style>