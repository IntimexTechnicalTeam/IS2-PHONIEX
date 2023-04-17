<template>
  <div id="container" class="MembercenterMobile" :class="{'ENG' : $Storage.get('locale') === 'E'}">
    <div class="MemberInfoBg">
      <div class="MemberInfoMain">
          <!-- 用户账号 -->
            <Form
              :model="ruleForm"
              :rules="rules"
              ref="ruleForm"
              class="demo-ruleForm"
            >
              <FormItem :label="$t('Register.UserEmail')" prop="Email">
                <ElInput
                  v-model="ruleForm.Email"
                  :placeholder="$t('Register.UserEmail')"
                  disabled
                ></ElInput>
              </FormItem>
              <FormItem :label="$t('Register.UserFirstName')" prop="FirstName">
                <ElInput
                  v-model="ruleForm.FirstName"
                  :placeholder="$t('Register.UserFirstName')"
                  clearable
                ></ElInput>
              </FormItem>
              <FormItem :label="$t('Register.UserLastName')" prop="LastName">
                <ElInput
                  v-model="ruleForm.LastName"
                  :placeholder="$t('Register.UserLastName')"
                  clearable
                ></ElInput>
              </FormItem>
              <FormItem prop="Mobile" :label="$t('DeliveryAddress.Mobile')">
                <ElInput
                  v-model="ruleForm.Mobile"
                  :placeholder="$t('DeliveryAddress.Mobile')"
                  clearable
                ></ElInput>
              </FormItem>
                <FormItem prop="BirthDateString" :label="$t('Register.UserBirthDate')">
                  <ElInput
                  v-model="ruleForm.BirthDateString"
                  :placeholder="$t('MemberInfo.DDMM')"
                  clearable
                ></ElInput>
              </FormItem>
              <FormItem :label="$t('Register.UserGender')" prop="Gender">
                <RadioGroup v-model="ruleForm.Gender">
                  <Radio label="0">{{$t('Register.UserMale')}}</Radio>
                  <Radio label="1">{{$t('Register.UserFemale')}}</Radio>
                </RadioGroup>
              </FormItem>
              <FormItem :label="$t('Register.UserLanguage')" prop="Language">
                <Select
                  v-model="ruleForm.Language"
                  :placeholder="$t('Register.UserLanguage')"
                >
                  <Option :value="1" :label="$t('Register.UserLanguageT')">{{$t('Register.UserLanguageT')}}</Option>
                  <Option :value="0" :label="$t('Register.UserLanguageE')">{{$t('Register.UserLanguageE')}}</Option>
                  <!-- <Option :value="2" :label="$t('Register.UserLanguageS')">{{$t('Register.UserLanguageS')}}</Option> -->
                </Select>
              </FormItem>
              <FormItem style="margin-top:22px;">
                <ElButton
                  type="primary"
                  @click="SubmitForm('ruleForm')"
                >{{$t('DeliveryAddress.SaveBtn')}}</ElButton>
              </FormItem>
            </Form>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
