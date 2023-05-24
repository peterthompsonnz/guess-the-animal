<!-- App.svelte -->
<script>
  import { animals } from './lib/animalNames.js'

  let selectedAnimal = ''
  let guessCount = 0
  let guessLimit = 7
  let incorrectGuesses = []
  let correctGuesses = []
  let guessInput = ''
  let isGameOver = false
  let selectedAnimals = []

  $: goesLeft = guessLimit - guessCount

  function startGame() {
    // Select an animal and make sure it has not already been used in this game session
    selectedAnimal = animals[Math.floor(Math.random() * animals.length)]
    while (selectedAnimals.includes(selectedAnimal)) {
      selectedAnimal = animals[Math.floor(Math.random() * animals.length)]
    }
    selectedAnimals.push(selectedAnimal)
    guessCount = 0
    guessInput = ''
    incorrectGuesses = []
    correctGuesses = Array(selectedAnimal.length).fill('_')
    isGameOver = false
  }

  function guessLetter() {
    if (isGameOver) return
    if (
      !guessInput.length ||
      incorrectGuesses.includes(guessInput) ||
      correctGuesses.includes(guessInput)
    )
      return

    if (selectedAnimal.includes(guessInput)) {
      for (let i = 0; i < selectedAnimal.length; i++) {
        if (selectedAnimal[i] === guessInput) {
          correctGuesses[i] = guessInput
        }
      }
      if (correctGuesses.join('') === selectedAnimal) {
        isGameOver = true
      }
    } else {
      // Have to reassign array name to an altered copy of the array to get reactivity to work
      incorrectGuesses = [...incorrectGuesses, guessInput]
      guessCount++
      if (guessCount === guessLimit) {
        isGameOver = true
      }
    }
    guessInput = ''
  }

  function init(el) {
    el.focus()
  }
</script>

<header>
  <h1>Guess the animal's<br />name</h1>
  <img src="robot.png" alt="" />
</header>

<main>
  {#if !selectedAnimal}
    <button on:click={startGame}>Start Game</button>
  {:else}
    <p>You have {guessLimit} goes to guess the name.</p>
    <p>Goes left: <span class="goes-left">{goesLeft}</span></p>
    <p>Animal: <span class="correct">{correctGuesses.join('')}</span></p>
    <p>
      Incorrect Guesses: <span class="incorrect"
        >{incorrectGuesses.join(',')}</span
      >
    </p>
    {#if isGameOver}
      {#if correctGuesses.join('') === selectedAnimal}
        <p>Congratulations, you guessed the animal!</p>
      {:else}
        <p>
          Game Over, you used all your guesses. The animal was: <span
            class="incorrect">{selectedAnimal}</span
          >.
        </p>
      {/if}
      <button on:click={startGame}>Play Again</button>
    {:else}
      <div>
        <label for="guess-input">Guess a letter:</label>
        <input
          type="text"
          id="guess-input"
          size="3"
          bind:value={guessInput}
          on:keyup={guessLetter}
          use:init
        />
        <button on:click={guessLetter}>Guess</button>
      </div>
    {/if}
  {/if}
</main>

<style>
  input[type='text'] {
    display: inline-block;
    margin-left: 0.25rem;
    margin-bottom: 1rem;
    font: inherit;
    border-radius: 8px;
    border: 2px solid #6eaafd;
    padding: 0 0.15rem;
    line-height: 0;
  }
  h1 {
    font-size: 1.85rem;
    margin-bottom: 1rem;
    font-weight: 400;
    text-transform: uppercase;
    line-height: 1.4;
    color: #6eaafd;
  } 
  button {
    display: inline-block;
    font: inherit;
    line-height: 1.2;
    border-radius: 8px;
    border: 2px solid #c6d7f9;
    padding: 0.2rem 0.5rem;
    color: #09081a;
    background-color: #6eaafd;   
    cursor: pointer;
    transition: border-color 0.25s;
  }
  button:hover {
    border-color: #6eaafd;
  }
  button:focus,
  button:focus-visible {
    outline: 4px auto -webkit-focus-ring-color;
  }
  .incorrect {
    color: #6eaafd;
  }
  .correct,
  .incorrect,
  .goes-left {
    display: inline-block;
    margin-left: 0.5rem;
    letter-spacing: 0.1rem;
    font-size: 1.2em;
  }
  .correct {
    color: #6eaafd;
    letter-spacing: 0.3rem;
  }
  .goes-left {
    color: #6eaafd;
  }
</style>
