<template>
    <div class="collect" v-if="show_collect">
     <div class="content-warp">
       <div class="content">
        <div class="head"><span class="head-title">添加到收藏夹</span></div><span class="head-delete" @click="closeCollect">×</span>
         <div class="collect-list-warp">
           <ul class="collect-list">
             <li v-for="(item,index) in collect_list" class="list-item" :class="{collected:item.if_collect}">
               <div class="list-left"><span class="item-name">{{item.name}}</span><span class="item-num">{{item.nums}}个作品</span></div>
               <div class="list-right"><img :src="item.if_collect?delete_image:add_image" @click="action(item.if_collect,item)"></div>
             </li>
           </ul>
         </div>
         <div class="content-bottom"><div class="add-new" @click="showAdd">新建收藏夹</div></div>
       </div>
     </div>
      <create_folder_card ref="show_create_folder" @reload="reload"/>
    </div>
</template>

<script>
  import {addImage,deleteImage} from "../../api/user";
  import create_folder_card from '../../components/card/create_folder'
  import cookie from "../../utils/cookie";
  import {getCollectList} from "../../api/get";

  export default {
        name: "collect",
    props:[''],
    components:{
      create_folder_card
    },
      data(){
          return {
            show_add:false,
            add_image:require('../../assets/plus-circle.svg'),
            delete_image:require('../../assets/delete_image.png'),
            show_collect:false,
            collect_list:[],
            image_id:-1,
            image_item:{}
          }
      },
    computed:{

    },
    created(){

    },
    methods:{
        showCollect(item){
          document.documentElement.style.overflow='hidden'
          this.show_collect=!this.show_collect
          this.image_item=item
          this.image_id=item.id
          getCollectList(item.id).then((res)=>{
            this.collect_list=res.data
          }).catch((error)=>{
            console.log(error)
          })
        },
        showAdd(){
          this.$refs.show_create_folder.changeShow()
        },
        closeCollect(){
          document.documentElement.style.overflow = 'auto';
          this.show_collect=!this.show_collect

        },
        //添加/删除图片 刷新收藏夹
        action(status,item){
          let data=new FormData();
          data.append('image',this.image_id);
          data.append('folder',item.id);
          data.append('user',cookie.getCookie('user_id'))
          if(status){
            deleteImage(item.if_collect).then(()=>{
              item.if_collect=false
              item.nums--
              this.image_item.collection_nums--
              if(!this.checkCollect())
                this.image_item.if_collect=false
            }).catch((error)=>{
              console.log(error)
            })
          }else{
            addImage(data).then((res)=>{
             item.if_collect=res.data.id
              item.nums++
              this.image_item.if_collect=res.data.id
              this.image_item.collection_nums++
            }).catch((error)=>{
              console.log(error)
            })
          }
        },
        checkCollect(){
          for(let i=0;i<this.collect_list.length;i++){
            if(this.collect_list[i].if_collect)
              return true
          }
          return false
        },
      reload(){
        getCollectList(this.image_id).then((res)=>{
          this.collect_list=res.data
        }).catch((error)=>{
          console.log(error)
        })
      }
      },
    }
</script>

<style scoped>
.collect{
  position: fixed;
  top: 0;
  left: 0;
  z-index: 101;
  width: 100%;
  height: 100%;
  background: rgba(0,0,0,0.6);
}
  .content-warp{
    display: flex;
    width: 100%;
    height: 100%;
    justify-content: center;
    align-items: center;
  }
  .content{
    position: relative;
    display: flex;
    flex-direction: column;
    width: 29.25rem;
    height: 37.5rem;
    background: #fff;
    padding:2.8rem 0 2.25rem;
  }
  .head{
    display: flex;
    align-items: center;
    flex-direction: row;
    justify-content: space-between;
    margin-bottom: 1rem;
    padding: 0 3rem;
  }
  .head-title{
    flex-direction: row;
    justify-content: space-between;
    font-size: 1.125rem;
    white-space: nowrap;
    font-weight: 600;
  }
  .head-delete{
    position: absolute;
    width: 1.5rem;
    height: 1.5rem;
    right: 2rem;
    top: 1rem;
    font-size: 3rem;
    cursor: pointer;
    color:#b3b3b3;
  }
  .collect-list-warp{
    height: 25rem;
    overflow-y: auto;
    margin-bottom: 1.5rem;
    padding: 0 3rem ;
  }
  .collect-list-warp::-webkit-scrollbar{
    width: .5rem;
  }
  .collect-list-warp::-webkit-scrollbar-button{
    display: none;
  }
  .collect-list-warp::-webkit-scrollbar-thumb{
    background: #d9d9dd;
    border-radius: 0.7rem;
  }
  .collect-list{
    display: flex;
    align-items: center;
    flex-direction: column;
    width: 23.25rem;
  }
  .list-item{
    -webkit-border-radius: 0.25rem;
    -moz-border-radius: 0.25rem;
    border-radius:0.25rem;
    display: flex;
    justify-content: space-between;
    width: 23.25rem;
    height: 4.5rem;
    margin-bottom: 0.625rem;
    background: #F0F1F2;
    background-size: cover;
  }
  .collected{
    background: #9AD3E2;
  }
  .list-left{
    display: flex;
    flex-direction: column;
    padding-left: 1.25rem;
    overflow: hidden;
    padding-top: .8rem;
  }
  .item-num{
    display:flex;
    justify-content: flex-start;
    padding-top: 0.625rem;
    font-size: .8rem;
  }
  .item-name{
    display: flex;
    justify-content: flex-start;
    font-size:1rem;
    font-weight: bold;
  }
  .list-right{
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 5rem;
  }
  .list-right img{
    cursor: pointer;
    width: 1.5rem;
    height: 1.5rem;
  }
  .content-bottom{
    display: flex;
    justify-content: center;

  }
  .add-new{
    height:3rem;
    width: 11.25rem;
    background: #9AD3E2;
    color: #fff;
    line-height:3rem ;
    border-radius: 1.5625rem;
    cursor: pointer;
    font-weight: 600;
    font-size: 1.1rem;
  }
  .add-new:hover{
    background: #2cbec6;
  }

</style>
