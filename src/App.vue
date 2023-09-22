<template>
  <div class="container">
    <CoursesView />
    <InstancesView />
    <footer class="text-center text-lg-start bg-light text-muted">
      <div
        class="text-center p-4"
        style="background-color: rgba(0, 0, 0, 0.05)"
      >
        Submission by Neeraj Rajeev Shetkar |
        <a href="mailto:neerajshetkar@gmail.com">neerajshetkar@gmail.com</a> |
        <a href="tel:+916377759213">+91 6377759213</a>
      </div>
      <!-- Copyright -->
    </footer>
    <!-- Footer -->
  </div>
</template>

<script>
import InstancesView from "./components/Instances.vue";
import CoursesView from "./components/Courses.vue";

export default {
  name: "App",
  components: {
    InstancesView,
    CoursesView,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
