# sang

> A Vue.js project

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).
# 扇形选择组件

## Tag Name

SliderSecter

## Usage

```html
 <SliderSecter ref="secter" :canvasWidth='width'  :colorSecter='colorSecter' :origin='origin' :canvasHeight='height'/>
```

## Options
属性名   |    类型    |    默认值    |   说明
----    | ----      | ----        | ----    |
canvasWidth | Number | 300 | 画布宽度
canvasHeight | Number | 300 | 画布高度
origin | Object | { x: 100, y: 100, r: 100, startdeg: 240, opendeg: 25, count: 5 } | x,y 扇形原点坐标，r半径，startdeg 起始度数0～360，opendeg:扇形开合度数，count:扇形数量
colorSecter | Object | {bg: '', selectedBg: '',borderColor: '',selectedBorderColor: '',textColor: '' } | bg:扇形背景,selectedBg:选中,borderColor:边框颜色,selectedBorderColor：选中边框颜色,textColor:区域标示文案颜色。

## Events
事件名   |    说明    |   参数
----    | ----      | ----        |
change  | touchend扇形区域 | 返回值是所选中的全部区域表示1～count


注意：startdeg * opendeg <= 360  否则 canvas层级叠加后绘制的图形覆盖先绘制图形。
