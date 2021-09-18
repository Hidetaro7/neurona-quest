<template>
  <main class="main">
    <h1 class="title">{{ sizes }}</h1>
    <p class="publishedAt">{{ publishedAt }}</p>
    <div class="post" v-html="summary"></div>
  </main>
</template>

<script>
import axios from 'axios'

export default {
  async asyncData({ params }) {
    const { data } = await axios.get(
      `https://xxxx.microcms.io/api/v1/xxxxx/${params.slug}`,
      {
        headers: { 'X-API-KEY': 'XXXXXXXXXXXXXX' }
      }
    )
    console.log(data)
    return data
  }
}
</script>

<style lang="scss" scoped>
.main {
  p {
    @apply text-red-800;
  }
}
</style>
