<template>
  <div class="container d-flex flex-wrap">
    <div class="col-12 col-md-6 p-3">
      <div class="border rounded p-3">
        <h2>操作</h2>
        <form @submit.prevent>
          <BFormControls
              class="mb-3"
              label="名字"
              v-model="formDate.name"/>

          <BFormControls
              class="mb-3"
              label="年齡"
              type="number"
              v-model="formDate.age"/>

          <div class="d-flex gap-1">
            <button class="btn btn-success" @click="edit">修改</button>
            <button class="btn btn-warning" @click="create">新增</button>
          </div>
        </form>
      </div>
    </div>

    <div class="col-12 col-md-6 p-3">
      <div class="border rounded p-3">
        <table class="table">
          <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">名字</th>
            <th scope="col">年齡</th>
            <th scope="col">操作</th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="(v,i) in users" :key="`user_${i}`">
            <th scope="row">{{ v.id }}</th>
            <td>{{ v.name }}</td>
            <td>{{ v.age }}</td>
            <td class="d-flex gap-1">
              <button class="btn btn-success" @click="selectUser(v)">
                修改
              </button>
              <button class="btn btn-danger" @click="remove(v)">
                刪除
              </button>
            </td>
          </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>


<script setup lang="ts">
import axios from 'axios'
import {onMounted, ref} from 'vue';
import BFormControls from "./components/BFormControls.vue";

interface User {
  id: number;
  name: string;
  age: number;
}

const baseUrl = 'https://38780.wu.elitepro.ltd' // 由面試官提供
const users = ref<User[]>([])
const formDate = ref({
  // id readonly
  id: 0,
  name: '',
  age: 0,
})
const create = () => {
  if (!formDate.value.name.trim()) {
    alert("請填寫名字");
    return;
  }
  const age = parseInt(formDate.value.age as unknown as string, 10);
  if (!Number.isInteger(age) || age <= 0) {
    alert("年齡必須為大於 0 的整數");
    return;
  }

  if (!confirm(`確定要新增用戶 ${formDate.value.name} 嗎？`)) {
    return;
  }

  axios.post(baseUrl + '/api/user', {
    name: formDate.value.name.trim(),
    age: age
  }).then(res => {
    alert("新增成功！");
    
    
    users.value.push({
      id: users.value[users.value.length - 1].id+1,  // 確保 API 回應包含 `id`
      name: formDate.value.name.trim(),
      age: age
    });
  console.log(users.value);
    formDate.value = { id: 0, name: '', age: 0 }; // 清空表單
  }).catch(err => {
    console.error("新增失敗", err);
    alert("新增失敗，請稍後再試");
  });
};

const edit = () => {
  if (!formDate.value.id) {
    alert("請先選擇要修改的用戶");
    return;
  }
  const age = parseInt(formDate.value.age as unknown as string, 10);
  if (!Number.isInteger(age) || age <= 0) {
    alert("年齡必須為大於 0 的整數");
    return;
  }

  if (!confirm(`確定要修改用戶 ${formDate.value.name} 嗎？`)) {
    return;
  }

  axios.put(baseUrl + '/api/user', {
    id: formDate.value.id,
    name: formDate.value.name.trim(),
    age: age
  }).then(() => {
    alert("修改成功！");


    const index = users.value.findIndex(u => u.id === formDate.value.id);
    if (index !== -1) {
      users.value[index] = { ...formDate.value };
    }

    formDate.value = { id: 0, name: '', age: 0 }; // 清空表單
  }).catch(err => {
    console.error("修改失敗", err);
    alert("修改失敗，請稍後再試");
  });
};

const selectUser = (user: User) => {
  // 複製 user 的值，而不是直接賦值，避免影響原數據
  formDate.value = { ...user };
};

const remove = (user: User) => {
  if (!confirm(`確定要刪除用戶 ${user.name} 嗎？`)) {
    return;
  }

  axios.delete(baseUrl + '/api/user', {
    data: { id: user.id }
  }).then(() => {
    alert("刪除成功！");

    // **手動從 `users` 移除該用戶**
    users.value = users.value.filter(u => u.id !== user.id);

  }).catch(err => {
    console.error("刪除失敗", err);
    alert("刪除失敗，請稍後再試");
  });
};
const getUsers = () => { 
  axios({ 
    method: 'get', 
    url: baseUrl + '/api/user', 
  }).then(res => { 
    const {data} = res.data 
    users.value = data 
  }).catch(err => { 
    console.log(err) 
  }) 
 
} 
 
const setupPage = () => { 
  getUsers() 
} 
 
onMounted(setupPage) 
</script> 
 
<style scoped> 
 
</style> 