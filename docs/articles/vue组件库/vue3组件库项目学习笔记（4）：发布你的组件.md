# vue3组件库项目学习笔记（四）：发布你的组件

在完成我们的组件之后，我们肯定希望可以将它发布到 npm 等地，这样任何人都可以使用它了，这里我们使用 `gulp` 这个工具来自动化打包和发布我们的组件库

## 引入gulp

我们还是首先导入需要的依赖

```shell
pnpm i gulp @types/gulp sucrase -D -w
```

之后我们在根目录的 package.json 中添加打包组件库命令

```json
 "scripts": {
    "build": "gulp -f packages/components/script/build.ts"
  }
```

然后我们创建 packages/components/script/build.ts  这个文件并且编写一些简单的测试

```typescript
export default () => {
    console.log('test')
    return Promise.resolve()
}
```

我们运行 pnpm run build 命令，如果输出 test 说明我们引入工具成功

## 删除上一次的内容

发布内容我们要做的第一件事情就是删除上一次打包的结果，众所周知，vite打包生成的是 dist 文件夹，所以我们要做的就是删除上一次打包产生的痕迹，我们开始编写一个脚本来完成这个操作：

首先我们编写一个方法来拿到我们的打包的根目录，也就是 components 文件夹，将他放在 script/utils.ts 文件中，作为我们的公共方法，因为其他模块也需要这个方法：

```typescript
import { resolve } from 'path'

export const componentPath = resolve(__dirname, '../')
```

之后我们在 script/build.ts 中编写我们的脚本，这个脚本很简单，我们编写了一个 run 函数，它的作用就是在命令行运行我们提供的脚本，脚本内容是 `rimraf ${componentPath}/dist` 就是删除根目录下的dist文件夹，注意如果你在 linux环境下开发，你可以使用 `rm -rf` 命令，在window下开发的话，我们使用了 node 中一个模块来帮助我们完成这个操作，当然这个模块需要我们安装 `pnpm i rimraf -w`

```typescript
import { spawn } from 'child_process'
import { series } from 'gulp'
import { componentPath } from './utils'

const run = async (command: string) => {
    //获得我们的指令
    const [cmd, ...args] = command.split(' ')
    return new Promise((resolve, reject) => {
        const app = spawn(cmd, args, {
            cwd: componentPath,//执行命令的路径
            stdio: 'inherit', //输出共享给父进程
            shell: true
        })
        //执行完毕关闭并resolve
        app.on('close', resolve)
    })
}
//运行指令来删除我们的dist文件夹
export default series(async () => run(`rimraf ${componentPath}/dist`))
```

现在我们在 components文件夹创建一个 dist 文件夹，然后根目录运行脚本，如果文件夹被删除说明我们的脚本成功了

## 打包组件库

我们对于组件库的打包分为两个部分，首先是打包我们的样式文件，之后我打包组件库本身

- 打包样式

首先我们下载处理 less 的打包依赖

```shell
pnpm i gulp-less @types/gulp-less gulp-autoprefixer @types/gulp-autoprefixer -D -w
```

之后我们在 打包脚本中编写样式构建的相关内容：我们编写一个函数来处理样式，先定位到样式所在的位置，然后将样式打包处理后输出到指定位置，我们运行脚本，人工看到在components 文件夹下出现了 dist 目录并且里面含有样式文件，说明我们的打包成功了：

