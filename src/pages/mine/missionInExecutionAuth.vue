<template>
<div id="missionInExecutionBuss">
    <div class="main" v-if="mainShow">
        <!--任务详情（家长版）-->
        <div class="topBox bgW">
            <div class="hang flexSpace">
                <p class="left"><span class="title">发布者：</span>
                  <router-link tag="span" :to="{path:'/writerDetail',query:{'writerId':postUser.id}}"   class="mainText">{{postUser.nickname}}</router-link>
                  </p>
                <p class="right">{{showData.category}}/{{showData.classNo}}</p>
            </div>
            <div class="hang flexSpace">
                <p class="left"><span class="title">服务提供者：</span>
                 <router-link tag="span" :to="{path:'/writerDetail',query:{'writerId':serviceProvider.id}}"    class="mainText">{{serviceProvider.nickname}}</router-link>
                     </p>
                <p class="right">{{showData.priceType =='1'?'1对1':"1对多"}}/{{showData.coordination=="online"?"远程协作":showData.coordination=="author"?"家教上门":"家长拜访"}}</p>
            </div>
            <div class="hang flexSpace">
                <p class="left"><span class="title">服务接收者：</span>
                    <span @click="mainShow=!mainShow;serviceRecipientShow=!serviceRecipientShow">{{!nicknameArr?'':"查看详细名单共"+bussArr.length+"人"}}</span></p>

                <p class="right">{{showData.area}}</p>
            </div>
        </div>
        <div class="contentText">
            <div class="each">
                <span class="title">简述：{{showData.introduction}}</span>
            </div>
            <div class="each">
                <span class="title">详述：{{showData.content}}</span>
            </div>
        </div>

        <!--进度条-->
        <div class="stepBox flexSpace bgW">
            <div class="line"></div>
            <div :class="showData.status=='已选定服务人'?'active item flexCenter':''">
                <p class="circle"></p>
                <p class="text">待确认</p>
            </div>
            <div :class="showData.status=='执行中'?'active item flexCenter':''">
                <p class="circle"></p>
                <p class="text">执行</p>
            </div>
            <div :class="showData.status=='已结束'?'active item flexCenter':''">
                <p class="circle"></p>
                <p class="text">结束</p>
            </div>
        </div>
        <div class="statusBox flexSpace bgW">
            <p class="left">状态：{{showData.status}}</p>
            <div class="right">
                <button class="cancelExecute" @click="cancelExecute" v-if="showData.status!=4&&showData.taskType!=3&&showData.priceType!=2">取消执行</button>
            </div>
        </div>
        <!-- 是否有取消执行的信息 -->
        <div class="eachPart bgW">
            <div class="title flexSpace">
                <div class="left">取消执行确认</div>
                <div class="right"></div>
            </div>
            <!-- v-if="Boolean(missionCancelMsg)&&missionCancelMsg.receviceUid!=$store.state.uid" -->
            <div class="content cancelMsg" v-if="showData.status=='取消中'&&missionCancelMsg.uid!=$store.state.uid">
                <p><span class="name">{{missionCancelMsg.user.nickname}}</span> 提出取消任务执行</p>
                <p><span class="mainText">理由：</span> 经双方友好协商，同意取消执行当前任务！</p>
                <p class="last flexStart">
                    <span class='time'>剩余{{remainingTime.day}}天{{remainingTime.hour}}小时{{remainingTime.minute}}分</span>
                    <span class="agree" @click="agreeCancel('2')">同意取消</span>
                    <span class="agree agreeNot" @click="agreeCancel('3')">不同意</span>
                </p>
            </div>
        </div>

        <!-- 每个部分 -->
        <div class="eachPart bgW">
            <div class="title flexSpace">
                <div class="left">费用明细与细节</div>
                <div class="right" @click="priceDetailShow=!priceDetailShow">查看每期</div>
            </div>
            <div class="content">
                <div class="hang flexSpace">
                    <p class="left"><span class="title">总费用：</span><span class="mainText">{{stageDetail.price}}</span></p>
                    <p class="right "><span class="title">开始日期：</span><span class="mainText">{{stageDetail.beginDate.substr(0,10)}}</span></p>

                </div>
                <div class="hang flexSpace">
                    <p class="left "><span class="title">阶段次数:</span><span class="mainText">{{stageDetail.stage}}次</span></p>
                    <p class="right "><span class="title">结束日期：</span><span class="mainText">{{stageDetail.endDate.substr(0,10)}}</span></p>

                </div>
                <div class="hang flexSpace">
                    <p class="left"><span class="title">每次时长：</span><span class="mainText">{{stageDetail.timeStage}}</span></p>
                    <p class="right"><span class="title">支付方式：</span><span class="mainText">分阶段在线支付</span></p>
                </div>
                <div class="hang flexSpace">
                    <p class="left"><span class="title">总共时长:</span><span class="mainText">{{stageDetail.totalTime}}</span></p>
                    <p class="right"><span class="title">手续费({{serviceRate*100}}%)：</span><span class="mainText redText">{{stageDetail.handlingFee}}</span></p>
                </div>
                <div class="hang flexSpace">
                    <p class="left "><span class="title">费用单价：</span><span class="mainText">￥ {{Math.floor((stageDetail.price-stageDetail.handlingFee-stageDetail.adjustmentFee)/stageDetail.totalTime*100)/100}}/人时</span></p>
                    <p class="right"><span class="title">调解费({{adjustmentRate*100}}%)：</span><span class="mainText redText">{{stageDetail.adjustmentFee}}</span></p>

                </div>
                <div class="lastHang flexStart">
                    <p class="strongText">
                        人均支付：
                        <span
              class="mainText"
            >￥
            {{showData.taskType=='3'?Math.floor(stageDetail.price/(showData.busniessCount+1)*100)/100:Math.floor(stageDetail.price/showData.busniessCount*100)/100}}
            /人（服务接收者）
                </span>
                    </p>
                </div>
            </div>
        </div>
        <!-- 每个部分   每阶段明细 -->
        <div class="eachPart eachBox2   bgW" v-if="priceDetailShow">
            <div class="title">
                <div class="left mainText"> 服务执行方每阶段实际到账（参考）</div>
            </div>
            <div class="content">
                <div class="eachHang flexSpace" v-for="item,index in stageDetail.infos">
                    <p class="left">第{{index+1}}次</p>
                    <p class="center">{{item.date.substring(0,11)}}</p>
                    <p class="right mainText">￥{{item.amount}}</p>
                </div>
            </div>
        </div>
        <!-- 每个部分 -->
        <div class="eachPart bgW">
            <div class="title flexSpace">
                <div class="left">执行情况</div>
                <div class="">下一阶段：{{nextStageTime}}</div>
            </div>
            <div class="content">
                <div class="hang hangList" v-for="item,index in stageDetail.infos">
                    <p class="times flexSpace">
                        <span>第{{index+1}}次</span><span>{{item.date}}</span>
                    </p>
                    <p class="main">计划：{{item.content}}</p>
                    <p class="bottom">结果：{{!item.registerContent?"待完成":item.registerContent}}</p>
                </div>
            </div>
        </div>
           <div class="eachPart commentPart bgW">
            <div class="title flexSpace">
                <div class="left">家长评价</div>

            </div>
            <div class="content">
                <div class="eachComment flexStart" v-for="item,index in commentList">
                    <img  :src="$store.state.imgUrl+item.webUser.imgurl" alt="">
                    <div class="right">
                        <div class="flexSpace">
                            <p class="name">{{item.webUser.nickname}}</p>
                            <p class="date">{{item.commentTime}}</p>
                        </div>
                        <p class="content">{{item.content}}</p>
                        <p class="tags">
                            <span class="eachTags" v-for="item,index in item.commentTags">{{item}}</span>
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <div class="btnBox bgW flexSpace">
            <button class="long_btn" @click="gotoWrite">写阶段计划</button>
            <!--  :class="isCanRegister?'long_btn':'long_btn disabled'" -->
            <button :class="stageDetail.infos[stageDetail.infos.length-1].registerContent||!isCanRegister?'long_btn disabled':'long_btn'" @click="gotoRegist">登记阶段成果</button>
        </div>
    </div>
    <!-- ==============查看接收者================ -->
    <div id="serviceRecipient" v-if="serviceRecipientShow">
        <p class="title">
            共{{bussArr.length}}人，{{bussHasPay.length}}人
            <span class="mainText">已支付</span>
            ，{{bussNotPay.length}}人待支付，{{bussRefuse.length}}人
            <span class="redText">已拒绝</span>
        </p>
        <div class="content bgW">
            <div class="eachList flexSpace" v-for="item,index in bussArr">
                 <router-link tag="span" :to="{path:'/writerDetail',query:{'writerId':item.user.id}}"   class="mainText">{{item.user.nickname}}</router-link>
                  
                <span class="mainText">{{item.payText}}</span>
            </div>
        </div>
    </div>
