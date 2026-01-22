<script>
	import game from '@sudoku/game';
	import { modal } from '@sudoku/stores/modal';
	import { DIFFICULTY_CUSTOM } from '@sudoku/constants';
	import { difficulty } from '@sudoku/stores/difficulty';
	import { DIFFICULTIES } from '@sudoku/constants';

	function handleClick() {
		game.pause();
		// 直接显示Welcome模态框
		modal.show('welcome', {
			onHide: game.resume,
		});
	}
</script>

<div class="dropdown">
	<button class="dropdown-button" on:click={handleClick} title="New Game">
		<svg class="icon-outline mr-3" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
			<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 8h16M4 16h12" />
		</svg>

		<span class="text-2xl tracking-wider">{$difficulty === DIFFICULTY_CUSTOM ? 'Custom' : DIFFICULTIES[$difficulty]}</span>
	</button>
</div>

<style>
	.dropdown {
		@apply relative;
	}

	.dropdown-button {
		@apply relative z-30 block flex outline-none items-center;
	}
</style>