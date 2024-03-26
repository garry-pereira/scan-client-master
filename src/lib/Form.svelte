<script>
  let searchUPC = ''
  let results = {}
  let upc
  let updateStatus

  async function handleSearch() {
    if (searchUPC == null) {
      console.log('fill out form')
      return
    }
    // Send request to the server with searchTerm
    const response = await fetch('http://localhost:3000/', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ UPC: searchUPC })
    })
    
    // Assuming server responds with JSON
    const data = await response.json()
    console.log(data)
    console.log(typeof data)
    results = data
    upc = results.UPC
  }

  async function updateItem() {
    // make sure the UPC isn't changing
    if (results.UPC !== searchUPC) {
      console.log('cannot change UPC')
      return
    }

    // Send request to the server with searchTerm
    const response = await fetch('http://localhost:3000/update', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(results)
    })
    
    // Assuming server responds with JSON
    const data = await response.json();
    console.log(data)
    console.log(typeof data)
    if (data.err) {
      updateStatus = data.err
    } else {
      results = data
      upc = results.UPC
      updateStatus = 'Update success'
    }
  }
</script>

<main>
  <p><strong>search</strong></p>
  <form class="search-class-form" on:submit|preventDefault={handleSearch}>
    <label>
      UPC:
      <input type="text" bind:value={searchUPC}>
    </label>
    <button type="submit">검색</button>
  </form>
  {#if Object.keys(results).length === 0}
    <div>search by inputting UPC</div>
  {:else if Object.keys(results).length === 1}
    <div>Error: {results.err}</div>
  {:else}
    <!-- make it so that this is actually a form that gets displayed -->
    <div>
      <h3>update</h3>
      <form class="update-class-form" on:submit|preventDefault={updateItem}>
        <label>UPC:<input type="text" bind:value={results.UPC}></label> <br/>
        <label>Price:<input type="text" bind:value={results.Price}></label> <br/>
        <label>Name:<input type="text" bind:value={results.Name}></label> <br/>
        <label>OnHand:<input type="text" bind:value={results.OnHand}></label> <br/>
        <label>Cost:<input type="text" bind:value={results.Cost}></label> <br/>
        <button type="submit">업데이트</button>
      </form>
      {#if updateStatus}
        <div>{updateStatus}</div>
      {:else}
        <div></div>
      {/if}
    </div>
  {/if}
  </main>

<style>
  main {
    display: flex;
    flex-direction: column;
    justify-content: space-around;
  }
  .update-class-form {
    display: flex;
    flex-direction: column;
    margin-top: 5em;
    align-items: center;
  }
  .search-class-form {
  }
  label {
    border: black 2px solid;
    border-radius: 2em;
    font-size: 1.5rem;
    padding: 0.5em 1em;
  }
  input {
    background: #242424;
    font-size: 1.5rem;
    padding-left: 1em;
  }
</style>