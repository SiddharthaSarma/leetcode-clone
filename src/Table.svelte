<script>
  import { onMount } from "svelte";
  import Loader from "./components/Loader.svelte";
  import Pagination from "./components/Pagination.svelte";

  let list = [];
  let pageSize = 25;
  function optimizeQuestionList(questions) {
    list = questions
      .map(question => {
        let { stat } = question;
        return {
          difficulty: question.difficulty.level,
          frontendQuestionId: stat.frontend_question_id,
          isPaid: question.isPaid,
          title: stat.question__title,
          titleSlug: stat.question__title_slug
        };
      })
      .sort((a, b) => a.frontendQuestionId - b.frontendQuestionId);
  }
  async function fetchQuestions() {
    const questions = await fetch(
      "https://leetcode.com/api/problems/all/"
    ).then(r => r.json());
    optimizeQuestionList(questions.stat_status_pairs);
  }

  function handlePageSizeChanged(event) {
    pageSize = event.detail.pageSize;
    console.log(pageSize);
  }

  onMount(fetchQuestions);
</script>

<style>
  .pagination-content {
    background: #f5f5f5;
  }
</style>

<div class="row">
  <table class="table table-striped">
    <thead>
      <tr>
        <th>#</th>
        <th>Title</th>
        <th>Likes</th>
        <th>Dislikes</th>
        <th>Difficulty</th>
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
