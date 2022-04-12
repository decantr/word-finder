<script lang="ts">
	import {WordList} from './words';

	const words: string[] = WordList.words;
	const currentGameNo: number = Math.floor((new Date().getTime() - new Date('2021-06-19').getTime()) / (1000 * 3600 * 24));

	let confirmedClicks: number = 0;
	let absent: string = '';
	let is_0: string = '';
	let is_1: string = '';
	let is_2: string = '';
	let is_3: string = '';
	let is_4: string = '';
	let not_0: string = '';
	let not_1: string = '';
	let not_2: string = '';
	let not_3: string = '';
	let not_4: string = '';
	let nots: string = '';
	let letterMap: number[] = [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0];

	export let couldBe: string[] = [];
	export let wordScores: {word: string, score: number}[] = [];
	export let hasSearched: boolean = false;
	export let showAll: boolean = false;

	function lookFor(){
		hasSearched = true;
		const c: string[] = (is_0 + is_1 + is_2 + is_3 + is_4 + not_0 + not_1 + not_2 + not_3 + not_4).split('');
		const ic: string[] = absent.split('');
		nots = (not_0 + not_1 + not_2 + not_3 + not_4);

		couldBe = words
				.filter(word => {
					if ( c.every(l => word.includes(l)) && !ic.some(l => word.includes(l)) ) {
						if (is_0 !== '' && word[0] !== is_0) return false;
						if (is_1 !== '' && word[1] !== is_1) return false;
						if (is_2 !== '' && word[2] !== is_2) return false;
						if (is_3 !== '' && word[3] !== is_3) return false;
						if (is_4 !== '' && word[4] !== is_4) return false;

						if (not_0 !== '' && not_0.includes(word[0])) return false;
						if (not_1 !== '' && not_1.includes(word[1])) return false;
						if (not_2 !== '' && not_2.includes(word[2])) return false;
						if (not_3 !== '' && not_3.includes(word[3])) return false;
						if (not_4 !== '' && not_4.includes(word[4])) return false;
						return true;
					}
					return false;
				});

		if (couldBe.length === words.length) {
			couldBe = [];
			hasSearched = false;
		}

		letterMap = [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0];
		couldBe.forEach(w => w.split('').forEach(l=>letterMap[l.charCodeAt(0) - 97]++));
		wordScores = findBestWord(couldBe);
	}

	function findBestWord(wordlist: string[]){
		return wordlist.map(w => {
			let score = 0;
			for (const l of w) {
				if (w.match(new RegExp(l, 'g')).length === 1)
					score += letterMap[l.charCodeAt(0) - 97];
				else
					score -= letterMap[l.charCodeAt(0) - 97];

				if (['a','e','i','o','u'].includes(l))
					score += wordlist.length;
			}
			return {word: w, score};
		}).sort((a,b) => a.score < b.score ? 1 : -1);

	}

	function lowercase(node: HTMLInputElement): void {
		node.addEventListener('input', () => node.value = node.value.toLowerCase(), { capture: true })
	}

	function isWordUsed(word: string): boolean {
		return words.findIndex(w => w === word) < currentGameNo;
	}

	function showMe(): void{
		if (confirmedClicks !== 10) {
			++confirmedClicks;
			return;
		}

		confirmedClicks = 0;
		if (confirm('Are you sure?')) {
			const word = words[currentGameNo];
			is_0 = word[0];
			is_1 = word[1];
			is_2 = word[2];
			is_3 = word[3];
			is_4 = word[4];
			nots = absent = not_0 = not_1 = not_2 = not_3 = not_4 = '';
			wordScores = [{word, score: 1}];
		}
	}
</script>

