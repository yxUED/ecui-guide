###  table控件  下面是项目中的例子
```
  html:
  <div ui="type:table;id:sourceListTable">
    <table>
            <thead>
                <tr>
                    <th>车辆编号</th>
                    <th class="car-info">车辆信息</th>
                    <th>车辆类型</th>
                    <th>归属车商</th>
                    <th>车商标价</th>
                    <th>入场状态</th>
                    <th>入场时长</th>
                    <th>管理</th>
                </tr>
            </thead>
            <tbody>
                <!-- for: ${sourceSearchList.record} as ${item}-->
                <tr data-id="${item.carId}">
                    <td>${item.carNumber}</td>
                    <td class="car-info">
                        <!-- use: sourceSearchTableItem(headImage=${item.headImage},title=${item.brandDescription}, sub="上牌："+${item.firstLicenseTag | dateFormat('yyyy-MM')} +"|" + ${item.odographNum | toFixed(10000,2)}+"万公里", detail="VIN："+${item.vinCode}) -->
                    </td>
                    <td> ${item.type | parseBaseInfo('carType')} </td>
                    <td> ${item.shopName}</td>
                    <td> ${item.exhibitionPrice | toFixed(10000,2)}万 </td>
                    <td> ${item.marketStatus | parseBaseInfo('marketStatus')} </td>
                    <td> ${item.entryMarketTime| defaultText}天 </td>
                    <td>
                        <!-- var: statusMap= {"1":true,"2":true,"3":false,"4":false,"5":true} -->
                        <!-- var:carStatus= ${statusMap}[${item.marketStatus}] ? {class:'enter', text:'入场'} :{class:'out', text:'出场'}-->
                        <a  class='${carStatus.class}' data-item-id='${item.carId}'>${carStatus.text} </a>
                        <!--if: ${item.marketStatus}=== 3 -->
                        |<a href="#source.card.edit~id=${item.carId}">打印展示证</a>
                        <!-- /if -->
                    </td>
                </tr>
                <!-- /for -->
            </tbody>
        </table>
  </div>
```

```
js代码(这些代码写都 onafterrender 里面):   

        //非chrome浏览器下表格错乱，重新resize
        if (!ecui.chrome) {
            ecui.get('sourceListTable').$resize();
        }   

       //点击事件
       ecui.get('sourceListTable').onclick = function(ev) {
            var target = ev.target;
            while (target.tagName !== 'TR') {
                target = ecui.dom.getParent(target);
            }
            //点击 a 标签
            if (ev.target.tagName === "A") {
            //给a标签增加对应的 class 执行不同的事件
                if (ecui.dom.hasClass(ev.target, 'enter')) {
                    //执行入场操作
                    var id = ecui.dom.getAttribute(ev.target, 'data-item-id');
                    _this.changeStatus(context, 'enter', id);
                } else if (ecui.dom.hasClass(ev.target, 'out')) {
                    //执行启用操作
                    var id = ecui.dom.getAttribute(ev.target, 'data-item-id');
                    _this.changeStatus(context, 'out', id);
                }
            }
             //点击了tr
             else if (target.tagName === "TR") {
                //获取tr的 data-id
                var id = ecui.dom.getAttribute(target, 'data-id');
                //如果存在data-id 就执行
                if (id) {
                    ecui.esr.redirect('source.detail~id=' + id);
                }
            }
        };


```
