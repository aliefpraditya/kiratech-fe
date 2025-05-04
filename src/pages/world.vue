<template>
  <div class="table-container">
    <table class="user-table" v-if="users.length > 0">
      <thead>
        <tr class="head-row">
          <th>Date</th>
          <th>Name</th>
          <th>Gender</th>
          <th>Country</th>
          <th>Email</th>
        </tr>
      </thead>
      <tbody>
        <tr
          class="body-row"
          v-for="(user, index) in filteredUsers"
          :key="index"
          @click="showDetailPopup(index)"
        >
          <td class="light-grey">{{ formatDate(user.dob.date) }}</td>
          <td class="dark-grey bold">{{ user.fullname }}</td>
          <td class="light-grey">{{ user.gender }}</td>
          <td class="dark-grey">{{ user.location.country }}</td>
          <td class="light-grey">{{ user.email }}</td>
        </tr>
      </tbody>
    </table>
    <div class="button-container">
      <button class="refresh-button" @click="getUserList()">Refresh</button>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "AppTable",
  data() {
    return {
      users: [],
      selectedUser: {},
      displayPopup: false,
      searchKeyword: "",
    };
  },
  async created() {
    await this.getUserList();
  },
  computed: {
    filteredUsers() {
      return this.users.filter((user) =>
        user.fullname.toLowerCase().includes(this.searchKeyword.toLowerCase())
      );
    },
  },
  methods: {
    async getUserList() {
      try {
        const response = await axios.get(
          "https://randomuser.me/api/?results=20"
        );
        this.users = response.data.results;
        this.users.forEach((item) => {
          item.fullname = item.name.first + " " + item.name.last;
        });
      } catch (error) {
        console.error("Error fetching users:", error);
      }
    },
    formatDate(date) {
      if (!date) return "-";
      return new Intl.DateTimeFormat("en-GB", {
        day: "2-digit",
        month: "short",
        year: "numeric",
      }).format(new Date(date));
    },
    showDetailPopup(index) {
      this.selectedUser = this.users[index];
      this.displayPopup = true;
    },
  },
};
</script>

<style lang="scss" scoped>
.table-container {
  max-width: 1280px;
  margin: 40px auto 130px;
}
.search-container {
  width: 360px;
  margin-left: auto;
  input {
    padding: 8px;
    width: 100%;
    border: 1px solid #bcbcbc;
    color: #303030;
    border-radius: 8px;
    outline: none;
    margin-bottom: 10px;
  }
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
