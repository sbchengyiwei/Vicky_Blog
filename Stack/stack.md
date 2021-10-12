### 1 **stack 题目规律**

1.有特殊符号

2.顺序存储

3.就近原则

PS:灵活一点 其实 stack 可以用很多种形式来实现 根据实际来选择最合适的 比如 array 和 deque



### 2 **stack 题型**

#### Pattern1: 平衡符号 

#### [678.Valid Parenthesis String](https://leetcode.com/problems/valid-parenthesis-string/)

```java
//greedy
class Solution {
    public boolean checkValidString(String s) {
       int lo = 0, hi = 0;
       for (char c: s.toCharArray()) {
           lo += c == '(' ? 1 : -1;
           hi += c == ')' ? -1 : 1;
           if (hi < 0) break;
           lo = Math.max(lo, 0);
       }
       return lo == 0;
    }
}
//stack
class Solution {
    public boolean checkValidString(String s) {
        int opening = 0;
        int star = 0;
        for (char c: s.toCharArray()) {
            if (c == '(') opening += 1;
            else if (c == ')' && opening > 0) opening -= 1;
            else if (c == ')' && star > 0) star--;
            else if (c == ')') return false;
            else {
                star++;
                if (opening > 0) {
                    opening--;
                    star++;
                }
            }
        }
        return opening == 0 ? true: false;
    }
}
```

#### [32. Longest Valid Parentheses](https://leetcode-cn.com/problems/longest-valid-parentheses/)

```java
/*
Stack 求 Parentheses 的题就是 考虑三种情况：
 )() 明显错误
())右边多 [ pop时候栈为空两种处理 1.设置不为空判断 **2**.和上面一起当做错误处理]
(() 左边多  [记得存储状态]
*/
class Solution {
    public int longestValidParentheses(String s) {
        Stack<Integer> stack = new Stack<>();
        int start = 0; // start 作为区间起点一般都设为-1
        int res = 0;
        int index = 0;
        for (char c : s.toCharArray()) {
            if (c == '(') {
                stack.push(index);
            } else {
                if (stack.isEmpty()) {
                    start = index + 1;
                } else {
                    stack.pop();
                    if (stack.isEmpty()) res = Math.max(res, index - start + 1);
                    else res =  Math.max(res, index - stack.peek());
                }
            }
            index++;
        }
        return res;
    }
}
/*
还不会 走边例子
)()
(()
())
*/
```



#### Pattern2: 压栈匹配

> **注意：** 只有没有（）这种 才能肆无忌惮的放入 stack 否则都是（才 push 要考虑用一个全局 res 存储目前的东西 然后当）push 后才把 res 重置

#### [394. Decode String](https://leetcode-cn.com/problems/decode-string/)

```java
class Solution {
    public String decodeString(String s) {
        if (s == null || s.length() == 0) return "";
        Stack<Integer> stack1 = new Stack<>();
        Stack<String> stack2 = new Stack<>();
        String res = "";
        int idx = 0;
        while (idx < s.length()) {  //下次都用 idx 要不 number 和其他不能用 else if 而是两个 if
            if (Character.isDigit(s.charAt(idx))) {
                int num = 0;
                while(Character.isDigit(s.charAt(idx))) {
                    num = num * 10 + (s.charAt(idx++) - '0');  
                }
                stack1.push(num);
            } else if (s.charAt(idx) == '[') {
                stack2.push(res);
                res = "";
                idx++;
            } else if (s.charAt(idx) == ']') {
                int time = stack1.isEmpty() ? 1 : stack1.pop();
                StringBuilder temp = new StringBuilder(stack2.pop()); 
                for (int i = 0; i < time; i++) {
                    temp.append(res);
                }
                res = temp.toString();
                idx++;
            } else {
                res += s.charAt(idx++);
            }
        }
        return res;
    }
}
```



#### Pattern3: 表达式计算

>  **注意：** 只有没有（）这种 才能肆无忌惮的放入 stack 否则都是（才 push 要考虑用一个全局 res 存储目前的东西 然后当）push 后才把 res 重置

