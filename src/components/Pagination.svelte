<script>
  import { createEventDispatcher } from "svelte";

  const dispatch = createEventDispatcher();
  let selected = 25;

  function handlePageSizeChange() {
    dispatch("pageSizeChange", { pageSize: +selected });
  }

  export let count;
</script>

<style>
  .form-control {
    display: inline-block;
    width: 10%;
    vertical-align: top;
    margin: 20px 0;
  }
  .pagination {
    float: right;
  }
</style>

<select
  class="form-control"
  bind:value={selected}
  on:change={handlePageSizeChange}>
  <option value="25">25</option>
  <option value="50">50</option>
  <option value="100">100</option>
  <option value="200">200</option>
  <option value={count}>all</option>
</select>
<ul class="pagination">
  <li>
    <a href="#" aria-label="Previous">
      <span aria-hidden="true">&laquo;</span>
    </a>
  </li>
  <li class="active">
    <a href="#">1</a>
  </li>
  <li>
    <a href="#">2</a>
  </li>
  <li>
    <a href="#" aria-label="Next">
      <span aria-hidden="true">&raquo;</span>
    </a>
  </li>
</ul>
