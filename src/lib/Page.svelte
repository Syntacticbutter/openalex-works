<script>
  import { onMount } from "svelte";
  import axios from "axios";

  let page = 1; // Initial page number
  const pageSize = 10;
  let items = []; // Array to store fetched items
  const ror = "04w4pwd42"; // Research Organization Registry ID of President University (https://ror.org/04w4pwd42)

  let searchTerm = "";
  const fetchData = async () => {
    try {
      let url = `https://api.openalex.org/works?page=${page}&per-page=${pageSize}&filter=institutions.ror:${ror}&sort=publication_date:desc`;

      // Append search term to URL if searchTerm is not empty
      if (searchTerm.trim() !== "") {
        url += `&search=${encodeURIComponent(searchTerm.trim())}`;
      }

      console.log(url);
      const response = await axios.get(url);
      items = response.data.results; // Assuming API response has a 'results' property containing an array
      console.log("GET request successful:", items);
      console.log(response.data);
      console.log(response.data.results);
    } catch (error) {
      console.error("There was a problem with the GET request:", error.message);
    }
  };

  onMount(fetchData); // Fetch initial data when component mounts

  async function nextPage() {
    page += 1;
    await fetchData(); // Fetch data for the next page
  }

  async function prevPage() {
    page -= 1;
    await fetchData(); // Fetch data for the previous page
  }

  function handleSearchInput(event) {
    searchTerm = event.target.value;
    // Reset page to 1 when search term changes to fetch from the beginning
    page = 1;
    fetchData(); // Fetch data with updated search term
  }
</script>

