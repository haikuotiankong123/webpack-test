# webpack-test
webapck 如何安装
网上关于webpack的教程不少，但是大多已经过时，由于webapck版本更新后许多操作变化很大，很多教程已经的教程已经老版本的。当我们使用npm安装webpack时，若不指定webpack的版本（很多小白都没有指定版本），将默认安装最新版。笔者测试时默认安装的是4.1.1.并不能照搬老教程的方法，。使用的是windows操作系统，如果你的是webpack4.x版本，可参考进行配置。


# 一、全局安装webpack
如果我们安装旧版本的安装方式，直接使用npm全局安装webpack，我们预期全局安装webpack后，便能在命令行中使用webpack指令。我们在命令行输入：
```
npm install -g webpack
```
当执行该操作后，便在`c:Users\你的用户名\AppData\Roaming\npm\node_modules`创建了webpack文件夹，里面储存了刚刚全局安装的webpack模块。


# 二、创建项目
我们在适合的位置新建一个文件夹webpack-test,用于存放我们的项目。
命令行中定位到webpack-test文件夹下(按住shift+右键:选 在此处打开命令窗口)，输入以下命令进行项目初始化：
```
npm init
```
这里要求设置很多选项，可以按项目情况配置也可以不填直接回车。完成后，我们发现文件夹中增加了`package.json`文件，它用于保存关于项目的信息。


# 三、尝试打包出现提示
我们在项目新建一个文件`hello.js`。并在其中输入代码：
```
alert('hello world!')
```
然后，我们便可以满怀期待地尝试打包，在命令行输入：
```
webpack hello.js bundle.js
```
意思是将hello.js打包成另一个bundle.js。但很不幸。4.x版本提示：
```
The CLI moved into a separate package: webpack-cli
Would you like to install webpack-cli? (That will run npm install -D webpack-cli) (yes/NO)
```
翻译成中文
>CLI（命令行工具）已经转移到了一个单独的包webpack-cli中。
-> 你想安装webpack-cli吗？：去执行npm install -D webpack-cli
意思是，我们需要额外安装webpack-cli，否则便不能在命令行中使用webpack的相关命令。


# 四、安装webpack-cli
我们在项目中本地安装webpack-cli
```
npm install -D webpack-cli
```
这里的-D参数和--save-dev的作用相同。只是一种简写而已，笔者这里安装完成后，显示webpack-cli版本2.0.10.
我们在根目录再次输入：
```
webpack hello.js bundle.js
```
很不幸，还是提示：
```
The CLI moved into a separate package: webpack-cli
Would you like to install webpack-cli? (That will run npm install -D webpack-cli) (yes/NO)
```
这表明我们本地安装webpack-cli后并没有起作用，在命令行中依然不能使用webpack命令。那么是什么地方出了问题呢？
我们不难想到
>旧版本的webpack中，webpack指令要能在命令行中使用，需要全局安装webpack，而不是本地安装，因此这里的webpack-cli也应该是同理。
我们卸载本地安装的webpack-cli ,全局安装webpack-cli：
```
npm uninstall webpack-cli
npm install -g webpack-cli
```


# 五、设置模式
我们再次尝试打包：
```
webpack hello.js bundle.js
```
看样子似乎是可以运行了，但又出现了新的提示：
```
WARNING in configuration
The 'mode' option has not been set. Set  'mode' option to 'development' or 'production' to enable defaults for this enviroment.
ERROR in multi ./hello.js bundle.js
Module not found:ERROR:Can't resolve 'bundle.js' in 'C:/Users/你的用户名/Desktop/webpack-test'
@ multi ./hello.js bundle.js
```
翻译成中文：
>配置警告：
>“mode”选项尚未设置。将“mode”选项设为“development”或“production”以启用此环境的默认设置。
>multi错误 ./ hello.js bundle.js
>未发现模块：错误：无法解析’C:/Users/你的用户名/Desktop/webpack-test’中的bundle.js
>@ multi ./hello.js bundle.js
这里提示我们存在的第一个问题是没有配置webpack的node选项，默认有production和development两种模式可以设置，因此我们尝试为development模式，在命令行输入：
```
webpack --mode development
```
我们看到进行了打包并了Hash、Version、Time、Build at信息，表明已经可以打包。不过，乃然有错误提示：
```
ERROR in Entry module not found:ERROR:Can't resolve './src' in 'C:/Users/你的用户名/Desktop/webpack-test'
```
翻译成中文：
>未找到入口模块发生错误：错误：无法解析’C:/Users/你的用户名/Desktop/webpack-test’中的’./src’


