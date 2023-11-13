<template>
  <article class="post" :class="post.slug">
    <div class="py-8 md:py-16 text-center mx-auto">
      <h1 class="text-lg md:text-xl lg:text-4xl xl:text-6xl">
        {{ post.title }}
      </h1>
    </div>
 <!-- Agregar visualización de Markdown  si está presente -->
    <div v-html="$md.render(post.content)" class="post__content markdown pt-4 md:pt-6 md:pb-24" />
     <!-- Visualización opcional de PDF --> <div v-if="post.pdf">
      <iframe :src="post.pdf" width="100%" height="600px" frameborder="0"></iframe>
    </div>
     <div v-if="post.pdf">
   <a :href="post.pdf" target="_blank" rel="noopener noreferrer">Ver PDF</a>
    </div> 

    <!-- Visualización opcional de la imagen destacada -->
    <img v-if="post.featuredImage" :src="post.featuredImage" alt="Featured Image" class="post__image" />
  </article>
</template>

<script lang="ts">
import { Component, Vue } from 'nuxt-property-decorator';
import { MetaInfo } from 'vue-meta';

@Component({
  head(): MetaInfo {
    return {
      title: this.post.title,
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: this.post.seoDescription,
        },
        {
          hid: 'og:image',
          name: 'og:image',
          content: this.post.seoMetaImage,
        },
      ],
    };
  },
})
export default class BlogPost extends Vue {
  post!: Post;

  async asyncData({ params, payload }): Promise<{ post: Post }> {
    if (payload) {
      return { post: payload };
    }

    try {
      const post = require(`@/content/blog/${params.slug}.json`);

      return {
        post,
      };
    } catch (e) {
      throw new Error('Not found');
    }
  }
}
</script>
