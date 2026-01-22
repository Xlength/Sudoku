<script>
	export let data = {};
	export let hideModal;

	const suggestion = data.suggestion;

	function handleApply() {
		if (data.apply) data.apply();
		hideModal();
	}
</script>

<div class="flex justify-between items-center mb-4">
	<h1 class="text-3xl font-semibold leading-none">Hint</h1>

	<button class="cursor-pointer" on:click={hideModal} aria-label="Close">
		<svg class="icon-outline" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
			<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
		</svg>
	</button>
</div>

{#if suggestion}
	<div class="space-y-3">
		<div>
			<div class="text-lg font-semibold">Next move</div>
			<div class="font-mono text-xl">Row {suggestion.position.y + 1}, Col {suggestion.position.x + 1} → {suggestion.value}</div>
		</div>

		<div>
			<div class="text-lg font-semibold">Strategy</div>
			<div class="text-base">{suggestion.strategy}</div>
			<div class="text-sm text-gray-700">{suggestion.explanation}</div>
		</div>

		<div>
			<div class="text-lg font-semibold">Candidates</div>
			<div class="font-mono text-base">{suggestion.candidates.join(', ')}</div>
		</div>
	</div>

	<div class="flex justify-end mt-6 space-x-3">
		<button class="btn btn-small" on:click={hideModal}>Skip</button>
		<button class="btn btn-small btn-primary" on:click={handleApply}>Apply hint</button>
	</div>
{:else}
	<div class="text-base mb-6">
		No logical hint available for the current grid. Try exploring or undoing a move.
	</div>

	<div class="flex justify-end">
		<button class="btn btn-small btn-primary" on:click={hideModal}>Got it</button>
	</div>
{/if}