# 六、创建入口文件
这表明webpack4.x是以项目根目录下的'./src'作为入口，但我们的项目中缺乏该路径，因此我们在根目录下创建src文件夹，事实上webpack4.x以'./src/index.js'作为入口，单单创建src文件而没有index.js文件仍然会报错，因此我们
>将hello.js移动到'./src'，并重命名为index.js。
现在如果我们再次执行
```
webpack index.js bundle.js
```
会提示can.t resolve相关的错误。
原因是，webpack4.x的打包已经不能用`webpack 文件a 文件b`的方式，而是直接运行`webpack --mode development`或者`webpack --mode production，`这样便会默认进行打包，入口文件是`'./src/index.js'`，输出路径是`'./dist/main.js'`，其中src目录即index.js文件需要手动创建，而dist目录及main.js会自动生成。
因此我们不再按`webpack 文件a 文件b`的方式运行webpack指令，而是直接运行
```
webpack --mode development
```
或者
```
webpack --mode production
```
这样便能够实现将`'./src/index.js'`打包成`'./dist/main.js'`。
不过每次都要输入这个命令，非常麻烦，我们在`package.json`中`scripts`中加入两个成员：
```
"dev":"webpack --mode development",
 "build":"webpack --mode production"
```
以后我们只需要在命令行执行`npm run dev`便相当于执行`webpack --mode development`，执行`npm run build`便相当于执行`webpack --mode production`。
我们在根目录执行：
```
npm run dev
```
可以看到根目录下生成了dist目录，并且dist目录下生成了main.js文件，main.js文件已经打包了src目录下index.js文件的代码。


# 七、配置其他参数
我们如果需要配置webpack指令的其他参数，只需要在`webpack –mode production/development`后加上其他参数即可，如：
```
webpack --mode development --watch --progress --display-modules --colors --display-reasons
```
当然，这也可以写`入package.json的scripts`之中。


# 八、总结
我们可以将以上探索进行整理总结，首先是注意事项：
> 1、webpack-cli必须要全局安装，否则不能使用webpack指令；
> 2、webpack也必须要全局安装，否则也不能使用webpack指令。
> 3、webpack4.x中webpack.config.js这样的配置文件不是必须的。
> 4、默认入口文件是./src/index.js，默认输出文件./dist/main.js。
配置步骤：
> 1、创建工程目录；
> 2、初始化工程目录：npm init。
> 3、全局安装webpack-cli。
> 4、全局安装webpack。
> 5、webpack –mode development/production进行打包，可在package.json中配置dev和build的脚本，便只需运行npm run dev/build，作用相同。
>6、在webpack –mode development/production可串联设置其他参数。



# 最后webapck官方提示
>>>不推荐全局安装 webpack。这会将你项目中的 webpack 锁定到指定版本，并且在使用不同的 webpack 版本的项目中，可能会导致构建失败。
> 可以直接使用本地的`webapck` 进行操作 例如： `node_modules/.bin/webpack-cli ./src/index.js ./dist/main.js` 


# 注意一下路径的问题
> 用本地cdm命令行时路径是斜杆“/”,用git工具时路径是反斜杆“\”.我们可以看自身的路径的写法就可以了

![image](https://raw.githubusercontent.com/haikuotiankong123/webpack-test/master/lujing.png)

# 本文转自
[简书] https://www.jianshu.com/p/9cf3190307d9



















































































