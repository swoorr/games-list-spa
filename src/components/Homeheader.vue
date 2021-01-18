<template>
  <div class="page-title">
    <h2>{{ pageGame }} games results...</h2>
    <div class="row">
      <div class="col-md-12 col-center-block">
        <form class="col-6" @submit.prevent="submitForm()">
          <div class="form-group">
            <label for=""></label>
            <input
              type="text"
              v-model="pageGameInput"
              class="form-control"
              placeholder="game keyword, please type"
            />
          </div>
          <button type="submit" class="form-control btn border btn-warning">
            Submit
          </button>
        </form>
      </div>
    </div>
  </div>
</template>
<script>
    export default {
      name: "Homeheader"

    }
</script>