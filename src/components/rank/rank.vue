<template lang="html">
  <div class="rank" ref="rank">
  	<scroll class="topList" ref="topList" :data="topList">
      <ul>
        <li @click="setItem(item)" class="item" v-for="item in topList">
          <div class="icon">
            <img width="100" height="100" :src="item.picUrl">
          </div>
          <ul class="songList">
            <li class="song" v-for="(song, index) in item.songList">
              <span>{{index + 1}}</span>
              <span>{{song.songname}}-{{song.singername}}</span>
            </li>
          </ul>
        </li>
      </ul>
      <div class="loading-container" v-show="!topList.length">
        <loading></loading>
      </div>
    </scroll>

    <router-view></router-view>
  </div>
</template>

<script>
import Loading from 'base/loading/loading'
import Scroll from 'base/scroll/scroll'
import { getTopList } from 'api/rank'
import { ERROR_OK } from 'api/config'
import {playlistMixin} from 'common/js/mixin'
import {mapMutations} from 'vuex'

export default {
  mixins: [playlistMixin],
  data() {
    return {
      topList: []
    }
  },
  created() {
    this._getTopList()
  },
  methods: {
    setItem(item) {
      this.$router.push({
        path: `/rank/${item.id}`
      })
      this.setTopList(item)
    },
    handlePlayList(playlist) {
      const bottom = playlist.length > 0 ? '60px' : '0'
      this.$refs.rank.style.bottom = bottom
      this.$refs.topList.refresh()
    },
    _getTopList() {
      getTopList().then((res) => {
        if (res.code === ERROR_OK) {
          this.topList = res.data.topList
        }
      })
    },
    ...mapMutations({
      setTopList: 'SET_TOP_LIST'
    })
  },
  components: {
    Loading,
    Scroll
  }
}
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
@import "~common/stylus/variable"
@import "~common/stylus/mixin"

  .rank
    position: fixed
    top: 88px
    bottom: 0
    width: 100%
    .topList
      overflow: hidden
      height: 100%
      .item
        display: flex
        margin: 0 20px
        padding-top: 20px
        height: 100px
        &:last-child
          padding-bottom: 20px
        .icon
          flex: 0 0 100px
          width: 100px
          height: 100px
        .songList
          flex: 1
          display: flex
          flex-direction: column
          justify-content: center
          height: 100%
          padding: 0 20px
          overflow: hidden
          background: $color-highlight-background
          color: $color-text-d
          font-size: $font-size-small
          .song
            no-wrap()
            line-height: 26px
      .loading-container
        position: absolute
        top: 50%
        width: 100%
        transform: translateY(-50%)
</style>
