
#  旋转字符串II

## 题目链接

[旋转字符串II](https://www.lintcode.com/problem/1790/?_from=ladder&fromId=161)

## 题目描述
给出一个字符串(以字符数组形式给出)，一个右偏移和一个左偏移，根据给出的偏移量循环移动字符串，并保存在一个新的结果集中返回。(left offest表示字符串向左的偏移量，right offest表示字符串向右的偏移量，左偏移量和右偏移量计算得到总偏移量，在总偏移量处分成两段字符串并交换位置)。

样例
样例 1:
```shell
输入：str ="abcdefg", left = 3, right = 1
输出："cdefgab"
解释：左偏移量为3，右偏移量为1，总的偏移量为向左2，故原字符串向左移动，变为"cdefg" + "ab"。
```

样例 2:
```shell
输入：str="abcdefg", left = 0, right = 0
输出："abcdefg" 
解释：左偏移量为0，右偏移量为0，总的偏移量0，故字符串不变。
```
样例 3:
```shell
输入：str = "abcdefg",left = 1, right = 2
输出："gabcdef"
解释：左偏移量为1，右偏移量为2，总的偏移量为向右1，故原字符串向右移动，变为"g" + "abcdef"。
```
## 解题思路
| 方法  |描述 |时间复杂度 |空间复杂度|
|---|---|---|---|
|  左右翻转法 | 下文代码实现  | $O(1)$|$(1)$|
|  第一步 | 计算offset,根据正负计算截取的位置  | $O(1)$|$(1)$|
|  第二步 | `offset>0,则offset= offset` | $O(1)$|$(1)$|
|  第三步 | `offset<0,则offset =offset+length`  | $O(1)$|$(1)$|
|  第四步|  `result = right+left` | $O(1)$|$(1)$|


## 代码实现

```java

public class Solution {
    /**
     * @param str: An array of char
     * @param left: a left offset
     * @param right: a right offset
     * @return: return a rotate string
     */
    public String RotateString2(String str, int left, int right) {
        // write your code here
        if (str == null || str.length() == 0) {
            return "";
        }
        int length = str.length();
        int offset = (left - right) % length;
        String leftStr = "";
        String rightStr = "";
        if (offset > 0) {
            leftStr = str.substring(0, offset);
            rightStr = str.substring(offset, length);
        } else if (offset < 0) {
            int realyOffset = offset + length;
            leftStr = str.substring(0, realyOffset);
            rightStr = str.substring(realyOffset, length);
        } else {
            return str;
        }

        return rightStr + leftStr;
    }
}
```