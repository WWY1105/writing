<template>
<div id="mailDetail">
    <!-- 信箱详情 -->
    <div class="eachMail flexSpace bgW">
        <div class="imgBox">
            <img :src="$store.state.imgUrl+mailDetail.user.imgurl"  class="userImg"  alt="">
            <!-- <span class="redDot"></span> -->
        </div>
        <div class="text">
            <p class="name">{{mailDetail.user.nickname}}</p>
            <p class="detailText">{{mailDetail.content}}</p>
            <p class="flexSpace">
                <span class="date">{{mailDetail.msgTime}}</span>
                <span class="money">红包：{{mailDetail.amount}}元</span>
            </p>
        </div>
    </div>

    <!-- 回复详情 -->
    <div class="eachMail replayBox flexSpace bgW" v-for="i,j in  comments" @click="replayMsgId(i.msgId)">
        <div class="imgBox">
            <img  :src="$store.state.imgUrl+i.user.imgurl"   class="userImg"  alt="">
        </div>
            <div class="text">
                <p class="name">{{i.user.nickname}}</p>
                <p class="detailText">{{i.content}}</p>
                <p class="flexSpace">
                    <span class="date">{{i.commentTime}}</span>
                    <!-- <span class="money">红包：1元</span> -->
                </p>
            </div>
        </div>
        <div class="replay bgW flexSpace" v-if="comments.length==0">
            <input type="text" placeholder="回复私信" v-model="content"  ref='input'>
            <button class="long_btn" @click="sendMsg">发送</button>
        </div>
    </div>
</template>

<script>
import {
    AlertModule
} from 'vux'
export default {
    data() {
        return {
            isSaw: false,
            mailId: this.$route.query.mailId,
            mailDetail: {},
            comments: [],
            msgId: '',
            content: ''
        }
    },
    components: {
        AlertModule
    },
    mounted() {
        this.getDetail();
        this.$refs.input.focus()
    },
    methods: {
        getDetail() {
            var that = this;
            var baseUrl = this.$store.state.baseUrl;
            that.$http('get', baseUrl + 'api/PrivateMsg/' + that.mailId).then(function (res) {
                if (res.data.code != '00') {
                    AlertModule.show({
                        title: res.data.msg
                    })
                } else {
                    that.mailDetail = res.data.data;
                    that.comments = res.data.data.comments;
                    // 设置已读
                    that.$http('put', baseUrl + 'api/PrivateMsg/read/' + that.mailId).then(function (res) {
                        if (res.data.code != '00') {
                            AlertModule.show({
                                title: res.data.msg
                            })
                        } else {
                            // that.mailDetail = res.data.data;
                            // that.comments = res.data.data.comments
                        }
                    })

                }
            })
        },
        // 选中要回复的信息
        replayMsgId(id) {
            this.msgId = id;
        },
        sendMsg() {
                var that = this;
                var baseUrl = this.$store.state.baseUrl;
                that.$http('post', baseUrl + 'api/PrivateMsg/comment', {
                    privateMsgId: that.mailDetail.id,
                    uid: that.$store.state.uid,
                    content: that.content
                }).then(function (res) {
                    if (res.data.code != '00') {
                        AlertModule.show({
                            title: res.data.msg
                        })
                    } else {
                       that.getDetail();
                       that.content=''
                    }   
                })
            
        }
    }
}
</script>

<style scoped>
#mailDetail {
    position: relative;
    height: 100%;
    width: 100%;
}

#mailDetail .eachMail {
    padding: 13px 23px 16px 11px;
    align-items: flex-start;
}

#mailDetail .eachMail .text {
    width: 100%;
}

#mailDetail .eachMail.replayBox {
    margin-top: 10px;
}

#mailDetail .eachMail .name {
    color: #333333;
    font-size: 15px;
    margin-bottom: 10px;

}

#mailDetail .eachMail .imgBox {
    position: relative;
}

#mailDetail .eachMail .detailText {
    font-size: 12px;
    color: #333;
    margin-bottom: 10px;
}

#mailDetail .eachMail .date {
    font-size: 10px;
    color: #686868;
}

#mailDetail .eachMail .money {
    font-size: 12px;
    color: #EA4A5C;
}

#mailDetail .userImg {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    margin-right: 10px;

}

#mailDetail .replay {
    padding: 9px 16px;
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;

}
#mailDetail .replay .long_btn{
    margin: 0;
    max-width: 80px;
    margin-left: 10px;
        height: 36px;
}
#mailDetail .replay input {
    width: 100%;
    height: 36px;
    border: 1px solid #E2E2E2;
    border-radius: 5px;
    background: #f2f2f2;
    padding-left: 15px;
    font-size: 15px;
}

#mailDetail .replay input::placeholder {
    color: #A2A2A2;

}
</style>
