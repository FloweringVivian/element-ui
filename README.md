# element-ui
在vue中使用element-ui过程中遇到的问题总结

## 将element-ui的默认语言设置为英文

1. main.js

```javascript
import Vue from 'vue'
import ElementUI from 'element-ui'
import locale from 'element-ui/lib/locale/lang/en'

Vue.use(ElementUI, { locale })
```

2. webpack.dev.conf.js 和 webpack.prod.conf.js 在plugins增加

```javascript
plugins: [
  new webpack.NormalModuleReplacementPlugin(/element-ui[\/\\]lib[\/\\]locale[\/\\]lang[\/\\]zh-CN/, 'element-ui/lib/locale/lang/en')
]
```

## vue + element-ui实现表单校验时，根据条件不同设置必填校验的方法实现

例如：登录页面密码输错3次以后显示图片验证码，并对图片验证码进行非空校验

```javascript
<template>
...
    <el-form-item prop="captcha" v-show="isShowCaptcha" 
        :rules="this.isShowCaptcha == true?loginRule.captcha:[{required: false, message: '请输入图片验证码', trigger: 'blur'}]">
    </el-form-item>
...
</template>

<script>
export default {
    return {
        loginRule: {
            captcha: [{required: true, message: '请输入图片验证码', trigger: 'blur'}]
        }
    }
}
</script>
```

## element-ui自定义表单规则后，红色必填星号不显示

在自定义的一项那里加上required星号就显示出来了

```javascript
<el-form-item label="公司名称" prop="name" required>
  <el-input v-model="form.name"></el-input>
</el-form-item>
```

## element-ui表单校验某几项

有时候我们需要只校验表单的某一项，比如获取手机验证码，要校验手机号必填，此时可以用validateField

```javascript
//phone就是定义的prop字段名称
this.$refs.registerForm.validateField('phone', (errMsg) => {
    if (!errMsg) {
        console.log('手机号校验通过);
        //调接口
    } else {
        console.log('手机号校验不通过);
    };
})
```

如果校验2项或以上，比如获取手机验证码，要校验手机号和图片验证码，此时prop可以传数组

```javascript
this.$refs.registerForm.validateField(['phone', 'captcha'], (errMsg) => {
    if (!errMsg) {
        console.log('手机号、图片验证码校验通过);
        //调接口
    } else {
        console.log('手机号、图片验证码校验不通过);
    };
})
```

这样写之后发现一个问题，第一次点击“获取手机验证码”后正常，手机号和图片验证码出现必填提示

但是手机号填了之后，图片验证码没填的情况下，点击“获取手机验证码”就走到调接口这步了，说明校验通过了

所以，下面这个写法才是最终正确的答案。

```javascript
if (this.registerForm.phone == '' || this.registerForm.captcha == '') {
    this.$refs['registerForm'].validateField('phone');
    this.$refs['registerForm'].validateField('captcha');
} else {
    console.log('手机号、图片验证码校验通过);
    //调接口
})
```

## element-ui 遮罩层el-dialog 中间内容被被蒙板遮住

解决：在 el-dialog 标签里添加 :append-to-body="true"

```javascript
<el-dialog title="添加用户" :visible.sync="editVisible" width="30%" :append-to-body="true">
...
</el-dialog>
```

## 使用国际化i18n

