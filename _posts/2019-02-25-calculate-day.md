使用库函数可以计算两个时间的毫秒差值，在转换成天数就可以了。
```java
public class CalDays {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		System.out.print(calDay(2008, 3, 4, 2001, 5, 23));
	}
	
	public static int calDay(int y1, int m1, int d1, int y2, int m2, int d2) {
		int result = 0;
		
		Date date1 = new Date(y1, m1, d1); //先转换成日期对象
		Date date2 = new Date(y2, m2, d2);
		
		long dis = date1.getTime() - date2.getTime();//时间毫秒差值
		
		result = Math.abs( (int) (dis / 1000 / 24 / 60 / 60));//转换为天数
		
		return result;
	}

}
```

使用简单的加减乘除实现两个日期（年月日）的相隔天数。
1. 相同年份只需要算当年的天数再相减
2. 连续的年份算开始年份当年的剩下时间加上结束年份当年已过的天数
3. 不连续的年份在连续的年份的基础上还要加上中间的年份的天数

闰年要分开算，闰年366天，否则365年。2月分开算，闰年29天，平年28天，否则是根据积偶月份算30天或31天。


```java
public class CalDays2 {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		//System.out.print(calDay(2001, 5, 23, 2008, 3, 4)); //2477
		System.out.print(calDay(2001, 1, 1, 2001, 5, 23));
	}
	
	public static int calDay(int y1, int m1, int d1, int y2, int m2, int d2) { //1为开始时间，2为结束时间
		int result = 0;
		
		if(y1 < y2) { //相差2年及以上
			for(int i = y1 + 1; i < y2; i++) {
				if(isLeapYear(i)) {//闰年366天
					result += 366;
				}else {
					result += 365;
				}
			}
		}
		if(y1 != y2) { //不同年份
			if(isLeapYear(y1)) {//闰年下
				result += 366 - calDaysCurrentYear(y1, m1, d1) + calDaysCurrentYear(y2, m2, d2);
			} else {
				result += 365 - calDaysCurrentYear(y1, m1, d1) + calDaysCurrentYear(y2, m2, d2);
			}
		} else {
			result = calDaysCurrentYear(y2, m2, d2) - calDaysCurrentYear(y1, m1, d1);
		}
		
		return result;
	}
	
	public static int calDaysCurrentYear(int year, int month, int day) { //当年过了多少天
		int res = 0;
		month -= 1; //当月不算
		if(month > 1) { //超过2月份了
			if(month % 2 == 0) { //偶数月
				res = (month / 2 - 1) * 30 + month / 2 * 31;
			} else { 
				res = (month / 2 - 1) * 30 + (month / 2 + 1) * 31;
			}
			if(isLeapYear(year)) { //另算闰年的二月份
				res += 29;
			} else {
				res += 28;
			}
		} else if(month == 1) {
			res = 31;
		}
		res += day;
		return res;
	}
	
	public static boolean isLeapYear(int year) { //是否是闰年
		if(year % 4 == 0 && year % 100 != 0 || year % 400 == 0)
			return true;
		return false;
	}

}
```

