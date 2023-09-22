<template>
  <h1>Instances</h1>
  <div class="row row-cols-2 g-3">
    <div class="col">
      <div class="card">
        <div class="card-body">
          <h5 class="card-title">Add Instance</h5>
          <div class="mb-3 row">
            <label for="inputPassword" class="col-sm-3 col-form-label"
              >Course</label
            >
            <div class="col-sm-9">
              <select
                class="form-select"
                aria-label="Default select example"
                v-model="newInstance.course_id"
              >
                <option selected disabled>Open this select menu</option>
                <option
                  v-for="course in courses"
                  :key="course.id"
                  :value="course.course_id"
                >
                  {{ course.course_name }} ({{ course.course_code }})
                </option>
              </select>
            </div>
          </div>
          <div class="mb-3 row">
            <label for="inputPassword" class="col-sm-3 col-form-label"
              >Year</label
            >
            <div class="col-sm-9">
              <select class="form-select" v-model="newInstance.year">
                <option selected disabled>Open this select menu</option>
                <option
                  v-for="year in 15"
                  :key="year"
                  :value="current_year + year"
                >
                  {{ current_year + year }}
                </option>
              </select>
            </div>
          </div>
          <div class="mb-3 row">
            <label for="inputPassword" class="col-sm-3 col-form-label"
              >Semester</label
            >
            <div class="col-sm-9">
              <input
                type="number"
                class="form-control"
                v-model="newInstance.semester"
              />
            </div>
          </div>
          <div class="d-grid gap-2">
            <button
              class="btn btn-primary"
              type="button"
              @click="createNewInstance"
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
    <div class="col">
      <div class="card">
        <div class="card-body">
          <h5 class="card-title">Find Instance</h5>
          <div class="row align-items-center">
            <div class="col-auto">
              <select class="form-select" v-model="find.year">
                <option selected disabled>Select year</option>
                <option v-for="year in years" :key="year" :value="year">
                  {{ year }}
                </option>
              </select>
            </div>
            <div class="col-auto">
              <select class="form-select" v-model="find.semester">
                <option selected disabled>Select semester</option>
                <option
                  v-for="semester in semesters"
                  :key="semester"
                  :value="semester"
                >
                  {{ semester }}
                </option>
              </select>
            </div>
            <div class="col-auto">
              <button
                class="btn btn-primary"
                type="button"
                @click="findInstances"
              >
                Find
              </button>
            </div>
            <div class="col-auto">
              <button
                class="btn btn-danger"
                type="button"
                @click="findInstances"
              >
                Clear
              </button>
            </div>
          </div>
          <table class="table table-hover">
            <thead>
              <tr>
                <th scope="col">Course Code</th>
                <th scope="col">Course</th>
                <th scope="col">Year</th>
                <th scope="col">Semester</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="instance in instances" :key="instance.id">
                <td>{{ instance.course_code }}</td>
                <td>{{ instance.course_name }}</td>
                <td>{{ instance.year }}</td>
                <td>{{ instance.semester }}</td>
                <td>
                  <button
                    class="btn btn-danger"
                    @click="
                      deleteInstance(
                        instance.year,
                        instance.semester,
                        instance.course_id
                      )
                    "
                  >
                    X
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { api_url } from "@/constants";
import axios from "axios";
export default {
  name: "InstancesView",
  data() {
    return {
      api_url: api_url,
      courses: [],
      instances: [],
      years: [],
      semesters: [],
      current_year: new Date().getFullYear() - 10,
      find: {
        year: null,
        semester: null,
      },
      newInstance: {
        course_id: null,
        year: null,
        semester: null,
      },
    };
  },
  methods: {
    getInstances() {
      axios
        .get(`${api_url}instances/`)
        .then((response) => {
          this.instances = response.data;
          this.years = [
            ...new Set(this.instances.map((item) => item.year)),
          ].sort((a, b) => a - b);
          this.semesters = [
            ...new Set(this.instances.map((item) => item.semester)),
          ].sort((a, b) => a - b);
        })
        .catch((error) => {
          console.log(error);
        });
    },
    clearText() {
      this.newInstance.course = "";
      this.newInstance.year = "";
      this.newInstance.semester = "";
    },
    createNewInstance() {
      if (
        this.newInstance.course_id == null ||
        this.newInstance.year == null ||
        this.newInstance.semester == null
      ) {
        alert("Please fill all fields");
      } else if (
        this.newInstance.semester > 8 ||
        this.newInstance.semester < 1
      ) {
        alert("Invalid value for semester");
      } else {
        axios
          .post(`${api_url}instances/`, this.newInstance)
          .then(() => {
            this.getInstances();
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },
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
    deleteInstance(year, semester, course_id) {
      axios
        .delete(`${api_url}/instances/${year}/${semester}/${course_id}`)
        .then(() => {
          this.getInstances();
        })
        .catch((error) => {
          console.log(error);
        });
    },
    findInstances() {
      axios
        .get(`${api_url}courses/${this.find.year}/${this.find.semester}/`)
        .then((response) => {
          this.instances = [response.data];
        })
        .catch((error) => {
          console.log(error);
        });
    },
    clearFilter() {
      this.find.year = null;
      this.find.semester = null;
      this.getInstances();
    },
  },
  mounted() {
    this.getInstances();
    this.getCourses();
    console.log(this.api_url);
  },
};
</script>
