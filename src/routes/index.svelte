<script>
	import { onMount } from 'svelte';
	import Timer from './timer';
	const backendURL = 'http://10.33.81.9/newzhiban/zhiban.php';
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
	// 局领导
	let name1 = '';
	// 指挥室领导
	let name2 = '';

	// 补零
	const zeroPlus = (/** @type {number} */ num) => (num > 9 ? num.toString() : '0' + num.toString());

	// 获取值班领导
	async function getName() {
		try {
			const res = await fetch(backendURL);
			const text = await res.text();
			if (res.ok) {
				return text.split('-');
			} else {
				throw new Error(text);
			}
		} catch(e) {
			return '未知'
		}
	}

	onMount(async () => {
		[name1,name2] = await getName();
		// 每秒更新时间
		setInterval(() => {
			timeNow = Date.now();
		}, 1000);

		// 每日更新值班领导
		setInterval(async () => {
			[name1,name2] = await getName();
		}, 600000);
	});

	$: [day, hour, min, sec] = Timer(target, timeNow);
	$: date = new Date(timeNow);
	$: name1;
	$: name2;
</script>

<main class="main">

	<div class="title">
		<div class="title2">
			<span class="text-title">局领导：{name1}</span>
			<span class="text-title">值班领导：{name2}</span>
		</div>
	
		<div class="title2">
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
	</div>
	
	<div class="timer">
		<span class="text-title">{myText}</span>
		<span class="text-title"> {day} 天 {hour} 时 {min} 分 {sec} 秒</span>
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
		top: 8%;

		justify-content: center;
		align-items: center;
	}

	.title2 {
		flex-direction: column;
		display: flex;
		align-items: center;
	}

	.timer {
		flex-direction: column;
		position: relative;
		bottom: -55%;
		display: flex;
		align-items: center;
		text-align: center;
		justify-content: center;
	}

	.text-title {
		font-family: sjbsjt;
		font-style: normal;
		font-weight: normal;
		font-size: 7vh;
		color: #fdec96;
		text-shadow: 0px 5px 10px rgba(128, 67, 38, 0.9);
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
