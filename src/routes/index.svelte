<script>
	import { onMount } from 'svelte';
	import Timer from './timer';
	const backendURL = 'http://127.0.0.1:3001';
	const target = '2021/07/01 00:00:00';
	const myText = '距建党100周年';
	const weekDict = {
		1: '一',
		2: '二',
		3: '三',
		4: '四',
		5: '五',
		6: '六',
		7: '日'
	};
	let timeNow = Date.now();
	let name = '';

	// 补零
	const zeroPlus = (/** @type {number} */ num) => (num > 9 ? num.toString() : '0' + num.toString());

	// 获取值班领导
	async function getName() {
		try {
			const res = await fetch(backendURL);
			const text = await res.json();
			if (res.ok) {
				return text.name1;
			} else {
				throw new Error(text);
			}
		} catch(e) {
			return '未知'
		}
	}

	onMount(async () => {
		name = await getName();
		// 每秒更新时间
		setInterval(async () => {
			timeNow = Date.now();
		}, 1000);

		// 每日更新值班领导
		setInterval(async () => {
			name = await getName();
		}, 86400000);
	});

	$: [day, hour, min, sec] = Timer(target, timeNow);
	$: date = new Date(timeNow);
	$: name;
</script>

<main class="main">
	<div class="name">
		<span class="text-name">值班领导：{name}</span>
	</div>

	<div class="title">
		<span class="text-title"
			>今日{`${date.getFullYear()}年${date.getMonth() + 1}月${date.getDate()}日 星期${
				weekDict[date.getDay()]
			}`}</span
		>
		<span class="text-title"
			>现在时间 {zeroPlus(date.getHours())}:{zeroPlus(date.getMinutes())}:{zeroPlus(
				date.getSeconds()
			)}</span
		>
	</div>
	<div class="timer">
		<span class="text-title">{myText}</span>
		<span class="text-timer"> {day} 天 {hour} 时 {min} 分 {sec} 秒</span>
	</div>
</main>

<style>
	@font-face {
		font-family: sjbsjt;
		src: url('/static/sjbsjt.ttf');
	}

	.title {
		position: relative;
		display: flex;
		top: 6%;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}

	.name {
		position: absolute;
		display: flex;
		right: 0px;
		bottom: 0px;
	}

	.timer {
		flex-direction: column;
		position: relative;
		bottom: -50%;
		display: flex;
		align-items: center;
		text-align: center;
		justify-content: center;
	}

	.text-title {
		font-family: sjbsjt;
		font-style: normal;
		font-weight: normal;
		font-size: 8vh;
		color: #fdec96;
		text-shadow: 0px 5px 10px rgba(100, 53, 31, 0.7);
	}

	.text-timer {
		font-family: sjbsjt;
		font-style: normal;
		font-weight: normal;
		font-size: 7vh;
		color: #fdec96;
		text-shadow: 0px 5px 10px rgba(100, 53, 31, 0.7);
	}

	.text-name {
		font-family: sjbsjt;
		font-style: normal;
		font-weight: normal;
		font-size: 5vh;
		color: #fdec96;
		text-shadow: 0px 5px 10px rgba(100, 53, 31, 0.7);
	}

	.main {
		position: fixed;
		top: 0;
		right: 0;
		bottom: 0;
		left: 0;
		background-size: 100% 100%;
		background-image: url('/static/back.jpg');
	}
</style>
