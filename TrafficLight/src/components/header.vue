<!-- Begin Header Component -->
<template>
    <div>
        <div class="newsNowLogoDiv">
            <img class="newsNowLogo" src="@/assets/horizontalLogo.png" />
        </div>
        <!-- headerTitle retreived from the props value sent in from the App.vue parent component -->
        <div class="serverStatusHeaderDiv">
            <h2 class="serverStatusHeader">{{ headerTitle }}</h2>
        </div>
    </div>
</template>

<script>
    export default {
      name: "LogoHeader",
      props: {
        headerTitle: String
      },
    };
    </script>    

<style scoped>
.newsNowLogoDiv {
  margin: 10px auto;
  width: 90%;
  max-width: 250px;
}
.newsNowLogoDiv .newsNowLogo {
  width: 100%;
  height: auto;
}
</style>
