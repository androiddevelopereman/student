<template>
  <div class="filters-table">
    <div class="filter-left">
      <div class="search-container">
        <img src="../../../assets/search.svg" alt="Search Icon" />
        <input type="text" class="search-input" v-model="searchQuery" :placeholder="$t('Search')" />
      </div>
      <select v-model="selectedFilter" class="filter-dropdown search-container">
        <option disabled value="">All</option>
        <option value="filter1">license expired</option>
        <option value="filter2">license not expired</option>
        <option value="filter3">Tracking</option>
        <option value="filter4">Without Tracking</option>
      </select>

      <div class="export-button">
        <button class="btn-export" @click="toggleExport">
          {{ $t("Export") }}
        </button>
        <ul v-if="showExportDropdown" class="export-options">
          <li @click="exportData('excel')">Excel</li>
          <li @click="exportData('pdf')">PDF</li>
        </ul>
      </div>
      <button class="btn-refresh" title="Notifications">
        <img src="../../../assets/refresh.svg" class="icon" />
      </button>
      <div><i class="fa-duotone fa-solid fa-arrow-rotate-right"></i></div>
    </div>
    <div class="columns-button">
      <button class="btn btn-primary my-3" data-bs-toggle="modal" data-bs-target="#driverModal" @click="openAddModal">
        {{ $t("Add Driver") }}
      </button>
      <DriverModal :mode="modalMode" :user="selectedUser" @submit="handleUserSubmit" />
    </div>
  </div>
  <table class="table">
    <thead>
      <tr>
        <th>#</th>
        <th>{{ $t("Driver Name") }}</th>
        <th>{{ $t("Mobile #") }}</th>
        <th>{{ $t("Email") }}</th>
        <th>{{ $t("Dallas") }}</th>
        <th></th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(driver, index) in drivers" :key="index">
        <td>{{ index + 1 }}</td>
        <td class="driver-name">{{ driver.name }}</td>
        <td>{{ driver.mobile || "-" }}</td>
        <td>{{ driver.email || "-" }}</td>
        <td>{{ driver.dallas || "-" }}</td>
        <td>
          <div class="dropdown">
            <button class="btn btn-link" type="button" id="dropdownMenuButton" data-bs-toggle="dropdown"
              aria-expanded="false">
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor"
                class="bi bi-three-dots-vertical" viewBox="0 0 16 16">
                <path
                  d="M3 9.5a1.5 1.5 0 1 1 3 0 1.5 1.5 0 0 1-3 0zm0-5a1.5 1.5 0 1 1 3 0 1.5 1.5 0 0 1-3 0zm0 10a1.5 1.5 0 1 1 3 0 1.5 1.5 0 0 1-3 0z" />
              </svg>
            </button>
            <ul class="dropdown-menu" aria-labelledby="dropdownMenuButton">
              <li>
                <a class="dropdown-item" href="#" data-bs-toggle="modal" data-bs-target="#driverModal"
                  @click.prevent="openEditModal(driver)">{{ $t("Edit") }}</a>
              </li>
              <li>
                <a class="dropdown-item" href="#" data-bs-toggle="modal" data-bs-target="#detailsModel"
                  @click.prevent="viewDetails(driver)">{{ $t("Details") }}</a>
              </li>
              <li>
                <a class="dropdown-item" data-bs-toggle="modal" data-bs-target="#deleteModal"
                  @click="openDeleteModal(driver)">
                  {{ $t("Delete") }}
                </a>
              </li>
            </ul>
          </div>
        </td>
      </tr>
    </tbody>
    <div class="modal fade" id="detailsModel" tabindex="-1" aria-labelledby="detailsModelLabel" aria-hidden="true"
      data-bs-backdrop="false">
      <div class="modal-dialog">
        <div class="modal-content" v-if="selectedUser">
          <div class="modal-header">
            <h5 class="modal-title" style="color: gray" id="exampleModalLabel">
              {{ $t(" User details") }}
            </h5>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="d-flex justify-content-between mt-3 mb-4 px-4" style="">
            <h2>{{ selectedUser?.name }}</h2>
            <button class="btn-profile">
              {{ $t(" User Profile") }}
            </button>
          </div>
          <div class="modal-body body">
            <div class="one">
              <div>
                <span> {{ $t(" Name") }}</span>
                <p>{{ driverDetails?.name }}</p>
              </div>
              <div>
                <span> {{ $t(" Mobile #") }}</span>
                <p>{{ driverDetails?.mobile }}</p>
              </div>
            </div>
            <hr />
            <div class="one">
              <div>
                <span> {{ $t("Phone") }} </span>
                <p>{{ driverDetails?.phone }}</p>
              </div>
              <div>
                <span> {{ $t("Email") }}</span>
                <p>{{ driverDetails?.email }}</p>
              </div>
            </div>
            <hr />
            <div class="one">
              <div>
                <span> {{ $t(" Position") }} </span>
                <p>{{ driverDetails?.position }}</p>
              </div>

            </div>
            <hr />
            <div class="one">
              <div>
                <span> {{ $t("Birthate") }} </span>
                <p>{{ driverDetails?.birth_date }}</p>
              </div>
              <div>
                <span> {{ $t("Age") }}</span>
                <p>{{ driverDetails?.age }}</p>
              </div>
            </div>
            <hr />
            <div class="one">
              <div>
                <span> {{ $t("licenseNumber") }} </span>
                <p>{{ driverDetails?.licenseNumber }}</p>
              </div>
              <div>
                <span> {{ $t("licenseType") }}</span>
                <p>{{ driverDetails?.licenseType }}</p>
              </div>
            </div>

          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary footer-details" data-bs-dismiss="modal">
              {{ $t(" Close") }}
            </button>
          </div>
        </div>
      </div>
    </div>
  </table>
  <DeleteDriverModal @delete-confirmation="deleteDriver" />