</div>
</template>

<script>
import common from '@/assets/js/common'

import {
    XButton,
    Group,
    PopupPicker,
    Datetime,
    AlertModule
} from 'vux'

export default {
    components: {
        XButton,
        Group,
        PopupPicker,
        Datetime,
        AlertModule
    },
    data() {
        return {
            missionId: this.$route.query.id,
            // 下一阶段时间
            nextStageTime: "",
            // 剩余时间
            remainingTime: {},
            showData: {},
            nickname: '',
            stageDetail: {},
            nicknameArr: [],
            confirmList: [],
            missionCancelMsg: {},
            isCanRegister: false,
            isPay: false,
            serviceRate: 0,
            adjustmentRate: 0,
            postUser: {},
            // 服务接受者
            serviceReceiver: "",
            // 服务提供者
            serviceProvider: "",
            bussArr: [],
            bussNotPay: [],
            bussRefuse: [],
            bussHasPay: [],
            mainShow: true,
            serviceRecipientShow: false,
            priceDetailShow: false,
            commentList:[]

        }
    },

    mounted() {
        // 调解费
        this.getStageDetail()
        this.getBaseRate();
        this.getCancelMsg();
        this.getMissiondetail(this.missionId);
       
         this.getCommentList(this.missionId);

    },
    methods: {
        ...common,
         //    获取任务评价列表
        getCommentList(id) {
            var that = this;
            that.$http('get', that.$store.state.baseUrl + "api/TaskComment/List?taskId=" + id).then(function (res) {
                if (res.data.code == '00') {
                   var  result=res.data.data
                    for(var i in result){
                        result[i].commentTags= result[i].commentTags.split(',')
                    }
                    that.commentList = res.data.data
                    console.log(that.commentList)
                }
            })
        },
        // 获取已经确认得用户信息
        getConfirmWriter(id) {
            var that = this;
            var postData = {
                taskId: id,
                status: "2"
            };
            that
                .$http(
                    "get",
                    that.$store.state.baseUrl + "api/Task/Apply/List",
                    postData
                )
                .then(function (res) {
                    // console.log(res.data.data);
                    if (res.data.code != "00") {
                        AlertModule.show({
                            title: res.data.msg
                        });
                    } else {
                        var result = res.data.data;
                        for (var i in result) {
                            if (result[i].uid == that.showData.authorId) {
                                result.splice(i, 1)
                            }
                        }
                        // console.log(result)
                        // 判断任务类型,如果是1,3.就把发布者也算到家长列表中
                        if (that.showData.taskType == 1 || that.showData.taskType == 3) {
                            var obj = {}
                            obj.user = {}
                             obj.user.id=that.showData.webUser.id;
                            obj.user.nickname = that.showData.webUser.nickname
                            if (!that.showData.payTime) {
                                obj.pay = 2
                            } else {
                                obj.pay = 3
                            }
                            result.push(obj)
                        }

                        // 支付状态(1-不需要支付,2-待支付,3-已支付,4-已拒绝)
                        for (var j in result) {
                            if (result[j].pay == '2') {
                                result[j].payText = "待支付"
                                that.bussNotPay.push(result[j])
                            } else if (result[j].pay == '4') {
                                result[j].payText = '已拒绝'
                                that.bussRefuse.push(result[j])
                            } else if (result[j].pay == '3') {
                                result[j].payText = '已支付'
                                that.bussHasPay.push(result[j])
                            }
                        }
                        var nicknameArr = [];
                        that.bussArr = result;
                        that.nicknameArr = nicknameArr;
                        console.log('弹窗家长')
                        console.log(that.bussArr)
                    }
                });
        },
        // 获取任务详情
        getMissiondetail(id) {
            var that = this;
            that.$http('get', that.$store.state.baseUrl + 'api/Task/' + id + "?uid=" + this.$store.state.uid).then(function (res) {
                if (res.data.code == '00') {
                    that.postUser = res.data.data.webUser;

                    var obj = res.data.data.webUser
                    that.nickname = obj.nickname;
                    // 1-发布中,2-已选定服务人,3-双方已确认代付款,4-已结束,6-已经取消,7-已有人报名,8,执行中 99-已失败,88-取消中
                    switch (res.data.data.status) {
                        case 1:
                            res.data.data.status = '发布中'
                            break;
                        case 2:
                            res.data.data.status = '已选定服务人'
                            break;
                        case 3:
                            res.data.data.status = '双方已确认代付款'
                            break;
                        case 4:
                            res.data.data.status = '已结束'
                            break;
                        case 6:
                            res.data.data.status = '已取消'
                            break;
                        case 7:
                            res.data.data.status = '已有人报名'
                            break;
                        case 8:
                            res.data.data.status = '执行中'
                            break;
                        case 88:
                            res.data.data.status = '取消中'
                            break;
                        case 99:
                            res.data.data.status = '已失败'
                            break;
                    }
                    that.showData = res.data.data;
                    console.log(that.showData.status)
                    console.log(that.missionCancelMsg.uid + "---" + that.$store.state.uid)
                    console.log(that.showData.status == 88 && that.missionCancelMsg.uid != that.$store.state.uid)
                    if (Boolean(res.data.data.allPay)) {
                        that.isPay = true
                    } else {
                        that.isPay = false
                    }
                    that.$http("get", that.$store.state.baseUrl + "api/WebUser/" + that.showData.authorId).then(function (res1) {
                        if (res1.data.code != "00") {
                            AlertModule.show({
                                title: res1.data.msg
                            });
                        } else {
                            that.serviceProvider = res1.data.data;

                            that.$http("get", that.$store.state.baseUrl + "api/WebUser/" + that.showData.uid).then(function (res2) {
                                if (res2.data.code != "00") {
                                    AlertModule.show({
                                        title: res.data.msg
                                    });
                                } else {
                                    that.postUser = res2.data.data;
                                    // 被选择的家教
                                    that.getConfirmWriter(that.missionId);
                                }
                            })
                        }

                    });

                } else {
                    AlertModule.show({
                        title: res.data.msg
                    })
                }

            })
        },
        // 是否同意别人的取消
        agreeCancel(num) {
            var that = this;
            var postData = {
                status: num
            }

            var url = that.$store.state.baseUrl + 'api/Task/Cancel/' + that.missionCancelMsg.id;
            // console.log(url)
            that.$http('put', url, postData).then(function (res) {
                if (res.data.code == '00') {
                    AlertModule.show({
                        title: "操作成功"
                    })
                } else {
                    AlertModule.show({
                        title: "操作失败"
                    })
                }
            })
        },
        // 取消执行
        cancelExecute() {
            var that = this;
            if (that.showData.taskType == '3' || that.showData.priceType == '2') {
                AlertModule.show({
                    title: "抱歉，该类型任务不能取消"
                })
            } else {
                if (that.showData.allPay) {
                    that.$http('post', that.$store.state.baseUrl + 'api/Task/Cancel', {
                        taskId: that.missionId,
                        uid: that.$store.state.uid
                    }).then(function (res) {
                        if (res.data.code == '00') {
                            AlertModule.show({
                                title: "提交取消成功"
                            })
                        } else {
                            AlertModule.show({
                                title: res.data.msg
                            })
                        }
                    })
                } else {
                    AlertModule.show({
                        title: "对方未支付，不能申请取消"
                    })
                }

            }
        },
        // 获取任务取消信息
        getCancelMsg() {
            var that = this;
            that.$http('get', that.$store.state.baseUrl + 'api/Task/CancelTask', {
                taskId: that.missionId
            }).then(function (res) {
                if (res.data.code == '00') {
                    if (res.data.data) {
                        that.missionCancelMsg = res.data.data;
                        var time = res.data.data.applyTime;
                        var time3;
                        time3 = new Date(time.replace(/-/g, "/")).getTime() + 1000 * 60 * 60 * 24 * 3;
                        time3 = that.format(time3)
                        var end = that.format(new Date())
                        var day = that.GetDateDiff(end, time3, "day")
                        var hour = Number(that.GetDateDiff(end, time3, "hour")) - day * 24
                        var minute = that.GetDateDiff(end, time3, "minute") - (day * 24 * 60 + hour * 60)
                        console.log('时间' + day + "-" + hour + "-" + minute)
                        that.remainingTime = {
                            day: day,
                            hour: hour,
                            minute: minute
                        };
                    }

                } else {
                    AlertModule.show({
                        title: res.data.msg
                    })
                }
            })
        },
        // 转时间相关======================================
        GetDateDiff(startTime, endTime, diffType) {
            //将xxxx-xx-xx的时间格式，转换为 xxxx/xx/xx的格式 
            startTime = startTime.replace(/\-/g, "/");
            endTime = endTime.replace(/\-/g, "/");

            //将计算间隔类性字符转换为小写
            diffType = diffType.toLowerCase();
            var sTime = new Date(startTime); //开始时间
            var eTime = new Date(endTime); //结束时间
            //作为除数的数字
            var divNum = 1;
            switch (diffType) {
                case "second":
                    divNum = 1000;
                    break;
                case "minute":
                    divNum = 1000 * 60;
                    break;
                case "hour":
                    divNum = 1000 * 3600;
                    break;
                case "day":
                    divNum = 1000 * 3600 * 24;
                    break;
                default:
                    break;
            }
            return parseInt((eTime.getTime() - sTime.getTime()) / parseInt(divNum));
        },

        add0(m) {
            return m < 10 ? '0' + m : m
        },

        format(shijianchuo) {
            var that = this;
            //shijianchuo是整数，否则要parseInt转换
            var time = new Date(shijianchuo);
            var y = time.getFullYear();
            var m = time.getMonth() + 1;
            var d = time.getDate();
            var h = time.getHours();
            var mm = time.getMinutes();
            var s = time.getSeconds();
            return y + '-' + that.add0(m) + '-' + that.add0(d) + ' ' + that.add0(h) + ':' + that.add0(mm) + ':' + that.add0(s);
        },
        // ====================================================
        // 获取任务费用明细
        getStageDetail() {
            var that = this;
            that.$http('get', that.$store.state.baseUrl + 'api/Task/Stage/' + that.missionId).then(function (res) {
                if (res.data.code == '00') {
                    // nextStageTime
                    var result = res.data.data.infos;
                    for (var i in result) {
                        if (result[i].success) {
                            continue;
                        } else {
                            that.nextStageTime = result[i].date;
                            break;
                        }
                    }
                    // alert(that.nextStageTime)
                    // alert(new Date().getTime() >= new Date(that.nextStageTime.replace(/-/g,'/')).getTime())
                    if (new Date().getTime() >= new Date(that.nextStageTime.replace(/-/g,'/')).getTime()) {
                        that.isCanRegister = true;
                    }
                    that.stageDetail = res.data.data;

                }
            })

        },
        // 去写阶段计划
        gotoWrite() {
            var that = this;
            if (that.isPay) {
                that.$router.push({
                    path: 'stagePlan',
                    query: {
                        missionId: that.missionId,
                        stageDetail: JSON.stringify(that.stageDetail)
                    }
                })
            } else {
                AlertModule.show({
                    title: "对方尚未支付"
                })
            }

        },
        // 登记阶段成果
        gotoRegist() {
            var that = this;

            if (that.isPay) {
                that.$router.push({
                    path: '/registAchievement',
                    query: {
                        missionId: that.missionId,
                        stageDetail: JSON.stringify(that.stageDetail)
                    }
                })
            } else {
                AlertModule.show({
                    title: "对方尚未支付"
                })
            }

        }

    }
}
</script>

