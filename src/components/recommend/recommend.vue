<template>
  <div class="recommend" ref="recommend">
  	<scroll ref="scroll" class="recommend-content" :data="discList">
  		<div>
  			<div v-if="recommends.length" class="slider-wrapper" ref="sliderWrapper">
  				<slider>
  					<div v-for="item in recommends">
  						<a :href="item.linkUrl">
                  <img @load="loadImage" :src="item.picUrl" alt="">
                </a>
  					</div>
  				</slider>
  			</div>
  			<div class="recommend-list">
  				<h1 class="list-title">热门歌单推荐</h1>
  				<ul>
  					<li @click="selectItem(item)" v-if="discList.length" v-for="(item, index) in discList" class="item">
  						<div class="icon">
  							<img width="60" height="60" v-lazy="item.imgurl" alt="">
  						</div>
  						<div class="text">
  							<h2 class="name" v-html="item.creator.name"></h2>
  							<p class="disc" v-html="item.dissname"></p>
  						</div>
  					</li>
  				</ul>
  			</div>
  		</div>
  		<div class="loading-container" v-show="!discList.length">
  			<loading></loading>
  		</div>
  	</scroll>
    <router-view></router-view>
</div>
</template>

<script type="text/ecmascript-6">
  import {getRecommend, getDiscList} from 'api/recommend'
  import {ERROR_OK} from 'api/config'
  import Slider from 'base/slider/slider'
  import Scroll from 'base/scroll/scroll'
  import Loading from 'base/loading/loading'
  import {playlistMixin} from 'common/js/mixin'
  import { mapMutations } from 'vuex'

  export default {
    mixins: [playlistMixin],
  	data() {
  		return {
        recommends: [],
        discList: []
  		}
  	},
  	created() {
  		this._getRecommend()
  		this._getDiscList()
  	},
  	methods: {
      selectItem(item) {
        this.$router.push({
          path: `/recommend/${item.dissid}`
        })
        this.setDisc(item)
      },
      handlePlayList(playlist) {
        const bottom = playlist.length > 0 ? '60px' : '0'
        this.$refs.recommend.style.bottom = bottom
        this.$refs.scroll.refresh()
      },
  		_getRecommend() {
  			getRecommend().then((res) => {
  				if (res.code === ERROR_OK) {
  					this.recommends = res.data.slider
  				}
  			})
  		},
  		_getDiscList() {
  			getDiscList().then((res) => {
  				if (res.code === ERROR_OK) {
  					this.discList = res.data.list
  				}
  			})
  		},
  		loadImage() {
  			// 轮播图是动态生成 列表会少计算这一高度 所以等图片加载完刷新列表
  			if (!this.checkedLoad) {
  				this.$refs.scroll.refresh()
  				this.checkedLoad = true
  			}
  		},
      ...mapMutations({
        setDisc: 'SET_DISC'
      })
  	},
  	components: {
  		Slider,
  		Scroll,
  		Loading
  	}

  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "~common/stylus/variable"

  .recommend
    position: fixed
    width: 100%
    top: 88px
    bottom 0
    .recommend-content
      height 100%
      overflow hidden
      .recommend-list
        .list-title
          height 65px
          line-height 65px
          text-align center
          font-size $font-size-medium
          color $color-theme
        .item
          display: flex
          box-sizing border-box
          text-align center
          padding: 0 20px 20px 20px
          .icon
            flex: 0 0 60px
            width: 60px
            padding-right: 20px
          .text
            display: flex
            flex-direction column
            overflow hidden
            font-size $font-size-medium
            line-height 20px
            justify-content center
            text-align left
            .name
              color $color-text
              margin-bottom 10px
            .disc
              color $color-text-d
    .slider-wrapper
      position: relative
      width: 100%
      overflow: hidden
    .loading-container
      position: absolute
      width: 100%
      top: 50%
      transform: translateY(-50%)
</style>
