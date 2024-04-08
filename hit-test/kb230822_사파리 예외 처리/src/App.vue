<template>
    <div id="app">
        <section class="wrap">
            <header class="header"></header>
            <main class="content">
                <!--ScrollView 컴포넌트만 사용  나머진 목업-->
                <Visual />
                <!--//ScrollView 컴포넌트만 사용  나머진 목업-->
                <div class="etc" ref="etc"></div>
            </main>
            <footer class="footer"></footer>
        </section>
        <!-- <img alt="Vue logo" src="./assets/logo.png" /> -->
    </div>
</template>

<script>
import Visual from "./components/ScrollView.vue"
// import Visual from "./components/HelloWorld.vue"

export default {
    name: "App",
    components: {
        Visual
    }
}
</script>

<style lang="scss" scoped>
.wrap {
    width: 100%;
    overflow: hidden;
}
.etc {
    position: relative;
    height: 300vh;
}
header {
    position: fixed;
    left: 0;
    top: 0;
    // background: yellow;
    width: 100%;
    height: 90px;
    z-index: 900;
}
</style>
