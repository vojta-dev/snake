<script>
  import Square from './Square.svelte';

  const GRID_SIZE = 30;

  let grid = Array(GRID_SIZE)
    .fill(null)
    .map(() => Array(GRID_SIZE).fill('empty'));

  let snake = [[Math.floor(GRID_SIZE / 2), Math.floor(GRID_SIZE / 2)]];
  let direction = [1, 0];
  let food;
  spawnNewFood();

  let interval = setInterval(() => {
    snake = [[snake[0][0] + direction[0], snake[0][1] + direction[1]], ...snake];

    if (isSnakeHeadOn(food)) {
      spawnNewFood();
    } else {
      snake = snake.slice(0, -1);
    }

    const snakeHeadSquare = gridWithSnakeAndFood[snake[0][0]]?.[snake[0][1]];
    if (snakeHeadSquare !== 'empty' && snakeHeadSquare !== 'food') {
      clearInterval(interval);
      alert('You died!');
      location.reload();
    }
  }, 100);

  $: gridWithSnakeAndFood = grid.map((row, i) => {
    return row.map((square, j) => {
      if (snake.some(([x, y]) => x === i && y === j)) return 'snake';
      if (food[0] === i && food[1] === j) return 'food';
      return square;
    });
  });

  function spawnNewFood() {
    const randomNum = () => Math.floor(Math.random() * GRID_SIZE);
    food = [randomNum(), randomNum()];
  }

  function isSnakeHeadOn(position) {
    return snake[0][0] === position[0] && snake[0][1] === position[1];
  }

  function handleKeydown(event) {
    switch (event.key) {
      case 'ArrowDown':
        direction = [1, 0];
        break;
      case 'ArrowUp':
        direction = [-1, 0];
        break;
      case 'ArrowRight':
        direction = [0, 1];
        break;
      case 'ArrowLeft':
        direction = [0, -1];
        break;
    }
  }
</script>

<svelte:window on:keydown={handleKeydown} />

<main>
  <div id="snake" style="grid-template: repeat({GRID_SIZE}, 1.5rem) / repeat({GRID_SIZE}, 1.5rem)">
    {#each gridWithSnakeAndFood.flat() as type, i (i)}
      <Square {type} />
    {/each}
  </div>
</main>

<style>
  main {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100%;
    background-color: #94a3b8;
  }

  #snake {
    display: grid;
    gap: 1px;
  }

  :global(body) {
    margin: 0;
    height: 100vh;
  }
</style>