<main>
	<h1>Word Finder</h1>
	<p class="subtitle">
		Game <span class="highlight" on:click={showMe}>{currentGameNo}</span> of {words.length}
	</p>

	<div>
		<label>Correctly Placed Letters</label>
		<input maxlength="1" class="is" autocomplete="off" autocapitalize="off" spellcheck="false" use:lowercase bind:value={is_0} on:keyup={lookFor}/>
		<input maxlength="1" class="is" autocomplete="off" autocapitalize="off" spellcheck="false" use:lowercase bind:value={is_1} on:keyup={lookFor}/>
		<input maxlength="1" class="is" autocomplete="off" autocapitalize="off" spellcheck="false" use:lowercase bind:value={is_2} on:keyup={lookFor}/>
		<input maxlength="1" class="is" autocomplete="off" autocapitalize="off" spellcheck="false" use:lowercase bind:value={is_3} on:keyup={lookFor}/>
		<input maxlength="1" class="is" autocomplete="off" autocapitalize="off" spellcheck="false" use:lowercase bind:value={is_4} on:keyup={lookFor}/>
	</div>

	<div>
		<label>Incorrectly Placed Letters</label>
		<input maxlength="4" class="almost" autocomplete="off" autocapitalize="off" autocorrect="off" spellcheck="false" use:lowercase bind:value={not_1} on:keyup={lookFor}/>
		<input maxlength="4" class="almost" autocomplete="off" autocapitalize="off" autocorrect="off" spellcheck="false" use:lowercase bind:value={not_0} on:keyup={lookFor}/>
		<input maxlength="4" class="almost" autocomplete="off" autocapitalize="off" autocorrect="off" spellcheck="false" use:lowercase bind:value={not_2} on:keyup={lookFor}/>
		<input maxlength="4" class="almost" autocomplete="off" autocapitalize="off" autocorrect="off" spellcheck="false" use:lowercase bind:value={not_3} on:keyup={lookFor}/>
		<input maxlength="4" class="almost" autocomplete="off" autocapitalize="off" autocorrect="off" spellcheck="false" use:lowercase bind:value={not_4} on:keyup={lookFor}/>
	</div>

	<div>
		<label>Incorrect letters</label>
		<input class="not" autocomplete="off" autocapitalize="off" autocorrect="off" spellcheck="false" use:lowercase bind:value={absent} on:keyup={lookFor}/>
	</div>

	<div class="results">
		<span>
			{#if hasSearched && !couldBe.length}
				Could not find a match in word list.
			{/if}
		</span>

		{#each wordScores as word, i}
			{#if showAll || i < 10}
			<div>
				<div>
					<input readonly class="{word.word[0] === is_0 ? 'is' : (nots.includes(word.word[0]) && word.word[0] !== not_0 ? 'almost' : '')}" value={word.word[0]} />
					<input readonly class="{word.word[1] === is_1 ? 'is' : (nots.includes(word.word[1]) && word.word[1] !== not_1 ? 'almost' : '')}" value={word.word[1]} />
					<input readonly class="{word.word[2] === is_2 ? 'is' : (nots.includes(word.word[2]) && word.word[2] !== not_2 ? 'almost' : '')}" value={word.word[2]} />
					<input readonly class="{word.word[3] === is_3 ? 'is' : (nots.includes(word.word[3]) && word.word[3] !== not_3 ? 'almost' : '')}" value={word.word[3]} />
					<input readonly class="{word.word[4] === is_4 ? 'is' : (nots.includes(word.word[4]) && word.word[4] !== not_4 ? 'almost' : '')}" value={word.word[4]} />
				</div>
				{#if isWordUsed(word.word) }
					<svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
						<path stroke-linecap="round" stroke-linejoin="round" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
					</svg>
				{/if}
			</div>
			{:else if showAll || i === 10}
				<label on:click={() => showAll = !showAll}>Click to show remaining {couldBe.length} options</label>
			{/if}
		{/each}
		{#if showAll}
			<label on:click={() => showAll = !showAll}>Click to only show top 10</label>
		{/if}
	</div>
</main>

<style>
	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}

	main {
		text-align: center;
		padding: 0 1em;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 3em;
		font-weight: 100;
	}

	label {
		padding: 0.5em 0 0.125em 0;
		font-size: small;
	}

	input {
		border: none;
		text-align: center;
		padding: 0.5em;
	}

	.subtitle {
		font-size: smaller;
		margin-top: -2em;
	}

	.highlight {
		border-bottom: 1px solid #96CEB4;
	}

	.results {
		margin-top: 1em;
		display: flex;
		justify-content: space-around;
		flex-direction: column;
	}

	.results>div {
		display: flex;
		justify-content: center;
	}

	.results input {
		width: 2em;
	}

	.results svg {
		width: 1.5em;
		margin: -.5em -2em 0 .5em;
	}

	.is {
		width: 2em;
		background-color: #96CEB4;
	}
	.almost {
		width: 4em;
		background-color: #FFEEAD;
	}
	.not {
		background-color: #9D9D9D;
	}
</style>
