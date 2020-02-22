<script>
  let search;

  function handleChange() {
    console.log(search);
  }
</script>

<style>
  .search-bar-control {
    margin-bottom: 20px;
  }
  .list-search-bar {
    border-radius: 1020px;
  }
</style>

<div class="row search-bar-control">
  <div class="col-sm-6 col-lg-7">
    <input
      type="text"
      class="form-control list-search-bar"
      placeholder="Search question titles, description or IDs"
      bind:value={search}
      on:change={handleChange} />
  </div>
</div>