</template>

<script setup>
import { ref, onMounted, computed } from "vue";
import axios from "axios";
import "bootstrap/dist/css/bootstrap.min.css";
import "bootstrap/dist/js/bootstrap.bundle.min.js";
import DriverModal from "./DriverModal.vue";
import DeleteDriverModal from "./deleteDriverModal.vue";
const users = ref([]);
const modalMode = ref("add");
const selectedUser = ref(null);
const driverDetails = ref(null);
const openAddModal = () => {
  modalMode.value = "add";
  selectedUser.value = {
    name: "",
    mobile: "",
    email: "",
    position: "",
    licenseNumber: "",
    licenseType: "",
    licenseIssue: "",
    licenseExpiry: "",
    birth_date: "",
    phone: "",
    homeAddress: "",
    workAddress: "",
  };
};

const openEditModal = (user) => {
  modalMode.value = "edit";
  selectedUser.value = { ...user };
};

const openDeleteModal = (driver) => {
  selectedUser.value = { ...driver };
  modalMode.value = "delete";
};
const deleteDriver = async () => {
  console.log("delete", selectedUser.value);

  // call api to delete driver
  const authData = JSON.parse(localStorage.getItem("userData"));
  const token = authData.authorization.token;
  const selectedAccount = authData.user.accounts[0]?.id;
  const selectedLanguage = authData.user.accounts[0]?.language || "english";

  const response = await axios.delete(
    `http://192.168.100.22:8091/api/drivers/${selectedUser.value.id}`,
    {
      headers: {
        Authorization: `Bearer ${token}`,
        "Selected-Account": selectedAccount,
        "Selected-Language": selectedLanguage,
      },
    }
  );
};
const viewDetails = async (driver) => {
  selectedUser.value = { ...driver };
  // const modal = new bootstrap.Modal(document.getElementById("detailsModel"));
  // modal.show();
  await fetchDriver();

};
async function fetchDriver() {
  try {
    const authData = JSON.parse(localStorage.getItem("userData"));
    const token = authData.authorization.token;
    const selectedAccount = authData.user.accounts[0]?.id;
    const selectedLanguage = authData.user.accounts[0]?.language || "english";
    const response = await axios.get("http://192.168.100.22:8091/api/drivers/" + selectedUser.value.id, {
      headers: {
        Authorization: `Bearer ${token}`,
        "Selected-Account": selectedAccount,
        "Selected-Language": selectedLanguage,
        "Content-Type": "application/json",
      },
      withCredentials: true,
    });


    driverDetails.value = response.data.result;

  } catch (error) {
    console.error("حدث خطأ أثناء جلب بيانات السائقين:", error);
    return [];
  }
}
const handleUserSubmit = (userData) => {
  if (modalMode.value === "add") {
    users.value.push(userData);
  } else {
    const index = users.value.findIndex(
      (u) => u.username === userData.username
    );
    if (index !== -1) {
      users.value[index] = { ...userData };
    }
  }
};

const searchQuery = ref("");
const selectedFilter = ref("");
const value = ref([]);
const options = ["Test 1", "Test 2", "Test 3", "Test 4", "Test 5"];
const showExportDropdown = ref(false);
const drivers = ref([]);

