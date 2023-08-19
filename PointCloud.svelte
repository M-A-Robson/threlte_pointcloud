<script lang="ts">
	import { Canvas, OrbitControls, T} from '@threlte/core'
	import {
		BufferGeometry,
		Float32BufferAttribute,
		SRGBColorSpace,
		Color,
		PointsMaterial,
		CanvasTexture,
		ImageBitmapLoader,
		} from 'three';
	import { degToRad } from 'three/src/math/MathUtils'
	import {
		onMount
	} from 'svelte'

	let material = new PointsMaterial({
		size:50,
		sizeAttenuation:true,
		alphaTest: 0.75,
		transparent:true,
		vertexColors:true,
		depthWrite:false})

	onMount(() => {
		// loads texture image from example to show points as circles rather than cubes
		new ImageBitmapLoader().load("https://threejs.org/examples/textures/sprites/disc.png", function ( imageBitmap ) {
			const texture = new CanvasTexture( imageBitmap )
			texture.colorSpace = SRGBColorSpace
			material.setValues({'map':texture})
		})
	})

	const particles = 10000 //set upper limit of num particles
	const positions = []
	const colors = []
	const color = new Color()
	const geom = new BufferGeometry()
	
	// replace with code to get point cloud, e.g. on 30fps hook
	// points outside view frustrum will not be displayed
	const n = 1000
	for (let i = 0; i < particles; i++){
		const x = Math.random() * n - n/2;
		const y = Math.random() * n - n/2;
		const z = Math.random() * n - n/2;
		positions.push( x, y, z );

		const vx = ( x / n ) + 0.5;
		const vy = ( y / n ) + 0.5;
		const vz = ( z / n ) + 0.5;
		color.setRGB( vx, vy, vz, SRGBColorSpace );
		colors.push( color.r, color.g, color.b );
	}

	geom.setAttribute( 'position', new Float32BufferAttribute( positions, 3 ) );
	geom.setAttribute( 'color', new Float32BufferAttribute( colors, 3 ) );
	geom.computeBoundingBox();
</script>

<div>
	<Canvas>
		<T.PerspectiveCamera makeDefault position={[0, 0, 2750]} fov={27} near={5} far={3500} aspect={16/9}>
			<OrbitControls maxPolarAngle={degToRad(80)} enableZoom={true} />
		</T.PerspectiveCamera>

		<T.DirectionalLight castShadow position={[3000, 1000, 1000]} />
		<T.DirectionalLight position={[-3000, 1000, -1000]} intensity={0.2} />
		<T.AmbientLight intensity={0.2} />
		
		<T.Points
			geometry = {geom}
			material = {material}>
		</T.Points>
	</Canvas>
</div>

<style>
	div {
		height: 720px;
		width: 1080px;
	}
</style>