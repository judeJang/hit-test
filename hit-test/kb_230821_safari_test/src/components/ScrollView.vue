<template>
    <article class="visual" ref="visualWrap">
        <div class="text_area">
            <picture class="title1">
                <source media="(max-width:720px)" :srcset="require('@/assets/mo_blue_title.png')" />
                <img :src="require('@/assets/pc_blue_title.png')" />
            </picture>
            <picture class="title2">
                <source media="(max-width:720px)" :srcset="require('@/assets/mo_blue_title2.png')" />
                <img :src="require('@/assets/pc_blue_title2.png')" />
            </picture>
            <a href="#" class="btn">
                <picture>
                    <source media="(max-width:720px)" :srcset="require('@/assets/mo_btn_participate.png')" />
                    <img :src="require('@/assets/pc_btn_participate.png')" />
                </picture>
            </a>
        </div>
        <div class="sequence">
            <!-- 
                0821 수정 
            -->
            <div v-for="index in 10" :key="index" class="bg pc" :class="`pc_${index}`"></div>
            <div v-for="index in 10" :key="index" class="bg mo" :class="`mo_${index}`"></div>
            <!-- 
               E 0821 수정 
            -->
        </div>
        <div class="console" v-if="isTest">
            <p>Scroll Direction: {{ scrollDirection }}</p>
            <p>Scroll Count: {{ scrollCount }}</p>
            <p>Scroll blocked: {{ isScrollBlocked }}</p>
        </div>
    </article>
</template>

