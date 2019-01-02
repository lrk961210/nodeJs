## 操纵DOM元素

#### ElementRef
在框架中原有的定义：
```
    export class ElementRef {
      public nativeElement: any;
      constructor(nativeElement: any) { this.nativeElement = nativeElement; }
    }
```
使用：
```
    @Component({
      selector: 'my-app',
      template: `
        <h1>Welcome to Angular World</h1>
        <div>Hello {{ name }}</div>
      `,
    })
    export class AppComponent {

      name: string = 'Semlinker';

      // 在构造函数中 this.elementRef = elementRef 是可选的，编译时会自动赋值
      // function AppComponent(elementRef) { this.elementRef = elementRef; }
      constructor(private elementRef: ElementRef) { } 

      ngAfterViewInit() { // 模板中的元素已创建完成
        console.dir(this.elementRef.nativeElement.querySelector('div'));
      }
    }
```
优化：使用 viewChild 来关联模版中特定元素
#### @ViewChild
```
@Component({
    selector: 'sample',
    template: `
        <span #tref>I am span</span>    // 模板引用变量 #tref 标记一个 DOM 元素
    `
})
export class SampleComponent implements AfterViewInit {
    @ViewChild("tref", {read: ElementRef}) //  @ViewChil中关联模版，read(可选)中表示引用类型
    tref: ElementRef;    

    ngAfterViewInit(): void {
        // outputs `I am span`
        console.log(this.tref.nativeElement.textContent);
    }
}
```
再度优化： 使用 renderer 对象提供的 API 来减少应用层和渲染层之间的耦合
注：在 Angular 4.x+ 版本，我们使用 Renderer2 替代 Renderer (Angular V2)
```
    import { Component, ElementRef, ViewChild, AfterViewInit, Renderer } from '@angular/core';

    @Component({
      selector: 'my-app',
      template: `
        <h1>Welcome to Angular World</h1>
        <div #greet>Hello {{ name }}</div>
      `,
    })
    export class AppComponent {
      name: string = 'Semlinker';

      @ViewChild('greet')
      greetDiv: ElementRef;

      constructor(private elementRef: ElementRef, private renderer: Renderer) { } // 引入renderer

      ngAfterViewInit() {
        // 修改之处
        // this.greetDiv.nativeElement.style.backgroundColor  = 'red';
        this.renderer.setElementStyle(this.greetDiv.nativeElement, 'backgroundColor', 'red');
      }
    }
```
