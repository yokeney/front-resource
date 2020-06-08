# 浅入浅出webpack

### 一、核心概念

Webpack具有四个核心的概念，想要入门Webpack就得先好好了解这四个核心概念。它们分别是`Entry（入口）`、`Output（输出）`、`loader`和`Plugins（插件）`。接下来详细介绍这四个核心概念 

### 1.Entry

Entry是Webpack的入口起点指示，它指示webpack应该从哪个模块开始着手，来作为其构建内部依赖图的开始。可以在配置文件（webpack.config.js）中配置entry属性来指定一个或多个入口点，**默认为./src**（webpack 4开始引入默认值）。

```
entry: string | Array<string>

```

前者一个单独的string是配置单独的入口文件，配置为后者（一个数组）时，是多文件入口。

另外还可以**通过对象语法进行配置**：

```javascript
entry: {
    [entryChunkName]: string | Array<string>
}
```



 

 比如： 

```javascript
//webpack.config.js
module.exports = {
    entry: {
        app: './app.js',
        vendors: './vendors.js'
    }
};
```



 以上配置表示从app和vendors属性开始打包构建依赖树，这样做的好处在于分离自己开发的业务逻辑代码和第三方库的源码，因为第三方库安装后，源码基本就不再变化，这样分开打包有利于提升打包速度，减少了打包文件的个数，`Vue-Cli`采取的就是这种分开打包的模式。但是为了支持拆分代码更好的DllPlugin插件，以上语法可能会被抛弃。

### 2.Output

Output属性告诉webpack在哪里输出它所创建的bundles，也可指定bundles的名称，**默认位置为./dist**。整个应用结构都会被编译到指定的输出文件夹中去，最基本的属性包括`filename`（文件名）和`path`（输出路径）。 

值得注意的是，即是你配置了多个入口文件，你也只能有一个输出点。 

```javascript
output: {
    filename: 'bundle.js',
    path: '/home/proj/public/dist'
}

```



 **多个入口时，使用下面的语法输出多个bundle：** 

```javascript
// webpack.config.js
module.exports = {
    entry: {
        app: './src/app.js',
        vendors: './src/vendors.js'
    },
    output: {
        filename: '[name].js',
        path: __dirname + '/dist'
    }
}
```

### 3.Loaders

loader可以理解为webpack的编译器，它使得webpack可以处理一些非JavaScript文件，比如png、csv、xml、css、json等各种类型的文件，使用合适的loader可以让JavaScript的import导入非JavaScript模块。JavaScript只认为JavaScript文件是模块，而webpack的设计思想即万物皆模块，为了使得webpack能够认识其他“模块”，所以需要loader这个“编译器”

webpack中配置loader有两个目标：

- （1）test属性：标志有哪些后缀的文件应该被处理，是一个正则表达式。
- （2）use属性：指定test类型的文件应该使用哪个loader进行预处理。



比如webpack.config.js: 

```javascript
module.exports = {
    entry: '...',
    output: '...',
    module: {
        rules: [
            {
                test: /\.css$/,
                use: 'css-loader'
            }
        ]
    }
};
```

**配置多个loader**： 

```javascript
{
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    {
                        loader: 'style-loader'
                    },
                    {
                        loader: 'css-loader'
                    }
                ]
            }
        ]
    }
}
```

### 4.Plugins

loader用于转换非JavaScript类型的文件，而插件可以用于执行范围更广的任务，包括打包、优化、压缩、搭建服务器等等，功能十分强大。要是用一个插件，一般是先使用npm包管理器进行安装，然后在配置文件中引入，最后将其实例化后传递给plugins数组属性。

插件是webpack的支柱功能，目前主要是解决loader无法实现的其他许多复杂功能，**通过plugins属性使用插件：** 

```javascript
// webpack.config.js
const webpack = require('webpack');
module.exports = {
    plugins: [
        new webpack.optimize.UglifyJsPlugin()
    ]
}

```



 向plugins属性传递实例数组即可。 

### webpack 优化打包速度

 1 关闭开发工具(source-map严重影响js打包体积大小， 还会造成项目源文件暴露)

```javascript
devtool: false,
```

2 提取不变文件 

​	1 extenals // 不变文件cdn 打包，如jquery， react， vue等等 （优先使用，cdn文件需要进行锁版本） 

​	 2 dllplugins // 提取不变库，减少打包文件（针对于没有cdn源的npm包） 

 	3 多进程loader编译 

​        	1 thread-loader // 多进程运行loader, 针对于js，vue 等非常消耗资源的文件

 	4 缓存loader编译的文件 

​	5 cache-loader // 缓存js,vue等非常消耗资源的文件进行缓存

​	6 多进程缓存文件压缩 

 

 

 

 

 

 

 

 