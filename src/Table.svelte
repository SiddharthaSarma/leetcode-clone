<script>
  import { onMount } from "svelte";
  import Loader from "./components/Loader.svelte";
  import Pagination from "./components/Pagination.svelte";

  let list = [];
  let sort = {
    sortId: "frontendQuestionId",
    sortVal: 0 // 0 - ascending  1 - descending
  };
  let pageSize = 25;
  const tableColumns = {
    frontendQuestionId: "#",
    title: "Title",
    likes: "Likes",
    dislikes: "Dislikes",
    difficulty: "Difficulty"
  };

  function optimizeQuestionList(questions) {
    let tempData = [];
    let questionsSlugs = [];
    tempData = questions.map(question => {
      let { stat } = question;
      return {
        difficulty: question.difficulty.level,
        frontendQuestionId: stat.frontend_question_id,
        isPaid: question.isPaid,
        title: stat.question__title,
        titleSlug: stat.question__title_slug
      };
    });
    list = sortList(tempData, sort.sortId, sort.sortVal);
  }

  function sortList(list, key, order) {
    return list.sort((a, b) => {
      if (order) {
        return b[key] - a[key];
      }
      return a[key] - b[key];
    });
  }

  async function fetchQuestions() {
    const questions = await fetch("http://localhost:8050").then(r => r.json());
    optimizeQuestionList(questions.stat_status_pairs);
  }

  function handlePageSizeChanged(event) {
    pageSize = event.detail.pageSize;
  }

  function handleSort(columns) {
    if (sort.sortId === columns[0]) {
      sort.sortVal = !sort.sortVal;
    } else {
      sort.sortVal = 0;
    }
    sort.sortId = columns[0];
    list = sortList(list, sort.sortId, sort.sortVal);
  }

  onMount(fetchQuestions);
</script>

<style>
  .pagination-content {
    background: #f5f5f5;
  }
  table th {
    cursor: pointer;
  }
</style>

<div class="row">
  <table class="table table-striped">
    <thead>
      <tr>
        {#each Object.entries(tableColumns) as columns}
          <th on:click={() => handleSort(columns)}>
            {columns[1]}
            {#if columns[0] == sort.sortId}
              {#if sort.sortVal == 0}
                <i class="fa fa-fw fa-sort-asc" />
              {:else}
                <i class="fa fa-fw fa-sort-desc" />
              {/if}
            {/if}
          </th>
        {/each}
      </tr>
    </thead>
    {#if !list.length}
      <div class="text-center">
        <Loader />
      </div>
    {:else}
      <tbody>
        {#each list.slice(0, pageSize) as question}
          <tr>
            <td>{question.frontendQuestionId}</td>
            <td>
              <a
                href="https://leetcode.com/problems/{question.titleSlug}"
                target="_blank">
                {question.title}
              </a>
            </td>
            <td>100</td>
            <td>200</td>
            <td>
              {#if question.difficulty == 1}
                <span class="label label-success round">Easy</span>
              {:else if question.difficulty == 2}
                <span class="label label-warning round">Medium</span>
              {:else}
                <span class="label label-danger round">Hard</span>
              {/if}
            </td>
          </tr>
        {/each}
        <tr class="pagination-content">
          <td colspan="5">
            <Pagination
              count={list.length}
              on:pageSizeChange={handlePageSizeChanged} />
          </td>
        </tr>
      </tbody>
    {/if}
  </table>
</div>
