<template>
<div id="writerDetail">
    <!--顶部简介-->
    <div class="topBox">
        <writers :hang2IsShow='hang2IsShow' :tag="userData.tag" :serviceHours="userData.serviceHours" :serviceOrderCount="userData.serviceOrderCount" :serviceUserCount="userData.serviceUserCount" :authorAuth="userData.authorAuth" :type="userData.type" :isMissionDetail="isMissionDetail" :missionNum="userData.taskCount" :userTags="userTags" :realAuth="userData.realAuth" :eduAuth="userData.eduAuth" :userCount="userData.userCount" :avaTaskCount="userData.avaTaskCount " :cost="userData.cost" :workAge="workAge" :workHours="userData.workHours" :imgurl="userImg" :shortName="userData.nickname"></writers>
        <!--三个按钮-->
        <div class="btnBox bgW">
            <x-button v-if="!isLocalUser" @click.native="fellowThisWriter" :class="userData.follow?'hasFellow':''">
                <i class="iconfont icon-aixin"></i>{{userData.follow?'取消关注':"关注"}}</x-button>
            <x-button @click.native="gotoSendMsg" v-if="!isLocalUser">
                <i class="iconfont icon-feiji"></i>私信</x-button>
            <x-button v-if="!isLocalUser" @click.native="gotoSendOrientationTask">
                <i class="iconfont icon-xie"></i>发起任务</x-button>
        </div>
    </div>
    <!--工作方式-->
    <div class="eachBox">
        <p class="title">工作方式</p>
        <div class="content">
            <div class="eachHang">
                <p class="leftText">书本年级：</p>
                <p class="rightText">{{classNo}}</p>
            </div>
            <div class="eachHang">
                <p class="leftText">书本科目：</p>
                <p class="rightText">{{subject}}</p>
            </div>
            <div class="eachHang">
                <p class="leftText">参考费用：</p>
                <p class="rightText">
                    1对1：{{soleCost}}/人时；
                    1对多：{{jointCost}}/人时；
                </p>
            </div>
            <div class="eachHang">
                <p class="leftText">上门方式：</p>
                <p class="rightText">{{coordination}}</p>
            </div>
            <div class="eachHang">
                <p class="leftText">上门区域：</p>
                <p class="rightText">{{area}}</p>
            </div>
        </div>
    </div>

    <!--他的圈子-->

    <div class="eachBox circle2">
        <p class="title">TA的圈子</p>
        <router-link class="content flexSpace" tag="div" :to="{path:'/myMineCircle',query:{uid:writerId}}" v-if="circleList.length==0?false:true">

            <div v-if="circleObj.imgurls==''?false:true">
                <img v-for="item,index in circleObj.imgurls" :src="item.src" alt="">
            </div>
                <div v-if="circleObj.imgurls==''?true:false">
                    <span>{{circleObj.content}}</span>
                </div>
                <x-icon type="ios-arrow-right" size="30"></x-icon>
        </router-link>
    </div>
    <!--他的任务-->
    <div class="eachBox">
        <p class="title flexTitle">
            <span>TA的任务（2018-7-1起的最近10次）</span>
            <router-link tag="span" class="right" :to="{path:'/myPostMission',query:{uid:writerId,showAllMsg:true}}">更多
                <x-icon type="ios-arrow-right" size="13"></x-icon>
            </router-link>
        </p>
        <div class="content" v-if="taskList.length==0?false:true" @click="toseeMissionDetail">
            <div class="peple flexSpace">
                <div class="left flexStart">
                    <img :src="$store.state.imgUrl+taskObj.webUser.imgurl" alt="">
                    <div class="desc">
                        <p class="name">{{taskObj.webUser==null?'':taskObj.webUser.nickname}}</p>
                        <p class="tag">
                            <span>实名
                                    <i class="iconfont icon-wenhao" v-if="taskObj.webUser.realAuth==2?false:true"></i>
                <i class="iconfont icon-gouxuan" v-if="taskObj.webUser.realAuth==2?true:false"></i><span>{{taskObj.webUser.realAuth==2?'已':"未"}}认证</span>
                            </span>
                            <span >学历
                                   <i class="iconfont icon-wenhao" v-if="taskObj.webUser.eduAuth==2?false:true"></i>
                <i class="iconfont icon-gouxuan" v-if="taskObj.webUser.eduAuth==2?true:false"></i><span>{{taskObj.webUser.eduAuth==2?'已':"未"}}认证</span>
                            </span>
                        </p>
                    </div>
                </div>
                <div class="right">
                    <!-- <p class="smallBtn">找家长</p> -->
                    <p class="button searchSS" v-if="taskObj.taskType=='2'?true:false">找家长</p>
                    <p class="button searchZZ" v-if="taskObj.taskType=='1'?true:false">找家教</p>
                    <p class="button searchSZ" v-if="taskObj.taskType=='3'?true:false">团家长找家教</p>
                    <p class="price">￥{{taskObj.priceRange}}/人时</p>
                </div>
            </div>
            <div class="otherDesc">
                <p class="flexSpace light">
                    <span>状态：{{taskObj.supplement?"补充登记":taskObj.status}}</span>
                    <span>截至报名：{{taskObj.deadline}}</span>
                </p>
                <p class="flexSpace">
                    <span>{{taskObj.classNo}} / {{taskObj.category}} / {{taskObj.coordination}}</span>
                    <span>已报名：{{taskObj.applicationCount}} </span>
                </p>
                <p class="flexSpace">
                    <span>{{taskObj.coordination}} / {{taskObj.area}}</span>
                    <span>发布日期：{{taskObj.createTime}}</span>
                </p>
            </div>

        </div>
        <p class="shifDesc" v-if="taskList.length==0?false:true">
            简述：{{taskObj.introduction}}
        </p>
    </div>

    <!--评价标签-->
    <div class="eachBox" v-if="userData.type=='author'">
        <p class="title">家长评价标签（2018-7-1起）</p>
        <div class="content">
            <p class="evaluateTag" v-for="item,index in otherTags">
                <span>{{item.tag}}</span>
            </p>
        </div>
    </div>
    <!--自我简介-->
    <div class="eachBox">
        <p class="title">自我简述</p>
        <div class="content flexSpace">
            <p class="selfDesc">
                {{selfCon}}
            </p>
        </div>
    </div>
    <!--自我标签-->
    <div class="eachBox" v-if="userData.type=='author'">
        <p class="title">自我标签</p>
        <div class="content">
            <p class="evaluateTag" v-for="item,index in selfTag">
                <span>{{item.tag}}</span>
            </p>
        </div>
    </div>
    <!--家教经历-->
    <div class="eachBox" v-if="userData.type=='author'">
        <p class="title">家教经历</p>
        <div class="content">
            <p>{{experience}}</p>
        </div>
    </div>
    <!--家教成果-->
    <!-- <div class="eachBox" v-if="userData.type=='author'">
        <p class="title">家教成果</p>
        <div class="content">
            <p>{{achievement}}</p>
        </div>
    </div> -->
    <!--写过的著作的家长名单-->
    <!-- <div class="eachBox" v-if="userData.type=='author'">
        <p class="title">写过的著作的家长名单</p>
        <div class="content">
            <p>{{books}}</p>
        </div>
    </div> -->
    <!--背景信息-->
    <div class="eachBox">
        <p class="title">背景信息</p>
        <div class="content">
            <p class="eachBgMsg flexStart">
                <span>实名认证</span>
                <i class="iconfont icon-wenhao" v-if="userData.realAuth==2?false:true"></i>
                <i class="iconfont icon-gouxuan" v-if="userData.realAuth==2?true:false"></i>
                <!-- <span>{{userData.realAuth==2?'已':"未"}}认证</span> -->
            </p>
            <p class="eachBgMsg flexStart" v-if="userData.type=='author'">
                <span>学历认证</span>
                <i class="iconfont icon-wenhao" v-if="userData.eduAuth==2?false:true"></i>
                <i class="iconfont icon-gouxuan" v-if="userData.eduAuth==2?true:false"></i>
                <!-- <span>{{userData.eduAuth==2?'已':"未"}}认证</span> -->
                <span v-if="userData.eduAuth==2?true:false">{{userData.eduCertificate.school}},{{userData.eduCertificate.degree}}</span>
            </p>
            <p class="eachBgMsg flexStart" v-if="userData.type=='author'">
                <span>家教资历</span><i class="iconfont icon-wenhao" v-if="userData.authorAuth==2?false:true"></i>
                <i class="iconfont icon-gouxuan" v-if="userData.authorAuth==2?true:false"></i>
                <!-- <span>{{userData.authorAuth==2?'已':"未"}}认证</span> -->
            </p>
        </div>
    </div>
