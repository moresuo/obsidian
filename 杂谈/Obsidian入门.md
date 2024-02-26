## 二级标题

**ctrl+p:打开命名面板**
**ctrl+n:创建新的笔记**
**ctrl+s:保存笔记**
**创建文件夹相当于创建一个知识库**
**#加空格表示标题**
"<font color=red>使用font标签设置color属性</font>"
***
**连续3个※号表示分割线**
**ctrl+b:字体加粗**
[链接名](链接地址)：超链接
![图片名](链接地址)：图片链接
**‘-’加空格：列表**
**'>':引用**
`行内代码` 

	java 代码块
	首行一个tab键
	public class Test{
		public static void main(String[] args){
		}
	}
```java
public class Hello{
	public static void main(String[] args){
		
	}

}
```
**ctrl+l:任务列表**
- [x]  ✅ 2023-11-14
**标签：‘#’+标签名**
**ctrl+',':打开设置**
<mark style="background: [[FF5582A6]];">双向链接：‘[[]]’，#代表连接标题，^代表指定文本块，|代表链接别名，连接到其他知识库</mark>
![[每日任务#^0d9285|连接并显示目标文本]]
## 自定义快捷键
>F3:代码块
>Ctrl+k:插入链接
>F4:插入标注
>Ctrl+y:行内代码
>Ctrl+j:导出为PDF
>Ctrl+g:查看关系图谱
>Ctrl+n:新建笔记
>Ctrl+/:注释
>Ctrl+l: 任务列表
>F6: 跳出悬浮窗
><mark style="background: [[FFB86CA6]];">在[]加-: 折叠标注</mark>
>Ctrl+shift+v: 取出选中内容空行

> [!info] Title
> >asd
>>```
> >class Solution {
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int left=0;//记录窗口最左的下标
        int result=Integer.MAX_VALUE;//窗口初始大小
        int sum=0;//记录窗口中值的和
        for(int right=0;right<nums.length;right++){
            sum+=nums[right];//扩大窗口，增值
            while(sum>=target){//循环缩小窗口至sum小于目标值
                result=Math.min(result,right-left+1);//判断出出窗口最小长度
                sum-=nums[left++];//窗口缩小同时sum要减小
            }
        }
        return result==Integer.MAX_VALUE?0:result;
    }
}

```java
class Solution {

    public int search(int[] nums, int target) {

        if(target<nums[0]||target>nums[nums.length-1]){//因为是有序数组，当目标值不在数组范围内就直接返

            return -1;
        }
        int left=0;//左指针
        int right=nums.length-1;//右指针
        while(left<=right){//左闭右闭模式
            int mid=(left+right) >> 1;//右移一位相当于除以二，这种速度更快
            if(target<nums[mid]){//目标值在左边，缩小右边上限
                right=mid-1;
            }else if(target>nums[mid]){//目标值在右边，提升左边下限
                left=mid+1;
            }else{//找到目标值
                return mid;
            }
        }
        //循环结束，未找到
        return -1;
    }
}
```
> class Solution {
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int left=0;//记录窗口最左的下标
        int result=Integer.MAX_VALUE;//窗口初始大小
        int sum=0;//记录窗口中值的和
        for(int right=0;right<nums.length;right++){
            sum+=nums[right];//扩大窗口，增值
            while(sum>=target){//循环缩小窗口至sum小于目标值
                result=Math.min(result,right-left+1);//判断出出窗口最小长度
                sum-=nums[left++];//窗口缩小同时sum要减小
            }
        }
        return result==Integer.MAX_VALUE?0:result;
    }
}

  

}
>
>
> 



> [!这是标注] 这是标注
>```java
>	class Solution {
>
    public int search(int[] nums, int target) {
>
  >      if(target<nums[0]||target>nums[nums.length-1]){//因为是有序数组，当目标值不在数组范围内就直接返回
>
            return -1;
>
        }
>
        int left=0;//左指针
>
        int right=nums.length-1;//右指针
>
        while(left<=right){//左闭右闭模式
>
            int mid=(left+right) >> 1;//右移一位相当于除以二，这种速度更快
>
            if(target<nums[mid]){//目标值在左边，缩小右边上限
>
                right=mid-1;
>
            }else if(target>nums[mid]){//目标值在右边，提升左边下限

                left=mid+1;

            }else{//找到目标值

                return mid;

            }

        }

        //循环结束，未找到

        return -1;

    }

  

}
 用户端子系统可以包括以下构件：
    用户界面构件：负责提供用户交互的界面，如菜单、购物车、订单等
    用户业务逻辑构件：负责处理用户的业务逻辑，如登录、下单、支付等
    用户数据访问构件：负责访问用户的本地数据，如缓存、配置等
 商家端子系统可以包括以下构件：
    商家界面构件：负责提供商家交互的界面，如菜品管理、订单管理等
    商家业务逻辑构件：负责处理商家的业务逻辑，如接单、配送、回复评价等
    商家数据访问构件：负责访问商家的本地数据，如缓存、配置等
 中间件子系统可以包括以下构件：
    消息推送构件：负责向用户或商家推送消息，如订单状态、评价等
    短信通知构件：负责向用户或商家发送短信通知，如验证码、订单提醒等
    支付接口构件：负责提供支付接口，如微信支付、支付宝支付等
    地图接口构件：负责提供地图接口，如定位、导航等
    日志记录构件：负责记录系统的日志，如请求、响应、异常等
    监控报警构件：负责监控系统的运行状态，如性能、错误、警告等
 微服务子系统可以包括以下构件：
    服务注册与发现构件：负责注册和发现微服务，如Eureka、Zookeeper等
    负载均衡构件：负责实现微服务的负载均衡，如Ribbon、Nginx等
    熔断降级构件：负责实现微服务的熔断降级，如Hystrix、Sentinel等
    配置中心构件：负责管理微服务的配置，如Spring Cloud Config、Apollo等
    链路追踪构件：负责追踪微服务的调用链路，如Zipkin、Skywalking等
 数据库子系统可以包括以下构件：
    MySQL构件：负责存储系统的核心业务数据，如用户、订单、菜品等
    Redis构件：负责存储系统的缓存数据，如验证码、菜品列表等
    MongoDB构件：负责存储系统的非结构化数据，如日志、评价等

我希望这对你有帮助。如果你有其他的问题，欢迎继续和我聊天。😊
>```
> 
|   |   |
|---|---|
|登录/退出|内部员工必须登录后,才可以访问系统管理后台|
`


 登录/退出：内部员工必须登录后, 才可以访问系统管理后台 
 













