* 路由域页面跳转

  * 标签 navigator

  * ```js
    <navigtor url="跳转页面">
    ```

  * uni.navigateTo 保留当前页面,跳转到url页面

    eventChannel 向跳转的页面发送数据

    this.getOpenerEventChannel() 接收发送过来的数据
  
  * ```js
    // index 页，向 home 页面传数据
    uni.navigateTo({
        url:"跳转页面"
        success:function (res){
        	// eveentChannel 向被打开的页面传送数据
        	res.eventChannel.emit("getBill", that.confirmObj);
    	}
    })
    ```
    
  * ```js
    // home 页面，接收从 index 页面传来的参数
    const eventChannel = this.getOpenerEventChannel();
    eventChannel.once("getBill", function (res) {
          // 获取从index页传来的参数
    });
    ```
    
  * 
    
  * uni.redirecTo 关闭当前页面并跳转url页面
  
  * ```js
    uni.redirecTo({
        url:"跳转页面"
    })
    ```
  
  节点信息
  
  uni.createSelectorQuery()
  
  返回一个selectQuery实例，可以在实例上使用select等方法获取节点，并使用boundingClientRect等方法选择查询的信息
  
  示例
  
  ```js
  const query = uni.uni.createSelectorQuery().in(this);
  query.select('#id').boundingClientRect(data=>{
      console.log("得到布局位置信息" + JSON.stringify(data));
      console.log("节点离页面顶部的距离为" + data.top);
  }).exec()
  ```
  
* eventChannel 页面通信

  * eventChannel.emit() 处罚一个事件

  * eventChannel.once() 监听一个事件一次，触发后失效 
  * eventChannel.on() 持续监听一个事件
  * eventChannel.off() 取消监听一个事件，给出第二个参数时，只取消给出的监听函数，否则取消所有监听函数