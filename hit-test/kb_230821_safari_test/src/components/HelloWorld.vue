<template>
    <article class="visual" ref="visualWrap">
        <div class="text_area">
            <img :src="require('@/assets/pc_blue_title.png')" alt="" class="pc title1" />
            <img :src="require('@/assets/pc_blue_title2.png')" alt="" class="pc title2" />
            <a href="#" class="btn">
                <img :src="require('@/assets/pc_btn_participate.png')" alt="" class="pc" />
            </a>
        </div>
        <div class="sequence"></div>
        <div class="console">
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
            lastScrollPosition: window.pageYOffset || document.documentElement.scrollTop
        }
    },
    methods: {
        scrollEvent() {
            //direction
            this.st = window.pageYOffset || document.documentElement.scrollTop
            console.log(2, this.st)
            if (this.st > this.lastScrollPosition) {
                this.scrollDirection = "down"
            } else if (this.st < this.lastScrollPosition) {
                this.scrollDirection = "up"
            }
            this.lastScrollPosition = this.st

            //down start
            const visualTop = this.vObj.offsetTop

            switch (this.scrollDirection) {
                case "down":
                    if (this.st >= visualTop) {
                        if (this.scrollCount == 0) {
                            this.fixed()
                        }
                        this.scrolling = true
                        clearTimeout(this.scrollTimeout)
                        this.scrollTimeout = setTimeout(() => {
                            this.scrolling = false

                            if (this.scrollCount < 2) {
                                this.scrollCount++
                            }
                            if (this.scrollCount == 2 && this.isFixed) {
                                this.release(this.st)
                                this.saveRelativePosition = this.st
                            }
                            if (this.scrollCount == 2) {
                                return
                            }
                        }, 100)
                    }
                    break
                case "up":
                    if (this.st <= this.saveRelativePosition) {
                        if (this.scrollCount == 2) {
                            this.fixed()
                        }
                        this.scrolling = true
                        clearTimeout(this.scrollTimeout)
                        this.scrollTimeout = setTimeout(() => {
                            this.scrolling = false
                            if (this.scrollCount == 0 && this.isFixed) {
                                this.release(this.st)
                                console.log("release")
                            }
                            if (this.scrollCount == 0) {
                                return
                            }
                            this.scrollCount--
                        }, 100)
                    }
                    break
            }
        },
        // 고정
        fixed() {
            this.isFixed = true
            this.vObj.classList.add("fixed")
            this.vObj.style.top = 0
        },
        //해제
        release(top) {
            this.isFixed = false
            const value = `${top}px`
            this.vObj.classList.remove("fixed")
            this.vObj.style.top = value
        },
        resize() {
            this.windowWidth = window.innerWidth
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
            if (this.sequenceCount === 10) {
                this.increment = -1
            } else if (this.sequenceCount === 1) {
                this.increment = 1
            }
            this.sequenceCount += this.increment

            const sq = document.querySelector(".visual .sequence")
            sq.style.backgroundImage = `url(${require(`@/assets/pc_splite_${this.sequenceCount}.png`)})`
        }
    },
    watch: {
        scrollCount(n) {
            const visual = document.querySelector(".visual")

            if (n == 1) {
                this.sequence()
                visual.classList.add("phase2")
                this.sTimeout = setInterval(() => {
                    this.sequence()
                }, 100)
            } else {
                if (n == 2) {
                    visual.classList.add("phase2")
                } else {
                    visual.classList.remove("phase2")
                }
                clearInterval(this.sTimeout)
            }
        }
    },
    mounted() {
        this.vObj = this.$refs.visualWrap

        window.addEventListener("scroll", this.scrollEvent)
        // window.addEventListener("resize", this.resize)

        //route 사용시 페이지 새로 고침 시 상단으로 고정 부탁드립니다.
        // window.scrollTo(0, 100000)
        // this.scrollCount = 0
    },
    beforeDestroy() {
        window.removeEventListener("wheel", this.scrollEvent)
        // window.removeEventListener("resize", this.resize)
    }
}
</script>
