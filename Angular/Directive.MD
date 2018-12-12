## 指令
在 Angular 中有三种类型的指令 ：
- 组件（拥有模板的指令）
- 结构型指令（通过添加和移除DOM元素改变DOM布局）如 NgFor、NgIf
- 属性型指令（改变元素、组件、或其他指令 的外观/行为 的指令） 如 NgStyle

#### 属性型指令
用CLI命令创建： 
` ng g d appHighlight `
CLI命令会创建相应文件，并在跟模块AppModule中声明这个指令类

生成的 src/app/highlight.appHighlight.ts 文件如下
```
  import { Directive } from '@angular/core';

  @Directive({
    selector: '[appHighlight]' //指定了该指令的 CSS 属性型选择器
  })
  export class HighlightDirective {
    constructor() { }
  }
```
可以将上面的代码改成：
```
import { Directive, ElementRef } from '@angular/core';  // 新增 ElementRef， 来引用宿主 DOM 元素 

@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {
    // 修改背景颜色
    constructor(el: ElementRef) {
       el.nativeElement.style.backgroundColor = 'yellow';
    }
}

```

使用： 

```
  <p appHighlight>高亮显示</p>
```

也可以将自动生成的代码改成
```
  import { Directive, ElementRef, HostListener } from '@angular/core'; // 新增 ElementRef\ HostListener

  @Directive({
    selector: '[appHighlight]'
  })
  export class HighlightDirective {
    constructor(private el: ElementRef) { }  // 声明要注入的元素 el: ElementRef。
    
    // 监听 mouseenter 事件
    @HostListener('mouseenter') onMouseEnter() {
      this.highlight('yellow');
    }
    // 监听 mouseleave 事件
    @HostListener('mouseleave') onMouseLeave() {
      this.highlight(null);
    }
    // 封装一个高亮方法
    private highlight(color: string) {
      // 为 DOM 元素（el）设置颜色
      this.el.nativeElement.style.backgroundColor = color;
    }
  }
```

