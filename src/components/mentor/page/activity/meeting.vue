<template>
  <div id="meeting">
    <div class="d-flex justify-content-between align-items-center">
      <h6>Meeting</h6>
      <button
        class="btn-mentoring btn-sm py-1 btn-type-3 mx-1"
        @click="modal = 'new'"
      >
        <i class="fa-solid fa-add"></i>
      </button>
    </div>
    <hr class="mt-2 mb-0" />
    <div class="row mt-1">
      <div
        class="col-12 d-flex w-100 mentoring-scroll overflow-auto py-2"
        style="white-space: nowrap"
      >
        <button
          class="btn-mentoring btn-sm mx-1 py-1 px-3"
          :class="tab == 'pending' ? 'bg-primary' : ''"
          @click="goTab('pending')"
        >
          Pending
        </button>
        <button
          class="btn-mentoring btn-sm mx-1 py-1 px-3"
          :class="tab == 'upcoming' ? 'bg-primary' : ''"
          @click="goTab('upcoming')"
        >
          Upcoming
        </button>
        <button
          class="btn-mentoring btn-sm mx-1 py-1 px-3"
          :class="tab == 'history' ? 'bg-primary' : ''"
          @click="goTab('history')"
        >
          History
        </button>
      </div>
    </div>
    <div class="border p-3">
      <!-- Data  -->

      <!-- Pending  -->
      <v-pending
        v-if="tab == 'pending'"
        :meeting="meeting_data"
        @check="checkData"
      />

      <!-- Upcoming  -->
      <v-upcoming
        v-if="tab == 'upcoming'"
        :meeting="meeting_data"
        @check="checkData"
      />

      <!-- History  -->
      <v-history
        v-if="tab == 'history'"
        :meeting="meeting_data"
        @check="checkData"
      />

      <!-- Pagination  -->
      <nav class="my-0 mt-2" v-if="meeting_data?.data?.length != 0">
        <ul class="pagination justify-content-center pb-0 mb-0">
          <li class="page-item" v-if="meeting_data.current_page != 1">
            <a class="page-link" @click="getPage(meeting_data.links[0].url)">
              <i class="fa-solid fa-chevron-left"></i>
            </a>
          </li>
          <div v-for="(i, index) in meeting_data.last_page" :key="index">
            <li
              class="page-item"
              v-if="
                meeting_data.current_page - 2 < i &&
                meeting_data.current_page + 2 > i
              "
            >
              <a
                class="page-link"
                :class="
                  meeting_data.current_page == i ? 'bg-primary text-white' : ''
                "
                href="#"
                @click="getPage(meeting_data.path + '?page=' + i)"
                >{{ i }}</a
              >
            </li>
          </div>
          <li
            class="page-item"
            v-if="meeting_data.current_page != meeting_data.last_page"
          >
            <a class="page-link" @click="getPage(meeting_data.next_page_url)">
              <i class="fa-solid fa-chevron-right"></i>
            </a>
          </li>
        </ul>
      </nav>
    </div>
  </div>

  <!-- Modal  -->
  <div class="vue-modal-overlay" v-if="modal != ''" @click="modal = ''"></div>

  <!-- ADD Meeting -->
  <transition name="pop">
    <div
      class="vue-modal vue-modal-lg"
      style="min-height: 300px"
      v-if="modal == 'new'"
    >
      <h5>New Meeting</h5>
      <hr class="my-1 mb-3" />

      <form method="post" @submit.prevent="handleSubmit">
        <div class="row">
          <div class="col-md-6">
            <div class="mb-2">
              <multiselect
                v-model="user_select"
                :options="user_list"
                placeholder="Select one mentee"
                deselect-label="Can't remove this value"
                track-by="id"
                :custom-label="customLabel"
                :searchable="true"
                :allow-empty="false"
                @select="checkUser"
                style="margin-top: 13px"
              >
              </multiselect>
            </div>
          </div>
          <div class="col-md-6">
            <div class="row">
              <div class="col-7">
                <div class="mb-2">
                  <input-group>
                    <input
                      v-model="meeting_date.date"
                      :min="this.$customDate.tomorrowDateOnly()"
                      :type="input.meeting"
                      class="form-mentoring w-100"
                      required
                      @focus="input.meeting = 'date'"
                      @blur="input.meeting = 'text'"
                    />
                    <label>Meeting Date</label>
                  </input-group>
                </div>
              </div>
              <div class="col-5">
                <div class="mb-2">
                  <input-group>
                    <input
                      v-model="meeting_date.time"
                      :type="input.time"
                      class="form-mentoring w-100"
                      @focus="input.time = 'time'"
                      @blur="input.time = 'text'"
                      required
                    />
                    <label>Time</label>
                  </input-group>
                </div>
              </div>
            </div>
          </div>
          <div class="col-md-8">
            <div class="mb-2">
              <input-group>
                <input
                  v-model="call_data.location_link"
                  type="text"
                  class="form-mentoring w-100"
                  required
                />
                <label>Location Link</label>
                <small class="text-danger" v-if="error_form?.location_link">
                  {{ error_form.location_link[0] }}
                </small>
              </input-group>
            </div>
          </div>
          <div class="col-md-4">
            <div class="mb-2">
              <input-group>
                <input
                  v-model="call_data.location_pw"
                  type="text"
                  class="form-mentoring w-100"
                />
                <label>Password</label>
              </input-group>
            </div>
          </div>
          <div class="col-md-12">
            <div class="mb-2">
              <label class="mt-2 mb-1 w-100" style="font-size: 0.8em">
                Subject
              </label>
              <div class="row row-cols-md-4 row-cols-2 align-items-stretch g-2">
                <div class="col mb-2">
                  <label class="w-100">
                    <input
                      v-model="call_data.module"
                      type="radio"
                      required
                      name="product"
                      class="card-input-element"
                      value="life skills"
                    />
                    <div class="panel panel-default card-input">
                      <div class="panel-heading">Life Skill</div>
                    </div>
                  </label>
                </div>
                <div class="col mb-2">
                  <label class="w-100">
                    <input
                      v-model="call_data.module"
                      type="radio"
                      required
                      name="product"
                      class="card-input-element"
                      value="career exploration"
                    />
                    <div class="panel panel-default card-input">
                      <div class="panel-heading">Career Exploration</div>
                    </div>
                  </label>
                </div>
                <div class="col mb-2">
                  <label class="w-100">
                    <input
                      v-model="call_data.module"
                      type="radio"
                      required
                      name="product"
                      class="card-input-element"
                      value="university admission"
                    />
                    <div class="panel panel-default card-input">
                      <div class="panel-heading">University Admission</div>
                    </div>
                  </label>
                </div>
                <div class="col mb-2">
                  <label class="w-100">
                    <input
                      v-model="call_data.module"
                      type="radio"
                      required
                      name="product"
                      class="card-input-element"
                      value="life at university"
                    />
                    <div class="panel panel-default card-input">
                      <div class="panel-heading">Life at University</div>
                    </div>
                  </label>
                </div>
              </div>
            </div>
          </div>
          <hr class="mt-3" />
          <div class="col-6">
            <button
              type="button"
              class="btn-mentoring btn-sm py-1 px-3 btn-outline-danger"
              @click="modal = ''"
            >
              Cancel
            </button>
          </div>
          <div class="col-6 text-end">
            <button
              type="submit"
              class="btn-mentoring btn-sm py-1 px-3 btn-success"
            >
              Save Meeting
            </button>
          </div>
        </div>
      </form>
    </div>
  </transition>
