<script>
	import appState from '$lib/app/core/stores/appState.svelte.js';
	import psi from '$lib/app/ui/utils/psi.js';
	import { resetStyle } from '$lib/app/ui/utils/resetStyle.svelte.js';
	import BasicBlock from '$lib/app/ui/components/BasicBlock.svelte';

	function actualiseLocalStorage(storageName, key, value) {
		let storedData = JSON.parse(localStorage.getItem(storageName)) || {};

		storedData[key] = value;

		localStorage.setItem(storageName, JSON.stringify(storedData));
	}
	/**
	 * Stroke types settings
	 *
	 */
	const strokeTypes = ['round', 'bevel', 'miter'];
	function changeStrokeType() {
		let currentType = appState.icoGlyphUserSettings.style['stroke-linejoin'];
		let currentIndex = strokeTypes.indexOf(currentType);

		let nextIndex = (currentIndex + 1) % strokeTypes.length;
		appState.icoGlyphUserSettings.style['stroke-linejoin'] = strokeTypes[nextIndex];
		appState.icoGlyphUserSettings.style['stroke-linecap'] = strokeTypes[nextIndex];

		actualiseLocalStorage(
			'icoGlyphsUserStyle',
			'stroke-linecap',
			appState.icoGlyphUserSettings.style['stroke-linecap']
		);
		actualiseLocalStorage(
			'icoGlyphsUserStyle',
			'stroke-linejoin',
			appState.icoGlyphUserSettings.style['stroke-linejoin']
		);
	}

	let inputWidth = $derived(100 / psi + '%');

	const svgStylerPadding = 'var(--spacing-medium)';

	/**
	 * Functions
	 */
	function resetStyleButtonAction() {
		localStorage.removeItem('icoGlyphsUserStyle');
		resetStyle();
	}

	let titleSection = $derived.by(() => {
		if (appState.modes.designerMode.value == true && appState.modes.devMode.value == true) {
			return 'Designer & Developer tools';
		} else if (appState.modes.designerMode.value == true) {
			return 'Designer tools';
		} else if (appState.modes.devMode.value == true) {
			return 'Developer tools';
		}
	});

	// $inspect(appState.icoGlyphUserSettings.style);
</script>

{#if appState.modes.designerMode.value == true || appState.modes.devMode.value == true}
	<BasicBlock>
		{#snippet title()}
			<h2>{titleSection}</h2>
		{/snippet}

		<!-- {#snippet text()}
			You can have more options to customize your experience in the settings menu.
		{/snippet} -->

		{#snippet el()}
			{#if appState.modes.designerMode.value == true}
				<div class="mod-color-container">
					<div class="column-container">
						<label for="stroke-size"
							>Stroke size : {appState.icoGlyphUserSettings.style['stroke-width']} px</label
						><input
							style="width: {inputWidth};"
							id="stroke-size"
							class="color-input"
							bind:value={appState.icoGlyphUserSettings.style['stroke-width']}
							oninput={() =>
								actualiseLocalStorage(
									'icoGlyphsUserStyle',
									'stroke-width',
									appState.icoGlyphUserSettings.style['stroke-width']
								)}
							min="0.3"
							max="9"
							step="0.1"
							type="range"
						/>
					</div>
				</div>
			{/if}

			{#if appState.modes.designerMode.value == true}
				<div class="mod-color-container">
					<div class="column-container" id="stroke-color-container">
						<label for="stroke-color-input" id="stroke-color-label">Stroke color</label>
						<input
							style="width: {inputWidth};"
							id="stroke-color-input"
							class="color-input"
							type="color"
							bind:value={appState.icoGlyphUserSettings.style.stroke}
							oninput={() =>
								actualiseLocalStorage(
									'icoGlyphsUserStyle',
									'stroke',
									appState.icoGlyphUserSettings.style['stroke']
								)}
						/>
					</div>
				</div>

				<div class="small-button-container">
					<button onclick={changeStrokeType} class="button-text-only">
						Stroke style :
						{appState.icoGlyphUserSettings.style['stroke-linejoin']}
					</button>
					<button onclick={resetStyleButtonAction} class="button-text-only">Reset style</button>
				</div>
			{/if}
			{#if appState.modes.devMode.value == true}
				<div class="mod-color-container">
					<label for="toggle-import-style">Import style when copying & downloading (SVG only)</label
					><input
						id="toggle-import-style"
						class="color-input"
						type="checkbox"
						onchange={() =>
							actualiseLocalStorage(
								'useStyleForSvgDownload',
								appState.icoGlyphUserSettings.useStyleForSvgDownload
							)}
						bind:checked={appState.icoGlyphUserSettings.useStyleForSvgDownload}
					/>
				</div>
			{/if}
		{/snippet}
	</BasicBlock>
{/if}

<style>
	/* #displayButtonContainer {
		border: 5px solid var(--b1);
		border-radius: var(--border-radius);
		background: var(--b1);
		position: relative;
		bottom: -25px;
		z-index: 1;
		transition: 1s;
	} */

	/* .svg-styler-container {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		border: var(--b2) var(--border-width-medium) solid;
		border-radius: var(--border-radius);
		width: 100%;
		gap: var(--spacing-medium);
		max-width: var(--max-width-medium);
		transition: 500ms;
	} */

	.mod-color-container {
		display: flex;
		align-items: center;
		justify-content: center;
		background: var(--b2);
		border-radius: var(--border-radius);
		width: 100%;
		padding: var(--spacing-medium);
		gap: var(--spacing-medium);
	}

	.column-container {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		width: 100%;
		gap: var(--spacing-medium);
	}

	#stroke-color-label {
		position: absolute;
		left: 50%;
		top: 50%;
		transform: translate(-50%, -50%);
		pointer-events: none;
		background: var(--t2);
		padding: var(--spacing-small) var(--spacing-medium);
		border-radius: var(--border-radius);
	}
	.small-button-container {
		display: flex;
		gap: var(--spacing-medium);
		width: 100%;
		justify-content: space-around;
	}
	#stroke-color-container {
		position: relative;
	}

	#stroke-color-input {
		height: 40px;
	}
</style>
