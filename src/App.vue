<script setup lang="ts">
import { ref } from "vue";

interface userObject {
  name: string;
  email: string;
  password: string;
}

interface errors {
  email: string;
  password: string;
}

const usersList = <userObject[]>[
  {
    name: "Amir Samimi",
    email: "amirnsamimi@gmail.com",
    password: "123456",
  },
  {
    name: "Javid Izadfar",
    email: "Javid.Izadfar@gmail.com",
    password: "654321",
  },
  {
    name: "Alireza Gosili",
    email: "Alireza.Gosili@gmail.com",
    password: "123654",
  },
];


const date:Date = new Date()
const dayState = ref<string>("")
const name = ref<string>("")
const email = ref<string>("");
const password = ref<string>("");
const isLoggedIn = ref<boolean>(false);
const errors = ref<errors>({ email: "", password: "" });
const sanitizedAndValidatedEmail = ref<string>("");

const emailSanitizer = (email: any): string => {
  const emailValidatorRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (emailValidatorRegex.test(email)) {
    errors.value = { email: "", password: "" };
    return email
      .replace(/&/g, "&amp;")
      .replace(/</g, "&lt;")
      .replace(/>/g, "&gt;")
      .replace(/"/g, "&quot;")
      .replace(/'/g, "&#039;");
  } else {
    console.log("hi");
    return "";
  }
};

const dateHandler = ():void => {
  if(date.getHours() < 12){
    dayState.value = "Good Morning"
  }else if(date.getHours() > 12 && date.getHours() < 18){
    dayState.value = "Good Afternoon"
  }else if(date.getHours() > 18 ){
    
    dayState.value = "Good Evening"
  }else{
    dayState.value = "Good Day"
  }
}


const logoutHandler = ():void => {
isLoggedIn.value = false;
}

const formHandler = (): void => {
  dateHandler()
  let userFound: boolean = false;
  if (email.value && password.value) {
    sanitizedAndValidatedEmail.value = emailSanitizer(email.value);
    if (sanitizedAndValidatedEmail.value && password.value) {
      usersList.forEach((user) => {
        if (
          user.email.toLowerCase() ===
          sanitizedAndValidatedEmail.value.toLowerCase()
        ) {
          userFound = true;
          if (user.password === password.value) {
            name.value = user.name
            isLoggedIn.value = true;
            return;
          } else {
            errors.value = {
              email: "",
              password: "password not matched. please check your password.",
            };
          }
          return;
        }
      });
      if (!userFound) {
        errors.value = {
          ...errors.value,
          email: "No user found. Please check your email.",
        };
      }
    } else if (sanitizedAndValidatedEmail.value && !password.value) {
      errors.value = { email: "", password: "please enter your password" };
    } else if (!sanitizedAndValidatedEmail.value && password.value) {
      errors.value = { password: "", email: "enter a valid email address." };
    } else {
      errors.value = {
        email: "please enter your email address.",
        password: "please enter your password",
      };
    }
  } else if (email.value && !password.value) {
    errors.value = { email: "", password: "please enter your password" };
  } else if (!email.value && password.value) {
    errors.value = { password: "", email: "please enter your email address." };
  } else {
    errors.value = {
      email: "please enter your email address.",
      password: "please enter your password",
    };
  }
};
</script>

<template>
  <div v-if="isLoggedIn" class="container">
    <div>
    <div>  {{ dayState + " " + name }}</div>
    <br>
    <div>
    <button @click="logoutHandler" class="logout">Logout</button>
  </div>
  </div>
  </div>
  <div v-if="!isLoggedIn" class="container">
    <form @submit.prevent="formHandler">
      <label>
        email
        <input v-model="email" type="text" />
        <span>{{ errors.email }}</span>
      </label>
      <label>
        password
        <input v-model="password" type="password" />
        <span>{{ errors.password }}</span>
      </label>
      <button class="login">Login</button>
    </form>
  </div>
</template>

<style>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}
form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}
label {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

input {
  outline: none;
  border-radius: 0.5rem;
  border: 1px solid black;
  padding: 0.5rem 1rem;
  min-width: 300px;
  width: 100%;
  max-width: 600px;
}

span {
  overflow: hidden;
  text-wrap: wrap;
  overflow-wrap: break-word;
  color: red;
}

.login {
  width: max-content;
  padding: 0.5rem 1rem;
  border-radius: 0.5rem;
  border: 1px solid green;
  background-color: transparent;
  color: green;
}

.logout {
  width: max-content;
  padding: 0.5rem 1rem;
  border-radius: 0.5rem;
  border: 1px solid red;
  background-color: transparent;
  color: red;
}
</style>
