<template>

    <div style="width:100%; margin:0 auto; height: 100%;">
        <div class="page">
            <Sidebar />
            <div class="content-bar">
                <NavBar />
                <hr>
                <div style="display: flex; justify-content: space-between;">
                    <div>
                        <ProfileSidebar />
                    </div>
                    <div class="container all-content ">
                        <form class="form">
                            <div class="form-group">
                                <label for="company">Company</label>
                                <input id="company" type="text" v-model="formData.company_name" />
                            </div>

                            <div class="form-group">
                                <label for="address">Address</label>
                                <input id="address" type="text" v-model="formData.address" />
                            </div>
                            <div class="form-group">
                                <label for="email">Email</label>
                                <input id="email" type="email" v-model="formData.email" />
                            </div>

                            <div class="form-group">
                                <label for="mobile">Mobile</label>
                                <div class="mobile-input">
                                    <select id="mobile-code" v-model="formData.mobile_prefix">
                                        <option value="970">970</option>
                                    </select>
                                    <input id="mobile" type="text" v-model="formData.mobile" />
                                </div>
                            </div>

                            <div class="form-group">
                                <label for="phone">Phone #</label>
                                <input id="phone" type="text" v-model="formData.phone" />
                            </div>

                            <div class="form-group">
                                <label for="area">Area</label>
                                <input v-model="formData.region" id="area" type="text" />
                            </div>
                            <div class="form-group">
                                <label for="timezone">Time Zone</label>
                                <input id="timezone" type="text" v-model="formData.time_zone" />
                            </div>

                            <div class="form-group">
                                <label for="currency">Currency</label>
                                <select id="currency" v-model="formData.currency_id">
                                    <option value="">Select Currency</option>
                                </select>
                            </div>

                        </form>

                    </div>


                </div>
            </div>

        </div>
    </div>
</template>

<script setup>
import NavBar from '../components/NavBar.vue';
import Sidebar from '../components/Sidebar.vue';
import ProfileSidebar from './ProfileSidebar.vue';
import { ref } from "vue";

const formData = ref({
    company_name: null,
    address: null,
    email: null,
    mobile: null,
    phone: null,
    time_zone: null,
    currency_id: null,
    mobile_prefix: null,
    region: null,

});

const userDataJson = localStorage.getItem("userData");
const userData = ref(JSON.parse(userDataJson));
formData.value.company_name = userData.value.user.accounts[0].company_name;
formData.value.address = userData.value.user.accounts[0].address;
formData.value.mobile = userData.value.user.accounts[0].mobile;
formData.value.phone = userData.value.user.accounts[0].phone;
formData.value.time_zone = userData.value.user.accounts[0].time_zone;
formData.value.currency_id = userData.value.user.accounts[0].currency_id;
formData.value.email = userData.value.user.email;
formData.value.mobile_prefix = userData.value.user.accounts[0].mobile_prefix;
formData.value.region = userData.value.user.accounts[0].region;
</script>
<style scoped>
.page {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100%;
    background-color: #F0F5FA;
    display: flex;
}

.content-bar {
    background-color: #F0F5FA;
    width: 100% !important;
}

.all-content {
    display: flex;
    justify-content: space-between;
    max-width: 700px;
    margin: 20px auto;
    background: #1c5c9c;
    padding: 20px;
    border-radius: 8px;
    background-color: white;
    height: 70vh;
}

.form {
    max-width: 500px;
    padding: 5px;
}

.form-group {
    margin-bottom: 2px;
}

label {
    display: block;

    font-weight: bold;
}

input,
select {
    width: 100%;
    padding: 2px;
    box-sizing: border-box;
    border: 1px solid #ccc;
    border-radius: 4px;
}

.mobile-input {
    display: flex;
    gap: 10px;
}
</style>