<template>
  <view class="user">
    <view v-if="$store.getters.token||userInfo.uid">
      <view class="header bg-color-red acea-row row-between-wrapper">
        <view class="picTxt acea-row row-between-wrapper">
          <view class="pictrue">
            <image :src="userInfo.avatar" />
          </view>
          <view class="text">
            <view class="acea-row row-middle">
              <view class="name line1">{{ userInfo.nickname }}</view>
              <view class="member acea-row row-middle" v-if="userInfo.vip">
                <image :src="userInfo.vipIcon" />
                <text>{{ userInfo.vipName }}</text>
              </view>
            </view>
            <view @click="goPersonalData()" class="id" v-if="userInfo.phone">
              <text>ID：{{ userInfo.uid || 0}}</text>
              <text class="iconfont icon-bianji1"></text>
            </view>
            <!-- #ifdef MP-WEIXIN -->
            <button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber" class="binding" v-else>
              <text>绑定手机号</text>
            </button>
            <!-- #endif -->

            <!-- #ifndef MP-WEIXIN -->
            <button class="binding" @click="goBindPhone()" v-else>
              <text>绑定手机号</text>
            </button>
            <!-- #endif -->
          </view>
        </view>
        <text class="iconfont icon-shezhi" @click="goPersonalData()"></text>
      </view>
      <view class="wrapper">
        <view class="nav acea-row row-middle">
          <view @click="goUserAccount()" class="item">
            <text>我的余额</text>
            <text class="num">{{ userInfo.nowMoney || 0 }}</text>
          </view>
          <view @click="goUserPromotion()" class="item" v-if="userInfo.isPromoter === 1 || userInfo.statu === 2">
            <text>当前佣金</text>
            <text class="num">{{ userInfo.brokeragePrice || 0 }}</text>
          </view>
          <view @click="goUserPromotion()" class="item" v-else>
            <text>当前收益</text>
            <text class="num">{{ userInfo.brokeragePrice || 0 }}</text>
          </view>
          <view @click="goUserPromotion()" class="item">
            <text>预估收益</text>
            <text class="num">{{ userInfo.bounsPre || 0 }}</text>
          </view>
        </view>
        <div class="bg-white border5 flex align-center" style="height:214rpx;margin-top:15rpx;">
          <div class="left" style="padding: 50rpx 30rpx 50rpx 60rpx;" @click="onPerson">
            <div class="flex align-center" >
              <div class="font16 text-bold">{{!userInfo.levelGr?'开通':''}}个人账户</div>
              <div class="left"></div>
              <text class="iconfont icon-jiantou font12"></text>
            </div>
            <div
              
              v-if="!userInfo.levelGr"
              class="text-center font12"
              style="margin-top:24rpx;width:148rpx;line-height:50rpx;border-radius:25rpx;background: linear-gradient(90deg, #FFC04B 0%, #F69239 100%);color:#fff"
            >开启</div>
            <div v-else class="font12" style="color:#c9c9c9;margin-top:24rpx;">
              当前级别: <span style="color:#F86741">{{userInfo.levelGrInfo&&userInfo.levelGrInfo.name}}</span>
            </div>
          </div>
          <div style="height:136rpx;border-left:1rpx dashed #E1E1E1;border-right:1rpx dashed #E1E1E1"></div>
          <div class="left" style="padding: 50rpx 30rpx 50rpx 60rpx;" @click="onStore">
            <div class="flex align-center" >
              <div class="font16 text-bold">{{!userInfo.levelDl?'开通':''}}企业账户</div>
              <div class="left"></div>
              <text class="iconfont icon-jiantou font12"></text>
            </div>
            <div
            	
            	v-if="!userInfo.levelDl"
            	class="text-center font12"
            	style="margin-top:24rpx;width:148rpx;line-height:50rpx;border-radius:25rpx;background: linear-gradient(90deg, #FFC04B 0%, #F69239 100%);color:#fff"
            >开启</div>
            <div v-else class="font12" style="color:#c9c9c9;margin-top:24rpx;">
              当前级别: <span style="color:#F86741">{{userInfo.levelDlInfo&&userInfo.levelDlInfo.name}}</span>
            </div>
          </div>
        </div>
        <view class="myOrder">
          <view class="title acea-row row-between-wrapper">
            <text>我的订单</text>
            <text @click="goMyOrder()" class="allOrder">
              <text>全部订单</text>
              <text class="iconfont icon-jiantou"></text>
            </text>
          </view>
          <view class="orderState acea-row row-middle">
            <view @click="goMyOrder(0)" class="item">
              <view class="pictrue">
                <image src="@/static/images/dfk.png" />
                <text class="order-status-num"
                  v-if="userInfo.orderStatusNum.unpaidCount > 0">{{ userInfo.orderStatusNum.unpaidCount }}</text>
              </view>
              <view>待付款</view>
            </view>
            <view @click="goMyOrder(1)" class="item">
              <view class="pictrue">
                <image src="@/static/images/dfh.png" />
                <text class="order-status-num"
                  v-if="userInfo.orderStatusNum.unshippedCount > 0">{{ userInfo.orderStatusNum.unshippedCount }}</text>
              </view>
              <view>待发货</view>
            </view>
            <view @click="goMyOrder(2)" class="item">
              <view class="pictrue">
                <image src="@/static/images/dsh.png" />
                <text class="order-status-num"
                  v-if="userInfo.orderStatusNum.receivedCount > 0">{{ userInfo.orderStatusNum.receivedCount }}</text>
              </view>
              <text>待收货</text>
            </view>
            <view @click="goMyOrder(3)" class="item">
              <view class="pictrue">
                <image src="@/static/images/dpj.png" />
                <text class="order-status-num"
                  v-if="userInfo.orderStatusNum.evaluatedCount > 0">{{ userInfo.orderStatusNum.evaluatedCount }}</text>
              </view>
              <text>待评价</text>
            </view>
            <view @click="goReturnList()" class="item">
              <view class="pictrue">
                <image src="@/static/images/sh.png" />
                <text class="order-status-num"
                  v-if="userInfo.orderStatusNum.refundCount > 0">{{ userInfo.orderStatusNum.refundCount }}</text>
              </view>
              <text>售后/退款</text>
            </view>
          </view>
        </view>
        <view class="myService">
          <view class="serviceList acea-row row-middle">
            <template v-for="(item, MyMenusIndex) in MyMenus">
              <view class="item" :key="MyMenusIndex" @click="goPages(MyMenusIndex)">
                <view class="pictrue">
                  <image :src="item.pic" />
                </view>
                <view class="cell">{{ item.name }}</view>
                <text class="iconfont icon-jiantou"></text>
              </view>
            </template>
            <!-- <view class="item" @click="goPages2()">
              <view class="pictrue"></view>
              <view class="cell">hexiao</view>
              <text class="iconfont icon-jiantou"></text>
            </view>-->
          </view>
        </view>
      </view>
      <view class="by">
        <view>
          <text class="by-text">卿悦商城</text>
        </view>
      </view>
      <!-- <SwitchWindow
        v-on:changeswitch="changeswitch"
        :switchActive="switchActive"
        :login_type="userInfo.login_type"
      ></SwitchWindow>-->
    </view>
    <Authorization v-else />
  </view>
