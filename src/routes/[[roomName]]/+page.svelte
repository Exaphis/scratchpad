<script lang="ts">
	import 'codemirror/lib/codemirror.css';
	import '../../codemirror.css';

	import * as Y from 'yjs';
	import CodeMirror from 'codemirror';
	import { CodemirrorBinding } from 'y-codemirror';
	import { WebrtcProvider } from 'y-webrtc';
	import { Icon } from '@steeze-ui/svelte-icon';
	import { Share, ArrowDownTray, ChartBar, Home } from '@steeze-ui/heroicons';
	import { onMount } from 'svelte';
	import { v4 as uuidv4 } from 'uuid';
	import { page } from '$app/stores';

	let roomName = $page.params.roomName || '';
	// whether or not the user started the room
	// used to determine whether or not to save the text to localStorage
	const local = !$page.params.roomName;

	let editorContainer: HTMLTextAreaElement;
	let ydoc: Y.Doc;
	let ytext: Y.Text;
	let yUndoManager: Y.UndoManager;
	let binding: CodemirrorBinding;

	let wordCount = 0;
	let characterCount = 0;
	let characterCountWithoutSpaces = 0;

	function countWords(s: string) {
		const matches = s.match(/\S+/g);
		return matches ? matches.length : 0;
	}

	function countCharacters(s: string) {
		return s.replace(/\n/g, '').length;
	}

	function countCharactersWithoutSpaces(s: string) {
		return s.replace(/\s/g, '').length;
	}

	onMount(() => {
		const editor = CodeMirror.fromTextArea(editorContainer, {
			lineNumbers: true
		});

		ydoc = new Y.Doc();
		ytext = ydoc.getText('codemirror');
		yUndoManager = new Y.UndoManager(ytext);

		binding = new CodemirrorBinding(ytext, editor, undefined, {
			yUndoManager
		});

		ytext.observe(() => {
			const text = ytext.toString();
			wordCount = countWords(text);
			characterCount = countCharacters(text);
			characterCountWithoutSpaces = countCharactersWithoutSpaces(text);

			if (local) {
				localStorage.setItem('text', text);
			}
		});

		if (local) {
			const savedText = localStorage.getItem('text');
			// must be after observe so word count gets updated
			if (savedText) {
				ytext.insert(0, savedText);
			}
		}

		if (roomName) {
			share();
		}
	});

	function share() {
		if (!binding || !ydoc || !ytext || !yUndoManager) {
			throw new Error("Can't share before editor is ready");
		}

		if (!roomName) {
			roomName = uuidv4();
		}

		localStorage.log = 'y-webrtc';
		const provider = new WebrtcProvider(roomName, ydoc);

		// re-create binding with awareness
		const editor = binding.cm;
		binding.destroy();
		binding = new CodemirrorBinding(ytext, editor, provider.awareness, {
			yUndoManager
		});

		// add roomName to URL
		history.replaceState(null, '', `/${roomName}`);
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
</script>

<svelte:head>
	<title>Scratchpad</title>
</svelte:head>

<textarea id="editor" bind:this={editorContainer} />

<!-- fancy class fixes btn-group css when buttons are wrapped in tooltips (https://github.com/saadeghi/daisyui/issues/1203#issuecomment-1270692288) -->
<div
	class="btn-group absolute right-3 bottom-3 [&>:first-child>.btn]:rounded-l-lg [&>:last-child>.btn]:rounded-r-lg [&>*>.btn]:rounded-none"
>
	<!-- ensure we reload when going home so localStorage is loaded -->
	<!-- for some reason the disabled attribute is broken on <a> tags, so add btn-disabled to the class name -->
	<a class={`btn` + (roomName ? '' : ' btn-disabled')} href="/" target="_self" rel="external"
		><Icon src={Home} size="20px" /></a
	>
	<div class="tooltip tooltip-left" data-tip="Enter collaborative session with current text">
		<button class="btn" on:click={share} disabled={!!roomName}
			><Icon src={Share} size="20px" /></button
		>
	</div>
	<div class="tooltip tooltip-left" data-tip="Download as text file">
		<button class="btn" on:click={() => downloadFile('scratchpad.txt', binding.cm.getValue())}
			><Icon src={ArrowDownTray} size="20px" /></button
		>
	</div>
	<div class="tooltip tooltip-left" data-tip="Show word count">
		<label class="btn" for="word-count-modal"><Icon src={ChartBar} size="20px" /></label>
	</div>
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