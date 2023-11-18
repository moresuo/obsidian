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
>```
> 

%% 这是注释 %%`














