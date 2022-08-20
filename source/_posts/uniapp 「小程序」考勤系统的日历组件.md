---
title: 'uniapp 「小程序」考勤系统的日历组件'
date: 2021-03-04 15:50:56
summary: '用uniapp开发钉钉上的日历组件。'
tags: 
	- uniapp
---

由于是用uniapp写的，所以在网页上也可以用，先上预览图
![日历.gif](https://img-blog.csdnimg.cn/img_convert/4d5e029b6e47d2f4322b964e7292b10a.gif)

主要功能：
   - 点击选中样式
   - 滑动跳转月份
   - 点击非本月日期跳转相应月份

## 一、css部分

css样式原理如下，红色框为用户视图。拖拉的时候改变left数值就可以了。

![视图.png](https://img-blog.csdnimg.cn/img_convert/ece1b1280db4402ef8a2d2296b208b41.png)

>小tips：
当宽度为自适应，不知道具体数值，而需要用宽度计算时，可以用`padding-bottom`。这样我们就可以一行放下七个圆形了。
```css
.day-item{
  width: 14%;
  display: block;
  border-radius: 50%;
  padding-bottom: 100%;
  height: 0;
}
```
所有css如下：
数字偏上是预留位置给当天的状态显示。
```scss
.module{
  width: 92%;
  margin: 15px auto;
  background-color: #fff;
  border-radius:16px;
  padding: 30rpx 20rpx;
  overflow: hidden;

  .threeMonth{
    display: flex;
    width: 300%;
    position: relative;
  }
  
  .title{
    font-size: 35rpx;
    font-weight: bold;
    padding-bottom: 30rpx;
  }
  
  .day{
    display: flex;
    position: relative;
    width: 100%;
    justify-content: space-around;
    flex-wrap: wrap;
    text-align: center;
    align-content: flex-start;
    
    .active{
      background-color: #1972F0;
      color: #fff;
    }
  }

  
  .day-item{
    width: 14%;
    text{
      display: block;
      border-radius: 50%;
      width: 100%;
      padding-top: calc(50% - 1em);
      padding-bottom: calc(50% + 1em);
      height: 0;
    }
  }

}
```

## 二、template部分
```html
<view class="day">
  <view class="day-item" v-for="(item,index) in ['日','一','二','三','四','五','六']">
      {{item}}
  </view>
</view>

<!-- 日期 -->
<view 
	class="threeMonth"    
	@touchstart='touchstart'
	@touchmove='touchmove' 
	@touchend='touchend' 
	:style="'left:calc(-100% + '+dayLeft+'px)'"
>
    <!-- 遍历集合三个月的列表 -->
	<view class="day" 
		v-for="(item,index) in monthList"
		:key="index"
	>
        <!-- 遍历每个月的天数 -->
		<view 
			class="day-item" 
			v-for="(item2,index2) in item" 
			:key="index2"
			@click="dayClick(index2)"
		>
            <!-- 不是本月的天数颜色为灰色 -->
			<text 
              :class="item2.className" 
              :style="item2.fromMonth=='nowMonth'?'':'color:#c8c9cc;'"
            >
              {{item2.day}}
            </text> 
		</view>
	</view>
</view>
```

## 三、js部分

功能与解释都在注释中写明。
```js
<script>
	export default {
		data() {
			return {
				nowYear:new Date().getFullYear(),//获取年份
				nowMonth:new Date().getMonth()+1,//获取月份
				monthList:[],
				dayLeft: 0,
				startLeft: 0
			}
		},
		created() {
			/*调用初始化当前考勤*/
			this.getThreeMonth();
		},
		methods: {
			// 日期模块点击
			touchstart(e){
				// 记录初始点击位置
				this.startLeft = e.touches[0].pageX
			},
			// 日期模块拖动
			touchmove(e){
				this.dayLeft = e.touches[0].pageX - this.startLeft
			},
			// 日期模块松手
			touchend(e){
				// 根据移动距离判断跳转上一月还是下一月
				if(this.dayLeft>100 )this.syy()
				if(this.dayLeft<-100 )this.xyy()
				this.dayLeft = 0
			},
			/*获取上一月*/
			syy(){
				if (this.nowMonth==1){
					this.nowYear=parseInt(this.nowYear)-1;
					this.nowMonth=12;
					this.getThreeMonth();
					return;
				}
				this.nowMonth=parseInt(this.nowMonth)-1;
				this.getThreeMonth();
			},
			/*获取下一月*/
			xyy(){
				if (this.nowMonth==12){
					this.nowYear=parseInt(this.nowYear)+1;
					this.nowMonth=1;
					this.getThreeMonth();
					return;
				}
				this.nowMonth=parseInt(this.nowMonth)+1;
				this.getThreeMonth();
			},

			/*闰年 时间判断*/
			isLeap(year) {
				return year % 4 == 0 ? (year % 100 != 0 ? 1 : (year % 400 == 0 ? 1 : 0)) : 0;
			},

			//获取某月日期
			getCalendar(Year,Month){
				//每个月的天数
				var days_per_month = new Array(31, 28 + this.isLeap(Year), 31, 30, 31, 30, 31, 31, 30, 31, 30, 31); 
				var dateList = []; 
				
				//获取本月的一号是星期几 0星期天
				var s=new Date(Year + '/' + Month + '/' + '01').getDay();
				//上月月份
				var lastMonth = Month==1? 12: Month-1
				
				// 上月天数
				var lastMonthDay = days_per_month[lastMonth - 1]
				// 补上 上月日期
				for(var i = s-1; i >= 0; i--) {
          var day = parseInt(lastMonthDay)-i;
          dateList.push({
              day,
              fromMonth: 'lastMonth',
              className: ''
          })//获取上月末尾天数  补齐本月日历显示
				 }
				 
				// 当月天数
				var nowMonthDay = days_per_month[Month - 1]
				
				// 添加当月日期
				for(var j = 0; j < nowMonthDay; j++) {
					dateList.push({
						day: j+1,
						fromMonth: 'nowMonth',
						className: ''
					})
				}
				
				//获取本月最后一天是星期几 0星期天
				var l =new Date(Year + '/' + Month + '/' + nowMonthDay).getDay();
				
				if(l < 6){
					// 补上 下月日期
					for(var i = 1; i <= 6-l; i++) {
            dateList.push({
                day: i,
                fromMonth: 'nextMonth',
              className: ''
            })
          }
				}
				
				return dateList
			},
			// 获取三月日期
			getThreeMonth(){
				let year,month
				this.monthList = []
				
				// 获取上一月日历
				if (this.nowMonth==1){
					year = parseInt(this.nowYear)-1;
					month = 12
				}else{
					year = this.nowYear
					month = parseInt(this.nowMonth)-1;
				}
				this.monthList.push(this.getCalendar(year,month))
				
				// 获取当前月日历
				this.monthList.push(this.getCalendar(this.nowYear,this.nowMonth))
				
				// 获取下一月日历
				if (this.nowMonth==12){
					year = parseInt(this.nowYear)+1;
					month = 1
				}else{
					year = this.nowYear
					month = parseInt(this.nowMonth)+1;
				}
				this.monthList.push(this.getCalendar(year,month))
			},
			
			//点击某一天
			dayClick(Index){
				
				// 如果 点击本月的日期
				if(this.monthList[1][Index].fromMonth=='nowMonth'){
					this.monthList[1].map((item,inx)=>{
						if(Index == inx){
							item.className = 'active'
						}else{
							item.className = ''
						}
					})
					return
				}
				
				//点击 哪一天
				let day = this.monthList[1][Index].day
				
				if(this.monthList[1][Index].fromMonth=='nextMonth'){
					// 如果 点击下一月的日期 跳转下一月
					this.xyy()
				}else if(this.monthList[1][Index].fromMonth=='lastMonth'){
					// 如果 点击上一月的日期 跳转上一月
					this.syy()
				}
				
				//对应日期 选中状态
				let indx = this.monthList[1].findIndex(e => e.fromMonth=='nowMonth'&&e.day==day)
				this.monthList[1][indx].className = 'active'
				
			},
		},
	}
</script>
```
