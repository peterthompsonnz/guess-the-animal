<!-- App.svelte -->
<script>
  const animals = [
    'lion',
    'tiger',
    'elephant',
    'giraffe',
    'zebra',
    'cheetah',
    'hippopotamus',
    'rhino',
    'kangaroo',
    'koala',
    'panda',
    'gorilla',
    'monkey',
    'leopard',
    'crocodile',
    'alligator',
    'snake',
    'turtle',
    'ostrich',
    'flamingo',
    'penguin',
    'dolphin',
    'whale',
    'shark',
    'octopus',
    'seahorse',
    'jellyfish',
    'starfish',
    'crab',
    'lobster',
    'squid',
    'butterfly',
    'bee',
    'ladybug',
    'ant',
    'grasshopper',
    'caterpillar',
    'spider',
    'scorpion',
    'bat',
    'owl',
    'eagle',
    'falcon',
    'hawk',
    'parrot',
    'hummingbird',
    'pigeon',
    'sparrow',
    'robin',
    'pelican',
    'cow',
    'horse',
    'sheep',
    'goat',
    'pig',
    'dog',
    'cat',
    'rabbit',
    'mouse',
    'rat',
    'hamster',
    'guineapig',
    'chimpanzee',
    'sloth',
    'squirrel',
    'polarbear',
  ]

  let selectedAnimal = ''
  let guessCount = 0
  let guessLimit = 5
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
    correctGuesses = Array(selectedAnimal.length).fill('*')
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

<header><h1>Guess the animal's name</h1></header>

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
          Game Over, you used all your guesses. The animal was <span
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
    border: 1px solid #ccc;
    padding: 0 0.15rem;
    line-height: 0;
  }
  h1 {
    font-size: 2rem;
    line-height: 1.1;
    color: #64c6ff;
  }
  button {
    display: inline-block;
    font: inherit;
    line-height: 1.2;
    border-radius: 8px;
    border: 1px solid #ccc;
    padding: 0.1rem 0.5rem;
    color: #1b1b1b;
    background-color: #64c6ff;
    margin-left: 1rem;
    cursor: pointer;
    transition: border-color 0.25s;
  }
  button:hover {
    border-color: #646cff;
  }
  button:focus,
  button:focus-visible {
    outline: 4px auto -webkit-focus-ring-color;
  }
  .correct {
    color: limegreen;
  }
  .incorrect {
    color: orangered;
  }
  .correct,
  .incorrect,
  .goes-left {
    letter-spacing: 0.1rem;
    font-weight: bold;
  }
  .goes-left {
    color: #64c6ff;
  }
</style>
