<script lang="ts">
  import {WordList} from './words';

  const words: string[] = WordList.words;
	let incorrect: string = '';

	let correct_0: string = '';
	let correct_1: string = '';
	let correct_2: string = '';
	let correct_3: string = '';
	let correct_4: string = '';

	let incorrect_0: string = '';
	let incorrect_1: string = '';
	let incorrect_2: string = '';
	let incorrect_3: string = '';
	let incorrect_4: string = '';

	let letterMap: number[] = [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0];
	words.forEach(w => w.split('').forEach(l=>letterMap[l.charCodeAt(0) - 97]++));

	export let couldBe: string[] = [];
	export let wordScores: {word: string, score: number}[] = [];
	export let hasSearched: boolean = false;
	export let alphabetic: boolean = false;
	export const bestWord: string = findBestWord(words)[0].word;

	function lookFor(){
		hasSearched = true;
		const c: string[] = (correct_0 + correct_1 + correct_2 + correct_3 + correct_4 + incorrect_0 + incorrect_1 + incorrect_2 + incorrect_3 + incorrect_4).split('');
		const ic: string[] = incorrect.split('');

		couldBe = words
			.filter(word => {
				if ( c.every(l => word.includes(l)) && !ic.some(l => word.includes(l)) ) {
					if (correct_0 !== '' && word[0] !== correct_0) return false;
					if (correct_1 !== '' && word[1] !== correct_1) return false;
					if (correct_2 !== '' && word[2] !== correct_2) return false;
					if (correct_3 !== '' && word[3] !== correct_3) return false;
					if (correct_4 !== '' && word[4] !== correct_4) return false;

					if (incorrect_0 !== '' && incorrect_0.includes(word[0])) return false;
					if (incorrect_1 !== '' && incorrect_1.includes(word[1])) return false;
					if (incorrect_2 !== '' && incorrect_2.includes(word[2])) return false;
					if (incorrect_3 !== '' && incorrect_3.includes(word[3])) return false;
					if (incorrect_4 !== '' && incorrect_4.includes(word[4])) return false;
					return true;
				}
				return false;
		});

		if (couldBe.length === words.length) {
			couldBe = [];
			hasSearched = false;
		}

		if (!alphabetic) {
			letterMap = [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0];
			couldBe.forEach(w => w.split('').forEach(l=>letterMap[l.charCodeAt(0) - 97]++));
			wordScores = findBestWord(couldBe);
		}
	}

	function findBestWord(wordlist: string[]){
			return wordlist.map(w => {
				let score = 0;
				for (const l of w) {
					if (w.match(new RegExp(l, 'g')).length === 1)
						score += letterMap[l.charCodeAt(0) - 97];
					else
						score -= letterMap[l.charCodeAt(0) - 97];
				}
				return {word: w, score: score};
			}).sort((a,b) => a.score < b.score ? 1 : -1);

	}

  function lowercase(node: HTMLInputElement): void {
    node.addEventListener('input', () => node.value = node.value.toLowerCase(), { capture: true })
  }
</script>

<main>
	<h1>Word Finder</h1>

	<button on:click="{() => alphabetic = !alphabetic}">Sort { alphabetic ? 'Ranked' : 'Alphabetically'}</button>

	<div>
		<label for="correct">Correctly Placed Letters</label>
		<input id="correct" type='text' maxlength="1" class="not" use:lowercase bind:value={correct_0} on:keyup={lookFor}/>
		<input id="correct" type='text' maxlength="1" class="not" use:lowercase bind:value={correct_1} on:keyup={lookFor}/>
		<input id="correct" type='text' maxlength="1" class="not" use:lowercase bind:value={correct_2} on:keyup={lookFor}/>
		<input id="correct" type='text' maxlength="1" class="not" use:lowercase bind:value={correct_3} on:keyup={lookFor}/>
		<input id="correct" type='text' maxlength="1" class="not" use:lowercase bind:value={correct_4} on:keyup={lookFor}/>
	</div>

	<div>
		<label for="incorrect">Correct Letters not In Position</label>
		<input id="incorrect" type='text' maxlength="4" class="almost" use:lowercase bind:value={incorrect_0} on:keyup={lookFor}/>
		<input id="incorrect" type='text' maxlength="4" class="almost" use:lowercase bind:value={incorrect_1} on:keyup={lookFor}/>
		<input id="incorrect" type='text' maxlength="4" class="almost" use:lowercase bind:value={incorrect_2} on:keyup={lookFor}/>
		<input id="incorrect" type='text' maxlength="4" class="almost" use:lowercase bind:value={incorrect_3} on:keyup={lookFor}/>
		<input id="incorrect" type='text' maxlength="4" class="almost" use:lowercase bind:value={incorrect_4} on:keyup={lookFor}/>
	</div>

	<div>
		<label for="inCorrectLetters">Incorrect letters</label>
		<input type='text' use:lowercase bind:value={incorrect} on:keyup={lookFor}/>
	</div>

	<div>
		{#if hasSearched && !couldBe.length}
			Could not find a match in word list.
		{:else if couldBe.length}
			{couldBe.length} options of {words.length} remain.
		{:else}
			There are {words.length} words in the list.<br>Best guess based on scores is {bestWord}
		{/if}
	</div>
	<table>
		{#if alphabetic}
			{#each couldBe as word}
				<tr>
					<td>{ word}</td>
				</tr>
			{/each}
		{:else}
			{#each wordScores as word}
				<tr>
					<td>{word.word}</td>
				</tr>
			{/each}
		{/if}
	</table>
</main>

<style>
	.not {
		width: 2em;
	}
	.almost {
		width: 4em;
	}
	label {
		padding: 0.5em;
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

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>