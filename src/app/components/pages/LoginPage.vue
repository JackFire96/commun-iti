<script setup lang="ts">
import { reactive } from "vue";
import { useRouter } from "vue-router";
import { AuthenticationService } from "@/modules/authentication/services";
import { ElMessage } from "element-plus";
import { useProvider } from "@/app/platform";
import type { FormInstance, FormRules } from "element-plus";

const [authService] = useProvider([AuthenticationService]);
const router = useRouter();

const loginModel = reactive({
  username: "",
  password: ""
});

const userNameRegex = /^(\w+)$/i;

const loginFormRules = reactive<FormRules>({
  username: [
    {
      required: true,
      message: "Pseudo obligatoire",
      pattern: userNameRegex
    }
  ],
  password: [
    {
      required: true,
      message: "Mot de passe obligatoire"
    }
      
  ]
});

async function onSubmit(this: any, form?: FormInstance) {
  if (!form) {
    return;
  }

  try {
    await form.validate();

    await authService.authenticate({
          username: this.form.username,
          password: this.form.password,
    });

    this.$router.push('/app');
  } catch (e) {
    console.error("Erreur d'authentification :", e);
    return;
  }
}
</script>
<template>
  <div class="login center-children full-h">
    <main class="width-s">
      <h1 class="login-title">Se connecter</h1>

      <div class="login-form">
        <el-form
          ref="form"
          :model="loginModel"
          :rules="loginFormRules"
          label-position="top"
          class="login-form"
          @submit.prevent=""
        >
          <el-form-item label="Pseudo" prop="username"> </el-form-item>
          <el-input type="text" name="Pseudo"></el-input>
          <el-form-item label="Mot de passe" prop="password"> </el-form-item>
          <el-input type="password" name="Mot de passe"></el-input>

          <el-form-item>
            <div class="form-actions">
              <el-button type="primary" native-type="submit">Connexion</el-button>
              <router-link to="/register">Je n'ai pas de compte</router-link>
            </div>
          </el-form-item>
        </el-form>
      </div>
    </main>
  </div>
</template>
<style scoped lang="scss">
@use "@/app/styles/var";
@use "@/app/styles/mixins";
@use "sass:list";

.form-actions {
  width: 100%;
  display: flex;
  justify-content: space-between;
}
</style>
