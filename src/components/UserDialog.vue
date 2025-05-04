<template>
  <v-dialog v-model="dialog" max-width="500px">
    <v-card class="px-4 py-4">
      <v-card-title>
        <span class="text-h4"
          ><b>{{ user.name.first }} {{ user.name.last }}</b></span
        >
      </v-card-title>
      <v-card-text>
        <div v-if="user">
          <p><strong>Status:</strong> {{ user.status || "-" }}</p>
          <p><strong>Gender:</strong> {{ user.gender }}</p>
          <p><strong>Email:</strong> {{ user.email }}</p>
          <p><strong>Phone:</strong> {{ user.phone }}</p>
        </div>
        <div v-else>
          <p>No user selected.</p>
        </div>
      </v-card-text>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  name: "UserDialog",
  props: {
    user: {
      type: Object,
      default: null,
    },
  },
  data() {
    return {
      dialog: false,
    };
  },
  methods: {
    openDialog() {
      this.dialog = true;
    },
    closeDialog() {
      this.dialog = false;
    },
  },
};
</script>

<style scoped></style>
