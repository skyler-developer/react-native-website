---
id: text
title: Text
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

一个用于显示文本的 React 组件，并且它也支持嵌套、样式，以及触摸处理。

在下面的例子里，嵌套的标题和正文文字会继承来自`styles.baseText`的`fontFamily`字体样式，不过标题上还附加了它自己额外的样式。标题和文本会在顶部依次堆叠，并且被代码中内嵌的换行符分隔开。

<Tabs groupId="syntax" defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=Text%20Functional%20Component%20Example
import React, { useState } from "react";
import { Text, StyleSheet } from "react-native";

const TextInANest = () => {
  const [titleText, setTitleText] = useState("Bird's Nest");
  const bodyText = useState("This is not really a bird nest.");

  const onPressTitle = () => {
    setTitleText("Bird's Nest [pressed]");
  };

  return (
    <Text style={styles.baseText}>
      <Text style={styles.titleText} onPress={onPressTitle}>
        {titleText}
        {"\n"}
        {"\n"}
      </Text>
      <Text numberOfLines={5}>{bodyText}</Text>
    </Text>
  );
};

const styles = StyleSheet.create({
  baseText: {
    fontFamily: "Cochin"
  },
  titleText: {
    fontSize: 20,
    fontWeight: "bold"
  }
});

export default TextInANest;
```

</TabItem>
<TabItem value="classical">

```SnackPlayer name=Text%20Class%20Component%20Example
import React, { Component } from "react";
import { Text, StyleSheet } from "react-native";

class TextInANest extends Component {
  constructor(props) {
    super(props);
    this.state = {
      titleText: "Bird's Nest",
      bodyText: "This is not really a bird nest."
    };
  }

  onPressTitle = () => {
    this.setState({ titleText: "Bird's Nest [pressed]" });
  };

  render() {
    return (
      <Text style={styles.baseText}>
        <Text
          style={styles.titleText}
          onPress={this.onPressTitle}
        >
          {this.state.titleText}
          {"\n"}
          {"\n"}
        </Text>
        <Text numberOfLines={5}>{this.state.bodyText}</Text>
      </Text>
    );
  }
}

const styles = StyleSheet.create({
  baseText: {
    fontFamily: "Cochin"
  },
  titleText: {
    fontSize: 20,
    fontWeight: "bold"
  }
});

export default TextInANest;
```

</TabItem>
</Tabs>

## 嵌套文本

在 iOS 和 Android 中显示格式化文本的方法类似，都是提供你想显示的文本内容，然后使用范围标注来指定一些格式（在 iOS 上是用`NSAttributedString`，Android 上则是`SpannableString`）。这种用法非常繁琐。在 React Native 中，我们决定采用和 Web 一致的设计，这样你可以把相同格式的文本嵌套包裹起来：

```SnackPlayer name=Nested%20Text%20Example
import React from 'react';
import { Text, StyleSheet } from 'react-native';

const BoldAndBeautiful = () => {
  return (
    <Text style={styles.baseText}>
      I am bold
      <Text style={styles.innerText}> and red</Text>
    </Text>
  );
};

const styles = StyleSheet.create({
  baseText: {
    fontWeight: 'bold'
  },
  innerText: {
    color: 'red'
  }
});

export default BoldAndBeautiful;
```

而实际上在框架内部，这会生成一个扁平结构的`NSAttributedString`或是`SpannableString`，包含以下信息：

```jsx
"I am bold and red"
0-9: bold
9-17: bold, red
```

## 嵌套视图（仅限 iOS）

On iOS, you can nest views within your Text component. Here's an example:

```SnackPlayer
import React, { Component } from 'react';
import { Text, View } from 'react-native';

export default class BlueIsCool extends Component {
  render() {
    return (
      <Text>
        There is a blue square
        <View style={{width: 50, height: 50, backgroundColor: 'steelblue'}} />
        in between my text.
      </Text>
    );
  }
}
```

> In order to use this feature, you must give the view a `width` and a `height`.

## 容器

`<Text>`元素在布局上不同于其它组件：在 Text 内部的元素不再使用 flexbox 布局，而是采用文本布局。这意味着`<Text>`内部的元素不再是一个个矩形，而可能会在行末进行折叠。

```jsx
<Text>
  <Text>First part and </Text>
  <Text>second part</Text>
</Text>
// Text container: the text will be inline if the space allowed it
// |First part and second part|

// otherwise, the text will flow as if it was one
// |First part |
// |and second |
// |part       |

<View>
  <Text>First part and </Text>
  <Text>second part</Text>
</View>
// View container: each text is its own block
// |First part and|
// |second part   |

