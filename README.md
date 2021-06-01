# 自定义 Vue Cli 的实现方法

1、创建一个模板项目，根目录必须包含一个 `preset.json` 文件

在 `preset.json` 中配置 cli 待使用的插件（要注意，这些插件都需要先发布到 npm 上才能够使用）

如： `@wangwenzhang/vue-cli-plugin-ui-base`，这里先把插件名配上，后续再发布

2、为方便预览，这里把插件代码都放到 plugins 文件夹中，这里可以自己创建文件夹，也可以在外部用 lerna 去指定创建到 plugins 文件夹中: `npx lerna create <module-name> plugins`

注意文件夹的名字，需要保持 `vue-cli-plugin-` 开头，避免 vue 从 npm 中长时间检索不到该插件

插件名前面已经配置过了，这里也保持一致，文件夹就叫做 `vue-cli-plugin-ui-base`

cli 插件的代码需要放在 `./vue-cli-plugin-ui-base/generator/index.js` 下

index.js 中需要导出一个函数，函数内可以对 vue 项目代码做一些前置处理，如模板代码注入，依赖修改等：

```js
module.exports = (api) => {
  api.render('./template')

  api.injectImports('src/main.js', `import './ui'`)

  api.extendPackage({

    workspaces: [
      'packages/*'
    ],

    husky: {
      hooks: {
        "post-merge": "lerna bootstrap"
      }
    },

    scripts: {
      lib: 'lerna run lib',
      bootstrap: 'lerna bootstrap'
    },

    devDependencies: {
      'webpack-node-externals': '^1.7.2',
      'less-loader': '^4.1.0'
    }
  })
}

```

模板代码暂不赘述，可见仓库源码，核心还是 `generator/index.js`

完了之后就可以把 `vue-cli-plugin-ui-base` 发布到 npm 上了，这里名字起做： `@wangwenzhang/vue-cli-plugin-ui-base`

发布成功后就可以试了，我们找一个空文件夹, 执行如下命令，其中 `<project-name>` 为目标项目的名字 ：

```bash
vue create --preset pdsuwwz/test-vue-cli-plugins <project-name> --packageManager yarn
```



参考官方： [插件开发指南](https://cli.vuejs.org/zh/dev-guide/plugin-dev.html#%E5%BC%80%E5%A7%8B)