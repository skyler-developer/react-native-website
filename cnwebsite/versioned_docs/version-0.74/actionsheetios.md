---
id: actionsheetios
title: ActionSheetIOS
---

显示一个 iOS 原生的[Action Sheet](https://developer.apple.com/design/human-interface-guidelines/ios/views/action-sheets/)组件。

## 示例

```SnackPlayer name=ActionSheetIOS&supportedPlatforms=ios
import React, { useState } from "react";
import { ActionSheetIOS, Button, StyleSheet, Text, View } from "react-native";
const App = () => {
  const [result, setResult] = useState("🔮");
  const onPress = () =>
    ActionSheetIOS.showActionSheetWithOptions(
      {
        options: ["Cancel", "Generate number", "Reset"],
        destructiveButtonIndex: 2,
        cancelButtonIndex: 0,
        userInterfaceStyle: 'dark'
      },
      buttonIndex => {
        if (buttonIndex === 0) {
          // cancel action
        } else if (buttonIndex === 1) {
          setResult(Math.floor(Math.random() * 100) + 1);
        } else if (buttonIndex === 2) {
          setResult("🔮");
        }
      }
    );
  return (
    <View style={styles.container}>
      <Text style={styles.result}>{result}</Text>
      <Button onPress={onPress} title="Show Action Sheet" />
    </View>
  );
};
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center"
  },
  result: {
    fontSize: 64,
    textAlign: "center"
  }
});
export default App;
```

# 文档

## 方法

### `showActionSheetWithOptions()`

```tsx
static showActionSheetWithOptions: (
  options: ActionSheetIOSOptions,
  callback: (buttonIndex: number) => void,
);
```

在 iOS 设备上显示一个 ActionSheet 弹出框，其中`options`参数为一个对象，其属性必须包含以下一项或多项：

- `options`（字符串数组） - 按钮标题列表（必需）
- `cancelButtonIndex`（整数） - `options` 中取消按钮的索引
- `cancelButtonTintColor`（字符串） - 用于改变取消按钮文本颜色的颜色（[颜色](colors)）
- `destructiveButtonIndex`（整数或整数数组） - `options` 中破坏性按钮的索引
- `title`（字符串） - 显示在操作表上方的标题
- `message`（字符串） - 显示在标题下方的消息
- `anchor`（数字） - 操作表应锚定的节点（用于 iPad）
- `tintColor`（字符串） - 用于非破坏性按钮标题的颜色（[颜色](colors)）
- `disabledButtonIndices`（数字数组） - 应禁用的按钮索引列表
- `userInterfaceStyle`（字符串） - 用于操作表的界面样式，可以设置为 `light` 或 `dark`，否则将使用默认的系统样式
  
`callback`函数则仅接受一个参数，即所点击按钮的索引。

一个例子：

```
ActionSheetIOS.showActionSheetWithOptions({
  options: ['取消', '删除'],
  destructiveButtonIndex: 1,
  cancelButtonIndex: 0,
},
(buttonIndex) => {
if (buttonIndex === 1) { /* 当接收到的索引为1，即点击了删除按钮时，执行对应操作 */ }
});
```

---

### `dismissActionSheet()`

```tsx
static dismissActionSheet();
```

关闭当前显示的最顶层 ActionSheet。如果当前没有 ActionSheet，则会显示警告。

---

### `showShareActionSheetWithOptions()`

```tsx
static showShareActionSheetWithOptions: (
  options: ShareActionSheetIOSOptions,
  failureCallback: (error: Error) => void,
  successCallback: (success: boolean, method: string) => void,
);
```

在 iOS 设备上显示一个分享弹出框，其中`options`参数为一个对象，其属性包含以下几项（必须至少有 message 或 url）:

- `url` （字符串） - 要分享的 URL 地址
- `message` （字符串） - 要分享的信息
- `subject` （字符串） - 要分享的信息主题
- `excludedActivityTypes` （数组） - 指定在 actionsheet 中不显示的活动

注：如果`url`指向本地文件，或者是一个 base64 编码的 url，则会直接读取并分享相应的文件。你可以用这样的方式来分享图片、视频以及 PDF 文件等。If `url` points to a remote file or address it must conform to URL format as described in [RFC 2396](https://www.ietf.org/rfc/rfc2396.txt). For example, a web URL without a proper protocol (HTTP/HTTPS) will not be shared.

`failureCallback`函数仅接受一个错误对象参数。此对象中仅包含一个可选的`stack`属性，类型为字符串。

`successCallback`函数接受两个参数：

- 表示成功与否的布尔值
- 成功的话返回一个表示分享方式的字符串
