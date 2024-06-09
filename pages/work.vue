<template>
  <div class="bg-black min-h-screen px-6 py-12">
    <h2
      class="text-white text-5xl font-bold italic opacity-0 mb-4"
      ref="title"
      :class="titleClass"
    >
      MY WORK
    </h2>
    <div class="flex flex-col space-y-6 md:flex-row md:space-x-6 md:space-y-0">
      <div
        v-for="(p, index) in projects"
        :key="index"
        class="w-full opacity-0 transform translate-x-8"
        ref="projects"
        :class="projectClass"
      >
        <a :href="p.link" target="_blank" class="block overflow-hidden group">
          <div class="relative overflow-hidden">
            <img
              :src="p.image.url"
              class="object-cover h-[70vh] w-full transform scale-150 transition-transform duration-500 ease-in-out group-hover:scale-150"
              alt=""
            />
          </div>
          <p class="text-white text-sm mt-2">{{ p.title }}</p>
        </a>
      </div>
    </div>
  </div>
</template>

<script>
import nscImg from "~/static/1.png";
import aleenaImg from "~/static/2.png";
import githubImg from "~/static/3.png";
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

<style scoped>
/* Custom Tailwind CSS classes can be added here if needed */
</style>