</div>
</template>

<script>
import writers from '@/components/writer/writer'
import common from '@/assets/js/common'

import {
    XButton,
    AlertModule
} from 'vux'
export default {
    data() {
        return {
            hang2IsShow: true,
            authodShortName: '',
            workAge: '',
            userCount: '',
            // authorInfo:{},
            userData: {},
            classNo: '',
            subject: '',
            selfCon: '',
            coordination: '',
            jointCost: '',
            area: '',
            classNo: '',
            writerId: this.$route.query.writerId,
            userType: '',
            uid: this.$store.state.uid,
            taskObj: {},
            taskList: [],
            circleObj: {},
            isFellow: false,
            soleCost: '',
            experience: '',
            achievement: "",
            books: '',
            isLocalUser: false,
            userTags: [],
            selfTag: [],
            otherTags: [],
            circleList: [],
            isMissionDetail:true,
            userImg:''

        }
    },
    components: {
        writers,
        XButton,
        AlertModule
    },
    mounted() {
        // 获取用户信息
        this.getWebUser(this.writerId);
        // 获取任务信息
        this.getMIssionlist()
        // 获取圈子信息
        this.getCircleLoist();
        // alert(this.writerId)
        if (this.writerId == this.$store.state.uid) {
            this.isLocalUser = true
        }
        // 获取标签列表
        this.getTasgs(this.writerId)

    },
    methods: {
        ...common,
        // 发起定向任务
        gotoSendOrientationTask() {
            var that = this;
            that.$router.push({
                path: "/taskOrientation",
                query: {
                    writerId: that.writerId
                }
            })
        },
        // 点击查看任务详情
        toseeMissionDetail(){
            var that=this;
            console.log(that.taskObj)
            that.$router.push({
                path:'/missionInAfterEvaluteBuss',
                query:{
                    id:that.taskObj.id,
                    showAllMsg:true
                }
            })
        },
        // 关注
        fellowThisWriter() {
            var that = this;
            var postData = {
                uid: that.uid, //当前用户id
                followingUid: that.writerId //被关注人的id
            }
            that.$http('post', that.$store.state.baseUrl + 'api/UserFollowing', postData).then(function (res) {
                if (res.data.code != '00') {
                    AlertModule.show({
                        title: res.data.msg
                    })
                } else {
                    AlertModule.show({
                        title: "操作成功！",
                        onHide() {
                            that.getWebUser(that.writerId);
                        }
                    })
                    that.isFellow = true
                }
            })
        },
        // 私信
        gotoSendMsg() {
            var that = this;
            // alert(that.writerId)
            that.$router.push({
                path: '/privateMsg',
                query: {
                    writerId: that.writerId
                }
            })
        },
        // 获取圈子列表
        getCircleLoist() {
            var that = this;
            var baseUrl = this.$store.state.baseUrl;
            that.$http('get', baseUrl + 'api/Circle/List', {
                loginUid: that.uid,
                uid: that.writerId
            }).then(function (res) {
                var result = res.data.data;
                that.circleList = res.data.data;
                if (result.length != 0) {
                    for (var i in result) {
                        if (result[i].imgurls != '') {
                            if (result[i].imgurls.indexOf(',') == -1) {
                                // 没有逗号，只有一条数据
                                var Arr = []
                                var obj = {
                                    src: that.$store.state.imgUrl + result[i].imgurls
                                }
                                Arr.push(obj)
                                result[i].imgurls = Arr;
                            } else {
                                result[i].imgurls = result[i].imgurls.split(',')
                                var arr2 = []
                                for (var k in result[i].imgurls) {
                                    var obj = {};
                                    obj.src = that.$store.state.imgUrl + result[i].imgurls[k];
                                    arr2.push(obj)
                                }
                                result[i].imgurls = arr2;
                            }
                        }
                    }
                    that.circleObj = result[0]
                }
                console.log('圈子')
                console.log(result)
                // for (var i in result) {
                //     if (result[i].imgurls != '') {
                //         //什么都没有
                //         if (result[i].imgurls.indexOf(',') == -1) {
                //             // 没有逗号，只有一条数据
                //             var Arr = []
                //             var obj = {
                //                 src: that.$store.state.imgUrl + result[i].imgurls
                //             }
                //             Arr.push(obj)
                //             result[i].imgurls = Arr;
                //         } else {
                //             result[i].imgurls = result[i].imgurls.split(',')
                //             var arr2 = []
                //             for (var k in result[i].imgurls) {
                //                 var obj = {};
                //                 obj.src = that.$store.state.imgUrl + result[i].imgurls[k];
                //                 arr2.push(obj)
                //             }
                //             result[i].imgurls = arr2;
                //         }
                //     }
                // }

            })
        },
        // 获取任务列表
        getMIssionlist(status) {
            var that = this;
           
         that.postData = {
                uid: that.writerId
            }
            
           
            // that.postData.status = status;
            console.log(that.postData)
            that.$http('get', that.$store.state.baseUrl + 'api/Task/correlation-task', that.postData).then(function (res) {
                // that.postList = res.data.data;
                if (res.data.code == '00') {
                    var result = res.data.data;
                    // console.log(result.webUser.type)
                    for (var i in result) {
                        var arr = result[i].priceRange.split('-')
                        result[i].priceMin = arr[0];
                        result[i].priceMax = arr[1];

                        if (result[i].taskType == 1) {
                            result[i].borderColor = 'zz'
                        } else if (result[i].taskType == 2) {
                            result[i].borderColor = 'ss'
                        } else {
                            result[i].borderColor = 'sz'
                        }
                    }
                    // console.log('我的任务')
                    // console.log(res.data.data)
                    that.taskList = res.data.data
                    // that.postList = res.data.data;
                    if( res.data.data.length>0){
                         that.taskObj = res.data.data[0];
                    }
                   
                   
                    switch (that.taskObj.status) {
                        case 1:
                            that.taskObj.status = '发布中'
                            break;
                        case 2:
                            that.taskObj.status = '已选定服务人'
                            break;
                        case 3:
                            that.taskObj.status = '双方已确认代付款'
                            break;
                        case 4:
                            that.taskObj.status = '已结束'
                            break;
                        case 6:
                            that.taskObj.status = '已取消'
                            break;
                        case 7:
                            that.taskObj.status = '已有人报名'
                            break;
                        case 8:
                            that.taskObj.status = '执行中'
                            break;
                        case 88:
                            that.taskObj.status = '取消中'
                            break;
                        case 99:
                            that.taskObj.status = '已失败'
                            break;
                    }
                } else {}
            })
        },
    }
}
</script>

