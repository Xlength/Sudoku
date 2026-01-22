<script>
	import { onMount} from 'svelte';
	import game from '@sudoku/game';
	import { modal } from '@sudoku/stores/modal';
	import { gameWon } from '@sudoku/stores/game';
	import { puzzleImporter } from '@sudoku/services/puzzle-importer';
	import Board from './components/Board/index.svelte';
	import Controls from './components/Controls/index.svelte';
	import Header from './components/Header/index.svelte';
	import Modal from './components/Modal/index.svelte';

	// 导入gameMode → 保留
	import { gameMode } from '@sudoku/stores/game-mode';

	// 修复2：初始化unsubscribe为null，避免undefined报错
	let unsubscribe = null;

	onMount(() => {
		// 订阅gameMode → 逻辑保留，优化缩进
		unsubscribe = gameMode.subscribe((mode) => {
			// 清空原有类名
			document.body.classList.remove('cheat-mode', 'solve-mode');
			// 根据模式添加类名
			if (mode === 'cheat') {
				document.body.classList.add('cheat-mode');
			} else if (mode === 'solve') {
				document.body.classList.add('solve-mode');
			}
		});

		// 原有onMount逻辑 → 修复3：添加容错处理，避免sencode为undefined
		let hash = location.hash;
		if (hash.startsWith('#')) {
			hash = hash.slice(1);
		}
		// 初始化sencode为null，避免undefined
		let sencode = null;
		// 加try-catch，防止puzzleImporter报错中断代码
		try {
			const imported = puzzleImporter.toSencode(hash);
			if (imported && imported.sencode) {
				sencode = imported.sencode;
			}
		} catch (e) {
			console.warn('解析数独链接失败:', e);
		}
		// 显示欢迎弹窗 → 逻辑保留
		modal.show('welcome', { onHide: game.resume, sencode });
	});

	// gameWon订阅逻辑 → 保留
	gameWon.subscribe((won) => {
		if (won) {
			game.pause();
			modal.show('gameover');
		}
	});
</script>

<!-- 组件结构完全保留 → 修复JS后会正常渲染 -->
<header>
	<Header />
</header>

<section>
	<Board />
</section>

<footer>
	<Controls />
</footer>

<Modal />

<style global>
	@import "./styles/global.css";
</style>