```typescript
import { spawn } from 'child_process'
import { series, src, dest, task } from 'gulp'
import { componentPath } from './utils'
import less from "gulp-less"
import autoprefixer from 'gulp-autoprefixer'

/**   **/
//处理样式
const buildStyle = () => {
    return src(`${componentPath}/src/**/style/**.less`)
        .pipe(less())
        .pipe(
            autoprefixer()
        )
        .pipe(dest(`${componentPath}/dist/lib/src`))
        .pipe(dest(`${componentPath}/dist/es/src`));
};

export default series(async () => run(`rimraf ${componentPath}/dist`),async () => buildStyle())
```

- 打包组件

众所周知，打包组件的配置在 vite 中配置的，所以我们需要配置我们的 vite.config.ts 文件，其作用是将我们的源代码打包成发布需要的文件：

```json
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue"
import dts from 'vite-plugin-dts'
import { resolve } from 'path'
export default defineConfig(
    {
        build: {
            target: 'modules',
            //打包文件目录
            outDir: "es",
            emptyOutDir:false,
            //压缩
            minify: true,
            //css分离
            //cssCodeSplit: true,
            rollupOptions: {
                //忽略打包vue文件
                external: ['vue', /\.less/,'@ls-ui/utils'],
                input: ['src/index.ts'],
                output: [
                    {
                        format: 'es',
                        //不用打包成.es.js,这里我们想把它打包成.js
                        entryFileNames: '[name].js',
                        //让打包目录和我们目录对应
                        preserveModules: true,
                        //配置打包根目录
                        dir: resolve(__dirname, './dist/es'),
                        preserveModulesRoot: 'dist'
                    },
                    {
                        format: 'cjs',
                        //不用打包成.mjs
                        entryFileNames: '[name].js',
                        //让打包目录和我们目录对应
                        preserveModules: true,
                        //配置打包根目录
                        dir: resolve(__dirname, './dist/lib'),
                        preserveModulesRoot: 'src'
                    }
                ]
            },
            lib: {
                entry: './index.ts',
                formats: ['es', 'cjs']
            }
        },
        plugins: [
            vue(),
            dts({
                outputDir: resolve(__dirname, './dist/es/src'),
                //指定使用的tsconfig.json为我们整个项目根目录下掉,如果不配置,你也可以在components下新建tsconfig.json
                tsConfigFilePath: '../../tsconfig.json'
            }),
            //因为这个插件默认打包到es下，我们想让lib目录下也生成声明文件需要再配置一个
            dts({
                outputDir: resolve(__dirname, './dist/lib/src'),
                tsConfigFilePath: '../../tsconfig.json'
            }),

            {
                name: 'style',
                generateBundle(config, bundle) {
                    //这里可以获取打包后的文件目录以及代码code
                    const keys = Object.keys(bundle)

                    for (const key of keys) {
                        const bundler: any = bundle[key as any]
                        //rollup内置方法,将所有输出文件code中的.less换成.css,因为我们当时没有打包less文件
                        this.emitFile({
                            type: 'asset',
                            fileName: key,//文件名名不变
                            source: bundler.code.replace(/\.less/g, '.css')
                        })
                    }
                }
            }

        ],
        resolve: {
            alias: {
                '@': resolve(__dirname, 'src'),
            },
        }
    }
)
```

之后我们修改我们的 package.json 文件，添加 build 方法，并且完善一些细节

```json
{
  "name": "ls-ui",
  "version": "1.0.1",
  "main": "index.ts",
  "scripts": {
    "build": "vite build"
  },
  "files": [
    "es",
    "lib"
  ],
  "keywords": [
    "ls-ui",
  ],
  "author": "zs",
  "license": "MIT",
  "description": "",
  "typings": "lib/index.d.ts",
  "dependencies": {
    "@ls-ui/utils": "workspace:^1.0.1"
  }
}
```

在有了打包命令以后，我们在脚本文件里添加一个方法来打包我们的组件，这个方法就是执行刚刚添加的 build 命令

```typescript
const buildComponent = async () => {
  await run(`cd ${componentPath}`);
  run("pnpm run build");
};

export default series(
  async () => run(`rimraf ${componentPath}/dist`),
  async () => buildStyle(),
  async () => buildComponent()
);
```

之后我们只要在根目录运行打包命令就可以完成我们组件的打包，如果成功打包可以看到出现了 dist 文件，其中有 es 和 lib 两个文件夹

## 发布组件

在有了一个完成打包的文件夹后之后，我们希望将我们打包完成的组件库发布到 npm ，我们添加一个脚本来完成我们的发布工作，取名为  publish.ts 放在与 build.ts 同一个目录下，这个脚本的作用是，为我们的组件库添加一个 package.json 文件，然后使用 npm 的发布功能发布我们的组件：

```json
import { spawn } from 'child_process'
import { series, src, dest } from 'gulp'
import { componentPath } from './utils'
const run = async (command: string, path: string) => {
    const [cmd, ...args] = command.split(' ')
    return new Promise((resolve, reject) => {
        const app = spawn(cmd, args, {
            cwd: path,//执行命令的路径
            stdio: 'inherit', //输出共享给父进程
            shell: true //mac不需要开启，windows下git base需要开启支持
        })
        app.on('close', resolve)
    })
}
//复制
const copypackage = async () => {
    return src(`${componentPath}/transitpkg/**`).pipe(dest(`${componentPath}/dist/`));
};
//发布任务
const publish = async () => {
    await run('pnpm version patch', `${componentPath}/transitpkg`)
    //复制到dist目录
    await copypackage()
    //在dist下执行发布命令
    await run('npm publish', `${componentPath}/dist`)
}
export default series(
    async () => publish()
)
```

为了实现这个脚本，我们现在根目录添加运行我们这个脚本命令

```json
"scripts": {
    "publish": "gulp -f packages/components/script/publish.ts"
  },
```

之后我们在 components 文件夹下新建一个  transitpkg  文件夹，在其中添加一个 package.json 文件：

```json
{
    "name": "ls-ui",
    "version": "1.0.1",
    "main": "lib/index.js",
    "module": "es/index.js",
    "files": [
        "es",
        "lib"
    ],
    "keywords": [
        "ls-ui",
    ],
    "author": "zs",
    "license": "MIT",
    "description": "",
    "typings": "lib/index.d.ts"
}
```

我们的脚本就是把这个文件复制到dist内，之后将这个包含配置信息的包发布到 npm 官方，在发布之前，我们需要先登录自己的账号，我们需要先登录官方账号注册一个账号（https://www.npmjs.com/），之后在命令行登录：

```shell
npm login
```

在登录成功后运行我们的发布脚本就可以发布我们的组件了

```shell
pnpm run publish
```

