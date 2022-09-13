<template>
    <div class="container">
        <table class="table my-5">
            <thead>
            <tr>
                <th scope="col">#</th>
                <th scope="col">Name</th>
                <th scope="col">Email</th>
                <th scope="col"></th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="(user, index) in userList" :key="index">
                <th scope="row">
                    {{index}}
                </th>
                <td>
                    {{user.name}}
                </td>
                <td>
                    {{user.email}}
                </td>
                <td>
                    <button class="btn btn-primary"
                            type="submit" @click="showPermissions(user.id)">
                        show
                    </button>
                </td>
            </tr>
            </tbody>
        </table>

        <table class="table w-50 text-center mx-auto">
            <thead>
            <tr>
                <th scope="col">#</th>
                <th scope="col">Permission Name</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="(permission, index) in permissionList" :key="index">
                <th scope="row">
                    {{index}}
                </th>
                <td>
                    {{permission.permission_name}}
                </td>
            </tr>
            </tbody>
        </table>
        <div class="my-3 p-4 border text-center"
             :class="{'d-block' : selectedUser.length!==0, 'd-none' : selectedUser.length===0}">
            <div>
                {{selectedUser.name}}
            </div>
            <div>
                {{selectedUser.email}}
            </div>
            <span v-for="(perm, i) in permissionsForCurrentUser">
        {{perm}}
        <span v-if="i !== permissionsForCurrentUser.length-1">, </span>
        <span v-if="i === permissionsForCurrentUser.length-1">. </span>
      </span>
            <form @submit.prevent="addPermisionForCurrentUser">
                <input hidden v-model="form.user_id=selectedUser.id" id="user_id" name="user_id">
                <select class="form-select w-50 mx-auto"
                        id="permissions_for_user_id"
                        name="permissions_for_user_id"
                        v-model="form.permissions_for_user_id">
                    <option v-for="permission in permissionList" :value="permission.id">
                        {{ permission.permission_name }}
                    </option>
                </select>
                <button type="submit" class="btn btn-dark">Add</button>
            </form>
        </div>
    </div>
</template>

<script>

    import axios, * as others from 'axios';

    export default {
        name: 'HomeView',
        components: {},
        data() {
            return {
                selectedUser: '',
                permissionsForCurrentUser: '',
                userList: '',
                permissionList: '',
                form: {
                    user_id: '',
                    permissions_for_user_id: ''
                }
            }
        },
        created() {
            axios.get('http://127.0.0.1:8000/api/user')
                .then((response) => {
                    this.userList = response.data.data;
                });

            axios.get('http://127.0.0.1:8000/api/users/permission')
                .then((response) => {
                    this.permissionList = response.data.data;
                });
        },
        methods: {
            showPermissions(userId) {
                axios.get('http://127.0.0.1:8000/api/user/' + userId)
                    .then((response) => {
                        this.selectedUser = response.data.data;
                    });
                axios.get('http://127.0.0.1:8000/api/users/permission/' + userId)
                    .then((response) => {
                        this.permissionsForCurrentUser = response.data.data;
                    });
            },
            addPermisionForCurrentUser() {
                axios.post('http://127.0.0.1:8000/api/users/permission', this.form);
            }
        }
    }
</script>
