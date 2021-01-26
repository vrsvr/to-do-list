<template>
  <div class="row">
    <div v-if="task" class="col s6 offset-s3">
      <h1>{{task.title}}</h1>

      <form @submit.prevent="submitHandler">
        
        <div class="chips" ref="chips"></div>

        <div class="input-field" style="margin-top: 2rem">
          <textarea v-model="description" id="description" class="materialize-textarea" style="min-height: 150px;     overflow-y: auto; margin-top: 10px;"/>
          <label for="description">Description</label>
          <span class="character-counter" style="float: right; font-size: 12px;">{{description.length}}/2048</span>
        </div>

        <input type="text" ref="datepicker">
        <div v-if="task.status !== 'completed'">
          <button class="btn" type="submit" style="margin-right: 1rem">Update task</button>
          <button class="btn blue" type="button" @click="completeTask">Complete task</button>
        </div>
        <div v-else>
          <p>This task is completed</p>
        </div>
      </form>
    </div>
    <div v-else>
      <p>Task not found</p>
    </div>
  </div>
</template>

<script>
export default {
  computed: {
    task() {
      return this.$store.getters.taskById(+this.$route.params.id)
    }
  },
  data: () => ({
    description: '',
    chips: null,
    date: null
  }),
  mounted() {
    // eslint-disable-next-line no-undef
    this.chips = M.Chips.init(this.$refs.chips, {
      placeholder: 'New tag',
      data: this.task.tags
    })
    // eslint-disable-next-line no-undef
    this.date = M.Datepicker.init(this.$refs.datepicker, {
      format: 'dd.mm.yyyy',
      defaultDate: new Date(this.task.date),
      setDefaultDate: true
    })
    this.description = this.task.description
    setTimeout(() => {
    // eslint-disable-next-line no-undef
      M.updateTextFields()
    }, 0);
  },
  methods: {
    submitHandler() {
      this.$store.dispatch('updateTask', {
        id: this.task.id,
        description: this.description,
        date: this.date.date,
        tags: this.task.tags
      })
      this.$router.push('/list')
    },
    completeTask() {
      this.$store.dispatch('completeTask', this.task.id),
      this.$router.push('/list')
    }
  },
  destroyed() {
    if (this.date && this.date.destroy) {
      this.date.destroy()      
    }
    if (this.chips && this.date.destroy) {
      this.chips.destroy()      
    }
  }
}
</script>