<main>
  <div>
    <input
      type="text"
      bind:value={searchTerm}
      placeholder="Title, abstract, or fulltext"
      on:input={handleSearchInput}
    />
    <table>
      <thead>
        {#if items.length == 0}
          <tr>
            <th></th>
          </tr>
        {:else}
          <tr>
            <th>
              <h1 style="text-align: left;">Title</h1>
            </th>
            <th>
              <h1 style="text-align: right;">Concepts</h1>
            </th>
          </tr>
        {/if}
      </thead>
      {#each items as work}
        <tbody>
          <tr class="separator">
            <td>
              <h2 style="text-align: center;">{work.title}</h2>

              <h3 class="subhead">
                Published on <span style="color: white; font-weight: normal"
                  >{work.publication_date}</span
                >
              </h3>

              <h3 class="subhead">
                Is open access?
                {#if work.open_access.is_oa === true}
                  <span class="true">{work.open_access.is_oa}</span>
                {:else}
                  <span class="false">{work.open_access.is_oa}</span>
                {/if}
              </h3>

              <h3 class="subhead">
                Open access status
                {#if work.open_access.oa_status === "gold"}
                  <span class="gold">{work.open_access.oa_status}</span>
                {:else if work.open_access.oa_status === "green"}
                  <span class="green">{work.open_access.oa_status}</span>
                {:else if work.open_access.oa_status === "hybrid"}
                  <span class="hybrid">{work.open_access.oa_status}</span>
                {:else if work.open_access.oa_status === "bronze"}
                  <span class="bronze">{work.open_access.oa_status}</span>
                {:else}
                  <span class="false">{work.open_access.oa_status}</span>
                {/if}
              </h3>

              <h3 class="subhead">Authors</h3>
              {#each work.authorships as author}
                <ul class="list">
                  <li>
                    {author.author.display_name}
                  </li>
                </ul>
              {/each}

              <h3 class="subhead">Topics</h3>
              {#each work.topics as topic}
                <ul class="list">
                  <li>
                    {topic.display_name}
                  </li>
                </ul>
              {/each}

              <div class="links" style="text-align: left;">
                <p>
                  <a href={work.ids.doi} target="_blank">
                    {work.ids.doi || ""}
                  </a>
                </p>

                <p>
                  <a href={work.ids.openalex} target="_blank">
                    {work.ids.openalex || ""}
                  </a>
                </p>

                <p>
                  <a href={work.ids.pmid} target="_blank">
                    {work.ids.pmid || ""}
                  </a>
                </p>
              </div>
            </td>

            <td style="text-align: right;">
              {#each work.concepts as concept}
                <p>
                  <span class="tag">
                    {concept.display_name}
                  </span>
                </p>
              {/each}
            </td>
          </tr>
        </tbody>
      {/each}
      <tfoot></tfoot>
    </table>
    <div
      style="display: flex; justify-content: space-between; padding: 1rem; position: sticky; bottom: 0"
    >
      {#if page == 1}
        <button style="visibility: hidden;"></button>
      {:else}
        <button on:click={prevPage}>Prev</button>
      {/if}

      {#if items.length < pageSize}
        <button style="visibility: hidden;"></button>
      {:else}
        <button on:click={nextPage}>Next</button>
      {/if}
    </div>
  </div>
</main>

<style>
  table,
  th,
  td {
    border-collapse: collapse;
  }

  .subhead {
    text-align: left;
    margin-left: 2rem;
    color: gray;
  }

  .list {
    text-align: left;
    color: white;
  }

  .links {
    display: flex;
    justify-content: space-evenly;
    /* border: 1px solid white; */
  }

  .tag {
    background-color: midnightblue;
    border-radius: 10px;
    padding-left: 0.5rem;
    padding-right: 0.5rem;
    padding-top: 0.25rem;
    padding-bottom: 0.25rem;
  }

  .true {
    background-color: white;
    color: black;
    text-transform: uppercase;
    border-radius: 10px;
    padding-left: 0.5rem;
    padding-right: 0.5rem;
    padding-top: 0.25rem;
    padding-bottom: 0.25rem;
  }

  .false {
    background-color: black;
    color: white;
    text-transform: uppercase;
    border-radius: 10px;
    padding-left: 0.5rem;
    padding-right: 0.5rem;
    padding-top: 0.25rem;
    padding-bottom: 0.25rem;
  }

  .gold {
    background: #fdc830; /* fallback for old browsers */
    background: -webkit-linear-gradient(
      to right,
      #f37335,
      #fdc830
    ); /* Chrome 10-25, Safari 5.1-6 */
    background: linear-gradient(
      to right,
      #f37335,
      #fdc830
    ); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
    color: #111111;
    text-transform: uppercase;
    border-radius: 10px;
    padding-left: 0.5rem;
    padding-right: 0.5rem;
    padding-top: 0.25rem;
    padding-bottom: 0.25rem;
  }

  .green {
    background: #dce35b; /* fallback for old browsers */
    background: -webkit-linear-gradient(
      to right,
      #45b649,
      #dce35b
    ); /* Chrome 10-25, Safari 5.1-6 */
    background: linear-gradient(
      to right,
      #45b649,
      #dce35b
    ); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
    color: #111111;
    text-transform: uppercase;
    border-radius: 10px;
    padding-left: 0.5rem;
    padding-right: 0.5rem;
    padding-top: 0.25rem;
    padding-bottom: 0.25rem;
  }

  .hybrid {
    background: #8e2de2; /* fallback for old browsers */
    background: -webkit-linear-gradient(
      to right,
      #4a00e0,
      #8e2de2
    ); /* Chrome 10-25, Safari 5.1-6 */
    background: linear-gradient(
      to right,
      #4a00e0,
      #8e2de2
    ); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
    color: white;
    text-transform: uppercase;
    border-radius: 10px;
    padding-left: 0.5rem;
    padding-right: 0.5rem;
    padding-top: 0.25rem;
    padding-bottom: 0.25rem;
  }

  .bronze {
    background: #d1913c; /* fallback for old browsers */
    background: -webkit-linear-gradient(
      to right,
      #ffd194,
      #d1913c
    ); /* Chrome 10-25, Safari 5.1-6 */
    background: linear-gradient(
      to right,
      #ffd194,
      #d1913c
    ); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
    color: #111111;
    text-transform: uppercase;
    border-radius: 10px;
    padding-left: 0.5rem;
    padding-right: 0.5rem;
    padding-top: 0.25rem;
    padding-bottom: 0.25rem;
  }

  .separator {
    border-top: 1px gray solid;
    border-bottom: 1px gray solid;
  }
</style>
