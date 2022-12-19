<script lang="ts">
	import { Icon } from '@steeze-ui/svelte-icon';
	import { Share, ArrowDownTray, ChartBar } from '@steeze-ui/heroicons';
	import CodeMirror from 'svelte-codemirror-editor';

	function countWords(s: string) {
		var matches = s.match(/\S+/g);
		return matches ? matches.length : 0;
	}

	function countCharactersWithoutSpaces(s: string) {
		return s.replace(/\s/g, '').length;
	}

	function downloadFile(title: string, contents: string) {
		const element = document.createElement('a');
		element.setAttribute('href', 'data:text/plain;charset=utf-8,' + encodeURIComponent(contents));
		element.setAttribute('download', title);

		element.style.display = 'none';
		document.body.appendChild(element);

		element.click();

		document.body.removeChild(element);
	}

	var value = '';

	var wordCount = 0;
	var characterCount = 0;
	var characterCountWithoutSpaces = 0;
	$: {
		wordCount = countWords(value);
		characterCount = value.length;
		characterCountWithoutSpaces = countCharactersWithoutSpaces(value);
	}
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

<div class="btn-group absolute right-3 bottom-3">
	<button class="btn"><Icon src={Share} size="20px" /></button>
	<button class="btn" on:click={() => downloadFile('scratchpad.txt', value)}
		><Icon src={ArrowDownTray} size="20px" /></button
	>
	<label class="btn" for="word-count-modal"><Icon src={ChartBar} size="20px" /></label>
</div>

<input type="checkbox" id="word-count-modal" class="modal-toggle" />
<div class="modal">
	<div class="modal-box max-w-md">
		<h3 class="font-bold text-lg">Word count</h3>
		<table class="table w-full">
			<tbody>
				<tr>
					<td>Words</td>
					<td class="text-end">{wordCount}</td>
				</tr>
				<tr>
					<td>Characters</td>
					<td class="text-end">{characterCount}</td>
				</tr>
				<tr>
					<td>Characters excluding spaces</td>
					<td class="text-end">{characterCountWithoutSpaces}</td>
				</tr>
			</tbody>
		</table>
		<div class="modal-action">
			<label for="word-count-modal" class="btn">OK</label>
		</div>
	</div>
</div>
