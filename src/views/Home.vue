<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue';
import axios from 'axios';

export default {
  name: 'Home',
  components: {
    HelloWorld,
  },
  created() {
    axios({
      method: 'post',
      url: 'https://api.github.com/graphql',
      headers: {
        Authorization: 'bearer a3e874c4b3fa2b0d4a08cb52d935c9bebfb2061d',
      },
      data: {
        query: `{
          user(login: "letsoon") {
            name
            avatarUrl
            email
            websiteUrl
            url
            bio
            login
          }
        }
        `,
      },
    }).then((res) => {
      console.log(res.data);
    });
  },
};
</script>
