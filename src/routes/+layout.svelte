<script>
	import Footer from './_layoutComponents/Footer.svelte';
	import NavBar from './_layoutComponents/navBar/NavBar.svelte';
	import psi from '$lib/app/ui/utils/psi.js';
	import appState from '$lib/app/core/stores/appState.svelte.js';

	// make animation for waiting for the page to be ready
	let readyToDisplay = $state(false);

	$effect(() => {
		readyToDisplay = true;

		// return () => {
		// 	localStorage.setItem('monChat', 'fff');
		// };
	});

	// $inspect(appState.icoGlyphUserSettings.style);

	let { children } = $props();

	let windowWidth = $state(0);

	let sideMargin = $derived(windowWidth / Math.pow(psi, 10) + 'px');

	// $inspect(appState.modes.devMode.value);
</script>

<svelte:window bind:innerWidth={windowWidth} />

{#if readyToDisplay}
	<div style="margin: 0 {sideMargin};" id="page-wrapper">
		<NavBar />

		{@render children?.()}

		<Footer />
	</div>
{/if}