<style scoped>
body {
    background: #F5F5F5 !important;
}

/* // -------------------------------------- */
.eachPart .content .eachHang {
    font-size: 14px;
    margin-bottom: 10px;
}

.eachPart .content .eachHang .center {
    color: #242424;
    padding: 4px 15px;
    border-radius: 5px;
    background: #f5f5f5;
    border: 1px solid #cecece;
    min-width: 120px;
}

.eachBox2 .content {
    padding: 21px 18px 0px 18px;
    line-height: 25px;
    color: #999999;
    font-size: 12px;
}

.eachBox2 .content .left {
    color: #999999;
}

.eachBox2 {
    padding-bottom: 16px;
}

.eachBox2 .content .right {
    padding-left: 0;
    font-weight: bold;
}

/* // -------------------------------------- */

#serviceRecipient .title {
    background: #F5F5F5;
    padding: 18px 0px;
    text-align: center;
    font-size: 14px;
}

#serviceRecipient .content {
    padding-left: 40PX;
    padding-top: 20px;
}

#serviceRecipient .content .eachList {
    height: 50px;
    line-height: 50px;
    border-bottom: 1px solid #CECECE;
    padding-right: 45px;
}

#missionInExecutionBuss {
    padding-bottom: 50px;

}

.topBox {
    margin-top: 13px;
    padding: 14px 10px;
}

