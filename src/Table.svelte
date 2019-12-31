<script>
  import { onMount } from "svelte";
  import Loader from "./components/Loader.svelte";
  import Pagination from "./components/Pagination.svelte";
  let list = [];
  function optimizeQuestionList(questions) {
    list = questions
      .map(question => {
        let { stat } = question;
        return {
          frontendQuestionId: stat.frontend_question_id,
          isPaid: question.isPaid,
          title: stat.question__title,
          titleSlug: stat.question__title_slug
        };
      })
      .sort((a, b) => a.frontendQuestionId - b.frontendQuestionId)
      .slice(0, 50);
  }
  async function fetchQuestions() {
    const questions = await fetch(
      "https://leetcode.com/api/problems/all/"
    ).then(r => r.json());
    optimizeQuestionList(questions.stat_status_pairs);
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
        <th>Difficulty</th>
      </tr>
    </thead>
    {#if !list.length}
      <div class="text-center">
        <Loader />
      </div>
    {:else}
      <tbody>
        {#each list as question}
          <tr>
            <td>{question.frontendQuestionId}</td>
            <td>
              <a
                href="https://leetcode.com/problems/{question.titleSlug}"
                target="_blank">
                {question.title}
              </a>
            </td>
            <td>
              <span class="label label-danger round">Hard</span>
            </td>
          </tr>
        {/each}
        <tr class="pagination-content">
          <td colspan="5">
            <Pagination />
          </td>
        </tr>
      </tbody>
    {/if}
  </table>
</div>