<style>
#writerDetail .reacResult {
    margin-top: 0;
}

#writerDetail .topBox {
    border-bottom: 10px solid #f5f5f5;
    padding-bottom: 15px;
}

#writerDetail .btnBox {
    padding-bottom: 10px;
    display: flex;
    justify-content: space-between;
    padding-left: 7px;
    padding-right: 7px;
    align-items: center;
}

#writerDetail .btnBox .weui-btn {
    width: 100px !important;
    height: 35px !important;
    font-size: 14px;
}

#writerDetail .btnBox .weui-btn i {
    margin-right: 7px;
}

#writerDetail .eachBox {
    border-bottom: 10px solid #f5f5f5;
    background: #fff;
}

#writerDetail .eachBox p.title {
    padding: 11px 14px;
    font-size: 12px;
    color: #3375C5;
    border-bottom: 1px solid #CECECE;
}

#writerDetail .content {
    padding: 13px 11px;
    color: #242424;
    font-size: 12px;
}

#writerDetail .content .eachHang {
    display: flex;
    justify-content: flex-start;
}

#writerDetail .content .eachHang .leftText {
    min-width: 70px;
}

#writerDetail .content .eachHang {
    margin-bottom: 8px;
}

#writerDetail .eachBox.circle2 .content {
    display: flex;
    /* justify-content: flex-start; */
    align-items: center;
    color: #242424;
}