// the text will flow in its own block
// |First part |
// |and        |
// |second part|
```

## 样式继承限制

在 Web 上，要想指定整个文档的字体和大小，我们只需要写：

```css
/* 这段代码是CSS, *不是*React Native */
html {
  font-family: 'lucida grande', tahoma, verdana, arial, sans-serif;
  font-size: 11px;
  color: #141823;
}
```

当浏览器尝试渲染一个文本节点的时候，它会在树中一路向上查询，直到根节点，来找到一个具备`font-size`属性的元素。这个系统一个不好的地方在于**任何**节点都可能会有`font-size`属性，包括`<div>`标签。这个设计为了方便而设计，但实际上语义上并不太正确。

在 React Native 中，我们把这个问题设计的更加严谨：**你必须把你的文本节点放在`<Text>`组件内**。你不能直接在`<View>`下放置一段文本。

```jsx
// 错误的做法：会导致一个错误。<View>下不能直接放一段文本。
<View>
  一些文本
</View>

// 正确的做法
<View>
  <Text>
    一些文本
  </Text>
</View>
```

并且你也不能直接设置一整颗子树的默认样式。此外，`fontFamily`样式只接受一种字体名称，这一点和 CSS 也不一样。使用一个一致的文本和尺寸的推荐方式是创建一个包含相关样式的组件`MyAppText`，然后在你的 App 中反复使用它。你还可以创建更多特殊的组件譬如`MyAppHeaderText`来表达不同样式的文本。

```jsx
<View>
  <MyAppText>
    这个组件包含了一个默认的字体样式，用于整个应用的文本
  </MyAppText>
  <MyAppHeaderText>这个组件包含了用于标题的样式</MyAppHeaderText>
</View>
```

Assuming that `MyAppText` is a component that simply renders out its children into a `Text` component with styling, then `MyAppHeaderText` can be defined as follows:

```jsx
class MyAppHeaderText extends Component {
  render() {
    return (
      <MyAppText>
        <Text style={{fontSize: 20}}>{this.props.children}</Text>
      </MyAppText>
    );
  }
}
```

Composing `MyAppText` in this way ensures that we get the styles from a top-level component, but leaves us the ability to add / override them in specific use cases.

React Native 实际上还是有一部分样式继承的实现，不过仅限于文本标签的子树。在下面的代码里，第二部分会在加粗的同时又显示为红色：

```jsx
<Text style={{fontWeight: 'bold'}}>
  I am bold
  <Text style={{color: 'red'}}>and red</Text>
</Text>
```

我们相信这种看起来不太舒服的给文本添加样式的方法反而会帮助我们生产更好的 App：

- (对开发者来说) React 组件在概念上被设计为强隔离性的：你应当可以在应用的任何位置放置一个组件，而且只要属性相同，其外观和表现都将完全相同。文本如果能够继承外面的样式属性，将会打破这种隔离性。

- (对实现者来说) React Native 的实现也被简化了。我们不需要在每个元素上都添加一个`fontFamily`字段，并且我们也不需要隐含地在显示文本的时候向上遍历树。唯一的样式继承在原生 Text 组件中编码，也不会影响到其它组件或者系统本身。

---

# 文档

## Props

### `accessibilityHint`

An accessibility hint helps users understand what will happen when they perform an action on the accessibility element when that result is not clear from the accessibility label.

| Type   |
| ------ |
| string |

---

### `accessibilityLabel`

Overrides the text that's read by the screen reader when the user interacts with the element. By default, the label is constructed by traversing all the children and accumulating all the `Text` nodes separated by space.

| Type   |
| ------ |
| string |

---

### `accessibilityRole`

Tells the screen reader to treat the currently focused on element as having a specific role.

On iOS, these roles map to corresponding Accessibility Traits. Image button has the same functionality as if the trait was set to both 'image' and 'button'. See the [Accessibility guide](accessibility.md#accessibilitytraits-ios) for more information.

On Android, these roles have similar functionality on TalkBack as adding Accessibility Traits does on Voiceover in iOS

| Type                                                 |
| ---------------------------------------------------- |
| [AccessibilityRole](accessibility#accessibilityrole) |

---

### `accessibilityState`

Tells the screen reader to treat the currently focused on element as being in a specific state.

You can provide one state, no state, or multiple states. The states must be passed in through an object. Ex: `{selected: true, disabled: true}`.

| Type                                                   |
| ------------------------------------------------------ |
| [AccessibilityState](accessibility#accessibilitystate) |

---

### `accessible`

When set to `true`, indicates that the view is an accessibility element.

See the [Accessibility guide](accessibility#accessible-ios-android) for more information.

| Type    | Default |
| ------- | ------- |
| boolean | `true`  |

---

### `adjustsFontSizeToFit` <div class="label ios">iOS</div>

指定字体是否随着给定样式的限制而自动缩放。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

---

### `selectable`

决定用户是否可以长按选择文本，以便复制和粘贴。

| 类型 | 必需 |
| ---- | ---- |
| bool | 否   |

---

### `ellipsizeMode`

这个属性通常和下面的 `numberOfLines` 属性配合使用，表示当 Text 组件无法全部显示需要显示的字符串时如何用省略号进行修饰。

该属性有如下 4 种取值:

- `head` - 从文本内容头部截取显示省略号。例如： "...efg"
- `middle` - 在文本内容中间截取显示省略号。例如： "ab...yz"
- `tail` - 从文本内容尾部截取显示省略号。例如： "abcd..."
- `clip` - 不显示省略号，直接从尾部截断。

The default is `tail`.

| 类型                                   | 必需 |
| -------------------------------------- | ---- |
| enum('head', 'middle', 'tail', 'clip') | 否   |

---

### `nativeID`

Used to locate this view from native code.

| 类型   | 必需 |
| ------ | ---- |
| string | 否   |

---

### `numberOfLines`

用来当文本过长的时候裁剪文本。包括折叠产生的换行在内，总的行数不会超过这个属性的限制。

此属性一般和`ellipsizeMode`搭配使用。

| 类型   | 必需 |
| ------ | ---- |
| number | 否   |

---

### `onLayout`

在加载时或者布局变化以后调用。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({nativeEvent: [LayoutEvent](layoutevent)}) => void` |