.topBox .hang {
    margin-bottom: 5px;
    font-size: 12px;
}

.topBox .hang .left span {
    color: #3374C4;
    font-size: 12px;
}

.topBox .hang .left .title {
    color: #999;
    font-size: 12px;
}

.topBox .hang .right {
    color: #999;
    font-size: 12px
}

.contentText {
    padding: 11px;
    color: #999;
    font-size: 12px;
}

.contentText .each {
    margin-bottom: 10px;
}

.stepBox .line {
    z-index: 0;
}

.statusBox {
    padding: 19px;
    margin-bottom: 13px;
}

.statusBox .left {
    color: #3374C4;
    font-size: 14px;
}

.statusBox .right button {
    color: #EA4A5C;
    font-size: 14px;
    width: 85px;
    height: 30px;
    /* padding: 9 13px; */
    border: 1px solid #EA4A5C;
    border-radius: 5px;
}

.eachPart>.title {
    padding: 14px 19px;
    border-bottom: 1px solid #B2B2B2;
    color: #999;
    font-size: 12px;
}

.eachPart>.title .right {
    color: #3374C4;
    text-decoration: underline;

}

.eachPart {
    margin-bottom: 10px;
}

.eachPart>.content {
    padding: 20px 18px;
}

.eachPart>.content {
    color: #3374C4;
}

