# virtualList
通过虚拟列表(virtual list)在vue中高性能渲染十万条数据
```
<template>
<!-- infinit-list-container 可视区域的容器 -->
    <div class="infinit-list-container" ref="list" @scroll="scrollEvent($event)">
        <!-- infinite-list-phantom容器内的占位，高度为总列表高度，用于形成滚动条 -->
        <div class="infinite-list-phantom" :style="{ height: listHeight + 'px'}"></div>
        <!-- 列表项渲染区域 -->
        <div class="infinite-list" :style="{transform: getTransform}">
            <div class="infinite-list-item"
              :style="{height: itemSize + 'px'}"
              ref="items"
              v-for="(item, index) in visiableData"
              :key="index"
            >
            {{item.value}}
            </div>
        </div>
    </div>
</template>
<script lang="ts">
export default {
    name: 'virtualList',
    props: {
        //所有列表数据
        listData: {
            type: Array,
            default: () => []
        }
    },
    data() {
        return {
            //每项高度
            itemSize: 100,
            //可视区域高度
            screenHeight: 0,
            // 偏移量
            startOffset: 0,
            //起始索引
            start: 0,
            //结束索引
            end: null
        }
    },
    computed: {
        // 列表总高度
        listHeight() {
            return this.listData.length * this.itemSize
        },
        // 可显示的列表项数
        visibleCount() {
            return Math.ceil(this.screenHeight / this.itemSize)
        },
        //偏移量对应的style
        getTransform() {
            return `translate3d(0,${this.startOffset}px,0)`
        },
        // 获取真实显示列表数据
        visiableData() {
            return this.listData.slice(this.start, Math.min(this.end, this.listData.length))
        }
    },
    mounted() {
        this.screenHeight = this.$el.clientHeight //可视列表高度
        this.start = 0
        this.end = this.start + this.visibleCount
    },
    methods: {
        scrollEvent() {
            //当前滚动位置
            let scrollTop = this.$refs.list.scrollTop
            //此时的开始索引
            this.start = Math.floor(scrollTop / this.itemSize)
            //此时的偏移量
            this.end = this.start + this.visibleCount
            // 此时的偏移量
            this.startOffset = scrollTop - (scrollTop % this.itemSize) //保证显示完整项
        }
    }
}
</script>
<style lang="scss" scoped>
.infinit-list-container {
    height: 100%;
    overflow: auto;
    position: relative;
    -webkit-overflow-scrolling: touch;
}
.infinite-list-phantom {
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
    z-index: -1;
}
.infinite-list {
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
}
.infinite-list-item {
    display: flex;
    justify-content: center;
    align-items: center;
    border: 1px solid #ccc;
    box-sizing: border-box;
}
</style>
```
