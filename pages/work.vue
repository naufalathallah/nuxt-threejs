<template>
  <div class="bg-black h-screen px-6 py-12">
    <h2
      class="text-white text-5xl font-bold italic opacity-0 mb-4"
      ref="title"
      :class="titleClass"
    >
      MY WORK
    </h2>
    <div class="flex space-x-6">
      <div
        v-for="(p, index) in projects"
        :key="index"
        class="w-full opacity-0 transform translate-x-8"
        ref="projects"
        :class="projectClass"
      >
        <a :href="p.link" target="_blank">
          <img :src="p.image.url" class="object-cover h-[70vh] w-full" alt="" />
          <p class="text-white text-sm">{{ p.title }}</p>
        </a>
      </div>
    </div>
  </div>
</template>

<script>
import nscImg from "~/static/tb 1.png";
import aleenaImg from "~/static/tb 2.png";
import githubImg from "~/static/td 1.png";
import gsap from "gsap";
export default {
  data() {
    return {
      projects: [
        {
          image: {
            url: nscImg,
          },
          title: "Nscreativestudio",
          link: "https://nscreativestudio.com",
        },
        {
          image: {
            url: aleenaImg,
          },
          title: "Aleenazaire",
          link: "https://aleenazaire.com",
        },
        {
          image: {
            url: githubImg,
          },
          title: "Github",
          link: "https://github.com/naufalathallah",
        },
      ],
      titleClass: "transform translate-y-8",
      projectClass: "transform translate-x-8",
    };
  },

  mounted() {
    gsap.to(this.$refs.title, {
      opacity: 1,
      duration: 1,
      y: 0,
      ease: "expo",
      onComplete: () => {
        this.titleClass = "transform translate-y-0";
      },
    });

    gsap.to(this.$refs.projects, {
      opacity: 1,
      duration: 1.5,
      stagger: 0.1,
      x: 0,
      delay: 0.4,
      ease: "expo",
      onComplete: () => {
        this.projectClass = "transform translate-x-0";
      },
    });
  },
};
</script>
