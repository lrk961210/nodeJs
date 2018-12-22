## ViewChild
log-view.component.ts :
```
  // ViewChild定义了一个视图查询，在视图的 DOM 中查找能匹配上的第一个元素或指令
  // logContainer对应 html模板里面的#logContainer
  @ViewChild('logContainer')
  logContainerEle: ElementRef; // logContainerEle 模板变量名
  
```

log-view.component.html
```
...
<div #logContainer>... </div>
...
```
