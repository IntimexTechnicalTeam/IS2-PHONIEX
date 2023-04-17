<template>
  <div class="insLogin_warrper" :class="{'ENG' : $Storage.get('locale') === 'E'}">
    <div class="insLogin_warrper_w">
      <div style="min-height:131px;">
        <div class="insLogin_title" v-if="!isIe">
          <div class="facebook_login" @click="fbLogin">
            <img src="/static/facebook.png" />
            <span>{{$t('Login.FaceBookUse')}}</span>
          </div>
        </div>
        <div class="insLogin_divide" v-if="!isIe">
            <div class="divide"></div>
            <div class="divide_or">{{$t('Register.or')}}</div>
            <div class="divide"></div>
        </div>
      </div>
      <div class="insLogin_main">
          <div class="login">
              <div>
                <div class="login_title">
                  <!-- <p>{{$t('Login.Already')}}</p> -->
                  <!-- <p>{{$t('Login.Signinnow')}}</p> -->
                  <p>{{$t('Login.Signin')}}</p>
                </div>
                <InsForm ref="loginForm" v-model="loginForm">
                    <InsInput2 :placeholder="$t('Register.UserEmail')" width="100%" v-model="loginForm.email" />
                    <InsInput2 :placeholder="$t('Register.UserRegPassword')" width="100%" v-model="loginForm.password" type="logopassword" />
                    <div class="remember_warpper">
                        <div class="remember">
                            <input
                                type="checkbox"
                                class="remember-btn"
                                name="remember-btn"
                                id="remember-btn"
                                value
                            />
                            <label for="remember-btn">{{$t('Login.RememberMe')}}</label>
                        </div>
                        <a class="forget" href="/account/forgetPassword">{{$t('Login.ForgetPwd')}}</a>
                    </div>
                </InsForm>
              </div>
              <InsButton :nama="$t('Login.Signin')" @click="login"  style="margin-top: 229px;"/>
          </div>
          <div class="register">
              <div>
                <div class="register_title">
                  <!-- <p>{{$t('Login.onlineaccount')}}</p> -->
                  <!-- <p>{{$t('Login.RegNow')}}</p> -->
                  <p>{{$t('Login.RegNow')}}</p>
                  </div>
                <InsForm ref="registerForm" v-model="registerForm">
                <div class="register_half">
                    <InsInput2 :placeholder="$t('Register.UserFirstName')" width="48%" v-model="registerForm.firstName" />
                    <InsInput2 :placeholder="$t('Register.UserLastName')" width="48%" v-model="registerForm.lastName"/>
                    <InsInput2 :placeholder="$t('Register.UserRegPassword')" width="100%" v-model="registerForm.password" type="password"/>
                    <InsInput2 :placeholder="$t('Register.UserConfirmPassword')" width="100%" v-model="registerForm.confirmPassword" type="confirmpassword" :rule="registerForm.password" />
                </div>
                <div class="register_half">
                     <InsInput2 :placeholder="$t('DeliveryAddress.Mobile')" width="100%" :must="false"  v-model="registerForm.Mobile"  type="phone"/>
                </div>
                <InsInput2 :placeholder="$t('Register.UserEmail')" v-model="registerForm.email" width="100%" type="email" />
                </InsForm>
                <!-- <div></div> -->
                <CheckboxGroup v-model="terms" style="margin: 20px 0 0 0">
                    <Checkbox name="type"></Checkbox><span><a href="/CMS/content/20440" target="_blank" style="font-size: 14px;padding-left: 14px;color: #666666;
    text-decoration: none;">{{$t('Register.RegisterAgree')}}</a></span>
                </CheckboxGroup>
                <CheckboxGroup v-model="registerForm.OptOutReceiving" style="margin: 10px 0 20px 0">
                    <Checkbox name="type"></Checkbox><span><a href="javascript:;" style="font-size: 14px;padding-left: 14px;color: #666666;
    text-decoration: none;">{{$t('Register.promotionalinformation')}}</a></span>
                </CheckboxGroup>
              </div>
              <InsButton :nama="$t('Forgetpassword.NextStep')" @click="register" style="margin-top:.4rem;" />
          </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Vue, Prop, Component } from 'vue-property-decorator';
