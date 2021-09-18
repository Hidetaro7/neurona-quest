<template>
  <div class="container">
    <ul>
      <li v-for="content in contents" :key="content.id">
        <nuxt-link :to="`/${content.id}`">
          {{ content.type }}
        </nuxt-link>
      </li>
    </ul>
  </div>

</template>

<script>
// https://blog.microcms.io/microcms-nuxt-jamstack-blog/
import axios from 'axios'
export default {
  async asyncData() {
    const { data } = await axios.get(
      `https://xxxx.microcms.io/api/v1/xxxxx/${params.slug}`,
      {
        headers: { 'X-API-KEY': 'XXXXXXXXXXXXXX' }
      }
    )
    return data
  }
}
</script>

<style>
/* Sample `apply` at-rules with Tailwind CSS
.container {
@apply min-h-screen flex justify-center items-center text-center mx-auto;
}
*/
.container {
  @apply min-h-screen flex justify-center items-center text-center mx-auto;
}

</style>
