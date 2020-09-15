> 利用两个栈计算表达式的值
>
> eg:
>
> 输入：`(1+((2+3)*(4*5)))`
>
> 输出：`101.0`



实现：

```java
import java.util.Stack;
/**
 * Dijkstra的双栈算术表达式求值算法
 * @author: wangze
 * @date: 2020年9月15日 上午1:19:03
 */
public class TwoStack {
	
	public static void main(String[] args) {
		Double result = solution("(1+((2+3)*(4*5)))");
		System.out.println(result);
	}

	public static Double solution(String expression) {
		Stack<String> ops = new Stack<String>();
		Stack<Double> vals = new Stack<Double>();
		for (int i = 0; i < expression.length(); i++) {
			String s = (char) expression.getBytes()[i] + "";
			if (s.equals("(")) {
				//忽略左括号
			} else if (s.equals("+")) {
				ops.push(s);
			} else if (s.equals("-")) {
				ops.push(s);
			} else if (s.equals("*")) {
				ops.push(s);
			} else if (s.equals("/")) {
				ops.push(s);
			} else if (s.equals("sqrt")) {
				ops.push(s);
			} else if (s.equals(")")) {
				//弹出运算符和操作数，计算结果并压入栈
				String op = ops.pop();
				double v = vals.pop();
				if (op.equals("+")) {
					v = vals.pop() + v;
				} else if (op.equals("-")) {
					v = vals.pop() - v;
				} else if (op.equals("*")) {
					v = vals.pop() * v;
				} else if (op.equals("/")) {
					v = vals.pop() / v;
				} else if (op.equals("sqrt")) {
					v = Math.sqrt(v);
				}
				vals.push(v);
			} else {
				//非运算符非括号，作为double亚茹栈
				vals.push(Double.parseDouble(s));
			}
		}
		return vals.pop();
	}
}
```