import InsInput2 from '@/components/base/pc/InsInput2.vue';
import InsButton from '@/components/base/pc/InsButton.vue';
import InsForm from '@/components/base/pc/InsForm.vue';
import { CheckboxGroup, Checkbox } from 'element-ui';
@Component({ components: { InsInput2, InsButton, InsForm, CheckboxGroup, Checkbox } })
export default class InsLoginN extends Vue {
    private terms: boolean = true;
    faceBookLogin:string='';
    lang:string[] = ['E', 'C', 'S'];
    private loginForm = {
      email: '',
      password: ''
    }
    private registerForm = {
      email: '',
      password: '',
      firstName: '',
      lastName: '',
      confirmPassword: '',
      Language: '',
      Mobile: '',
      OptOutReceiving: true
    }
    get currentlang () {
      return this.$Storage.get('locale');
    }
    login () {
      let _this = this;
      (this.$refs.loginForm as InsForm).validate((valid) => {
        if (valid) {
          this.$Api.member.login(this.loginForm.email, this.loginForm.password, true).then(
            function (response) {
              _this.$store.dispatch('doLogin');
              return _this.$route.query && _this.$route.query.returnurl ? _this.$route.query.returnurl : undefined;
            },
            function (response) {
              _this.$message({
                message: response.Message,
                type: 'error',
                customClass: 'messageBoxMobile'
              });
            }
          ).then(
            (url) => {
              this.$Api.member.getProfile().then(
                function (data) {
                  if (data) {
                    _this.loginForm = data;
                    _this.$store.dispatch('setUser', (data.FirstName + ' ' + data.LastName).toUpperCase());
                    _this.$i18n.locale = _this.lang[data.Language];
                    _this.$store.dispatch('setLang', _this.lang[data.Language]);
                    _this.$Storage.set('locale', _this.lang[data.Language]);
                    _this.$store.dispatch('setMemberInfo', data);
                    _this.getShopCart();
                    if (url) { window.location.href = (_this.$route.query.returnurl as string); } else { window.location.href = '/account/memberInfo'; }
                  } else {
                    _this.$store.dispatch('Logout');
                  }
                },
                function (data) {
                  _this.$message({
                    message: data,
                    type: 'error',
                    customClass: 'messageBoxMobile'
                  });
                }
              );
            }
          );
        } else {
          return false;
        }
      });
    }
    register () {
      let _this = this;
      let l = this.$Storage.get('locale');
      this.lang.forEach((element, index) => {
        if (l === element) this.registerForm.Language = '' + index;
      });
      (this.$refs.registerForm as InsForm).validate((valid) => {
        if (valid && this.terms) {
          this.$Api.member.register(this.registerForm).then((result) => {
            if (result.Succeeded) {
              this.$Api.member.login(this.registerForm.email, this.registerForm.password, true).then(
                function (response) {
                  _this.$store.dispatch('doLogin');
                  return _this.$route.query && _this.$route.query.returnurl ? _this.$route.query.returnurl : undefined;
                },
                function (response) {
                  _this.$message({
                    message: response.Message,
                    type: 'error',
                    customClass: 'messageBoxMobile'
                  });
                }
              ).then(
                (url) => {
                  this.$Api.member.getProfile().then(
                    function (data) {
                      if (data) {
                      // _this.registerForm = data;
                        _this.$store.dispatch('setUser', (data.FirstName + ' ' + data.LastName).toUpperCase());
                        _this.$i18n.locale = _this.lang[data.Language];
                        _this.$store.dispatch('setLang', _this.lang[data.Language]);
                        _this.$Storage.set('locale', _this.lang[data.Language]);
                        _this.$store.dispatch('setMemberInfo', data);
                        if (url) { window.location.href = (_this.$route.query.returnurl as string); } else { window.location.href = '/account/memberInfo'; }
                      } else {
                        _this.$store.dispatch('Logout');
                      }
                    },
                    function (data) {
                      _this.$message({
                        message: data,
                        type: 'error',
                        customClass: 'messageBoxMobile'
                      });
                    }
                  );
                }
              );
            } else {
              this.$message({
                message: result.Message,
                type: 'error',
                customClass: 'messageBoxMobile'
              });
            }
          });
        } else {
          console.log('error submit!!');
          return false;
        }
      });
    }
    getShopCart () {
      this.$store.dispatch('setShopCart', this.$Api.shoppingCart.getShoppingCart());
    }
    isIe = true;
    created () {
      if (window.navigator.userAgent.indexOf('Trident') !== -1) this.isIe = true;
      else this.isIe = false;
    }
    mounted () {
      window.dispatchEvent(new Event('faceBookLoad'));
    }
    fbLogin () {
      window['FB'].login(function (response) {
        window['checkLoginState']();
      }, { scope: 'email' });
    }
}
</script>
<style lang="less">
.messageBoxMobile{
      z-index: 100000!important;
}
.ENG.insLogin_warrper{
   .insLogin_main .login_title p, .insLogin_main .register_title p{
    font-size: 20px !important;
    font-family: 'Domine-Bold' !important;
   }
   .insLogin_main .input_warpper .input_main input{
    font-family: 'Domine-Regular' !important;
   }
}
</style>
<style lang="less" scoped>
.insLogin_warrper{
    width: 1200px;
    margin: 0 auto;
    margin-top: 38px;
    margin-bottom: 38px;
    padding-bottom: 78px;
    padding-top: 78px;
    background: url('/images/pc/RegisterLoginback.png') no-repeat center center;
    background-size: contain;
    .insLogin_warrper_w{
      width: 100%;
      background-color: #fff;
      padding: 40px;
      box-sizing: border-box;
      box-shadow: 0 0 5px #ebeded;
    }
    .insLogin_title{
        width: 100%;
        margin: 0 auto;
        text-align: center;
        // border: solid 1px rgba(0, 0, 0, .2);
        // padding: 20px;
        box-sizing: border-box;
        .facebook_login{
          display: inline-block;
          background-color: #4267b2;
          border: 1px solid #34518c;
          color: white;
          padding: 10px 85px;
          cursor: pointer;
          border-radius: 3px;
          font-weight: bold;
          user-select: none;
          span{
            vertical-align: middle;
            font-family: 'SourceHanSerifCN-Bold';
            font-size: 18px;
          }
          img{
            height: 32px;
            vertical-align: middle;
            padding-right: 16px;
          }
        }
    }
    .insLogin_divide{
        white-space: nowrap;
        width: 100%;
        text-align: center;
        margin: 36px auto;
        display: inline-block;
        .divide{
            display: inline-block;
            width: 510px;
            margin: 20px 0;
            border-top: solid 1px rgba(0, 0, 0, .2);
            vertical-align: bottom;
        }
        .divide_or{
            vertical-align: bottom;
            display: inline-block;
            line-height: 41px;
            margin: 0 39px;
            font-size: 20px;
            color: #666666;
        }
    }
    .insLogin_main{
        width: 100%;
        box-sizing: border-box;
        margin: 0 auto;
        // padding: 40px;
        // border: solid 1px rgba(0, 0, 0, .2);
        display: flex;
        .login{
          float: left;
          width: 500px;
          margin-right: 60px;
            .remember_warpper{
                // padding: 0 0 0 20px;
                margin: 30px 0 0 0;
                display: flex;
                justify-content: space-between;
                a{
                  color: #b59e72;
                }
                label{
                  color: #999999;
                  margin-left: 6px;
                }
            }
        }
        .register{
            width: 500px;
            float: left;
            padding-left: 60px;
            border-left: solid 1px rgba(0, 0, 0, .2);
            .register_half{
                display: flex;
                flex-wrap: wrap;
                justify-content: space-between;
            }
        }
        .login_title, .register_title{
          p{
            font-size: 22px;
            text-align: left;
            font-family: 'SourceHanSerifCN-Bold';
            // margin-bottom: 10px;
          }
        }
        /deep/ .input_warpper .input_main input{
          background-color: #fff;
          border: 1px solid #e5e5e5;
          border-radius: 3px;
          &:focus{
            border:1px solid #b59e72
          }
        }
        /deep/ .input_outer{
          padding-top: 20px;
        }
        /deep/ .large{
          font-size: 22px;
           font-family: 'SourceHanSerifCN-Bold';
           padding: 14px 0;
           border-radius: 3px;
           line-height: 22px;
        }
    }
}
</style>
