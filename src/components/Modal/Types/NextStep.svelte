<script>
	export let data = {};
	export let hideModal;

	const result = data.result || {};

	function handleApply() {
		if (data.apply) data.apply();
		hideModal();
	}
</script>

<div class="flex justify-between items-center mb-4">
	<h1 class="text-3xl font-semibold leading-none">Next Step</h1>

	<button class="cursor-pointer" on:click={hideModal} aria-label="Close">
		<svg class="icon-outline" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
			<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
		</svg>
	</button>
</div>

{#if result.type === 'solve' && result.solved}
	<div class="space-y-4 mb-6">
		{#if result.skipped && result.skipped.length > 0}
			<div class="p-3 bg-gray-50 rounded mb-2">
				<div class="text-xs text-gray-600">
					{#each result.skipped as skip}
						<div>{skip}: 无可操作单元格，跳过</div>
					{/each}
				</div>
			</div>
		{/if}
		<div class="p-4 bg-green-50 border-l-4 border-green-500 rounded">
			<div class="text-lg font-semibold text-green-800 mb-2">确定数字</div>
			<div class="text-base mb-2">
				<div class="font-semibold">步骤: {result.step}</div>
				<div class="text-sm text-gray-700 mt-1">{result.explanation}</div>
			</div>
			<div class="text-sm font-mono">
				{#each result.solved as solve}
					<div>行 {solve.position.y + 1}, 列 {solve.position.x + 1} → {solve.value}</div>
				{/each}
			</div>
		</div>
	</div>

	<div class="flex justify-end space-x-3">
		<button class="btn btn-small" on:click={hideModal}>Skip</button>
		<button class="btn btn-small btn-primary" on:click={handleApply}>Apply</button>
	</div>
{:else if result.type === 'eliminate' && result.eliminations}
	<div class="space-y-4 mb-6">
		{#if result.skipped && result.skipped.length > 0}
			<div class="p-3 bg-gray-50 rounded mb-2">
				<div class="text-xs text-gray-600">
					{#each result.skipped as skip}
						<div>{skip}: 无可操作单元格，跳过</div>
					{/each}
				</div>
			</div>
		{/if}
		<div class="p-4 bg-blue-50 border-l-4 border-blue-500 rounded">
			<div class="text-lg font-semibold text-blue-800 mb-2">删除候选数</div>
			<div class="text-base mb-2">
				<div class="font-semibold">步骤: {result.step}</div>
				<div class="font-semibold">策略: {result.strategy}</div>
				<div class="text-sm text-gray-700 mt-1">{result.explanation}</div>
			</div>
			<div class="text-sm">
				将删除 {result.eliminations.length} 个单元格中的候选数
			</div>
		</div>
	</div>

	<div class="flex justify-end space-x-3">
		<button class="btn btn-small" on:click={hideModal}>Skip</button>
		<button class="btn btn-small btn-primary" on:click={handleApply}>Apply</button>
	</div>
{:else if result.type === 'complete'}
	<div class="space-y-4 mb-6">
		<div class="p-4 bg-green-50 border-l-4 border-green-500 rounded">
			<div class="text-lg font-semibold text-green-800 mb-2">🎉 数独已完成！</div>
			<div class="text-sm text-green-700">
				{result.explanation}
			</div>
		</div>
	</div>

	<div class="flex justify-end">
		<button class="btn btn-small btn-primary" on:click={hideModal}>Got it</button>
	</div>
{:else if result.type === 'skip'}
	<div class="space-y-4 mb-6">
		<div class="p-4 bg-yellow-50 border-l-4 border-yellow-500 rounded">
			<div class="text-lg font-semibold text-yellow-800 mb-2">所有步骤已跳过</div>
			<div class="text-sm text-yellow-700 mt-2 space-y-1">
				{#each result.skipped || [] as skip}
					<div>{skip}: 无可操作单元格，跳过</div>
				{/each}
			</div>
			<div class="text-sm text-yellow-700 mt-3">
				请继续点击 Next Step 重新循环。
			</div>
		</div>
	</div>

	<div class="flex justify-end">
		<button class="btn btn-small btn-primary" on:click={hideModal}>Got it</button>
	</div>
{:else}
	<div class="space-y-4 mb-6">
		<div class="p-4 bg-yellow-50 border-l-4 border-yellow-500 rounded">
			<div class="text-lg font-semibold text-yellow-800 mb-2">未找到可用操作</div>
			<div class="text-sm text-yellow-700">
				{result.explanation || '当前没有可执行的操作。可能需要更高级的策略或手动填入一些数字。'}
			</div>
		</div>
	</div>

	<div class="flex justify-end">
		<button class="btn btn-small btn-primary" on:click={hideModal}>Got it</button>
	</div>
{/if}

<style>
	.icon-outline {
		@apply w-6 h-6;
	}
</style>
