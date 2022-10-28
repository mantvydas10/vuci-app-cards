<template>
<div>
  <a-card class="card">
      <div v-for="items in data " :key="items.id">
        <h3>{{items.title}}</h3>
        <!-- <div class="systemBar">

          <div style="width: auto; margin-left: auto; margin-right: 0;">

    <a-progress label="CPU" :percent="cpuUsage" :show-info="false" status="active" />
      </div>
  </div> -->
  <hr/>
      <div v-for="items2 in items.data" :key="items2.id">
        <h3>{{items2.title}}</h3>
        <h3>{{items2.value}}</h3>

        <hr/>
      </div>
    </div>
  </a-card>
</div>
</template>

<script>
export default {
  watch: {
    deep: true,
    handler: function (newValue, oldValue) {
      this.items = newValue
      console.log(newValue, oldValue)
    }
  },
  props: ['data'],
  data () {
    return {
      items: this.data
    }
  }

}

</script>

<style scoped>
.card {
  height: 100%;
}

  .systemBar {
    display: flex;
    align-items: flex-end;
  }

</style>