import { Form, Input, Message, FormItem, Button, Option, Select, Radio, RadioGroup } from 'element-ui';
import Cookie from 'js-cookie';
import sdk from '@/sdk/InstoreSdk';
import lang from '@/lang';
import storage from '@/sdk/common/Storage';
@Component({
  components: {
    accountHeader: () => import('@/components/hkTasteBusiness/mobile/account/accountHeader.vue'),
    Form,
    Input,
    FormItem,
    Button,
    Option,
    Select,
    Radio,
    RadioGroup
  }
})
export default class InsMemberInfo extends Vue {
  activeName: string = 'first';
  // 验证规则
  ruleForm: any = {
    FirstName: '',
    LastName: '',
    ContactNumber: '',
    BirthMD: '',
    Gender: '',
    Mobile: '',
    Language: ''
  };
  lang:string[] = ['E', 'C', 'S'];
  validatePass (rule, value, callback) {
    if (value === '') {
      callback(new Error(this.$t('MemberInfo.EnterUserName') as string));
    } else {
      callback();
    }
  }
  get rules () {
    return {
      FirstName: [
        {
          required: true,
          message: this.$t('MemberInfo.EnterUserName'),
          trigger: 'blur'
        }
      ],
      LastName: [
        {
          required: true,
          message: this.$t('MemberInfo.EnterUserLastName'),
          trigger: 'blur'
        }
      ],
      Mobile: [
        {
          required: true,
          message: this.$t('MemberInfo.EnterUserPhone'),
          trigger: 'blur'
        },
        { validator: function(rule, value, callback) {
          /* eslint-disable */
            var mobile = /^(\+)?(\d{0,4}\-?)?\d{7,11}$/;
            if ( mobile.test(value) === false ) {
              var t = lang.messages[storage.get('locale')].Input['phoneincorrect'];
                callback(new Error(t));
            } else {
                callback();
            }
        },
        trigger: 'blur' }
      ]
    };
  }
  changLange (lang) {
    this.$Api.member.setUILanguage(lang).then((result) => {
      this.$i18n.locale = lang;
      localStorage.setItem('locale', lang);
    }).catch((error) => {
      console.log(error);
    });
  }
  async getProfile () {
    let _this = this;
    await sdk.api.member.getProfile().then(
      function (data) {
        if (data) {
          _this.ruleForm = data;
          _this.$store.dispatch('setUser', (data.FirstName + ' ' + data.LastName).toUpperCase());
          _this.$i18n.locale = _this.lang[data.Language];
          if (data.BirthDateString !== '') {
            var newDate = new Date(data.BirthDateString.replace(/-/g, '/'));
            data.BirthDateString = newDate.getDate() + '/' + (newDate.getMonth() + 1);
          }
          _this.$store.dispatch('setLang', data.Language === 0 ? 'E' : 'C');
          _this.$Storage.set('locale', data.Language === 0 ? 'E' : 'C');
        } else {
          _this.$store.dispatch('Logout');
        }
      },
      function (data) {
        _this.$message({
          message: data,
          type: 'error'
        });
      }
    );
  }
  mounted () {
    this.getProfile();
  }

  // 提交用户信息表单
  SubmitForm (ruleForm) {
    let that = this;
    that.ruleForm.DateFormat = 'dd/MM';
    if (that.ruleForm.Gender === '') {
      that.ruleForm.Gender = 1;
    }
    // that.$nextTick(function () {
    (that.$refs.ruleForm as Form).validate(valid => {
      if (valid) {
        sdk.api.member.updateProfile(that.ruleForm).then(
          async function (response) {
            that.$message({
              message: response.Message,
              type: 'success',
              customClass: 'messageBoxMobile'
            });
            await that.getProfile();
            that.Reload();
          },
          function (response) {
            that.$message({
              message: response.Message,
              type: 'error',
              customClass: 'messageBoxMobile'
            });
          }
        );
      } else {
        console.log('error submit!!');
        return false;
      }
    });
  }
  // 重置用户信息表单
  ResetForm (ruleForm) {
    (this.$refs.ruleForm as Form).resetFields();
  }

