---
id: platform-specific-code
title: 特定平台代码
---

在编写跨平台的应用时，我们肯定希望尽可能多地复用代码。但是总有些时候我们会碰到针对不同平台编写不同代码的需求。

React Native 提供了两种方法来区分平台：

- 使用[`Platform`模块](platform-specific-code.md#platform模块).
- 使用[特定平台后缀](platform-specific-code.md#特定平台后缀).

另外有些内置组件的某些属性可能只在特定平台上有效。请在阅读文档时留意。

## Platform 模块

React Native 提供了一个检测当前运行平台的模块。如果组件只有一小部分代码需要依据平台定制，那么这个模块就可以派上用场。

```jsx
import {Platform, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  height: Platform.OS === 'ios' ? 200 : 100,
});
```

`Platform.OS`在 iOS 上会返回`ios`，而在 Android 设备或模拟器上则会返回`android`。

还有个实用的方法是 Platform.select()，它可以以 Platform.OS 为 key，从传入的对象中返回对应平台的值，见下面的示例：

```jsx
import {Platform, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    ...Platform.select({
      ios: {
        backgroundColor: 'red',
      },
      android: {
        backgroundColor: 'blue',
      },
    }),
  },
});
```

上面的代码会根据平台的不同返回不同的 container 样式 —— iOS 上背景色为红色，而 android 为蓝色。

这一方法可以接受任何合法类型的参数，因此你也可以直接用它针对不同平台返回不同的组件，像下面这样：

```jsx
const Component = Platform.select({
  ios: () => require('ComponentIOS'),
  android: () => require('ComponentAndroid'),
})();

<Component />;
```

### 检测 Android 版本

在 Android 上，`Version`属性是一个数字，表示 Android 的 api level：

```jsx
import {Platform} from 'react-native';

if (Platform.Version === 25) {
  console.log('Running on Nougat!');
}
```

### 检测 iOS 版本

在 iOS 上，`Version`属性是`-[UIDevice systemVersion]`的返回值，具体形式为一个表示当前系统版本的字符串。比如可能是"10.3"。

```jsx
import {Platform} from 'react-native';

const majorVersionIOS = parseInt(Platform.Version, 10);
if (majorVersionIOS <= 9) {
  console.log('Work around a change in behavior');
}
```

## 特定平台后缀

当不同平台的代码逻辑较为复杂时，最好是放到不同的文件里，这时候我们可以使用特定平台后缀。React Native 会检测某个文件是否具有`.ios.`或是`.android.`的后缀，然后根据当前运行的平台自动加载正确对应的文件。

比如你可以在项目中创建下面这样的组件：

```sh
BigButton.ios.js
BigButton.android.js
```

然后去掉平台后缀直接引用：

```jsx
import BigButton from './BigButton';
```

React Native 会根据运行平台的不同自动引入正确对应的组件。

如果你还希望在 web 端复用 React Native 的代码，那么还可以使用`.native.js`的后缀。此时 iOS 和 Android 会使用`BigButton.native.js`文件，而 web 端会使用`BigButton.js`。（注意目前官方并没有直接提供 web 端的支持，请在社区搜索第三方方案）。

比如在你的项目中存在如下的两个文件:

```sh
Container.js # 由 Webpack, Rollup 或者其他打包工具打包的文件
Container.native.js # 由 React Native 自带打包工具(Metro) 为ios和android 打包的文件
```

在引用时并不需要添加`.native.`后缀:

```jsx
import Container from './Container';
```

**提示:** 为避免在构建后的 Web 生产环境的代码中出现多余代码，要记得在你的 Web 打包工具中配置忽略`.native.js`结尾的文件, 这样可以减少打包后文件的大小。
