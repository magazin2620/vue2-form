<template>
  <div id="app" class="container py-4">
    <TheHeader />

    <div class="row">
      <div class="col-12">
        <form @submit.prevent="onSubmit">
          <BaseInput
            label="First Name:"
            :value="$store.state.user.firstName"
            @input="updateUser('firstName', $event)"
            :validator="$v.form.firstName"
          />

          <BaseInput
            label="Last Name:"
            :value="$store.state.user.lastName"
            @input="updateUser('lastName', $event)"
            :validator="$v.form.lastName"
          />

          <BaseInput
            label="Email:"
            :value="$store.state.user.email"
            @input="updateUser('email', $event)"
            :validator="$v.form.email"
            type="email"
          />

          <BaseInput
            label="Telephone:"
            type="text"
            :value="$store.state.user.telephone"
            @input="updateUser('telephone', $event)"
            :validator="$v.form.telephone"
            :mask="'(###)###-####'"
          />

          <BaseSelect
            label="Countries:"
            :options="countries"
            :value="$store.state.user.country"
            @input="updateUser('country', $event)"
            :validator="$v.form.country"
          />

          <div class="form-group">
            <button :disabled="$v.$error" type="submit" class="btn btn-primary">
              Submit
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { mapState } from "vuex";
import { alpha, email, required } from "vuelidate/lib/validators";
import BaseInput from "@/components/BaseInput";
import BaseSelect from "@/components/BaseSelect";
import TheHeader from "@/components/TheHeader";
export default {
  name: "app",
  components: { BaseInput, BaseSelect, TheHeader },
  data() {
    return {
      countries: [
        { label: "USA", value: "us" },
        { label: "China", value: "ch" },
        { label: "Russia", value: "ru" },
        { label: "India", value: "in" },
      ],
    };
  },
  computed: {
    ...mapState({ form: "user" }),
  },
  validations: {
    form: {
      firstName: { alpha, required },
      lastName: { alpha },
      email: { email, required },
      telephone: {
        validPhone: (phone) =>
          phone.match(/((\(\d{3}\) ?)|(\d{3}-))?\d{3}-\d{4}/) !== null,
      },
      country: { required },
    },
  },
  methods: {
    onSubmit() {
      this.$v.$touch();
      if (!this.$v.$invalid);
      axios
        .post("http://localhost:3000/new_user", { params: this.form })
        .then((response) => {
          console.log("Form has been posted", response);
        })
        .catch((err) => {
          console.log("An error ocurred", err);
        });
    },
    updateUser(property, value) {
      this.$store.dispatch("updateUserData", {
        property,
        value,
      });

      this.$v.form[property].$touch();
    },
  },
  created() {
    this.$store.dispatch("getLoggedInUser");
  },
};
</script>
