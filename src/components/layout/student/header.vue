<template>
  <div id="header" class="sticky-top" style="top: 0">
    <div class="v-navbar">
      <div class="container">
        <img
          src="@/assets/img/logo-white.webp"
          alt="ALL-in Mentoring"
          class="navbar-logo"
        />
        <vue-feather
          type="menu"
          class="navbar-icon d-md-none d-block float-end"
          @click="showMenu"
        ></vue-feather>

        <transition name="fade">
          <div class="navbar-overlay" v-if="menu" @click="menu = false">
            <vue-feather
              type="x"
              class="navbar-close d-md-none d-block"
              @click="menu = false"
            >
            </vue-feather>
          </div>
        </transition>

        <transition name="fade">
          <div class="navbar-title d-md-none d-block" v-if="menu">MENU</div>
        </transition>

        <div
          class="navbar-menu d-md-inline-block"
          :class="!menu ? 'd-none' : ''"
        >
          <ul>
            <li @click="goToMenu('')" :class="params == '' ? 'active' : ''">
              Dashboard
            </li>
            <li
              @click="goToMenu('my-activity')"
              :class="
                params == 'my-activity' ||
                params == 'groups' ||
                params == 'webinar' ||
                params == 'event'
                  ? 'active'
                  : ''
              "
            >
              My Activities
            </li>
            <li
              @click="goToMenu('uni-list')"
              :class="params == 'uni-list' ? 'active' : ''"
            >
              University Shortlisted
            </li>
            <li
              @click="goToMenu('uni-requirement')"
              :class="params == 'uni-requirement' ? 'active' : ''"
            >
              University Requirements
            </li>
            <!-- <li
              @click="goToMenu('my-files')"
              :class="params == 'my-files' ? 'active' : ''"
            >
              My Files
            </li> -->
          </ul>
        </div>
        <div class="navbar-button d-md-block" :class="!menu ? 'd-none' : ''">
          <button class="btn btn-allin bg-secondary px-4" @click="handleLogout">
            <strong> Sign Out </strong>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: "header",
  data() {
    return {
      menu: false,
      params: "",
    };
  },
  methods: {
    showMenu() {
      this.menu = true;
    },

    goToMenu(page) {
      this.menu = false;
      this.$router.push({ path: "/user/" + page });
    },

    handleLogout() {
      // localStorage.removeItem("token");
      // localStorage.removeItem("role");
      // localStorage.removeItem("mentee");
      localStorage.clear();
      this.$router.push({ path: "/" });
      this.$alert.toast("success", "You Successfully Logout");
    },
  },
  watch: {
    $route(to) {
      this.params = to.params.menu;
    },
  },
  created() {
    if (this.$route.params.menu) {
      this.params = this.$route.params.menu;
    }
  },
};
</script>
<style scoped>
.navbar-menu {
  margin-left: 150px;
}

@media only screen and (max-width: 800px) {
  .navbar-menu {
    margin-left: 0;
  }
}
</style>