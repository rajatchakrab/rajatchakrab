<template>
  <article class="post" :class="post.slug">
    <div class="py-8 md:py-16 text-center mx-auto">
      <h1 class="text-lg md:text-xl lg:text-4xl xl:text-6xl">
        {{ post.title }}
      </h1>
    </div>

    <div v-html="$md.render(post.content)" class="post__content markdown pt-4 md:pt-6 md:pb-24" />
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
        // Remove the following meta tag for og:image
        // {
        //   hid: 'og:image',
        //   name: 'og:image',
        //   content: this.post.seoMetaImage,
        // },
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
      // Modify the fetched post data to exclude any image-related information
      // For example, you can delete or set the image-related properties to null
      delete post.featuredImage;
      // Alternatively, you can create a new object with only the required properties
      // const postData = { title: post.title, content: post.content, ... };
      
      return {
        post,
      };
    } catch (e) {
      throw new Error('Not found');
    }
  }
}
</script>