.eachPart>.content.cancelMsg {
    color: #999999;
    font-size: 12px;
}

.eachPart>.content.cancelMsg p {
    margin-bottom: 16px;
}

.eachPart>.content.cancelMsg p.last {
    flex-direction: row;
    align-items: center;
    justify-content: flex-end;
    margin-top: 38px;
}

.eachPart>.content.cancelMsg .time {
    color: #F83345;
    font-size: 14px;
    font-weight: bolder;
}

.eachPart>.content .name {
    color: #3374C4;
    text-decoration: underline
}

.eachPart>.content .agree {
    display: inline-block;
    width: 85px;
    height: 30px;
    line-height: 30px;
    background: #F83345;
    border: 1px solid #F83345;
    border-radius: 5px;
    color: #fff;
    text-align: center;
    margin-left: 10px;

}

.eachPart>.content .agreeNot {
    color: #F83345;
    background: #fff;
}

.eachPart>.content .left .title {
    color: #999;
}

.eachPart>.content .hangList {
    border-bottom: 1px solid #cecece;
    margin-bottom: 10px;
    text-align: left;
    display: flex;
    flex-direction: column;
}

.eachPart>.content .bigTitle {
    color: #999999;
    font-size: 14px;
    font-weight: bolder;
    margin-top: 26px;
}

