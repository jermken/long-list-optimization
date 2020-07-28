<template>
    <div class="scroll-wrap">
        <div class="content-wrap"  ref="wrap">
            <div :style="{paddingTop: paddingTop + 'px'}"></div>
            <div class="item" v-for="item in renderData" :key="item.id">{{ item.val }}</div>
        </div>
    </div>
</template>
<script>
export default {
    name: 'Home',
    data() {
        return {
            data: [],
            renderData: [],
            paddingTop: 0,
            times: 1
        }
    },
    created() {
        let demo = []
        for (let i = 0; i < 1000; i++) {
            demo.push({
                id: i + 1,
                val: i + 1
            })
        }
        this.data = demo
        this.renderData = this.data.slice(0, 50)
    },
    mounted() {
        const scrollFunc = () => {
            let times = 1;
            let end = 50;
            let start = 0;
            let timer = null;
            let trigger = null;
            let prevScrollTop = 0;
            let i = 0;
            return (e) => {
                if (!trigger) {
                    let upScroll = this.$refs.wrap.scrollTop - prevScrollTop < 0
                    timer && clearTimeout(timer)
                    timer = setTimeout(() => {
                        upScroll && console.log('data refresh', ++i)
                        let top = this.$refs.wrap.scrollTop
                        let height = this.$refs.wrap.scrollHeight
                        let clientHeight = this.$refs.wrap.clientHeight
                        let bottom = height - clientHeight - top
                        // 往下滚动
                        if (bottom < 400 && !upScroll) {
                            this.renderData = [...this.renderData.slice(10), ...this.data.slice(end, end + 10)]
                            this.paddingTop += 400
                            times++
                            end += 10
                            start += 10
                            setTimeout(() => {
                                trigger = 'auto'
                                this.$refs.wrap.scrollTop = top
                            })
                        }
                        // 往上滚动
                        if (top < (this.paddingTop + 400) && upScroll && start > 0) {
                            this.renderData = [...this.data.slice( end - 50 - 10, end - 50), ...this.renderData.slice(0, this.renderData.length - 10)]
                            this.paddingTop -= 400
                            end -= 10
                            start -= 10
                            setTimeout(() => {
                                this.$refs.wrap.scrollTop = top
                            })
                        }
                    }, 4)
                }
                trigger = null
                prevScrollTop = this.$refs.wrap.scrollTop
            }
        }
        this.$refs.wrap.addEventListener('scroll', scrollFunc().bind(this))
    }
}
</script>
<style lang="scss">
* {
    padding: 0;
    margin: 0;
}
.scroll-wrap {
    height: 100vh;
    overflow: hidden;
    .content-wrap {
        box-sizing: border-box;
        max-height: 100%;
        overflow-y: scroll;
        .item {
            height: 40px;
            box-sizing: border-box;
            &:nth-child(odd) {
                background: rgb(238, 236, 236);
            }
        }
    }
}
</style>