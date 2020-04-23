# double 保留任意位的小数

```java
// ###################.####  小数点后面的#控制double 小数长度
double number =2000.2314560;
			String numberStr;
			if (((int) number * 1000) == (int) (number * 1000)) {
				//如果是一个整数
				numberStr = String.valueOf((int) number);
			} else {
				DecimalFormat df = new DecimalFormat("###################.####");
				numberStr = df.format(number);
			}
		System.err.println(numberStr);
```

