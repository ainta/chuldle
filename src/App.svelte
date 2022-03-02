<style global lang="postcss">
	@tailwind base;
	@tailwind components;
	@tailwind utilities;

    .backdrop {
    width: 100%;
    height: 100%;
    position: fixed;
    top: 0;
    left: 0;
    background: rgba(0, 0, 0, 0.8);
    }
    .modal {
    padding: 10px;
    border-radius: 10px;
    max-width: 400px;
    margin: 10% auto;
    text-align: center;
    background: white;
    }
    .succ .modal {
    background: green;
    color: white;
    }
    .fail .modal {
    background: crimson;
    color: white;
    }

	.title {
		font-family: 'Comic Sans MS', cursive;
		font-size: 2em;
	}
	.graybutton {
		background: gray;
		color: white;
	}
</style>

<script>
	import * as data from './data.json'
	import Cell from './Cell.svelte';


	const process = (word) => {
		return word.split('').reduce( (prv, cur) => [...prv, getConstantVowel(cur)], []).flat();
	};

	const stations = data["DATA"];
	const easyStations = stations.filter( (element) => ["01호선","02호선","03호선","04호선","05호선","06호선","07호선","08호선","09호선","10호선"].includes(element["line_num"]))
		.filter( (element) => process(element["station_nm"]).length <= 10).sort(() => Math.random() - 0.5);
	const names = stations.map( (element) => element["station_nm"] );
	const epochMs = new Date('January 1, 2022 00:00:00').valueOf();
	const now = Date.now();
	const life = 6;
	let remain = new Array(life).fill(0);
	const msInDay = 86400000;
	const index = Math.floor((now - epochMs) / msInDay);
	const nextday = (index + 1) * msInDay + epochMs;
	const solution = easyStations[index % easyStations.length]["station_nm"];
	let currentStatus = 0;

	const disSolution = process(solution);
	const emptyInfo = {letter: '',matched: 0};
	let currentGuess = '';
	let guesses = [];
	function stackGuesses() {
		if(!names.includes(currentGuess)){
			alert('역 이름이 잘못되었습니다.');
			return;
		}
		if(process(currentGuess).length != disSolution.length){
			alert('역의 자모 수가 일치하지 않습니다.');
			return;
		}
		let disassembled = calc(process(currentGuess), disSolution);
		
		guesses = [...guesses, {guess: currentGuess, disassembled: disassembled}];

		if(disassembled.filter( (element) => element.matched !== 1).length === 0){
			currentStatus=1;
		}
		else if(guesses.length == life){
			currentStatus=2;
		}
		console.log(currentStatus);
		currentGuess = '';
		remain = remain.slice(1);
	}
	const onKeyPress = e => {
		if (e.charCode === 13) stackGuesses();
	};
	const calc = (arr, dis) => {
		let len = arr.length;
		let res = new Array(arr.length).fill(0);
		let vis = new Array(arr.length).fill(0);

		let ans = []
		for (let i = 0; i < len; i++) {
			if(arr[i] === dis[i]){
				res[i] = 1;
				vis[i] = 1;
			}
		}
		for (let i = 0; i < len; i++) {
			for (let j = 0; j < len; j++) {
				if(arr[i] === dis[j] && res[i] === 0 && vis[j] === 0){
					res[i] = 2;
					vis[j] = 1;
				}
			}
			ans.push({letter: arr[i], matched: res[i]});
		}
		return ans;
	};
	let showIntro = false;
	function toggleIntro(){
		showIntro = !showIntro;
	}
	function getConstantVowel(kor) {
		if (kor < '가' || kor > '힣'){
			return [kor];
		}
		const f = ['ㄱ', 'ㄲ', 'ㄴ', 'ㄷ', 'ㄸ', 'ㄹ', 'ㅁ',
				'ㅂ', 'ㅃ', 'ㅅ', 'ㅆ', 'ㅇ', 'ㅈ', 'ㅉ',
				'ㅊ', 'ㅋ', 'ㅌ', 'ㅍ', 'ㅎ'];
		const s = ['ㅏ', 'ㅐ', 'ㅑ', 'ㅒ', 'ㅓ', 'ㅔ', 'ㅕ',
				'ㅖ', 'ㅗ', 'ㅘ', 'ㅙ', 'ㅚ', 'ㅛ', 'ㅜ',
				'ㅝ', 'ㅞ', 'ㅟ', 'ㅠ', 'ㅡ', 'ㅢ', 'ㅣ'];
		const t = ['', 'ㄱ', 'ㄲ', 'ㄳ', 'ㄴ', 'ㄵ', 'ㄶ',
				'ㄷ', 'ㄹ', 'ㄺ', 'ㄻ', 'ㄼ', 'ㄽ', 'ㄾ',
				'ㄿ', 'ㅀ', 'ㅁ', 'ㅂ', 'ㅄ', 'ㅅ', 'ㅆ',
				'ㅇ', 'ㅈ', 'ㅊ', 'ㅋ', 'ㅌ', 'ㅍ', 'ㅎ'];

		const ga = 44032;
		let uni = kor.charCodeAt(0);

		uni = uni - ga;

		let fn = parseInt(uni / 588);
		let sn = parseInt((uni - (fn * 588)) / 28);
		let tn = parseInt(uni % 28);

		if(t[tn]!='')return [f[fn],s[sn],t[tn]];
		return [f[fn],s[sn]]
	}
</script>



<div class="title flex justify-center">
	Chuldle
</div>

<div>
    {#each guesses as {guess, disassembled}}
		<div class="flex justify-center mb-1">
			{#each disassembled as letter}
				<Cell info={letter}/>
			{/each}
		</div>
    {/each}

    {#each remain as {_}}
		<div class="flex justify-center mb-1">
			{#each disSolution as letter}
				<Cell info={emptyInfo}/>
			{/each}
		</div>
    {/each}

</div>



{#if currentStatus === 1 || currentStatus === 2}
    <div class="backdrop" class:succ={currentStatus===1} class:fail={currentStatus===2}>
		<div class="modal">
		<p>{currentStatus===1 ? "Success": "Failure"}</p>
		<p>{currentStatus===1 ?  guesses.length + " guesses": "Answer:" + solution}</p>
		</div>
    </div>
{/if}

{#if showIntro}
    <div class="backdrop">
		<div class="modal">
			<p>Chuldle은 서울 지하철 역을 맞추는 게임입니다.</p>
			<p>답이 동작일 때, 행당이라고 입력한 경우 아래와 같이 표시됩니다.</p>
			<div class="flex justify-center mb-1">
				{#each calc(process('행당'), process('동작')) as letter}
					<Cell info={letter}/>
				{/each}
			</div>
			<p>답이 종로3가일 때, 종로4가라고 입력한 경우 아래와 같이 표시됩니다.</p>
			<div class="flex justify-center mb-1">
				{#each calc(process('종로3가'), process('종로4가')) as letter}
					<Cell info={letter}/>
				{/each}
			</div>
			<p>입력한 역 이름의 자모 수가 네모칸의 개수와 다르면 오류 메시지가 뜹니다.</p>
			<p>서울 지하철역 이름이 아닌 값을 입력해도 오류 메시지가 뜨게 됩니다.</p>
			<button on:click={toggleIntro} class="graybutton">close</button>
		</div>
    </div>
{/if}


<div class="flex justify-center mt-5">
	<input 
	on:keypress={onKeyPress}
	bind:value={currentGuess}
	>
	<button on:click={stackGuesses}>
		Enter
	</button>
	<button class="graybutton" on:click={toggleIntro}>
		info
	</button>
</div>