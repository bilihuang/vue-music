<template>
  <div class="recommend">
    <scroll class="recommend-content">
      <div>
        <div v-if="recommends.length" class="slider-wrapper" :data="discList">
          <slider>
            <div v-for="item in recommends" :key="item.id">
              <a :href="item.linkUrl">
                <img @load="loadImage" :src="item.picUrl">
              </a>
            </div>
          </slider>
        </div>
        <div class="recommend-list">
          <h1 class="list-title">热门歌单推荐</h1>
          <ul>
            <li v-for="item in discList" :key="item.id" class="item">
              <div class="icon">
                <img width="60" height="60" :src="item.picUrl">
              </div>
              <div class="text">
                <h2 class="desc">{{item.songListDesc}}</h2>
                <p class="author">{{item.songListAuthor}}</p>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </scroll>
  </div>
</template>

<script>
import Slider from 'base/slider/slider'
import Scroll from 'base/scroll/scroll'
import { getRecommend } from 'api/recommend'
import { ERR_OK } from 'api/config'

export default {
  components: {
    Slider,
    Scroll
  },
  data () {
    return {
      recommends: [],
      discList: []
    }
  },
  created () {
    this._getRecommend()
  },
  methods: {
    _getRecommend () {
      getRecommend().then((res) => {
        if (res.code === ERR_OK) {
          this.recommends = res.data.slider
          this.discList = res.data.songList
        }
      })
    },
    // 防止因为slider还未渲染导致refresh计算高度出错而滑不到底部
    loadImage () {
      // 只调一次，因为只需要一张图就可以撑开容器了
      if(!this.checkLoaded){
        this.$refs.scroll.refresh()
        this.checkLoaded = ture
      }
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/variable'
.recommend
  position: fixed
  width: 100%
  top: 88px
  bottom: 0
  .recommend-content
    height: 100%
    overflow: hidden
    .slider-wrapper
      position: relative
      width: 100%
      overflow: hidden
    .recommend-list
      .list-title
        height: 65px
        line-height: 65px
        text-align: center
        font-size: $font-size-medium
        color: $color-theme
      .item
        display: flex
        box-sizing: border-box
        align-items: center
        padding: 0 20px 20px 20px
        .icon
          flex: 0 0 60px
          width: 60px
          padding-right: 20px
        .text
          display: flex
          flex-direction: column
          justify-content: center
          flex: 1
          line-height: 20px
          overflow: hidden
          font-size: $font-size-medium
          .desc
            margin-bottom: 10px
            color: $color-text
          .author
            color: $color-text-d
    .loading-container
      position: absolute
      width: 100%
      top: 50%
      transform: translateY(-50%)
</style>