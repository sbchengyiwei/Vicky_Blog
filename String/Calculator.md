### 1 Calculator

#### 1.1 Calculator with 2 stack (通用解法)

```java
/*
stack1(store number)||
stack2(store operations)||
notice: - 2  + (-3) * 2 = -12  // when we meet the + we need to calculate the - 2 first
----------------------
traverse the string
    if meet number : save the number
    if meet ( : save(
    if meet ) : calculate the expression in the expression and pop the (
    if meet operator :
                *check the case -2 + 3 or (-2 + 3)  i == 0 or i-1 is a ( we need put a leading 0 to stack
                then:
                if current operator's  has lower or equal precedence than the previous operator (eg. 0 - 2 + 3 or 2 * 3 + 3)  [ when we calculate 1 + 2 * 3 ^3 we need add a loop --- until we meet a ( or stack2 is null ]
                else we just put the operand and operator to each stack
    if (stack2 is null) return stack1.peek
    if not null calculate until stack2 is null then reutnr stack1.peek

----------------------
*/
```

##### [772. Basic Calculator III](https://leetcode.com/problems/basic-calculator-iii/)

```java
class Calculator2Stack {

  public static int calculate(String s) {
    if (s == null || s.length() == 0) return 0;
    Map<Character, Integer> map = new HashMap<>();
    map.put('+', 0);
    map.put('-', 0);
    map.put('*', 1);
    map.put('/', 1);

    Stack<Integer> numbers = new Stack<>();
    Stack<Character> operators = new Stack<>();
    for (int i = 0 ; i< s.length(); i++) {
      char c = s.charAt(i);
      if (c == '(') {
        operators.push(c);
      } else if (c == ')') {
        while(operators.peek() != '(') {
          cal(numbers, operators);
        }
        operators.pop(); // pop('(')
      } else if (Character.isDigit(c)) {
        int num = 0;
        while (i < s.length() && Character.isDigit(s.charAt(i))) {  // out of range
          num = num * 10 + (s.charAt(i++) - '0');
        }
        i--;
        numbers.push(num);  // 记得 push
      } else {
        if (i == 0 || s.charAt(i - 1) == '(') numbers.push(0);
        while(!operators.isEmpty() && operators.peek() != '(' ) {  // 1 + 3 * 2 -2 要用 while 因为不是算到  3* 2 就停止 而是都算完才停
          if (map.get(operators.peek()) >= map.get(c)) cal(numbers, operators);
          else break;
        }
        operators.push(c); // 记得 push
      }
    }
    while (!operators.isEmpty()) {
      cal(numbers, operators);
    }
    return numbers.peek();
  }


  public static void cal (Stack<Integer> stack1, Stack<Character> stack2) {
    int b = stack1.pop();
    int a = stack1.pop();
    char op = stack2.pop();
    int ans = 0;
    switch(op){
      case '+':
        ans = a + b;
        break;
      case '-':
        ans = a - b;
        break;
      case '*':
        ans = a * b;
        break;
      case '/':
        ans = a / b;
        break;

    }

    stack1.push(ans); 
  }
}
```

#### 1.2 Calculator without Stack (没有括号)

```java
/*
  1. num -> cur = num
  2. is operator / cur is the last number
      2.1 update the last number
            +- : res += last, last = operator(+ -) * cur
            × / :last = last ×/ cur
      2.2 update operation = c
      2.3 update cur = 0
  3. res += last
  4.return res
*/
```

##### [227. Basic Calculator II](https://leetcode.com/problems/basic-calculator-ii/)

```java
public class CalculatorNonStack {
    public int calculate(String s) {
      if (s == null || s.isEmpty()) return 0;
      int length = s.length();

      int cur = 0, last = 0, result = 0;
      char operation = '+';

      for (int i = 0; i < s.length(); i++) {
        char c = s.charAt(i);

        //1. not last number
        if (Character.isDigit(c)) {
          cur = (cur * 10) + (c - '0');
        }

        //2. non-number or this is the last number
        if (!Character.isDigit(c) || i == length - 1) {
          // 2.1update last
          if (operation == '+' || operation == '-') {
            result += last;
            last = (operation == '+') ? cur : -cur;
          } else if (operation == '*') {
            last = last * cur;
          } else if (operation == '/') {
            last = last / cur;
          }

          //2.2 update operation and current number
          operation = c;
          cur = 0;
        }

      }
      // remember add the lastest number
      result += last;
      return result;
  }
}
```

