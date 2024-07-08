<script>
  import presunivLogo from "./assets/PU.png";
  import oaLogo from "./assets/openalex_icon.png";
  import Page from "./lib/Page.svelte";
</script>

<main>
  <a href="https://openalex.org" target="_blank" rel="noreferrer">
    <img src={oaLogo} class="logo openalex" alt="Openalex Logo" />
  </a>
  <a href="http://repository.president.ac.id/" target="_blank" rel="noreferrer">
    <img
      src={presunivLogo}
      class="logo presuniv"
      alt="President University Logo"
    />
  </a>

  <div>
    <Page />
  </div>
</main>

<style>
  .logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
    transition: filter 300ms;
  }
  .logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
  }
  .logo.openalex:hover {
    filter: drop-shadow(0 0 2em white);
  }
  .logo.presuniv:hover {
    filter: drop-shadow(0 0 2em blue);
  }
</style>
