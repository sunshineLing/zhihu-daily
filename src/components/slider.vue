<template>
    <div id="slider" :class="{show:isShowSlider}" @click="hideSlider()">
        <div class="left">
            <div class="user-info">
                <div class="login">请登录</div>
                <div class="info flex">
                    <span class="collect">我的收藏</span>
                    <span class="download">我的下载</span>
                </div>
            </div>
            <ul @click="get($event)">
                <li v-for="(item, index) in themesList" :key="item.id" :index="index" :class="[item.isSelected?'pressed':'', item.id=='0000'?'title':'']"  >
                    {{item.name}}
                    <span :class="[item.isAdd?'more':'add']" @click.stop="changeState(index, item.id, $event)" v-show="item.id!=='0000'"></span>
                </li>
            </ul>
        </div>
        <tooltip :isShow="showTooltip" :content="toolContent" @update:isShow="val=>showTooltip=val"></tooltip>
    </div>
</template>
<script>
import tooltip from './tooltip.vue'
import {mapState, mapMutations, mapActions} from 'vuex'
import API from '../api/API'
const api = new API();
export default {
    name:"slider",
    mounted(){
        let that = this;
        api.get('/themes')
            .then((res) => {
                if(res.status==200){
                    that.themesList = res.data.THEMES.others || [];
                    that.themesList.unshift({
                        id:'0000',
                        name:"首页",
                        isSelected:true
                    })
                }
            }).catch((err) => {
                throw err;
            })
        this.getNews({
            id:'0000'
        });
    },
    data(){
        return {
            isShow:true,
            themesList:[],
            toolContent:"you have added it",
            showTooltip:false
        }
    },
    computed:mapState([
       'isShowSlider'
    ]),
    methods:{
            ...mapMutations([
                'hideSlider'
            ]),
            ...mapActions([
                'getNews'
            ]),
            get(event){
                let currentItem = this.themesList[+event.target.getAttribute("index")];
                
                this.themesList.forEach((item, index) => {
                    item.isSelected = index==event.target.getAttribute("index")?true:false;
                })

                // event.target.className+="pressed";
                this.getNews({
                    id:currentItem.id
                });
            },
            changeState(currentIndex, id, event){
                let currentItem = this.themesList[+currentIndex];

                currentItem.isAdd = !currentItem.isAdd;
                // this.getNews({
                //     id:id
                // })
                event.preventDefault();
                this.showTooltip = true;
            },
            hideTooltip(){
                // this.showTooltip = false;
            }
    },
    components:{
        tooltip
    }
        
    
}
</script>
<style lang="sass">
    .show{
        transform:translate3d(0, 0, 0) !important;
        background-color:rgba(0, 0, 0, 0.5) !important;
    }
    #slider{
        position:absolute;
        transform:translate3d(-100%, 0, 0);
        width:100%;
        top:0;
        bottom:0;
        font-size:0.3rem;
        background-color:rgba(0,0,0,0);
        transition:all 0.5s ease-in 0s;
        z-index: 200;

        .left{
            width:75%;
            height:100%;
            background-color:#fff;
            
            .user-info{
                background-color:#00A1E9;
                color:#fff;
                height:2.5rem;
                .login{
                    background-image: url(../assets/svg/user.svg);
                    background-size:0.5rem;
                    background-repeat:no-repeat;
                    background-position:0.2rem center;
                    height:1rem;
                    line-height:1rem;
                    padding-left:0.9rem;
                }

                .info{
                    margin-top:0.5rem;
                    justify-content:flex-start;
                    span{
                        background-size:0.35rem;
                        background-position:0.4rem center;
                        background-repeat:no-repeat;
                        height:1rem;
                        line-height:1rem;
                        text-align:center;
                        width:50%;
                    }

                    .collect{
                        background-image: url(../assets/svg/star.svg)
                    }
                    .download{
                        background-image: url(../assets/svg/download.svg)
                    }
                }
        
            }
            
            ul{
                .title{
                    background-image: url(../assets/svg/home.svg);
                    background-size:0.5rem;
                    background-repeat:no-repeat;
                    background-position:0.5rem center;
                    padding-left:1.2rem;
                }
                .pressed{
                    background-color:#F0F0F0;
                    color:#1188BA;
                }
                li{
                    height:0.8rem;
                    line-height:0.8rem;
                    padding-left:0.5rem;
                    position:relative;
                    span{
                        position:absolute;
                        width:0.8rem;
                        height:0.8rem;
                        right:0;
                    }
                }
                .add{
                    background-image: url(../assets/svg/add.svg);
                    background-size:0.5rem;
                    background-repeat:no-repeat;
                    background-position:center;
                }
                .more{
                    background-image: url(../assets/svg/more-x.svg);
                    background-size:0.5rem;
                    background-repeat:no-repeat;
                    background-position:center;
                }
            }
        }
    }
</style>


