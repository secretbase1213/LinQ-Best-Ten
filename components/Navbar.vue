<template>
  <div>
    <b-navbar toggleable="lg" type="dark" style="background-color: #563d7c;">
      <div style="margin: auto">
        <b-navbar-brand>
          <img src="@/assets/logo.png" class="d-inline-block align-top" alt="LinQ" width="50">
        </b-navbar-brand>
        <b-navbar-brand class="ml-2" style="font-size: 14px">あなたが選ぶLinQ楽曲ベスト10</b-navbar-brand>
        <b-navbar-toggle target="navbar-toggle-collapse">
          <template #default="{ expanded }">
            <b-icon v-if="expanded" icon="chevron-bar-up"></b-icon>
            <b-icon v-else icon="chevron-bar-down"></b-icon>
          </template>
        </b-navbar-toggle>
        <b-collapse id="navbar-toggle-collapse" is-nav>
          <b-navbar-nav class="ml-auto">
            <b-nav-item href="https://www.loveinq.com/" target="_blank">LinQオフィシャルサイト</b-nav-item>
            <b-nav-item href="https://ws.formzu.net/fgen/S32747474/" target="_blank">リクエスト入力フォーム</b-nav-item>
          </b-navbar-nav>
        </b-collapse>
      </div>
    </b-navbar>
  </div>
</template>

<script>
export default {
  name: 'Navbar',
};
</script>

<style scoped>

</style>