- 顺序表达式

  - 有括号

    #### [224. Basic Calculator](https://leetcode-cn.com/problems/basic-calculator/)

    ```java
    class Solution {
        public int calculate(String s) {
            Stack<Integer> stack = new Stack<>();
            int sign = 1;
            int num = 0;
            for (int i = 0; i < s.length(); i++) {
                if (Character.isDigit(s.charAt(i))) {
                    int n = s.charAt(i) - '0';
                    while (i + 1 < s.length() && Character.isDigit(s.charAt(i + 1))) {
                        n = n * 10 + (s.charAt(i+1) - '0');
                        i++;
                    }
                    num += sign * n;
                }
                if (s.charAt(i) == '-') sign = -1;
                if (s.charAt(i) == '+') sign = 1;
                if (s.charAt(i) == '(') {
                    stack.push(num);
                    stack.push(sign);
                    //注意 不仅是num ！！ sign 也要变为 1 ！！
                    num = 0;  
                    sign = 1;
                }
                if (s.charAt(i) == ')') {
                    num = num *stack.pop() + stack.pop();
                }
            }
            return num;
        }
    }
    ```

  - 没有括号

    #### [227. Basic Calculator II](https://leetcode-cn.com/problems/basic-calculator-ii/)

    > 注意最后一个数字也要放入最后一个判断 。 ` i == s.length() - 1` 也可以看成不仅字符会触发上一个字符运算 最后一个结束的信号也会触发

    ```
    class Solution {
        public int calculate(String s) {
            if (s == null || s.length() == 0) return 0;
            Stack<Integer> stack = new Stack<>();
            int res = 0;
            char sign = '+';
            int num = 0;
            for (int i = 0; i < s.length(); i++) {
                if (Character.isDigit(s.charAt(i))) {
                    num = s.charAt(i) - '0';
                    while (i + 1 < s.length() && Character.isDigit(s.charAt(i+1))) {
                        num = num *10 + s.charAt(i + 1) - '0';
                        i++;
                    }
                }
                if (!Character.isDigit(s.charAt(i)) && s.charAt(i) != ' ' || i == s.length() - 1) {
                    if (sign == '+') stack.push(num);
                    if (sign == '-') stack.push(-num);
                    if (sign == '*') stack.push(stack.pop() * num);
                    if (sign == '/') stack.push(stack.pop() / num);
                    sign = s.charAt(i);
                    num = 0;
                }
            }
            for(int i : stack) {
                res += i;
            }
            return res;  
        }
    }
    ```

  

- 逆波兰表达式(easy)

  #### [150. Evaluate Reverse Polish Notation](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)

  附两种方法：
  纯数组模拟栈实现(推荐)：

  ```java
  class Solution {
  	//纯数组模拟栈实现(推荐)   3 ms	36 MB
  	public static int evalRPN(String[] tokens) {
  		int[] numStack = new int[tokens.length / 2 + 1]; //数组最大长度为tokens.length / 2 + 1
  		int index = 0;
  		for (String s : tokens) {
  			switch (s) {
  			case "+":
  				numStack[index - 2] += numStack[--index];
  				break;
  			case "-":
  				numStack[index - 2] -= numStack[--index];
  				break;
  			case "*":
  				numStack[index - 2] *= numStack[--index];
  				break;
  			case "/":
  				numStack[index - 2] /= numStack[--index];
  				break;
  			default:
  				// numStack[index++] = Integer.valueOf(s);
  				//valueOf改为parseInt，减少自动拆箱装箱操作
  				numStack[index++] = Integer.parseInt(s);
  				break;
  			}
  		}
  		return numStack[0];
  	}
  }
  
  ```

  栈实现：

  ```java
  class Solution {
  	// 栈实现   8 ms	36.7 MB	
  	public static int evalRPN(String[] tokens) {
  		Stack<Integer> numStack = new Stack<>();
  		Integer op1, op2;
  		for (String s : tokens) {
  			switch (s) {    //switch代替if-else，效率优化
  			case "+":
  				op2 = numStack.pop();
  				op1 = numStack.pop();
  				numStack.push(op1 + op2);
  				break;
  			case "-":
  				op2 = numStack.pop();
  				op1 = numStack.pop();
  				numStack.push(op1 - op2);
  				break;
  			case "*":
  				op2 = numStack.pop();
  				op1 = numStack.pop();
  				numStack.push(op1 * op2);
  				break;
  			case "/":
  				op2 = numStack.pop();
  				op1 = numStack.pop();
  				numStack.push(op1 / op2);
  				break;
  			default:
  				numStack.push(Integer.valueOf(s));
  				break;
  			}
  		}
  		return numStack.pop();
  	}
  }
  ```

  

#### Pattern4: 迭代极值（见 [monotone stack](https://github.com/sbchengyiwei/Vicky_Blog/blob/main/Monotone%20Stack/Monotone%20Stack.md)）

