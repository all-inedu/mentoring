<template>
  <div id="student">
    <!-- Detail  -->
    <div class="card-white">
      <div class="row">
        <div class="col-md-3 text-center">
          <img
            v-lazy="'https://picsum.photos/200/300'"
            alt="Profile"
            class="st-pic"
          />
        </div>
        <div class="col-md-9">
          <h5>
            {{ students_detail.first_name + " " + students_detail.last_name }}
          </h5>
          <hr class="my-0 mb-2" />
          <div class="row">
            <div class="col-md-6">
              <div class="mb-2">
                <label>Email</label> <br />
                {{ students_detail.email }}
              </div>
              <div class="mb-2">
                <label>School Name</label> <br />
                {{ students_detail.school_name }}
              </div>
              <div class="mb-2">
                <label>Grade</label> <br />
                {{ students_detail.grade }}
              </div>
            </div>
            <div class="col-md-6">
              <div class="mb-2">
                <label>Social Media</label> <br />
                <div v-for="i in students_detail.social_media" :key="i">
                  <div class="" v-if="i.hyperlink != null">
                    <vue-feather
                      :type="i.social_media_name"
                      class="float-start me-2 mt-1"
                      size="16"
                    ></vue-feather>
                    {{ i.hyperlink }} <br />
                  </div>
                </div>
              </div>
              <div class="mb-2">
                <label>Address</label> <br />
                {{ students_detail.address }}
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Activity  -->

    <div class="card-white">
      <div
        class="overflow-auto d-flex w-100 mentoring-scroll py-2"
        style="white-space: nowrap"
      >
        <button
          class="btn-mentoring me-2 py-1 px-3"
          :class="menus.key == i.slug ? 'btn-type-3' : 'btn-type-1'"
          @click="goTab(i.slug)"
          v-for="(i, index) in tab"
          :key="index"
        >
          {{ i.name }}
        </button>
      </div>

      <!-- Meetings -->
      <transition name="fade">
        <v-meeting
          v-if="menus.key == '' || menus.key == 'meeting'"
          :menus="menus"
        />
      </transition>

      <!-- Todos  -->
      <transition name="fade">
        <v-todos v-if="menus.key == 'todos'" :menus="menus" />
      </transition>

      <!-- Group  -->
      <transition name="fade">
        <v-group v-if="menus.key == 'group'" :menus="menus" />
      </transition>

      <!-- Webinar -->
      <transition name="fade">
        <v-webinar v-if="menus.key == 'webinar'" :menus="menus" />
      </transition>

      <!-- Unversity Shorlisted -->
      <transition name="fade">
        <v-uni-shortlisted
          v-if="menus.key == 'uni_shortlisted'"
          :menus="menus"
        />
      </transition>

      <!-- University Requirements -->
      <transition name="fade">
        <v-uni-requirement
          v-if="menus.key == 'uni_requirement'"
          :menus="menus"
        />
      </transition>

      <!-- Event -->
      <!-- <transition name="fade">
        <div class="border p-3 rounded mt-3" v-if="activity == 'event'">
          <div class="table-responsive">
            <table class="table align-middle">
              <thead>
                <tr class="text-center">
                  <th>No</th>
                  <th>Event Name</th>
                  <th>Date & Time</th>
                </tr>
              </thead>
              <tbody>
                <tr class="text-center" v-for="i in 5" :key="i">
                  <td>{{ i }}</td>
                  <td>Lorem Ipsum</td>
                  <td>20 Feburary 2022</td>
                </tr>
              </tbody>
            </table>
          </div>
          <hr />
          <nav class="mt-2">
            <ul class="pagination justify-content-center">
              <li class="page-item">
                <a class="page-link" href="#">Previous</a>
              </li>
              <li class="page-item"><a class="page-link" href="#">1</a></li>
              <li class="page-item"><a class="page-link" href="#">2</a></li>
              <li class="page-item"><a class="page-link" href="#">3</a></li>
              <li class="page-item"><a class="page-link" href="#">Next</a></li>
            </ul>
          </nav>
        </div>
      </transition> -->

      <!-- Files -->
      <!-- <transition name="fade">
        <div class="border p-3 rounded mt-3" v-if="activity == 'files'">
          <div class="table-responsive">
            <table class="table align-middle">
              <thead>
                <tr class="text-center">
                  <th>No</th>
                  <th>File Name</th>
                  <th>Uploaded Date</th>
                </tr>
              </thead>
              <tbody>
                <tr class="text-center" v-for="i in 5" :key="i">
                  <td>{{ i }}</td>
                  <td>Lorem Ipsum</td>
                  <td>20 Feburary 2022</td>
                </tr>
              </tbody>
            </table>
          </div>
          <hr />
          <nav class="mt-2">
            <ul class="pagination justify-content-center">
              <li class="page-item">
                <a class="page-link" href="#">Previous</a>
              </li>
              <li class="page-item"><a class="page-link" href="#">1</a></li>
              <li class="page-item"><a class="page-link" href="#">2</a></li>
              <li class="page-item"><a class="page-link" href="#">3</a></li>
              <li class="page-item"><a class="page-link" href="#">Next</a></li>
            </ul>
          </nav>
        </div>
      </transition> -->
    </div>
  </div>
</template>

<script>
import Meeting from "@/components/mentor/page/student/activity/meeting";
import Todos from "@/components/mentor/page/student/activity/todos";
import Group from "@/components/mentor/page/student/activity/group";
import Webinar from "@/components/mentor/page/student/activity/webinar";
import UniShortlisted from "@/components/mentor/page/student/activity/uni_shortlisted";
import UniRequirement from "@/components/mentor/page/student/activity/uni_requirement";

export default {
  name: "studentDetail",
  props: {
    menus: Object,
  },
  components: {
    "v-meeting": Meeting,
    "v-todos": Todos,
    "v-group": Group,
    "v-webinar": Webinar,
    "v-uni-shortlisted": UniShortlisted,
    "v-uni-requirement": UniRequirement,
  },
  data() {
    return {
      activity: "meeting",
      tab: [
        { slug: "meeting", name: "Meeting" },
        { slug: "todos", name: "Todos" },
        { slug: "group", name: "Group Project" },
        { slug: "webinar", name: "Webinar" },
        { slug: "uni_shortlisted", name: "University Shortlisted" },
        { slug: "uni_requirement", name: "University Requirementes" },
      ],
      students_detail: [],
    };
  },
  methods: {
    goTab(i) {
      this.$router.push({
        path: "/mentor/student/" + this.menus.submenu + "/" + i,
      });
    },

    async getData() {
      const id = this.menus.submenu;
      // console.log(email)
      const response = await this.$axios.get("student/detail/" + id);
      this.students_detail = response.data.data;
    },
  },
  created() {
    this.getData();
  },
};
</script>

<style>
.card-white {
  background: #fff;
  padding: 20px;
  border-radius: 10px;
}

.st-pic {
  width: 200px;
  height: 200px;
  border-radius: 50%;
}
</style>