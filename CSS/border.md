# border

border 用来定义元素的边框，分为 `border-width`、`border-style`、`border-color` 三部分，分别指定边框的宽度、样式和颜色。  

## 三个属性
### border-width：边框宽度
```css
{
  border-width: 宽度值 | thin/medium/thick | inherit/initial/unset;
}
```
### border-style：边框样式
```css
{
  border-style: none(默认) | dotted(点线) | dashed(虚线) | solid(实线) | double(双实线，宽度是border-width) | groove(凹槽) | ridge(脊线) | inset(嵌入) | outset(突出)
}
```

### border-color：边框颜色
```css
{
  border-color: 色值 | inherit
}
```



## 简写
以上3 个属性分为 top 、right 、bottom 、left 四个方向，可单独定义某个方向，例如：
```css
{
  border-top-style: solid;
}
```
也可对某个属性设置 1-4 个值 来定义四个边框的样式，例如：
```css
{
  border-style: solid dashed double; /* 上->左右->下 */
}
```
属性 `border` 是 `border-width`、`border-style`、`border-color` 的简写，需要注意的是 `border` 简写的时候是同时定义四条边的样式，而不能把每条边设置为不一样。  
语法：
```css
{
  border: border-width || border-style || border-color |inherit ;
}
```

