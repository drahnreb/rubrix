<template>
  <div class="container">
    <form class="form" @submit.prevent="userLogin">
      <Rubrix class="form__logo" />
      <p class="form__title">Track and iterate on data for AI</p>
      <div class="form__input" :class="{ active: login.username }">
        <input v-model="login.username" type="text" placeholder="ID" />
      </div>
      <div class="form__input" :class="{ active: login.password }">
        <input
          v-model="login.password"
          type="password"
          placeholder="Password"
        />
      </div>
      <ReButton type="submit" class="form__button button-primary"
        >Enter</ReButton
      >
      <p>{{ error }}</p>
    </form>
  </div>
</template>

<script>
export default {
  layout: "app",
  data() {
    return {
      error: undefined,
      login: {
        username: "",
        password: "",
      },
    };
  },
  async mounted() {
    if (this.$auth.loggedIn) {
      return;
    }
    if (!this.$config.securityEnabled) {
      await this.$auth.setUser("local");
      await this.$auth.setUserToken("mock-token");
      this.nextRedirect();
    }
  },
  methods: {
    nextRedirect() {
      const redirect_url = this.$nuxt.$route.query.redirect || "/";
      this.$router.push({
        path: redirect_url,
      });
    },
    async userLogin() {
      try {
        await this.$store.dispatch("entities/deleteAll");
        await this.$auth.loginWith("local", {
          data: `username=${this.login.username}&password=${this.login.password}`,
        });
        this.nextRedirect();
      } catch (err) {
        this.error = err;
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.container {
  background: $line-light-color;
  display: flex;
  align-items: center;
  min-height: 100vh;
}

.form {
  border-radius: 3px;
  background: $lighter-color;
  margin: auto;
  display: inline-block;
  padding: 2em 4em 1.5em 4em;
  box-shadow: 29px 29px 57px #d0d0d1, -29px -29px 57px #ffffff;
  text-align: center;
  max-width: 300px;
  // box-shadow: 0 2px 44px 16px #DFDFDF;
  border-radius: 3px;
  &__logo {
    text-align: center;
    margin-bottom: 1em;
  }
  &__title {
    text-align: center;
    @include font-size(20px);
    line-height: 1.2em;
    margin: 0 auto 2em auto;
  }
  &__button {
    margin: 2em auto 0 auto;
    text-align: center;
    display: block;
  }
  &__input {
    position: relative;
    display: block;
    margin-bottom: 1em;
    input {
      border: 1px solid palette(grey, smooth);
      padding: 0 1em;
      outline: none;
      background: transparent;
      min-height: 40px;
      min-width: 200px;
    }
  }
}
input:-webkit-autofill {
  box-shadow: 0 0 0px 1000px $lighter-color inset;
}
</style>
