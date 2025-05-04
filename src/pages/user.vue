<template>
  <v-container>
    <v-app-bar app color="white" dark class="mx-auto">
      <v-toolbar-title>
        <img src="@/assets/kiratech-logo.png" alt="Kiratech Logo" height="50" />
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn icon>
        <v-icon>mdi-bell</v-icon>
      </v-btn>
      <v-btn icon>
        <v-icon>mdi-cog</v-icon>
      </v-btn>
      <v-btn icon>
        <v-icon>mdi-logout</v-icon>
      </v-btn>
    </v-app-bar>
    <div
      class="bg-light-blue-lighten-2 text-white py-4 px-6 rounded-lg"
      style="max-height: 110px"
    >
      <v-row align="center" justify="space-between" class="">
        <img
          src="@/assets/avatar.png"
          alt="Kiratech Logo"
          height="120"
          class="mt-2 mb-3"
        />
        <v-col cols="3">
          <div class="mx-2">
            <h2>John Doe</h2>
            <span>Last Online: 2 days ago</span>
          </div>
        </v-col>

        <v-col>
          <v-btn
            variant="outlined"
            class="bg-sky-600 px-4 py-2 rounded shadow flex items-center mx-2 button-action"
          >
            <v-icon class="mr-2">mdi-send</v-icon> Send Message</v-btn
          >
          <v-btn
            variant="outlined"
            class="text-sky-600 px-4 py-2 rounded shadow flex items-center mx-2"
          >
            <v-icon class="mr-2">mdi-account-plus</v-icon> Add Friend</v-btn
          ></v-col
        >
      </v-row>
    </div>

    <div class="mt-5">
      <v-row>
        <v-col cols="5">
          <v-text-field
            class="rounded"
            v-model="searchQuery"
            label="Search Users"
            clearable
            dense
            prepend-inner-icon="mdi-magnify"
            variant="solo"
            @input="filterUsers"
          ></v-text-field>
        </v-col>
      </v-row>

      <v-row class="mt-0">
        <v-col cols="12">
          <v-data-table
            :headers="headers"
            :items="filteredUsers"
            :items-per-page="itemsPerPage"
            :row-props="row_classes"
            item-key="fullname"
            hide-default-footer
            class="all-row-align-top clickable-rows"
            @click:row="selectUser"
          >
            <template #[`item.fullname`]="{ item }">
              <span class="primary--text">
                {{ item.name.title }} {{ item.fullname }}
              </span>
            </template>
            <template #[`item.country`]="{ item }">
              <span class="primary--text">
                {{ item.location.country }}
              </span>
            </template>
          </v-data-table>
        </v-col>
      </v-row>
      <v-row align="center" justify="space-between">
        <v-col cols="12">
          <v-btn color="primary" @click="fetchUsers">
            <v-icon>mdi-refresh</v-icon> Refresh</v-btn
          >
        </v-col>
      </v-row>
      <v-pagination
        v-model="currentPage"
        :length="totalPages"
        @input="fetchUsers"
      ></v-pagination>
      <user-dialog v-model="dialog" :user="selectedUser" />
    </div>
  </v-container>
</template>

<script>
import axios from "axios";
import UserDialog from "@/components/UserDialog.vue";
export default {
  components: {
    UserDialog,
  },
  data() {
    return {
      users: [],
      filteredUsers: [],
      searchQuery: "",
      sortKey: "",
      sortOptions: ["Name", "Email"],
      headers: [
        { title: "Name", value: "fullname", sortable: true },
        { title: "Gender", value: "gender", sortable: true },
        { title: "Country", value: "country", sortable: true },
        { title: "Email", value: "email", sortable: true },
      ],
      dialog: false,
      selectedUser: null,
      currentPage: 1,
      itemsPerPage: 10,
      totalPages: 1,
    };
  },
  watch: {
    currentPage(nw, od) {
      this.fetchUsers();
    },
  },
  methods: {
    async fetchUsers() {
      try {
        const response = await axios.get(
          `https://randomuser.me/api/?page=${this.currentPage}&results=20`
        );
        this.users = response.data.results;

        this.filteredUsers = this.users.map((user) => ({
          ...user,
          fullname: ` ${user.name.first} ${user.name.last}`,
        }));
        this.totalPages = Math.ceil(
          response.data.info.results / this.itemsPerPage
        );
        console.log("Users fetched:", this.users);
      } catch (error) {
        console.error("Error fetching users:", error);
      }
    },
    filterUsers() {
      const query = this.searchQuery.toLowerCase();
      this.filteredUsers = this.users
        .filter((user) =>
          `${user.name.first} ${user.name.last}`.toLowerCase().includes(query)
        )
        .map((user) => ({
          ...user,
          fullname: ` ${user.name.first} ${user.name.last}`,
        }));
    },
    selectUser(event, user) {
      this.selectedUser = user.item;
      this.dialog = true;
      console.log("Selected user:", user.item);
    },

    row_classes({ item }) {
      return {
        class: {
          "user-item": true,
        },
      };
    },
  },
  mounted() {
    this.fetchUsers();
  },
};
</script>

<style lang="scss" scoped>
.v-container {
  margin-top: 20px;
}
.user-item {
  display: flex;
  flex-direction: column;
  padding: 16px;
  margin: 8px 0;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  background-color: black;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
.button-action {
  background: #ffffff;
  color: #20aac9;
  border: 1px solid #ffffff;
  text-decoration: none;
  font-size: 14px;

  box-sizing: border-box;
  &:hover {
    background-color: #2563eb;
    color: white;
    text-decoration: none;
    cursor: pointer;
    pointer-events: auto;
    .icon {
      margin-right: 10px;
      &.hover {
        display: block;
      }
      &.ori {
        display: none;
      }
    }
  }
}
.v-data-table {
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  padding: 16px;
  margin-top: 20px;
  th {
    background-color: #3b82f6;
    color: white;
    font-weight: bold;
    padding: 16px;
    border-radius: 8px 8px 0 0;
  }
  tbody {
    background-color: white;
    border-radius: 0 0 8px 8px;
  }
  tr {
    &:hover {
      background-color: #e0e7ff;
    }
  }
}
</style>