---

### `onLongPress`

当文本被长按以后调用此回调函数。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onPress`

在用户按下后调用的函数，在`onPressOut`触发之后被触发。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onPressIn`

在触摸开始时立即调用，早于`onPressOut`和`onPress`。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onPressOut`

当触摸释放时调用。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onResponderGrant`

| 类型                                                              |
| ----------------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void ｜ boolean` |

---

### `onResponderMove`

用户正在移动手指。

| 类型                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onResponderRelease`

触摸结束时触发。

| 类型                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onResponderTerminate`

响应器已从`View`中移除。在调用`onResponderTerminationRequest`后可能会被其他视图获取，或者可能在没有询问的情况下被操作系统获取（例如，在 iOS 上发生在控制中心/通知中心）。

| 类型                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onResponderTerminationRequest`

其他某个`View`想要成为响应者，并请求该`View`释放其响应者身份。返回`true`允许释放。

| 类型                                                      |
| --------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => boolean` |

---

### `onStartShouldSetResponderCapture`

如果父级 `View` 希望阻止子级 `View` 在触摸开始时成为响应者，则应该具有此处理程序，它返回 `true`。

| 类型                                                      |
| --------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => boolean` |

---

### `onTextLayout`

在文本布局更改时调用。

| 类型                                                 |
| ---------------------------------------------------- |
| ([`TextLayoutEvent`](text#textlayoutevent)) => mixed |

---

### `pressRetentionOffset`

当滚动视图被禁用时，这定义了您的触摸可以在按钮上移动多远，然后才会停用该按钮。一旦停用，请尝试将其移回，并且您会看到按钮再次被激活！在禁用滚动视图时来回移动它几次。确保传入一个常量以减少内存分配。

| Type                 |
| -------------------- |
| [Rect](rect), number |

---

### `allowFontScaling`

控制字体是否要根据系统的“字体大小”辅助选项来进行缩放。默认值为`true`。

| 类型 | 必需 |
| ---- | ---- |
| bool | 否   |

---

### `style`

| 类型  | 必需 |
| ----- | ---- |
| style | 否   |

- [View Style Props...](view-style-props.md#style)

- **`textShadowOffset`**: object: `{width: number,height: number}`

- **`color`**: [color](colors.md)

- **`fontSize`**: number

- **`fontStyle`**: enum('normal', 'italic')

- **`fontWeight`**: enum('normal', 'bold', '100', '200', '300', '400', '500', '600', '700', '800', '900')

  指定字体的粗细。大多数字体都支持'normal'和'bold'值。并非所有字体都支持所有的数字值。如果某个值不支持，则会自动选择最接近的值。

- **`lineHeight`**: number

- **`textAlign`**: enum('auto', 'left', 'right', 'center', 'justify')

  指定文本的对齐方式。其中'justify'值仅 iOS 和 Android>=8.0 支持，在 Android8.0 以下会变为`left`。

- **`textDecorationLine`**: enum('none', 'underline', 'line-through', 'underline line-through')

- **`textShadowColor`**: [color](colors.md)

- **`fontFamily`**: string

- **`textShadowRadius`**: number

- **`includeFontPadding`**: bool (_Android_)

  Android 在默认情况下会为文字额外保留一些 padding，以便留出空间摆放上标或是下标的文字。对于某些字体来说，这些额外的 padding 可能会导致文字难以垂直居中。如果你把`textAlignVertical`设置为`center`之后，文字看起来依然不在正中间，那么可以尝试将本属性设置为`false`。默认值为 true。

* **`textAlignVertical`**: enum('auto', 'top', 'bottom', 'center') (_Android_)

* **`fontVariant`**: array of enum('small-caps', 'oldstyle-nums', 'lining-nums', 'tabular-nums', 'proportional-nums') (_iOS_)

* **`letterSpacing`**: number

Increase or decrease the spacing between characters. The default is 0, for no extra letter spacing.

iOS: The additional space will be rendered after each glyph.

Android: Only supported since Android 5.0 - older versions will ignore this attribute. Please note that additional space will be added _around_ the glyphs (half on each side), which differs from the iOS rendering. It is possible to emulate the iOS rendering by using layout attributes, e.g. negative margins, as appropriate for your situation.

- **`textDecorationColor`**: [color](colors.md) (_iOS_)

- **`textDecorationStyle`**: enum('solid', 'double', 'dotted', 'dashed') (_iOS_)

- **`textTransform`**: enum('none', 'uppercase', 'lowercase', 'capitalize')

- **`writingDirection`**: enum('auto', 'ltr', 'rtl') (_iOS_)

---

### `testID`

用来在端到端测试中定位此视图。

| 类型   | 必需 |
| ------ | ---- |
| string | 否   |

---

### `selectionColor`

The highlight color of the text.

| 类型               | 必需 | 平台    |
| ------------------ | ---- | ------- |
| [color](colors.md) | 否   | Android |

---

### `textBreakStrategy` <div class="label android">Android</div>

Set text break strategy on Android API Level 23+, possible values are `simple`, `highQuality`, `balanced`.

| Type                                            | Default       |
| ----------------------------------------------- | ------------- |
| enum(`'simple'`, `'highQuality'`, `'balanced'`) | `highQuality` |

---

### `minimumFontScale`

当 adjustsFontSizeToFit 开启时，指定最小的缩放比（即不能低于这个值）。可设定的值为 0.01 - 1.

| 类型   | 必需 | 平台 |
| ------ | ---- | ---- |
| number | 否   | iOS  |

---

### `suppressHighlighting` <div class="label ios">iOS</div>

设为 true 时，当文本被按下会没有任何视觉效果。默认情况下，文本被按下时会有一个灰色的、椭圆形的高光。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

---

### `android_hyphenationFrequency` <div class="label android">Android</div>

Sets the frequency of automatic hyphenation to use when determining word breaks on Android API Level 23+.

| Type                                | Default  |
| ----------------------------------- | -------- |
| enum(`'none'`, `'normal'`,`'full'`) | `'none'` |

---

### `dataDetectorType` <div class="label android">Android</div>

Determines the types of data converted to clickable URLs in the text element. By default, no data types are detected.

You can provide only one type.

| Type                                                          | Default  |
| ------------------------------------------------------------- | -------- |
| enum(`'phoneNumber'`, `'link'`, `'email'`, `'none'`, `'all'`) | `'none'` |

---

### `disabled` <div class="label android">Android</div>

Specifies the disabled state of the text view for testing purposes.

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

## 类型定义

### TextLayout

`TextLayout` object is a part of [`TextLayoutEvent`](text#textlayoutevent) callback and contains the measurement data for `Text` line.

#### 示例

```js
{
    capHeight: 10.496,
    ascender: 14.624,
    descender: 4,
    width: 28.224,
    height: 18.624,
    xHeight: 6.048,
    x: 0,
    y: 0
}
```

#### 属性

| Name      | Type   | Optional | Description                                                         |
| --------- | ------ | -------- | ------------------------------------------------------------------- |
| ascender  | number | No       | The line ascender height after the text layout changes.             |
| capHeight | number | No       | Height of capital letter above the baseline.                        |
| descender | number | No       | The line descender height after the text layout changes.            |
| height    | number | No       | Height of the line after the text layout changes.                   |
| width     | number | No       | Width of the line after the text layout changes.                    |
| x         | number | No       | Line X coordinate inside the Text component.                        |
| xHeight   | number | No       | Distance between the baseline and median of the line (corpus size). |
| y         | number | No       | Line Y coordinate inside the Text component.                        |

### TextLayoutEvent

`TextLayoutEvent` object is returned in the callback as a result of component layout change. It contains a key called `lines` with a value which is an array containing [`TextLayout`](text#textlayout) object corresponeded to every rendered text line.

#### 示例

```js
{
  lines: [
    TextLayout,
    TextLayout,
    // ...
  ];
  target: 1127;
}
```

#### 属性

| Name   | Type                                    | Optional | Description                                           |
| ------ | --------------------------------------- | -------- | ----------------------------------------------------- |
| lines  | array of [TextLayout](text#textlayout)s | No       | Provides the TextLayout data for every rendered line. |
| target | number                                  | No       | The node id of the element.                           |

## 已知问题

- [react-native#22811](https://github.com/facebook/react-native/issues/22811): Nested Text elements do not support `numberOfLines` attribute
