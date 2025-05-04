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
            v-model="searchQuery"
            label="Search by name"
            clearable
            prepend-inner-icon="mdi-magnify"
            variant="outlined"
            dense
            hide-details
            @input="filterUsers"
          ></v-text-field>
        </v-col>
      </v-row>

      <v-row class="mt-0">
        <div class="table-container">
          <table class="user-table" v-if="users.length > 0">
            <thead>
              <tr class="head-row">
                <th>Name</th>
                <th>Gender</th>
                <th>Country</th>
                <th>Email</th>
              </tr>
            </thead>
            <tbody>
              <tr
                class="body-row"
                v-for="(user, index) in filteredUsers.slice(0, 10)"
                :key="index"
                @click="selectUser(index)"
              >
                <td class="light-grey">
                  {{ user.name.title }} {{ user.fullname }}
                </td>
                <td class="light-grey">{{ user.gender }}</td>
                <td class="dark-grey">{{ user.location.country }}</td>
                <td class="light-grey">{{ user.email }}</td>
              </tr>
            </tbody>
          </table>
          <div class="button-container">
            <button class="refresh-button" @click="fetchUsers">Refresh</button>
          </div>
        </div>
      </v-row>
      <v-pagination
        class="my-0"
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
  created() {
    this.fetchUsers();
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
    selectUser(index) {
      this.selectedUser = this.users[index];
      this.dialog = true;
      console.log("Selected user:", this.selectedUser);
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
.table-container {
  width: 3000px;
  margin: 40px auto 130px;
}

// clear table css
table,
caption,
tbody,
tfoot,
thead,
tr,
th,
td {
  margin: 0;
  padding: 0;
  border: 0;
  outline: 0;
  font-size: 100%;
  vertical-align: baseline;
  background: transparent;
  border-collapse: separate;
  border-spacing: 0 10px;
}
.user-table {
  width: 100%;
  margin-bottom: 39px;
  th {
    color: #bcbcbc;
    font-weight: 600;
    font-size: 13px;
    text-align: left;
    padding: 18px 37px;
    &:last-of-type {
      text-align: right;
    }
  }
  .body-row {
    box-shadow: 0px 2px 10px 0px #0000001a;
    border-radius: 8px;
    cursor: pointer;
    border-spacing: 0 10px;
    td {
      padding: 31px 37px;
      text-align: left;
      &:last-of-type {
        text-align: right;
      }
      &.light-grey {
        color: #979797;
        font-weight: 400;
      }
      &.dark-grey {
        color: #303030;
        font-weight: 400;
      }
      &.bold {
        font-weight: 600;
      }
    }
    &:hover {
      box-shadow: 0px 0px 0px 2px #35bad8;
      .bold {
        color: #35bad8;
      }
    }
  }
}
.button-container {
  text-align: center;
  .refresh-button {
    outline: none;
    border: none;
    pointer-events: auto;
    font-size: 14px;
    line-height: 130%;
    font-weight: 600;
    border-radius: 5px;
    padding: 16px 20px;
    width: 169px;
    height: 50px;
    background: #35bad8;
    color: #ffffff;
    cursor: pointer;
    &:hover {
      background: #55d9f6;
    }
    .icon {
      margin-right: 10px;
    }
  }
}
.popup-wrapper {
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 20%);
  position: fixed;
  top: 0px;
  left: 0px;
  .popup-container {
    position: relative;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 666px;
    border-radius: 8px;
    padding: 35px 47px;
    background: #ffffff;
    text-align: left;
    box-sizing: border-box;
    .close-button {
      position: fixed;
      top: 35px;
      right: 47px;
      cursor: pointer;
    }
    .name {
      color: #303030;
      font-size: 32px;
      font-weight: 700;
      margin-bottom: 40px;
    }
    .detail-container {
      display: flex;
      align-items: center;
      justify-content: flex-start;
      gap: 53px;
      margin-bottom: 19px;
      .label {
        color: #bcbcbc;
        font-size: 13px;
        width: 54px;
      }
      .data {
        color: #303030;
        font-size: 14px;
      }
    }
  }
}
</style>