<script>
export default {
    name: "visual-module",
    data() {
        return {
            //시퀀스 관련 변수
            sequenceCount: 1,
            increment: 1,
            sTimeout: null,
            sequenceCountTotal: 10,
            intervalCount: 1,

            //로직 관련 변수
            isFixed: false,
            saveRelativePosition: 0,

            //element 관련 변수
            vObj: null,
            visualTop: NaN,
            visualHeight: NaN,
            windowWidth: window.innerWidth,

            //scroll 관련 변수
            st: null,
            scrollCount: 0,
            scrollDirection: "",
            isScrollBlocked: false,
            scrolling: false,
            scrollTimeout: null,
            lastScrollPosition: window.pageYOffset || document.documentElement.scrollTop,

            //test
            isTest: false
        }
    },

    methods: {
        scrollEvent() {
            this.st = window.pageYOffset || document.documentElement.scrollTop

            // direction check
            if (this.st > this.lastScrollPosition) {
                this.scrollDirection = "down"
            } else if (this.st < this.lastScrollPosition) {
                this.scrollDirection = "up"
            }
            this.lastScrollPosition = this.st

            //offset top
            const visualTop = this.vObj.offsetTop

            //counting
            /*
                0 fixed
                1 fixed & animation
                2 scroll
            */

            if (this.scrollDirection == "up" && this.scrollCount == 2) {
                if (this.st <= this.saveRelativePosition) {
                    this.scrollCount = 1
                }
            }

            if (!this.scrolling) {
                if (this.scrollDirection == "down") {
                    if (this.scrollCount != 2 && this.st >= visualTop) {
                        this.scrollCount++
                    }
                    //longSwiper 예외처리 exception
                } else if (this.scrollDirection == "up" && this.scrollCount != 2) {
                    if (this.scrollCount != 0 && this.st <= this.saveRelativePosition) {
                        this.scrollCount--
                    }
                }
            }

            this.scrolling = true
            clearTimeout(this.scrollTimeout)
            this.scrollTimeout = setTimeout(() => {
                this.scrolling = false
                if (this.scrollCount == 1 && this.isFixed) {
                    this.saveRelativePosition = this.st
                }
                //long scroll 예외처리, 한번에 페이지 끝에 갔을 경우
                const vH = window.innerHeight
                const pageHeight = Math.max(document.body.scrollHeight, document.body.offsetHeight, document.documentElement.clientHeight, document.documentElement.scrollHeight, document.documentElement.offsetHeight)
                //down
                if (this.scrollDirection == "down" && this.st + vH >= pageHeight && this.scrollCount == 1) {
                    this.scrollCount = 2
                }
                //up
                if (this.scrollDirection == "up" && this.st == 0 && this.scrollCount == 1) {
                    this.scrollCount = 0
                }
            }, 100)
        },
        // 고정
        fixed() {
            this.isFixed = true
            this.vObj.classList.add("fixed")
            this.vObj.classList.add(this.scrollDirection)
            this.vObj.style.top = 0
        },
        //해제
        release(top) {
            this.isFixed = false
            const value = `${top}px`
            this.vObj.classList.add(this.scrollDirection)
            this.vObj.classList.remove("fixed")
            this.vObj.style.top = value
        },
        resize() {
            const sq = document.querySelector(".visual .sequence")
            this.windowWidth = window.innerWidth
            /*
                0821 수정
            */
            if (this.windowWidth <= 720) {
                sq.querySelectorAll(".pc").forEach((element) => {
                    element.style.opacity = 0
                    element.classList.remove("selected")
                })
                sq.querySelectorAll(".pc")[this.sequenceCount - 1].style.opacity = 1
                sq.querySelectorAll(".pc")[this.sequenceCount - 1].classList.add("selected")
                // sq.style.backgroundImage = `url(${require(`@/assets/mo_splite_${this.sequenceCount}.png`)})`
            } else {
                sq.querySelectorAll(".mo").forEach((element) => {
                    element.style.opacity = 0
                    element.classList.remove("selected")
                })
                sq.querySelectorAll(".mo")[this.sequenceCount - 1].style.opacity = 1
                sq.querySelectorAll(".mo")[this.sequenceCount - 1].classList.add("selected")
                // sq.style.backgroundImage = `url(${require(`@/assets/pc_splite_${this.sequenceCount}.png`)})`
            }
            /*
                E 0821 수정
            */
        },
        checkDeviceType() {
            const userAgent = navigator.userAgent.toLowerCase()
            if (userAgent.match(/android/i) || userAgent.match(/iphone|ipad|ipod/i)) {
                return "Mobile"
            } else {
                return "PC"
            }
        },
        //배경 이미지 시퀀스
        sequence() {
            //무한 루핑
            if (this.sequenceCount === this.sequenceCountTotal) {
                this.increment = -1
            } else if (this.sequenceCount === 1) {
                this.increment = 1
            }
            this.sequenceCount += this.increment

            /*
                0821 수정
            */
            const sq = document.querySelector(".visual .sequence")
            //let bgPath
            if (this.windowWidth <= 720) {
                sq.querySelectorAll(".mo").forEach((element) => {
                    element.style.opacity = 0
                    element.classList.remove("selected")
                })
                sq.querySelectorAll(".mo")[this.sequenceCount - 1].style.opacity = 1
                sq.querySelectorAll(".mo")[this.sequenceCount - 1].classList.add("selected")
                // bgPath = `url(${require(`@/assets/mo_splite_${this.sequenceCount}.png`)})`
            } else {
                sq.querySelectorAll(".pc").forEach((element) => {
                    element.style.opacity = 0
                    element.classList.remove("selected")
                })
                sq.querySelectorAll(".pc")[this.sequenceCount - 1].style.opacity = 1
                sq.querySelectorAll(".pc")[this.sequenceCount - 1].classList.add("selected")
                // bgPath = `url(${require(`@/assets/pc_splite_${this.sequenceCount}.png`)})`
            }
            // sq.style.backgroundImage = bgPath
            /*
                E 0821 수정
            */
        }
    },
    watch: {
        scrollCount(n) {
            /*
                0821 수정
            */
            const sq = document.querySelector(".visual .sequence")
            const visual = document.querySelector(".visual")
            if (n == 0) {
                this.fixed()
                clearInterval(this.sTimeout)
                if (this.windowWidth <= 720) {
                    // sq.style.backgroundImage = `url(${require(`@/assets/mo_splite_${1}.png`)})`
                    sq.querySelectorAll(".mo").forEach((element) => {
                        element.style.opacity = 0
                        element.classList.remove("selected")
                    })
                    sq.querySelectorAll(".mo")[0].style.opacity = 1
                    sq.querySelectorAll(".mo")[0].classList.add("selected")
                } else {
                    // sq.style.backgroundImage = `url(${require(`@/assets/pc_splite_${1}.png`)})`
                    sq.querySelectorAll(".pc").forEach((element) => {
                        element.style.opacity = 0
                        element.classList.remove("selected")
                    })
                    sq.querySelectorAll(".pc")[0].style.opacity = 1
                    sq.querySelectorAll(".pc")[0].classList.add("selected")
                }
                visual.classList.remove("phase2")
            }
            if (n == 1) {
                this.fixed()
                this.sequence()
                this.intervalCount = 1
                visual.classList.add("phase2")
                this.sTimeout = setInterval(() => {
                    if (this.intervalCount >= 30) {
                        clearInterval(this.sTimeout)
                        if (this.windowWidth <= 720) {
                            sq.querySelectorAll(".mo").forEach((element) => {
                                element.style.opacity = 0
                                element.classList.remove("selected")
                            })
                            sq.querySelectorAll(".mo")[9].style.opacity = 1
                            sq.querySelectorAll(".mo")[9].classList.add("selected")
                            // sq.style.backgroundImage = `url(${require(`@/assets/mo_splite_${10}.png`)})`
                        } else {
                            sq.querySelectorAll(".pc").forEach((element) => {
                                element.style.opacity = 0
                                element.classList.remove("selected")
                            })
                            sq.querySelectorAll(".pc")[9].style.opacity = 1
                            sq.querySelectorAll(".pc")[9].classList.add("selected")
                            // sq.style.backgroundImage = `url(${require(`@/assets/pc_splite_${10}.png`)})`
                        }

                        return
                    }
                    this.sequence()
                    this.intervalCount++
                }, 100)

                /*
                    E 0821 수정
                */
            }
            if (n == 2) {
                this.release(this.saveRelativePosition)
                clearInterval(this.sTimeout)
            }
        },
        scrollDirection(n) {
            const visual = document.querySelector(".visual")
            if (n == "up") {
                visual.classList.remove("down")
            } else {
                visual.classList.remove("up")
            }
        }
    },
    mounted() {
        this.vObj = this.$refs.visualWrap
        window.addEventListener("scroll", this.scrollEvent)
        window.addEventListener("resize", this.resize)
        this.scrollEvent()
        this.resize()
    },
    beforeDestroy() {
        window.removeEventListener("wheel", this.scrollEvent)
        window.addEventListener("resize", this.resize)
    }
}
</script>
