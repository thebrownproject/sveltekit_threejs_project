<script>
	import { onMount } from 'svelte';
	import * as THREE from 'three';
	import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';

	let container; // This will bind to the div element

	onMount(() => {
		if (!container) return; // Ensure container is available

		// Setup scene
		const scene = new THREE.Scene();
		scene.background = new THREE.Color(0x000000); // Black background (was 0xf0f0f0)

		// Setup camera
		const camera = new THREE.PerspectiveCamera(
			75,
			container.clientWidth / container.clientHeight, // Use container properties here
			0.1,
			1000
		);
		camera.position.set(3, 2, 5); // Corrected typo: position

		// Setup renderer
		const renderer = new THREE.WebGLRenderer({ antialias: true });
		renderer.setSize(container.clientWidth, container.clientHeight); // Use container properties here
		container.appendChild(renderer.domElement); // Append renderer here

		// Add Lights
		const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
		directionalLight.position.set(5, 5, 5);
		scene.add(directionalLight);
		scene.add(new THREE.AmbientLight(0x404040));

		// Create a wall (Box Geometry)
		const wallWidth = 4;
		const wallHeight = 2;
		const wallDepth = 0.2;
		const wallGeometry = new THREE.BoxGeometry(wallWidth, wallHeight, wallDepth);
		const wallMaterial = new THREE.MeshStandardMaterial({ color: 0xffffff });
		const wallMesh = new THREE.Mesh(wallGeometry, wallMaterial);
		scene.add(wallMesh);

		// Setup OrbitControls
		const controls = new OrbitControls(camera, renderer.domElement);
		controls.enableDamping = true; // Optional: makes controls smoother

		// Animation loop
		function animate() {
			requestAnimationFrame(animate);
			controls.update(); // Required if enableDamping is true or controls autoRotate is true
			renderer.render(scene, camera);
		}
		animate(); // Start the animation loop

		// Handle window resize
		const handleResize = () => {
			if (!container) return;
			camera.aspect = container.clientWidth / container.clientHeight;
			camera.updateProjectionMatrix();
			renderer.setSize(container.clientWidth, container.clientHeight);
		};
		window.addEventListener('resize', handleResize);

		// Cleanup on component destroy
		return () => {
			window.removeEventListener('resize', handleResize);
			// Dispose of Three.js objects to free up resources
			renderer.dispose();
			wallGeometry.dispose();
			wallMaterial.dispose();
			// Potentially dispose other geometries, materials, textures etc.
			// controls.dispose(); // If controls have a dispose method
		};
	});
</script>

<div bind:this={container} class="canvas-container" style="width: 100%; height: 100vh;"></div>
<!-- The Three.js canvas will be appended here -->
