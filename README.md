# Start

```js
git-clone https://github.com/Syntacticbutter/openalex-works.git
```

## Pages

```js
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
```

## Pagination

```js
async function nextPage() {
  page += 1;
  await fetchData(); // Fetch data for the next page
}

async function prevPage() {
  page -= 1;
  await fetchData(); // Fetch data for the next page
}
```

## Search across titles, abstracts, and fulltext

```js
function handleSearchInput(event) {
  searchTerm = event.target.value;
  // Reset page to 1 when search term changes to fetch from the beginning
  page = 1;
  fetchData(); // Fetch data with updated search term
}
```