</template>
<script>
  import {
    mapState,
    mapGetters,
    mapMutations,
    mapActions
  } from "vuex";
  import {
    getUserInfo,
    getMenuUser,
    bindingPhone
  } from "@/api/user";
  import {
    isWeixin,
    VUE_APP_RESOURCES_URL
  } from "@/utils";
  import cookie from "@/utils/store/cookie";
  import SwitchWindow from "@/components/SwitchWindow";
  import Authorization from "@/pages/authorization/index";

  const NAME = "User";

  export default {
    name: NAME,
    components: {
      SwitchWindow,
      Authorization
    },
    props: {},
    data: function () {
      return {
        MyMenus: [],
        switchActive: false,
        isWeixin: false
      };
    },
    computed: mapGetters(["userInfo"]),
    methods: {
      ...mapMutations(["updateAuthorizationPage"]),
      goReturnList() {
        this.$yrouter.push("/pages/order/ReturnList/index");
      },
      goMyOrder(type) {
        this.$yrouter.push({
          path: "/pages/order/MyOrder/index",
          query: {
            type
          }
        });
      },
      goBindPhone() {
        this.$yrouter.push({
          path: "/pages/user/BindingPhone/index",
        });
      },
      goUserCoupon() {
        this.$yrouter.push("/pages/user/coupon/UserCoupon/index");
      },
      goIntegral() {
        this.$yrouter.push("/pages/user/signIn/Integral/index");
      },
      goUserPromotion() {
        this.$yrouter.push("/pages/user/promotion/UserPromotion/index");
      },
      goUserAccount() {
        this.$yrouter.push({
          path: "/pages/user/UserAccount/index"
        });
      },
      goPersonalData() {
        this.$yrouter.push("/pages/user/PersonalData/index");
      },
      getPhoneNumber: function (e) {
        let thit = this;
        // 判断一下这里是不是小程序 如果是小程序，走获取微信手机号进行绑定
        if (e.mp.detail.errMsg == "getPhoneNumber:ok") {
          uni.showLoading({
            title: "绑定中"
          });
          bindingPhone({
              encryptedData: e.mp.detail.encryptedData,
              iv: e.mp.detail.iv
            })
            .then(res => {
              // this.User();
              thit.$store.dispatch("userInfo", true);
              uni.hideLoading();
              uni.showToast({
                title: res.msg,
                icon: "success",
                duration: 2000
              });
            })
            .catch(error => {
              uni.hideLoading();
              thit.$store.dispatch("userInfo", true);
              console.log(error);
              uni.showToast({
                title: error.msg ||
                  error.response.data.msg ||
                  error.response.data.message,
                icon: "none",
                duration: 2000
              });
            });
          // // 获取当前环境的服务商
          // uni.getProvider({
          //   service: "oauth",
          //   success: function (res) {
          //     // 此处可以排除h5
          //     if (res.provider) {
          //       uni.login({
          //         success: loginRes => {
          //           bindingPhone({
          //               code: loginRes.code,
          //               encryptedData: e.mp.detail.encryptedData,
          //               iv: e.mp.detail.iv
          //             })
          //             .then(res => {
          //               // this.User();
          //               thit.$store.dispatch("userInfo", true);
          //               uni.hideLoading();
          //               uni.showToast({
          //                 title: res.msg,
          //                 icon: "success",
          //                 duration: 2000
          //               });
          //             })
          //             .catch(error => {
          //               uni.hideLoading();
          //               thit.$store.dispatch("userInfo", true);
          //               console.log(error);
          //               uni.showToast({
          //                 title: error.msg ||
          //                   error.response.data.msg ||
          //                   error.response.data.message,
          //                 icon: "none",
          //                 duration: 2000
          //               });
          //             });
          //         },
          //         fail() {
          //           reject("绑定失败");
          //         }
          //       });
          //     }
          //   },
          //   fail() {
          //     reject("获取环境服务商失败");
          //   }
          // });
        } else {
          uni.showToast({
            title: "已拒绝授权",
            icon: "none",
            duration: 2000
          });
        }
      },
      changeswitch: function (data) {
        this.switchActive = data;
      },
      User: function () {
        let that = this;
        getUserInfo().then(res => {
          that.user = res.data;
          that.orderStatusNum = res.data.orderStatusNum;
        });
      },
      MenuUser: function () {
        let that = this;
        getMenuUser()
          .then(res => {
            uni.hideLoading();
            that.MyMenus = res.data.routine_my_menus;
          })
          .catch(error => {
            uni.hideLoading();
            console.log(error);
          });
      },
      goPages: function (index) {
        let url = this.MyMenus[index].uniapp_url;
        if (
          url === "/pages/user/promotion/UserPromotion/index" &&
          this.userInfo.statu === 1
        ) {
          if (!this.userInfo.isPromoter) {
            uni.showToast({
              title: "您还没有推广权限！！",
              icon: "none",
              duration: 2000
            });
            return;
          }
        }

        if (
          url === "/pages/orderAdmin/OrderIndex/index" &&
          !this.userInfo.adminid
        ) {
          uni.showToast({
            title: "您还不是管理员！！",
            icon: "none",
            duration: 2000
          });
          return;
        }
        console.log(this.userInfo)
        if (url === "/pages/orderAdmin/OrderCancellation/index" && !this.userInfo.checkStatus) {
          uni.showToast({
            title: "您没有核销权限,请后台店员设置！！",
            icon: "none",
            duration: 2000
          });
          return;
        }

        this.$yrouter.push({
          path: this.MyMenus[index].uniapp_url
        });
      },
      goPages2: function () {
        this.$yrouter.push({
          path: "/pages/orderAdmin/OrderCancellation/index"
        });
      },
      onStore() {
        if (this.apply) {
          this.$store.commit('setMember', {
            applyLevel: this.level,
            type: 3,
            apply: this.apply,
          })
          this.$yrouter.push('/pages/member/product/list')
        } else {
          this.$yrouter.push('/pages/member/firm/open')
        }
      },
      onPerson() {
        if (this.apply1) {
          this.$store.commit('setMember', {
            applyLevel: this.level1,
            type: 2,
            apply: this.apply1,
          })
          this.$yrouter.push({path: '/pages/member/product/list', query: {type: 2}})
        } else {
          this.$yrouter.push({
			  path: '/pages/member/person/select',
		  })
        }
      }
    },
    watch: {
      userInfo() {
        this.MenuUser();
      }
    },
    onShow() {
      if (this.$store.getters.token) {
        //
        uni.showLoading({
          title: "加载中"
        });
        this.$store.dispatch("getUser", true);
        this.MenuUser();
        this.isWeixin = isWeixin();

        this.$r.get('/store/applyContinue')
          .then(r => {
            if (r.data && r.data.id) {
              this.apply = r.data.id
              this.level = r.data.level
            } else {
              this.apply = null
            }
          })
        
        this.$r.get('/person/applyContinue')
          .then(r => {
            if (r.data && r.data.id) {
              this.apply1 = r.data.id
              this.level1 = r.data.level
            } else {
              this.apply = null
            }
          })
		 
				this.$r.post('/user/levelCodeList')
      }
    },
    onHide() {
      console.log("离开用户中心");
      this.updateAuthorizationPage(false);
    }
  };
</script>

<style lang="less">
  .footer-line-height {
    height: 1 * 100rpx;
  }

  .order-status-num {
    min-width: 0.33 * 100rpx;
    background-color: #fff;
    color: #eb3729;
    border-radius: 15px;
    position: absolute;
    right: -0.14 * 100rpx;
    top: -0.15 * 100rpx;
    font-size: 0.2 * 100rpx;
    padding: 0 0.08 * 100rpx;
    border: 1px solid #eb3729;
  }

  .pictrue {
    position: relative;
  }

  .switch-h5 {
    margin-left: 0.2 * 100rpx;
  }

  .binding {
    margin-top: 0.1 * 100rpx;
    display: inline-block;
    padding: 0.05 * 100rpx 0.2 * 100rpx;
    background-color: #ca1f10;
    border-radius: 50px;
    font-size: 0.22 * 100rpx;
    line-height: 1.5;
    border: 1px solid #e8695e;
    color: #ffffff;
  }

  .by {
    text-align: center;
    padding: 30rpx 0;
  }

  .by-text {
    text-align: center;
    font-size: 24rpx;
  }
</style>