#writerDetail .eachBox.circle2 .content img {
    width: 79px;
    height: 79px;
    margin-right: 5px;
}

#writerDetail .eachBox .content .peple img {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    margin-right: 11px;
}

#writerDetail .eachBox .content .peple .name {
    font-weight: bold;
    color: #646464;
    font-size: 14px;
    margin-bottom: 10px;
}

#writerDetail .eachBox .content .peple .tag {
    color: #999;
}

#writerDetail .eachBox .content .peple .tag i {
    color: #3375C5;
    margin-left: 4px;
}

#writerDetail .eachBox .content .peple {
    padding-bottom: 12px;
    border-bottom: 1px solid #cecece;
}

/* #writerDetail .eachBox .content .peple .right .smallBtn {
    color: #2EA200;
    border: 1px solid #2EA200;
    width: 60px;
    height: 20px;
    line-height: 20px;
    font-weight: bold;
    border-radius: 5px;
    text-align: center;
    margin-bottom: 8px;
} */
#writerDetail .eachBox .content .peple .right .button {
    font-size: 14px;
    padding: 1px 7px 1px 12px;
    border-radius: 5px;
    margin-bottom: 8px;
}

#writerDetail .eachBox .content .peple .right .button.searchSS {
    color: #2EA200;
    border: 1px solid #2EA200;
}

