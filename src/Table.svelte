<script>
  import { onMount } from "svelte";

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
      .sort((a, b) => a.frontendQuestionId - b.frontendQuestionId);
    console.log(list);
  }
  async function fetchQuestions() {
    const questions = await fetch(
      "https://leetcode.com/api/problems/all/"
    ).then(r => r.json());
    console.log(questions.stat_status_pairs);
    optimizeQuestionList(questions.stat_status_pairs);
  }

  onMount(fetchQuestions);
</script>

<div class="row">
  <table class="table table-striped">
    <thead>
      <tr>
        <th>#</th>
        <th>Title</th>
        <th>Difficulty</th>
      </tr>
    </thead>
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
    </tbody>
  </table>
</div>