function toggleExport() {
  showExportDropdown.value = !showExportDropdown.value;
}

function exportData(format) {
  alert(`Export data as ${format}`);
}

async function fetchDrivers() {
  try {
    const authData = JSON.parse(localStorage.getItem("userData"));
    const token = authData.authorization.token;
    const selectedAccount = authData.user.accounts[0]?.id;
    const selectedLanguage = authData.user.accounts[0]?.language || "english";
    const response = await axios.get("http://192.168.100.22:8091/api/drivers", {
      headers: {
        Authorization: `Bearer ${token}`,
        "Selected-Account": selectedAccount,
        "Selected-Language": selectedLanguage,
        "Content-Type": "application/json",
      },
      withCredentials: true,
    });

    console.log(response.data.result.data);
    drivers.value = response.data.result.data;
    return response.data;
  } catch (error) {
    console.error("حدث خطأ أثناء جلب بيانات السائقين:", error);
    return [];
  }
}

onMounted(async () => {
  console.log("mounted");
  await fetchDrivers();
});
</script>

<style scoped>
.filters-table {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 50px;
}

.filter-left {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 50px;
  gap: 10px;
}

.search {
  flex: 1;
}

.filter-dropdown,
.groups-filter {
  flex: 1;
}

.export-button,
.columns-button {
  position: relative;
}

.export-options,
.columns-options {
  position: absolute;
  background: white;
  border: 1px solid #ccc;
  list-style: none;
  padding: 0.5rem;
  margin: 0;
}

.export-options li,
.columns-options li {
  cursor: pointer;
  padding: 0.5rem 1rem;
}

.footer-details {
  margin-top: 10px;
  width: 100%;
  background-color: transparent;
  border-radius: 6px;
  border: solid 1px gray;
  padding: 10px 0px;
  color: rgb(49, 49, 49);
}

.export-options li:hover,
.columns-options li:hover {
  background: #f0f0f0;
}

.columns-button {
  position: relative;
  display: inline-block;
}

.columns-options {
  position: absolute;
  top: 100%;
  left: 0;
  background-color: white;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-top: 5px;
  padding: 10px;
  width: 200px;
  z-index: 1000;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.columns-options li {
  list-style: none;
  padding: 8px 10px;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 10px;
}

.columns-options li input[type="checkbox"] {
  cursor: pointer;
}

.search-container {
  display: flex;
  border: 1px solid #ccc;
  border-radius: 5px;
  padding: 10px 40px 10px 10px;
  align-items: center;

  border-radius: 5px;
  background-color: #fff;
  color: rgb(49, 49, 49);
  font-size: 16px;
}

.search-input {
  width: 100%;
  background-color: transparent;
  border: none;
  font-size: 16px;
  outline: none;
}

.search-container img {
  width: 20px;
  height: 20px;
  margin-right: 5px;
}

.search-placeholder {
  position: absolute;
  top: 50%;
  left: 10px;
  transform: translateY(-50%);
  display: flex;
  align-items: center;
  pointer-events: none;
  transition: opacity 0.3s ease;
}

.search-placeholder img {
  width: 20px;
  height: 20px;
  margin-right: 5px;
}

.search-input:focus+.search-placeholder,
.search-input:hover+.search-placeholder {
  opacity: 0;
}

.btn-export {
  padding: 10px 15px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #fff;
  color: rgb(49, 49, 49);
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
}

.btn-export:hover {
  background-color: #0056b3;
}

.one {
  display: flex;
  justify-content: space-between;
}

.btn-add {
  display: flex;
  gap: 10px;
  padding: 10px 15px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: green;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
  align-items: center;
}

.icon-plus {
  width: 10px;
  height: 10px;
  fill: #ffffff;
}

.btn-refresh {
  padding: 10px 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #fff;
  cursor: pointer;
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
}

.btn-refresh:hover {
  background-color: #0056b3;
}

.icon {
  width: 24px;
  height: 24px;
  fill: #0601ae;
}

.table {
  width: 95%;
  margin: 0 auto;
  background-color: transparent !important;
  border-radius: 10px;
  border: 1px solid #ccc;
}

.table th,
.table td {
  padding: 10px;
  font-size: 14px;
}

.table th {
  background-color: #f8f9fa;
  font-weight: bold;
}

.table .driver-name {
  color: #007bff;
  cursor: pointer;
}

.table .driver-name:hover {
  text-decoration: underline;
}
</style>
