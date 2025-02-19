<template>
  <div class="row justify-content-center pt-3">
    <template v-if="currentUser != null">
      <div class="dropdown">
        <button
          class="btn btn-default dropdown-toggle"
          type="button"
          id="userDropdown"
          aria-haspopup="true"
          aria-expanded="false"
          data-toggle="dropdown"
          @click="isUserDropdownVisible = !isUserDropdownVisible"
        >
          <UserBubble :creator="this.currentUserInfo" :size="24" />&nbsp;
          <span class="user-display-name">{{ currentUser }}</span
          >&nbsp;
        </button>
        <div
          class="dropdown-menu"
          style="display: block"
          aria-labelledby="UserDropdown"
          v-show="isUserDropdownVisible"
        >
          <a
            type="button"
            class="disabled dropdown-item btn login btn-link btn-default"
            aria-label="Account settings"
            ><font-awesome-icon icon="cog" /> Account settings</a
          >
          <a
            type="button"
            class="dropdown-item btn login btn-link btn-default"
            aria-label="Logout"
            :href="this.apiUrl + '/logout'"
            ><font-awesome-icon icon="sign-out-alt" /> Logout</a
          >
        </div>
      </div>
    </template>
    <template v-else>
      <div class="dropdown">
        <button
          class="btn btn-default dropdown-toggle"
          type="button"
          id="loginDropdown"
          data-toggle="dropdown"
          aria-haspopup="true"
          aria-expanded="false"
          @click="isLoginDropdownVisible = !isLoginDropdownVisible"
        >
          <font-awesome-icon icon="sign-in-alt" />&nbsp;Login
        </button>
        <div
          class="dropdown-menu"
          style="display: block"
          aria-labelledby="loginButton"
          v-show="isLoginDropdownVisible"
        >
          <a
            type="button"
            class="dropdown-item btn login btn-link btn-default"
            aria-label="Login via GitHub"
            :href="this.apiUrl + '/login/github'"
            ><font-awesome-icon :icon="['fab', 'github']" /> Login via GitHub</a
          >
          <a
            type="button"
            class="disabled dropdown-item btn login btn-link btn-default"
            aria-label="Login via ORCID"
            :href="this.apiUrl + '/login/orcid'"
            ><font-awesome-icon class="orcid-icon" :icon="['fab', 'orcid']" /> Login via ORCID</a
          >
          <a
            type="button"
            class="disabled dropdown-item btn login btn-link btn-default"
            aria-label="Login via email"
            :href="this.apiUrl + '/login/email'"
            ><font-awesome-icon :icon="['fa', 'envelope']" /> Login via email</a
          >
        </div>
      </div>
    </template>
  </div>
</template>

<script>
import { API_URL } from "@/resources.js";
import UserBubble from "@/components/UserBubble.vue";
import { getUserInfo } from "@/server_fetch_utils.js";
export default {
  data() {
    return {
      isLoginDropdownVisible: false,
      isUserDropdownVisible: false,
      apiUrl: API_URL,
      currentUser: null,
      currentUserInfo: {},
    };
  },
  components: {
    UserBubble,
  },
  props: {
    modelValue: Boolean,
  },
  watch: {
    modelValue(newValue) {
      if (newValue) {
        this.openModal();
      }
      if (!newValue) {
        this.closeModal();
      }
    },
  },
  methods: {
    async getUser() {
      let user = await getUserInfo();
      if (user != null) {
        this.currentUser = user.display_name;
        this.currentUserInfo = {
          display_name: user.display_name || "",
          immutable_id: user.immutable_id,
          contact_email: user.contact_email || "",
        };
        console.log(this.currentUser, this.currentUserInfo);
      }
    },
  },
  mounted() {
    this.getUser();
  },
};
</script>

<style scoped>
.btn:disabled {
  cursor: not-allowed;
}

.user-display-name {
  font-weight: bold;
}

.orcid-icon {
  color: #a6ce39;
}
</style>
