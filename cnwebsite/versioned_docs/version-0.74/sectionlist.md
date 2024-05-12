---
id: sectionlist
title: SectionList
---

高性能的分组(section)列表组件，支持下面这些常用的功能：

- 完全跨平台。
- 行组件显示或隐藏时可配置回调事件。
- 支持单独的头部组件。
- 支持单独的尾部组件。
- 支持自定义行间分隔线。
- 支持分组的头部组件。
- 支持分组的分隔线。
- 支持多种数据源结构
- 支持下拉刷新。
- 支持上拉加载。

如果你的列表不需要分组(section)，那么可以使用结构更简单的[`<FlatList>`](flatlist.md)。

## 示例

```SnackPlayer name=SectionList%20Example
import React from 'react';
import {
  StyleSheet,
  Text,
  View,
  SafeAreaView,
  SectionList,
  StatusBar,
} from 'react-native';

const DATA = [
  {
    title: 'Main dishes',
    data: ['Pizza', 'Burger', 'Risotto'],
  },
  {
    title: 'Sides',
    data: ['French Fries', 'Onion Rings', 'Fried Shrimps'],
  },
  {
    title: 'Drinks',
    data: ['Water', 'Coke', 'Beer'],
  },
  {
    title: 'Desserts',
    data: ['Cheese Cake', 'Ice Cream'],
  },
];

const App = () => (
  <SafeAreaView style={styles.container}>
    <SectionList
      sections={DATA}
      keyExtractor={(item, index) => item + index}
      renderItem={({item}) => (
        <View style={styles.item}>
          <Text style={styles.title}>{item}</Text>
        </View>
      )}
      renderSectionHeader={({section: {title}}) => (
        <Text style={styles.header}>{title}</Text>
      )}
    />
  </SafeAreaView>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: StatusBar.currentHeight,
    marginHorizontal: 16,
  },
  item: {
    backgroundColor: '#f9c2ff',
    padding: 20,
    marginVertical: 8,
  },
  header: {
    fontSize: 32,
    backgroundColor: '#fff',
  },
  title: {
    fontSize: 24,
  },
});

export default App;
```

本组件实质是基于[`<VirtualizedList>`](virtualizedlist.md)组件的封装，继承了其所有 props（也包括所有[`<ScrollView>`](scrollview.md))的 props）。此外还有下面这些需要注意的事项：

