<script lang="ts">
  import { onMount } from "svelte";
  interface Meme {
    id: number;
    name: string;
    url: string;
  }

  let todos: { text: string; completed: boolean }[] = [];
  let memes: Meme[] = [];
  let text = "";
  $: targetMemes = !text
    ? memes
    : memes.filter((meme: Meme) => {
        return meme.name.toLowerCase().indexOf(text) > -1;
      });
  onMount(() => {
    fetch("https://api.imgflip.com/get_memes")
      .then((response) => response.json())
      .then((data) => {
        console.log({ data });
        memes = data.data.memes;
      });
  });
</script>

<form
  on:submit|preventDefault={() => {
    todos = [{ text, completed: false }, ...todos];
    text = "";
  }}
>
  <input bind:value={text} placeholder='Search'/>
</form>

<ul>
  {#each todos as todo}
    <li
      class:complete={todo.completed}
      on:click={() => (todo.completed = !todo.completed)}
    >
      {todo.text}
    </li>
  {/each}
</ul>

<div>
  {#each targetMemes as meme}
    <div>
      <img src={meme.url} alt={meme.name} class="meme-img" />
      <div class="memedata">
        <p>{meme.name}</p>
        <a class="memeBtn" href={meme.url}>link</a>
      </div>
      <div class="generate-section">
        <a
          type="button"
          class="button memeBtn"
          href={`https://imgflip.com/memegenerator/${meme.id}`}>configure</a
        >
      </div>
    </div>
  {/each}
</div>

<style>
  .generate-section {
    display: flex;
    justify-content: center;
  }
  .complete {
    text-decoration: line-through;
  }
  .memedata {
    display: flex;
    justify-content: space-between;
  }
  .memeBtn {
    margin: 10px 0px;
  }
  .meme-img {
    padding: 20px;
    display: block;
    margin-left: auto;
    margin-right: auto;
    width: 50%;
  }
</style>
