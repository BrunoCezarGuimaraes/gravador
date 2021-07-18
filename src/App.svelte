<script>
  let gravando = false;
  let gravacoes = [];
  let cont = 0;
  let mediaRecorder = null;

  navigator.mediaDevices.getUserMedia({ audio: true }).then((stream) => {
    mediaRecorder = new MediaRecorder(stream);

    let chunks = [];
    mediaRecorder.ondataavailable = function (e) {
      chunks.push(e.data);
    };

    mediaRecorder.onstop = function (e) {
      const blob = new Blob(chunks, { type: "audio/ogg; codecs=opus" });
      chunks = [];
      const audioURL = URL.createObjectURL(blob);
      gravacoes = [...gravacoes, { id: ++cont, audioURL: audioURL }];
    };
  });

  function iniciaGravacao() {
    gravando = true;
    mediaRecorder.start();
  }

  function paraGravacao() {
    gravando = false;
    mediaRecorder.stop();
  }

  function excluiGravacao(gravacao) {
    gravacoes = gravacoes.filter((item) => item !== gravacao);
  }
</script>

<main>
  <div class="back">
    <h1>Gravador de áudio</h1>
    <div class="buttons">
      <button disabled={gravando} on:click={iniciaGravacao}>Iniciar</button>
      <button disabled={!gravando} on:click={paraGravacao}>Parar</button>
      {#if gravando}
        <span class="gravando">Gravando</span>
      {:else}
        <span>Aguardando</span>
      {/if}
    </div>
    <div>
      {#each gravacoes as gravacao}
        <div class="gravacao buttons">
          <span>Gravação {gravacao.id}</span>
          <!-- svelte-ignore a11y-media-has-caption -->
          <audio controls src={gravacao.audioURL} />
          <button on:click={() => excluiGravacao(gravacao)}>Excluir</button>
        </div>
      {/each}
    </div>
  </div>
</main>

<style>
  main {
    display: flex;
    flex-direction: column;
    align-items: center;
    font-family: Arial, Helvetica, sans-serif;
    border-radius: 10rem;
    color: #fff;
  }
  .back {
    background-color: rgb(140, 73, 248);
    margin-top: 10px;
  }
  h1 {
    margin: 1rem;
  }
  .buttons {
    margin: 1rem;
  }
  .buttons > * {
    margin-right: 0.5em;
    border-radius: 5px;
  }
  .gravando {
    color: rgb(100, 0, 0);
    font-weight: bold;
    animation: piscar 1s linear infinite;
  }
  .gravacao {
    margin: 1rem;
    display: flex;
    align-items: center;
  }
  audio {
    margin: 0 1rem;
  }
  @keyframes piscar {
    50% {
      color: red;
    }
  }
</style>
