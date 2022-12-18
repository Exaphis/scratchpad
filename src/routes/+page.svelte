<script>
	import CodeMirror from 'svelte-codemirror-editor';

	let value = '';
</script>

<CodeMirror bind:value styles={{ '&': { height: '100vh' } }} />
