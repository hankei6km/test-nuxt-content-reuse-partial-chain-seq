<template>
  <div>
    <h1>{{ page.title }}</h1>
    <p>{{ page.description }}</p>
    <nuxt-content :document="page" />
    <section>
      <h2>Posts</h2>
      <ul>
        <li v-for="post in posts" :key="post.position">
          <NuxtLink :to="`${post.slug}`">{{ post.title }}</NuxtLink>
        </li>
      </ul>
    </section>
    <nav>
      <ul>
        <li v-if="prev">
          <NuxtLink :to="`${prev.slug}`">prev: {{ prev.title }}</NuxtLink>
        </li>
        <li v-if="next">
          <NuxtLink :to="`${next.slug}`">next: {{ next.title }}</NuxtLink>
        </li>
      </ul>
    </nav>
  </div>
</template>

<script>
export default {
  async asyncData({ $content, params, error }) {
    const slug = params.slug || "1";
    const page = await $content(params.slug)
      .fetch()
      .catch((err) => {
        error({ statusCode: 404, message: "Page not found" });
      });

     const sorted = $content("")
       .only(["title", "slug"])
       .sortBy("position", "asc");
     const posts = await sorted.fetch();
     const [prev, next] = await sorted.surround(slug).fetch();

    // const sorted = ($content) =>
    //   $content("").only(["title", "slug"]).sortBy("position", "asc");
    // const [prev, next] = await sorted($content).surround(slug).fetch();
    // const posts = await sorted($content).fetch();

    return {
      page,
      posts,
      prev,
      next,
    };
  },
};
</script>