- 当某行滑出渲染区域之外后，其内部状态将不会保留。请确保你在行组件以外的地方保留了数据。
- 本组件继承自`PureComponent`而非通常的`Component`，这意味着如果其`props`在`浅比较`中是相等的，则不会重新渲染。所以请先检查你的`renderItem`函数所依赖的`props`数据（包括`data`属性以及可能用到的父组件的 state），如果是一个引用类型（Object 或者数组都是引用类型），则需要先修改其引用地址（比如先复制到一个新的 Object 或者数组中），然后再修改其值，否则界面很可能不会刷新。（译注：这一段不了解的朋友建议先学习下[js 中的基本类型和引用类型](https://segmentfault.com/a/1190000002789651)。）
- 为了优化内存占用同时保持滑动的流畅，列表内容会在屏幕外异步绘制。这意味着如果用户滑动的速度超过渲染的速度，则会先看到空白的内容。这是为了优化不得不作出的妥协，而我们也在设法持续改进。
- 默认情况下每行都需要提供一个不重复的 key 属性。你也可以提供一个`keyExtractor`函数来生成 key。

---

# 文档

## Props

继承了所有的[ScrollView Props](scrollview.md#props).

### `sections`

用来渲染的数据，类似于[FlatList](flatlist.md)中的`data`属性。

| 类型                                        | 必需 |
| ------------------------------------------- | ---- |
| array of [Section](sectionlist.md#section)s | 是   |

---

### `initialNumToRender`

指定一开始渲染的元素数量，最好刚刚够填满一个屏幕，这样保证了用最短的时间给用户呈现可见的内容。注意这第一批次渲染的元素不会在滑动过程中被卸载，这样是为了保证用户执行返回顶部的操作时，不需要重新渲染首批元素。

| 类型   | 必需 |
| ------ | ---- |
| number | 否   |

---

### `keyExtractor`

此函数用于为给定的 item 生成一个不重复的 key。Key 的作用是使 React 能够区分同类元素的不同个体，以便在刷新时能够确定其变化的位置，减少重新渲染的开销。若不指定此函数，则默认抽取 item.key 作为 key 值。若 item.key 也不存在，则使用数组下标。注意这只设置了每行（item）的 key，对于每个组（section）仍然需要另外设置 key。

| 类型                                  | 必需 |
| ------------------------------------- | ---- |
| (item: Item, index: number) => string | 是   |

---

### <div class="label required basic">必需</div>**`renderItem`**

用来渲染每一个 section 中的每一个列表项的默认渲染器。可以在 section 级别上进行覆盖重写。必须返回一个 react 组件。

| 类型 |
| ---- |
| 函数 |

渲染函数将传递一个带有以下键的对象：

- `item`（对象） - 此部分`data`键中指定的项目对象
- `index`（数字） - 该项在部分内的索引。
- `section`（对象） - 指定为`sections`中完整的部分对象。
- `separators`（对象） - 一个具有以下键的对象：
  - `highlight`（函数） - `() => void`
  - `unhighlight`（函数） - `() => void`
  - `updateProps`（函数） - `(select, newProps) => void`
    - `select` （枚举值）- 可能值为`leading`，`trailing`
    - `newProps` (对象)

---

### `extraData`

如果有除`data`以外的数据用在列表中（不论是用在`renderItem`还是 Header 或者 Footer 中），请在此属性中指定。同时此数据在修改时也需要先修改其引用地址（比如先复制到一个新的 Object 或者数组中），然后再修改其值，否则界面很可能不会刷新。

| 类型 | 必需 |
| ---- | ---- |
| any  | 否   |

---

### `ItemSeparatorComponent`

行与行之间的分隔线组件。不会出现在第一行之前和最后一行之后。By default, `highlighted`, `section`, and `[leading/trailing][Item/Separator]` props are provided. `renderItem` provides `separators.highlight`/`unhighlight` which will update the `highlighted` prop, but you can also add custom props with `separators.updateProps`.

| 类型                           | 必需 |
| ------------------------------ | ---- |
| [component, function, element] | 否   |

---

### `inverted`

翻转滚动方向。实质是将 scale 变换设置为-1。

| 类型      | 必需 |
| --------- | ---- |
| [boolean] | 否   |

---

### `ListFooterComponent`

尾部组件。

| 类型                           | 必需 |
| ------------------------------ | ---- |
| [component, function, element] | 否   |

---

### `ListEmptyComponent`

当列表数据为空时渲染的组件。

| 类型                           | 必需 |
| ------------------------------ | ---- |
| [component, function, element] | 否   |

---

### `onEndReachedThreshold`

决定当距离内容最底部还有多远时触发`onEndReached`回调。注意此参数是一个比值而非像素单位。比如，0.5 表示距离内容最底部的距离为当前列表可见长度的一半时触发。

| 类型     | 必需 |
| -------- | ---- |
| [number] | 否   |

---

### `onRefresh`

如果设置了此选项，则会在列表头部添加一个标准的[`RefreshControl`](refreshcontrol.md)控件，以便实现“下拉刷新”的功能。同时你需要正确设置`refreshing`属性。如果你想把刷新控件往下移动一些（比如 100 个 pt），可以设置`progressViewOffset={100}`。

| 类型         | 必需 |
| ------------ | ---- |
| [() => void] | 否   |

---

### `onViewableItemsChanged`

当行的可见性发生变化时调用，根据`viewabilityConfig`属性定义。

| 类型                                                                                                  |
| ----------------------------------------------------------------------------------------------------- |
| `md (callback: {changed: [ViewToken](viewtoken)[], viewableItems: [ViewToken](viewtoken)[]}) => void` |

---

### `refreshing`

在等待加载新数据时将此属性设为 true，列表就会显示出一个正在加载的符号。

| 类型      | 必需 |
| --------- | ---- |
| [boolean] | 否   |

---

### `removeClippedSubviews`

Note: may have bugs (missing content) in some circumstances - use at your own risk.

This may improve scroll performance for large lists.

| 类型    | 必需 |
| ------- | ---- |
| boolean | 否   |

---

### `ListHeaderComponent`

头部组件。

| 类型                         | 必需 |
| ---------------------------- | ---- |
| component, function, element | 否   |

---

### `renderSectionFooter`

每个组的尾部组件。

| Type                                                                      |
| ------------------------------------------------------------------------- |
| `md (info: {section: [Section](sectionlist#section)}) => element ｜ null` |

---

### `renderSectionHeader`

在每个 section 的头部渲染。在 iOS 上，这些 headers 是默认粘接在`ScrollView`的顶部的. 参见[`stickySectionHeadersEnabled`](#stickySectionHeadersEnabled)。

| Type                                                                      |
| ------------------------------------------------------------------------- |
| `md (info: {section: [Section](sectionlist#section)}) => element ｜ null` |

---

### `SectionSeparatorComponent`

在每个`section`的顶部和底部渲染(区别于`ItemSeparatorComponent`，它仅在列表项之间渲染)。它的作用是为了从视觉上把`section`与它上方或下方的`headers`区别开来，从这个意义上讲，它的作用和`ItemSeparatorComponent`是一样的. 它也接受`highlighted`, `[leading/trailing][Item/Separator]`这两个默认提供的属性或其他通过`separators.updateProps`添加的自定义属性.

| Type                         |
| ---------------------------- |
| component, element, function |

---

### `stickySectionHeadersEnabled`

当下一个 section 把它的前一个 section 的可视区推离屏幕的时候，让这个 section 的 header 粘连在屏幕的顶端。这个属性在 iOS 上是默认可用的，因为这是 iOS 的平台规范。

| 类型    | 必需 |
| ------- | ---- |
| boolean | 否   |

## 方法

### `scrollToLocation()`

```jsx
scrollToLocation(params);
```

将可视区内位于特定`sectionIndex` 或  `itemIndex` (section 内)位置的列表项，滚动到可视区的制定位置。比如说，`viewPosition` 为 0 时将这个列表项滚动到可视区顶部 (可能会被顶部粘接的 header 覆盖), 为 1 时将它滚动到可视区底部, 为 0.5 时将它滚动到可视区中央。

> 注意: 如果没有设置`getItemLayout`或是`onScrollToIndexFailed`，就不能滚动到位于外部渲染区的位置。

**参数：**

| 名称   | 类型   | 必需 | 说明         |
| ------ | ------ | ---- | ------------ |
| params | object | 是   | 看下面的说明 |

Valid `params` keys are:

- 'animated' (boolean) - Whether the list should do an animation while scrolling. Defaults to `true`.
- 'itemIndex' (number) - Index within section for the item to scroll to. Required.
- 'sectionIndex' (number) - Index for section that contains the item to scroll to. Required.
- 'viewOffset' (number) - 一个以像素为单位，到最终位置偏移距离的固定值，比如为了弥补粘接的 header 所占据的空间。
- 'viewPosition' (number) - A value of `0` places the item specified by index at the top, `1` at the bottom, and `0.5` centered in the middle.

---

### `recordInteraction()`

```jsx
recordInteraction();
```

主动通知列表发生了一个事件，以使列表重新计算可视区域。比如说当`waitForInteractions` 为 true 并且用户没有滚动列表时，就可以调用这个方法。不过一般来说，当用户点击了一个列表项，或发生了一个导航动作时，我们就可以调用这个方法。

---

### `flashScrollIndicators()`

```jsx
flashScrollIndicators();
```

短暂地显示滚动指示器。

## 类型定义

### Section

An object that identifies the data to be rendered for a given section.

| 类型 |
| ---- |
| any  |

**属性：**

| 名称                     | 类型                         | 说明                                                                                                                                                                   |
| ------------------------ | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| data                     | array                        | The data for rendering items in this section. Array of objects, much like [`FlatList`的 data 属性](flatlist.md#data).                                                  |
| [key]                    | string                       | Optional key to keep track of section re-ordering. If you don't plan on re-ordering sections, the array index will be used by default.                                 |
| [renderItem]             | function                     | Optionally define an arbitrary item renderer for this section, overriding the default [`renderItem`](sectionlist.md#renderitem) for the list.                          |
| [ItemSeparatorComponent] | component, function, element | Optionally define an arbitrary item separator for this section, overriding the default [`ItemSeparatorComponent`](sectionlist.md#itemseparatorcomponent) for the list. |
| [keyExtractor]           | function                     | Optionally define an arbitrary key extractor for this section, overriding the default [`keyExtractor`](sectionlist.md#keyextractor).                                   |
