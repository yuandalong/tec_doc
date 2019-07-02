# setInterval 设置时间间隔
setInterval() 方法可按照指定的周期（以毫秒计）来调用函数或计算表达式。

setInterval() 方法会不停地调用函数，直到 clearInterval() 被调用或窗口被关闭。由 setInterval() 返回的 ID 值可用作 clearInterval() 方法的参数。

```javascript
var int=setInterval("clock()",50)
function clock()
{
  var t=new Date()
  document.getElementById("clock").value=t
  //此处可调用clearInterval来停止定时
  if(条件成立)
  {
    clearInterval(int)
  }
}
```