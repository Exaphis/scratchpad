<script lang="ts">
	import { Icon } from '@steeze-ui/svelte-icon';
	import {
		InformationCircle,
		ExclamationCircle,
		ExclamationTriangle,
		CheckCircle
	} from '@steeze-ui/heroicons';

	export let message: string;
	export let type: 'error' | 'success' | 'info' | 'warning' = 'info';

	// must have full class names, or else tailwind will not include them
	// https://tailwindcss.com/docs/content-configuration#dynamic-class-names
	const TYPE_CLASS_MAP = {
		error: 'alert-error',
		success: 'alert-success',
		info: 'alert-info',
		warning: 'alert-warning'
	};

	const TYPE_ICON_MAP = {
		error: ExclamationCircle,
		success: CheckCircle,
		info: InformationCircle,
		warning: ExclamationTriangle
	};
</script>

<div class={`alert shadow-lg ${TYPE_CLASS_MAP[type]}`}>
	<div>
		<Icon class="w-6 h-6" src={TYPE_ICON_MAP[type]} />
		<span>{message}</span>
	</div>
</div>
