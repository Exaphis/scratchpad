<script>
	import '../../app.css';
	import { SvelteToast } from '@zerodevx/svelte-toast';
</script>

<SvelteToast options={{ reversed: true, intro: { y: 192 } }} />
<slot />

<style>
	:root {
		--toastWidth: auto;
		--toastBtnContent: '';
		--toastBtnHeight: 0;
		--toastBtnWidth: 0;
		--toastBarHeight: 0;
		--toastBoxShadow: none;
		--toastBackground: none;
		--toastPadding: 0;
		--toastMsgPadding: 0;
	}
</style>
