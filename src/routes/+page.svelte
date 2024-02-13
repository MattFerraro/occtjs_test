<script>
	import { visualizeShapes } from '$lib/visualize.js';
	// import initOpenCascade from 'opencascade.js';

	// import { browser } from '$app/environment';
	import { onMount } from 'svelte';
	import opencascade from 'opencascade.js/dist/opencascade.full.js';
	import opencascadeWasm from 'opencascade.js/dist/opencascade.full.wasm?url';

	let modelUrl = '';

	onMount(async () => {
		const module = await import('@google/model-viewer');
		// console.log('module', module);

		console.log('About to start loading opencascade');
		// @ts-ignore
		const oc = await opencascade({
			locateFile: () => opencascadeWasm
		});

		console.log('oc loaded!');
		// const sphere = new oc.BRepPrimAPI_MakeSphere_1(1);
		// console.log('sphere:', sphere);

		const builder = new oc.BRep_Builder();
		const aComp = new oc.TopoDS_Compound();
		builder.MakeCompound(aComp);
		const path = [
			[-50, 0, 0],
			[50, 0, 0],
			[50, 100, 0]
		].map(([x, y, z]) => new oc.gp_Pnt_3(x, y, z));
		const makePolygon = new oc.BRepBuilderAPI_MakePolygon_3(path[0], path[1], path[2], true);
		const wire = makePolygon.Wire();
		const f = new oc.BRepBuilderAPI_MakeFace_15(wire, false);
		builder.Add(aComp, f.Shape());

		modelUrl = visualizeShapes(oc, aComp);
		console.log('Shape is visible?');

		// const wasmInit = await import('opencascade.js?init');
		// console.log('wasm init: ', wasmInit);

		// use module here...
	});
</script>

<h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>

<model-viewer style="width:500px; height:500px" src={modelUrl} camera-controls enable-pan />
