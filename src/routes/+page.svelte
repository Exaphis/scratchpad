<script>
	import CodeMirror from 'svelte-codemirror-editor';

	let value = '';
</script>

<svelte:head>
	<title>Scratchpad</title>
</svelte:head>

<CodeMirror
	bind:value
	styles={{
		'&': { height: '100vh' },
		'.cm-gutters': { backgroundColor: 'hsl(var(--b2))', border: 'none' },
		'.cm-activeLineGutter': { backgroundColor: 'hsl(var(--b3))' }
	}}
/>