  // 打开地址
  openlink (link) {
    this.$router.push({ path: link });
  }
}
</script>
<style lang="less">
.messageBoxMobile{
      z-index: 100000!important;
}
.MembercenterMobile .el-card__body{
  background: #FFF;

}
#container .el-form-item__content{
  text-align: left!important;
  line-height: normal;
}
.MembercenterMobile .MemberInfoBg{
    width:100%;
    background:#fff;
    background-size: 100%;
    display: inline-block;
    box-sizing: border-box;
}
.MembercenterMobile .MemberInfoMain .el-button--primary span{
  font-size: 18px!important;
}
.MembercenterMobile .MemberInfoMain .el-form-item__label{
    width: auto;
    float: none;
    color: #999999 !important;
    padding: 0px;
    // padding-right: 10px;
    line-height: inherit;
    // height: 40px;
    display: flex;
    align-items: center;
    justify-content: left;
    text-align: center;
    margin-bottom: 1rem;
    font-size: 1.2rem;
}
.MembercenterMobile .MemberInfoMain .el-input{
  width: 100%!important;
}
.MembercenterMobile .MemberInfoMain .el-checkbox__label{
    display: inline-block;
    padding-left: 10px;
    line-height: 19px;
    font-size: 14px;
    width: 91%;
    word-break: break-all;
    vertical-align: text-top;
}
.MembercenterMobile .MemberInfoMain .el-checkbox, .el-checkbox__input{
  white-space: unset;
}
.MembercenterMobile .MemberInfoMain {
    width: 95%;
    margin: 0 auto;
    margin-top: 2rem;
    margin-bottom: 2rem;
}
.MembercenterMobile .MemberInfoMain .el-tabs__nav {
  height: 50px;
  line-height: 50px;
}
.MembercenterMobile .MemberInfoMain .el-tabs__content {
  text-align: left;
}
.MembercenterMobile .MemberInfoMain .el-input__icon {
  font-size: 16px;
}
.MembercenterMobile .MemberInfoMain .el-form-item__content,.MemberInfoMain .el-form-item__label-wrap{
  margin-left: 0px!important;
}
.MembercenterMobile .MemberInfoMain .el-button{
    width: 100%;
    display: block;
    background: #b59e72;
    background-size: 100%;
    /* height: 4rem; */
    border: 0px!important;
    padding: 0.8rem 1rem;
}
.MembercenterMobile .MemberInfoMain .el-input.is-disabled .el-input__inner{
      border: 1px solid #e5e5e5!important;
      background-color: transparent!important;
      color:#333333!important;
      border: none;
      border-radius: 3px;
}
.MembercenterMobile .MemberInfoMain  .el-input__inner{
      border: 1px solid #e5e5e5!important;
      background-color: transparent!important;
      color:#333333!important;
      border: none;
      border-radius: 3px;
}
.MembercenterMobile .el-form .el-radio__input .el-radio__inner{
    border: 1px solid #b59e72;
    width: 16px;
    height: 16px;
}
.MembercenterMobile .el-form .el-radio__input.is-checked+.el-radio__label{
  color: #b59e72;
}
.MembercenterMobile .el-form .el-radio__input.is-checked .el-radio__inner{
    border-color: #b59e72;
    background: #fff;
    width: 16px;
    height: 16px;
}
.MembercenterMobile .el-form .el-radio__input.is-checked .el-radio__inner::after{
  background-color: #b59e72;
  width: 8px;
  height: 8px;
}
// .MembercenterMobile .MemberInfoMain .el-radio__input.is-checked .el-radio__inner{
//     border-color: #333333;
//     background: #333333;
// }
// .MembercenterMobile .MemberInfoMain .el-radio__input.is-checked+.el-radio__label{
//   color: #606266;
// }
.MembercenterMobile .MemberInfoMain .el-select{
  width: 100%!important;
}
.MembercenterMobile .MemberInfoMain .el-select >.el-input{
  width: 100%!important;
  border: 1px solid #e5e5e5;
  border-radius: 3px;
}
.MembercenterMobile .MemberInfoMain .el-select >.el-input .el-input__inner{
  border: none !important;
}
.MembercenterMobile .MemberInfoMain .el-select .el-input__suffix{
  background: transparent;
  color:#fff;
  right: 0px!important;
}
.MembercenterMobile .MemberInfoMain .el-select .el-input .el-select__caret{
  color:#b59e72!important;
  font-weight: bold;
}
.MembercenterMobile .MemberInfoMain input::-webkit-input-placeholder {
        color:#999!important;
}
.MembercenterMobile .el-form-item__error{
      left: 0;
}
.ENG.MembercenterMobile{
   .MemberInfoMain  .el-input__inner{
    font-family: 'Domine-Regular';
   }
}
</style>
<style lang="less" scoped>
.MembercenterMobile .MemberInfoMain {
    /deep/ .el-form-item.is-required:not(.is-no-asterisk) .el-form-item__label-wrap>.el-form-item__label:before, /deep/.el-form-item.is-required:not(.is-no-asterisk)>.el-form-item__label:before{
      color: #b59e72;
    }
}

</style>