.eachPart>.content .bigTitle span {
    color: #3374C4;
}

.eachPart>.content .hang p {
    color: #999;
    margin-bottom: 10px;

}

.eachPart>.content .hang p.main {
    color: #242424;
    font-size: 14px;
}

.eachPart>.content .hang p.bottom {
    color: #3375C5;
    font-size: 14px;
}

.btnBox {
    position: fixed;
    bottom: 0;
    width: 100%;
}

.btnBox button {
    margin-top: 7px;
    margin-bottom: 7px;
    width: 40%;
}

.btnBox button.disabled {
    background: #C3C3C3;
    pointer-events: none;
}
.commentPart .eachComment {
    align-items: flex-start;
    padding-top: 10px;
}

.commentPart .right {
    padding-top: 5px;
    color: #333;
    font-size: 12px;
    border-bottom: 1px solid #B2B2B2;
    width: 100%;
    padding-bottom: 16px;
}

.commentPart img {
    margin-right: 10px;
    border: 1px solid #ccc;
    width: 42px;
    height: 42px;
    border-radius: 50%
}

.commentPart .name {
    color: #333;
    font-size: 12px;
    margin-bottom: 14px;
}

.commentPart .date {
    color: #686868;
    font-size: 10px;
}

.tags .eachTags {
    color: #333333;
    font-size: 12px;
    width: 65px;
    height: 22px;
    line-height: 22px;
    text-align: center;
    border: 1px solid #C9C37B;
    display: inline-block;
    margin-right: 5px;
    margin-top: 10px;
    background: #FFF6C5;
}
</style>
