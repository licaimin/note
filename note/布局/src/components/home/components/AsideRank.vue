<template>
  <div class="rank-container">
    <div class="rank">
      <div class="header">
        <h3 class="title">排行版</h3>
        <div class="switch-btn">
          <div :class="rank===1?'active btn':'btn'" @click="handleChangeRank(1)">周榜</div>
          <div :class="rank===2?'active btn':'btn'" @click="handleChangeRank(2)">月榜</div>
        </div>
      </div>
      <div class="rank-list">
        <rank-card v-for="item of rankList" :key="item.id" :data="item"></rank-card>
      </div>
    </div>
  </div>
</template>

<script>
import RankCard from './RankCard'
import axios from 'axios'

export default {
  name: 'AsideRank',
  components: {
    RankCard
  },
  props: {

  },
  data () {
    return {
      rank: 1,
      rankList: []
    }
  },
  methods: {
    handleChangeRank (rank) {
      this.rank = rank;
      if (rank === 1) {
        this.getRankList('week')      
      } else {
        this.getRankList('month')
      }
    },
    getRankList (arg) {
      axios.get('/api/rankList/'+arg) 
        .then(this.handleGetRankListSucc)
    },
    handleGetRankListSucc (res) {
      if (res.status === 200 && res.data.data) {
        this.rankList = res.data.data;
      }
    }
  },
  mounted () {
    this.getRankList('week')
  }
}
</script>

<style scoped>
.rank-container {
  width: 208px;
  color: #515151;
}

.rank {
  padding: 0 16px;
  padding-bottom: 17px;
  background-color: #fff;
}

.header {
  display: flex;
  justify-content: space-between;
}

.header .title {
  font-size: 16px;
  margin-top: 22px;
}

.switch-btn {
  display: flex;
  margin-top: 16px;
}

.switch-btn .btn {
  width: 48px;
  height: 24px;
  border-radius: 4px 0px 0px 4px;
  font-size: 12px;
  color: #c1c1c1;
  background-color: #f6f6f6;
  border: none;
}

.switch-btn .btn + .btn {
  border-radius: 0 4px 4px 0;
}

.switch-btn div.active {
  color: #fff;
  background-color: #4959F6;
}

.rank-list {
  margin-top: 24px;
}

.rank-list > .rank-card + .rank-card {
  margin-top: 16px;
}


</style>

