<template>
  <div class="form-page-container">
    <formAddTask/>
  </div>
</template>

<script>
import formAddTask from "../components/formAddTask.vue"

export default {
  name: "Form",

  components: {
    formAddTask,
  }
}
</script>

<style scoped>
.form-page-container {
  min-height: calc(100vh - 75px);
  display: flex;
  justify-content: center;
  align-items: flex-start;
  padding: 2.5rem 1rem;
}
</style>