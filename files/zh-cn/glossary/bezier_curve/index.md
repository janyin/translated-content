---
title: Bézier curve
slug: Glossary/Bezier_curve
original_slug: Glossary/Bézier_curve
---
**贝塞尔曲线**是一种使用数学方法描述的曲线，被广泛用于计算机图形学和动画中，在 {{Glossary("矢量图", "vector images")}} 里，贝塞尔曲线用于定义可无限放大的光滑曲线。

贝塞尔曲线由至少两个控制点进行描述。Web 技术中使用的是三次贝塞尔曲线，即使用四个控制点 \[P<sub>0</sub>, P<sub>1</sub>, P<sub>2</sub>, P<sub>3</sub>] 描述的曲线。

在绘制曲线的过程中，需要先作两条辅助线：\[P<sub>0</sub>,P<sub>1</sub> ] 和 \[P<sub>1</sub> , P<sub>2</sub>]（译者注：下图中的绿线）；辅助线的端点沿着所在连线平滑地移动到连线的另一端；采用同样的方法在辅助线 \[P<sub>0</sub>,P<sub>1</sub> ] 和 \[P<sub>1</sub> , P<sub>2</sub>] 上绘制第三条辅助线（译者注：下图中的蓝线）；在第三条辅助线上将一个点从一端平滑地移向另外一端，这个点的运动轨迹就是贝塞尔曲线。下面是这个绘图过程的动态演示：

![Drawing a Bézier curve](https://upload.wikimedia.org/wikipedia/commons/d/db/B%C3%A9zier_3_big.gif)

## 了解更多

### 常识

- [Bézier curve on Wikipedia](https://en.wikipedia.org/wiki/B%C3%A9zier_curve)

### 学习使用贝塞尔曲线

- [Cubic Bézier timing functions in CSS](</zh-CN/docs/Web/CSS/easing-function#The_cubic-bezier()_class_of_timing_functions>)
- {{SVGAttr("keySplines")}} SVG attribute
- [Cubic Bézier Generator](/zh-CN/docs/Web/CSS/Tools/Cubic_Bezier_Generator)
