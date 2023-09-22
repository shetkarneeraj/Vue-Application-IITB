<template>
  <h1>Courses</h1>
  <div class="row row-cols-2 g-3">
    <div class="col h-100">
      <div class="card">
        <div class="card-body">
          <h5 class="card-title">Add Course</h5>
          <div class="mb-3 row">
            <label for="inputPassword" class="col-sm-3 col-form-label"
              >Course Code</label
            >
            <div class="col-sm-9">
              <input
                type="text"
                class="form-control"
                v-model="newCourse.course_code"
              />
            </div>
          </div>
          <div class="mb-3 row">
            <label for="inputPassword" class="col-sm-3 col-form-label"
              >Course Title</label
            >
            <div class="col-sm-9">
              <input
                type="text"
                class="form-control"
                v-model="newCourse.course_name"
              />
            </div>
          </div>
          <div class="mb-3 row">
            <label for="inputPassword" class="col-sm-3 col-form-label"
              >Description</label
            >
            <div class="col-sm-9">
              <textarea
                class="form-control"
                id="exampleFormControlTextarea1"
                rows="3"
                v-model="newCourse.course_description"
              ></textarea>
            </div>
          </div>
          <div class="d-grid gap-2">
            <button
              class="btn btn-primary"
              type="button"
              @click="createNewCourse"
            >
              Submit
            </button>
            <button class="btn btn-secondary" type="button" @click="clearText">
              Clear
            </button>
          </div>
        </div>
      </div>
    </div>
    <div class="col h-100">
      <div class="card">
        <div class="card-body">
          <h5 class="card-title">Find Course</h5>
          <div class="mb-3 row">
            <div class="col-sm-12">
              <input
                type="text"
                class="form-control"
                placeholder="Search"
                v-model="searchKeyword"
              />
            </div>
          </div>
          <div class="accordion card-text" id="accordionExample">
            <div
              class="accordion-item"
              v-for="course in paginatedItems"
              :key="course.id"
            >
              <h2 class="accordion-header">
                <button
                  class="accordion-button collapsed"
                  type="button"
                  data-bs-toggle="collapse"
                  :data-bs-target="`#C${course.course_id}`"
                  aria-expanded="false"
                  :aria-controls="`C${course.course_id}`"
                >
                  {{ course.course_name }}
                </button>
              </h2>
              <div
                :id="`C${course.course_id}`"
                class="accordion-collapse collapse"
                data-bs-parent="#accordionExample"
              >
                <div class="accordion-body">
                  {{ course.course_description }}
                </div>
                <button
                  type="button"
                  class="btn btn-danger"
                  @click="deleteCourse(course.course_id)"
                >
                  Delete
                </button>
              </div>
            </div>
          </div>
          <div class="card-footer text-body-secondary">
            <ul class="pagination">
              <li class="page-item">
                <a class="page-link" href="#" @click="current_page--"
                  >Previous</a
                >
              </li>
              <li class="page-item" v-for="page in totalPages" :key="page">
                <a class="page-link" href="#" @click="current_page = page">{{
                  page
                }}</a>
              </li>
              <li class="page-item">
                <a class="page-link" @click="current_page++">Next</a>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { api_url } from "@/constants";
import axios from "axios";
export default {
  name: "CoursesView",
  data() {
    return {
      api_url: api_url,
      courses: [],
      current_page: 1,
      itemsPerPage: 5,
      searchKeyword: "",
      newCourse: {
        course_code: "",
        course_name: "",
        course_description: "",
      },
    };
  },
  methods: {
    getCourses() {
      axios
        .get(`${api_url}courses/`)
        .then((response) => {
          this.courses.push(...response.data);
        })
        .catch((error) => {
          console.log(error);
        });
    },
    clearText() {
      this.newCourse.course_code = "";
      this.newCourse.course_name = "";
      this.newCourse.course_description = "";
    },
    createNewCourse() {
      axios
        .post(`${api_url}courses/`, this.newCourse)
        .then((response) => {
          this.courses.push(response.data);
          this.clearText();
        })
        .catch((error) => {
          console.log(error);
        });
    },
    deleteCourse(id) {
      axios
        .delete(`${api_url}courses/` + id)
        .then(() => {
          this.courses = this.courses.filter(
            (course) => course.course_id != id
          );
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  mounted() {
    this.getCourses();
  },
  computed: {
    paginatedItems() {
      const start = (this.current_page - 1) * this.itemsPerPage;
      const end = start + this.itemsPerPage;
      return this.searchedCourses.slice(start, end);
    },
    totalPages() {
      return Math.ceil(this.searchedCourses.length / this.itemsPerPage);
    },
    searchedCourses() {
      return this.courses.filter((course) => {
        return (
          course.course_name
            .toLowerCase()
            .includes(this.searchKeyword.toLowerCase()) ||
          course.course_description
            .toLowerCase()
            .includes(this.searchKeyword.toLowerCase())
        );
      });
    },
  },
};
</script>
