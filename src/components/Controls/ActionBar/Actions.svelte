<script>
	import { candidates } from '@sudoku/stores/candidates';
	import { userGrid } from '@sudoku/stores/grid';
	import { cursor } from '@sudoku/stores/cursor';
	import { hints } from '@sudoku/stores/hints';
	import { notes } from '@sudoku/stores/notes';
	import { settings } from '@sudoku/stores/settings';
	import { keyboardDisabled } from '@sudoku/stores/keyboard';
	import { gamePaused } from '@sudoku/stores/game';
	import { history, historyState } from '@sudoku/services/history-manager';
	import { nextStepProcessor } from '@sudoku/services/nextstep-processor';
	import { hintStrategyManager } from '@sudoku/services/hint-strategy-manager';
	import { resultApplier } from '@sudoku/services/result-applier';
	import { modal } from '@sudoku/stores/modal';
	import { gameMode } from '@sudoku/stores/game-mode';
	import game from '@sudoku/game';

	$: hintsAvailable = $hints > 0;
	$: canUseNextStep = $gameMode === 'cheat';
	$: canUseHint = $gameMode === 'solve' && hintsAvailable; // 只有求解版才能使用提示

	function handleUndo() {
		if ($gamePaused || !$historyState.canUndo) return;
		history.undo();
	}

	function handleRedo() {
		if ($gamePaused || !$historyState.canRedo) return;
		history.redo();
	}

	function handleHint() {
		if (!canUseHint) return; // 只有求解版才能使用提示

		// 记录用户原有的候选数（用于后续恢复）
		const originalCandidates = JSON.parse(JSON.stringify($candidates));

		// 使用专门的提示策略管理器（内部会补齐候选数并执行多步循环）
		const suggestion = hintStrategyManager.executeStrategies();

		game.pause();
		modal.show('hint', {
			onHide: game.resume,
			suggestion,
			apply: suggestion ? () => {
				hints.useHint();
				history.applyChange(() => {
					resultApplier.applyHintResult(suggestion, originalCandidates);
				});
			} : null,
		});
	}

	function handleNextStep() {
		if ($gamePaused || !canUseNextStep) return;

		game.pause();
		const result = nextStepProcessor.processStep();

		modal.show('nextstep', {
			onHide: game.resume,
			result,
			apply: (result.type !== 'none' && result.type !== 'skip') ? () => {
				history.applyChange(() => {
					resultApplier.applyNextStepResult(result);
				});
			} : null,
		});
	}
</script>

<div class="action-buttons space-x-3">

	<button class="btn btn-round" disabled={$gamePaused || !canUseNextStep} title="Next Step" on:click={handleNextStep}>
		<svg class="icon-outline" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
			<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 7l5 5m0 0l-5 5m5-5H6" />
		</svg>
	</button>

	<button class="btn btn-round" disabled={$gamePaused || !$historyState.canUndo} title="Undo" on:click={handleUndo}>
		<svg class="icon-outline" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
			<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 10h10a8 8 0 018 8v2M3 10l6 6m-6-6l6-6" />
		</svg>
	</button>

	<button class="btn btn-round" disabled={$gamePaused || !$historyState.canRedo} title="Redo" on:click={handleRedo}>
		<svg class="icon-outline" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
			<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 10h-10a8 8 90 00-8 8v2M21 10l-6 6m6-6l-6-6" />
		</svg>
	</button>

	<button class="btn btn-round btn-badge" disabled={$keyboardDisabled || !canUseHint || $userGrid[$cursor.y][$cursor.x] !== 0} on:click={handleHint} title="Hints ({$hints})">
		<svg class="icon-outline" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
			<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z" />
		</svg>

		{#if $settings.hintsLimited && $gameMode === 'solve'}
			<span class="badge" class:badge-primary={hintsAvailable}>{$hints}</span>
		{/if}
	</button>

	<button class="btn btn-round btn-badge" on:click={notes.toggle} title="Notes ({$notes ? 'ON' : 'OFF'})">
		<svg class="icon-outline" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
			<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.232 5.232l3.536 3.536m-2.036-5.036a2.5 2.5 0 113.536 3.536L6.5 21.036H3v-3.572L16.732 3.732z" />
		</svg>

		<span class="badge tracking-tighter" class:badge-primary={$notes}>{$notes ? 'ON' : 'OFF'}</span>
	</button>

</div>


<style>
	.action-buttons {
		@apply flex flex-wrap justify-evenly self-end;
	}

	.btn-badge {
		@apply relative;
	}

	.badge {
		min-height: 20px;
		min-width:  20px;
		@apply p-1 rounded-full leading-none text-center text-xs text-white bg-gray-600 inline-block absolute top-0 left-0;
	}

	.badge-primary {
		@apply bg-primary;
	}
</style>