</template>

<script>
import Multiselect from "vue-multiselect";

import Pending from "@/components/mentor/page/activity/meeting/pending";
import Upcoming from "@/components/mentor/page/activity/meeting/upcoming";
import History from "@/components/mentor/page/activity/meeting/history";

export default {
  name: "meetings",
  components: {
    Multiselect,

    "v-pending": Pending,
    "v-upcoming": Upcoming,
    "v-history": History,
  },
  data() {
    return {
      tab: "pending",
      user_select: "",
      modal: "",
      meeting_data: [],
      user_list: [],
      call_data: {
        student_id: "",
        std_act_status: "waiting",
        location_link: "",
        location_pw: "",
        call_with: "mentor",
        module: "",
        call_date: "",
        created_by: "mentor",
      },
      meeting_date: {
        date: "",
        time: "",
      },
      input: {
        meeting: "text",
        time: "text",
      },
      error_form: [],
    };
  },
  methods: {
    goTab(tab) {
      this.tab = tab;
      this.getData(tab);
    },

    customLabel({ first_name, last_name }) {
      return `${first_name} ${last_name}`;
    },

    checkUser(i) {
      this.user_select = i;

      if (this.user_select) {
        this.call_data.student_id = this.user_select.id;
      }
    },

    checkData(e) {
      this.tab = e;
      this.getData(e);
    },

    async getData(tab) {
      this.meeting_data = [];
      try {
        const response = await this.$axios.get(
          "../v2/list/activities/1-on-1-call/" + tab
        );
        this.meeting_data = response.data.data;
        // console.log(response.data);
      } catch (e) {
        console.log(e.response);
      }
    },

    async getPage(link) {
      this.meeting_data = [];
      try {
        const response = await this.$axios.get(link);
        this.meeting_data = response.data.data;
        // console.log(response.data);
      } catch (e) {
        console.log(e.response);
      }
    },

    async getMentee() {
      try {
        const response = await this.$axios.get("student/list?paginate=no");
        this.user_list = response.data.data;
      } catch (e) {
        console.log(e.response);
      }
    },

    async handleSubmit() {
      // Meeting Date

      let date = this.meeting_date.date;
      let time = this.meeting_date.time;
      let datetime = date + "T" + time;

      this.call_data.call_date = datetime;
      this.$alert.loading();

      try {
        const response = await this.$axios.post(
          "../v2/create/activities/1-on-1-call",
          this.call_data
        );

        // console.log(response.data);
        if (response.data.success) {
          this.user_select = "";
          this.call_data = {
            student_id: "",
            std_act_status: "waiting",
            location_link: "",
            location_pw: "",
            call_with: "mentor",
            module: "",
            call_date: "",
            created_by: "mentor",
          };
          this.meeting_date = {
            date: "",
            time: "",
          };

          this.modal = "";
          this.tab = "pending";
          this.getData(this.tab);
          this.$alert.toast("success", response.data.message);
        } else {
          this.$alert.toast("error", response.data.error);
        }
      } catch (e) {
        console.log(e.response.data);
        if (e.response.data) {
          this.error_form = e.response.data.error;
        }
        this.$alert.toast("error", "Please try again.");
      }
    },
  },
  created() {
    this.getMentee();
    this.getData(this.tab);
  },
};
</script>

<style scoped>
.card-input-element {
  display: none;
}

.card-input {
  width: 100%;
  background: transparent;
  border: 2px solid #dedede;
  color: #1f1f21;
  text-align: center;
  border-radius: 10px;
  padding: 5px 10px;
  height: 65px;
  display: flex;
  align-items: center;
  transition: all 0.4s;
}

.panel-heading {
  width: 100%;
  display: block;
  font-size: 0.8em;
  line-height: 1em;
  text-align: center;
}

.card-input:hover {
  cursor: pointer;
}

.card-input-element:checked + .card-input {
  color: #fff;
  background: #223872;
}
</style>