<template>
  <div class="city_body">
  <div class="city_list">
  <div class="city_hot">
    <h2>热门城市</h2>
    <ul class="clearfix"  @click="removes">
      <li v-for="item in hotList" :key="item.id" @click="changeCity(item.nm,item.id)">{{item.nm}}</li>
    </ul>
  </div>
  <div class="city_sort" ref="city_sort">
    <div v-for="item in cityList" :key="item.index">
      <h2>{{item.index}}</h2>
      <ul  @click="removes">
        <li v-for="items in item.list" :key="items.id" @click="changeCity(items.nm,items.id)">{{items.nm}}</li>
      </ul>
    </div>
  </div>
  </div>
  <div class="city_index">
    <ul>
      <li v-for="(item,index) in cityList" :key="item.index" @click="handleToIndex(index)" class="lis">{{item.index}}</li>
    </ul>
  </div>
  </div>
</template>

<script>
export default {
data() {
  return {
    cityList:[],
    hotList:[]
  }
},
methods: {
  changeCity(nm,id){
   this.$store.commit('city/CITY_INFO',{nm ,id})
  },
  removes(){
 this.$parent.isShow = false;
  },
   //将数据进行处理
  fromatCityList(cities){
  let cityList =[];
  let hotList = [];
  for(let i=0;i<cities.length;i++){
    if(cities[i].isHot ===1){
      hotList.push(cities[i])
    }
  }

  for(let i=0;i<cities.length;i++){
    let firstLetter = cities[i].py.substring(0,1).toUpperCase()//截取第一个字母
    if(toCom(firstLetter)){  //新添加index
     cityList.push({index:firstLetter ,list:[ {nm:cities[i].nm , id: cities[i].id} ]})
    }else{ //累加到已有index中
     for(let j=0;j<cityList.length;j++){
       if(cityList[j].index ===firstLetter){
         cityList[j].list.push({nm : cities[i].nm,id:cities[i].id})
       }
     }
    }
  }
  cityList.sort((n1,n2)=>{
    if(n1.index>n2.index){
      return 1
    }
    else if(n1.index<n2.index){
      return -1
    }else{
      return 0
    }
  })

  function toCom(firstLetter){
    for(let i=0;i<cityList.length;i++){
      if(cityList[i].index ===firstLetter){
      return false
      }
    }
    return true
  }
  return {
    cityList,hotList
  }
  },
  handleToIndex(index){
  let h2 = this.$refs.city_sort.getElementsByTagName('h2')
 this.$refs.city_sort.parentNode.scrollTop= h2[index].offsetTop
  
  }
},
mounted() {
   let cityList = window.localStorage.getItem('cityList')
    let hotList = window.localStorage.getItem('hotList')
     if(cityList&&hotList){
     this.cityList = JSON.parse(cityList);
     this.hotList = JSON.parse(hotList)
     }
     else{
   this.axios.get('/api/cityList').then((res)=>{
     let cities =res.data.data.cities
     let {cityList,hotList} = this.fromatCityList(cities)
      this.cityList=cityList
      this.hotList =hotList;
      //本地存储，避免每次都请求
      window.localStorage.setItem('cityList',JSON.stringify(cityList))
       window.localStorage.setItem('hotList',JSON.stringify(hotList))
   
   })
}
}
}

</script>

<style scoped>
.city_body{
  display: flex;
  width: 100%;
  position: absolute;
  top: 0;
  bottom: 0;
  z-index: 1000;
}
.city_list{
  flex: 1;
  overflow: auto;
  background: #fff5f0;
}
.city_list::-webkit-scrollbar{
  background: transparent;
  width: 0;
}
.city_hot{
  margin-top: 20px;
}
.city_hot h2{
  padding-left: 15px;
  line-height: 30px;
  font-size: 14px;
  background: #f0f0f0;
  font-weight: normal;
}
.city_hot ul li{
  float: left;
  background: #fff;
  width: 29%;
  height: 33px;
  margin-top: 15px;
  margin-left: 3%;
  padding: 0 4px;
  border: 1px solid #e6e6e6;
  border-left: 30px;
  line-height: 33px;
  text-align: center;
  box-sizing: border-box
}
.city_sort div{
  margin-top: 20px
}
.city_sort h2{
  padding-left: 15px;
  line-height: 30px;
  font-size: 14px;
  background: #f0f0f0;
  font-weight: normal
}
.city_sort ul{
  padding-left: 10px;
  margin-top: 10px;
}
.city_sort ul li {
  line-height: 30px;
}
.city_index{
  width: 20px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  text-align: center;
  border-left: 1px solid #e6e6e6;
}
.lis{
  padding-top: 5px;
}
</style>