#writerDetail .eachBox .content .peple .right .button.searchZZ {
    color: #3375C5;
    border: 1px solid #3375C5;
}

#writerDetail .eachBox .content .peple .right .button.searchSZ {
    color: #FEAB29;
    border: 1px solid #FEAB29;
}

#writerDetail .eachBox .content .peple .right .price {
    color: #FF3636;
    font-weight: bold;
}

#writerDetail .eachBox .flexTitle {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

#writerDetail .eachBox .flexTitle span.right {
    color: #242424;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

#writerDetail .eachBox .otherDesc {
    color: #999;
    font-size: 12px;
    padding-top: 13px;
}

#writerDetail .eachBox .otherDesc p {
    line-height: 21px;
}

#writerDetail .eachBox .otherDesc .light {
    color: #3375C5;
}

#writerDetail .eachBox .shifDesc {
    color: #999;
    font-size: 12px;
    padding: 11px 14px;
    background: #f5f5f5;
    border-bottom: 4px solid #2EA200;
}

#writerDetail .eachBox .evaluateTag {
    /*padding: 9px 13px;*/
    display: inline-block;
    margin-right: 5px;
    margin-bottom: 14px;
    height: 35px;
    line-height: 35px;
    text-align: center;
    width: 108px;
    color: #868686;
    font-size: 14px;
    border: 1px solid #848484;
}

#writerDetail .eachBox .evaluateTag .num {
    color: #3375C5;
    margin-left: 10px;
}

#writerDetail .eachBox .content .selfDesc {
    color: #242424;
}

#writerDetail .eachBgMsg {
    margin-bottom: 15px;
}

#writerDetail .eachBgMsg .iconfont {
    color: #3375C5;
    margin-left: 7px;
    margin-right: 10px;
}

#writerDetail .btnBox .weui-btn.hasFellow {
    background: #ccc !important;
    border-color: #ccc !important;
}
#writerDetail .btnBox .hasFellow.weui-btn:after{
    display: none;
}
</style>
