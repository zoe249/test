## msak 属性

原文链接： [奇妙的 CSS MASK · Issue #80 · chokcoco/iCSS (github.com)](https://github.com/chokcoco/iCSS/issues/80)

在过去，CSS `mask`属性在使用的时候就是`mask: xxx`，但是现在随着这个属性的规范化，`mask`属性实际上已经成为了诸多`mask-*`的缩写，这和`background`, `border`性质是一样的。

具体是哪些属性的缩写呢，可以参见下面的列表：

- mask-image
- mask-mode
- mask-repeat
- mask-position
- mask-clip
- mask-origin
- mask-size
- mask-type
- mask-composite

## conic-gradient

三种颜色的圆锥渐变

```css
#grad {
  background-image: conic-gradient(red, yellow, green);
}
```

##### 定义与用法

conic-gradient() 函数创建一个由渐变组成的图像。

圆锥渐变是颜色过渡围绕中心点旋转（而不是从中心向外辐射）。

创建圆锥渐变，至少需要设置两个色标。

![](https://www.runoob.com/wp-content/uploads/2022/02/87082642-D47E-4517-B796-9D117B2A380B.jpg)

从图中可以看到，圆锥渐变的渐变方向和起始点。起始点是图形中心，然后以顺时针方向绕中心实现渐变效果。

* 设置起始角度

  ```css
  #grad {
    background-image: conic-gradient(from 90deg, red, yellow, green);
    border-radius: 50%;
  }
  ```

* 指定中心位置

  ```css
  #grad {
    background-image: conic-gradient(at 60% 45%, red, yellow, green);
    border-radius: 50%;
  }
  ```

* 指定一个位置，并且位置一个角度

  ```css
  #grad {
    background-image: conic-gradient(from 90deg at 60% 45%, red, yellow, green);
    border-radius: 50%;
  }
  ```

* 创建一个取色卡

  ```css
  #grad {
    background: radial-gradient(closest-side, gray, transparent),
          conic-gradient(red, magenta, blue, aqua, lime, yellow, red);
    border-radius: 50%;
  }
  ```

  