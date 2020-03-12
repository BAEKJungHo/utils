## 날짜 처리 함수 

  ```java 
  package util;

import java.text.SimpleDateFormat;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.Calendar;
import java.util.Date;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class DateHandler {
	private static final Logger LOG = LoggerFactory.getLogger(DateHandler.class);
	// 어제 시간
	public String beforeTime() {
		LocalDateTime yTime = LocalDateTime.now();
		yTime = yTime.minusDays(1);
    	DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm");	
    	return yTime.format(dtf);
	}
	
	// 어제 시간
	public String beforeDay() {
		LocalDateTime yDay = LocalDateTime.now();
		yDay = yDay.minusDays(1);
    	DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd");	
    	return yDay.format(dtf);
	}
	
	// 전 달
	public String beforeMonth() {
		LocalDateTime yMonth = LocalDateTime.now();
		yMonth = yMonth.minusMonths(1);
    	DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM");	
    	return yMonth.format(dtf);
	}
	
	// 어제 날짜
	public String yesterday() {
		LocalDateTime yesterday = LocalDateTime.now();
		yesterday = yesterday.minusDays(1);
    	DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd");	
    	return yesterday.format(dtf);
	}
	
	// 현재 시간
	public String currentTime() {
		LocalDateTime cTime = LocalDateTime.now();	
    	DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm");
    	return cTime.format(dtf);
	}
	
	// 현재 날짜
	public String getToday() {
		LocalDateTime today = LocalDateTime.now();	
    	DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd");
    	return today.format(dtf);
	}
	
	// 현재 달
	public String getCurrentMonth() {
		LocalDateTime curMonth = LocalDateTime.now();	
    	DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM");
    	return curMonth.format(dtf);
	}
	
	// 문자열을 날짜로 변환
	public Date transStringToDate(String date) {
		LOG.debug(date);
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm");
		Date cpDate = null;
		try {
			cpDate = sdf.parse(date);
			LOG.debug("cpDate : " + String.valueOf(cpDate));
		} catch(Exception e) {}
		return cpDate;
	}
	
	// 문자열을 날짜로 변환 yyyy-MM-dd
	public Date transStringToDateNotHours(String date) {
		LOG.debug(date);
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
		Date cpDate = null;
		try {
			cpDate = sdf.parse(date);
			LOG.debug("cpDate : " + String.valueOf(cpDate));
		} catch(Exception e) {}
		return cpDate;
	}
	
	// 날짜를 문자열로 변환
	public String transDateToString(Date date) {
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm");
		String time = sdf.format(date);
		return time;
	}
	
	// 날짜를 문자열로 변환 yyyy-MM-dd
	public String transDateToStringNotHours(Date date) {
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
		String time = sdf.format(date);
		return time;
	}
	
	// 오전 오후 구하기 
	public String getAmPm(Date time) {
		Calendar cal = Calendar.getInstance();
		cal.setTime(time);
		LOG.debug("getAmPm() : " + String.valueOf(cal.getTime()));
		if (cal.get(Calendar.AM_PM) == Calendar.AM)
			return "am";
		else if(cal.get(Calendar.AM_PM) == Calendar.PM)
			return "pm";
		return null;
	}
	
	// 오전 오후 시간
	public String getNumericTime(String amPm) {
		String timeStr = null;
		if (amPm.equals("am"))
			timeStr = " 09:00:00";
		if (amPm.equals("pm"))
			timeStr = " 18:00:00";
		return timeStr;
	}
	
	// 납품 전용 시간 
	public String getReleaseTime(String time) {
		String releaseTime = null;
		if(time.compareTo(beforeDay()) == 0) {
			releaseTime = getToday() + " 10:00:00";
			return releaseTime;
		}
		return time;
	}
	
	// 청구 전용 시간
	public String getChargeTime(String time) {
		String chargeTime = null;
		if(time.equals(beforeMonth())) {
			chargeTime = getCurrentMonth() + "-01";
			return chargeTime;
		}
		return time;
	}
	
	// 지급 전용 시간
	public String getPayTime(String time) {
		String payTime = null;
		if(time.equals(beforeMonth())) {
			payTime = getCurrentMonth() + "-01";
			return payTime;
		}
		return time;
	}
}
  ```

## 시작과 끝 사이에 존재하는 날짜 구하기(시작과 끝 포함)

```java
/**
* 시작과 끝 날짜 사이에 존재하는 날짜 구하기
* @param startDate
* @param endDate
* @return
*/
public static List<String> findHiddenDateBetweenStartAndEnd(String startDate, String endDate) {
	final String DATE_PATTERN = "yyyy-MM-dd";
	List<String> dates = new ArrayList<>();
	SimpleDateFormat sdf = new SimpleDateFormat(DATE_PATTERN);

	Date parsedStartDate = null;
	Date parsedEndDate = null;

	try {
	    parsedStartDate= sdf.parse(startDate);
	    parsedEndDate = sdf.parse(endDate);
	} catch (ParseException e) {
	    e.printStackTrace();
	}

	while(parsedStartDate.compareTo(parsedEndDate) <= 0) {
	    dates.add(sdf.format(parsedStartDate));
	    Calendar calendar = Calendar.getInstance();
	    calendar.setTime(parsedStartDate);
	    calendar.add(Calendar.DAY_OF_MONTH, 1);
	    parsedStartDate = calendar.getTime();
	}
	return dates;
}
```    

## 특정 날짜 요일 구하기

```java
/**
* 특정 날짜에 대해여 요일 구하기
* @param date
* @param dateType
* @return
* @throws Exception
*/
public static String getDateDay(String date, String dateType) {
	String day = "" ;

	SimpleDateFormat dateFormat = new SimpleDateFormat(dateType) ;
	Date newDate = null;
	try {
	    newDate = dateFormat.parse(date);
	} catch (ParseException e) {
	    throw new ShowUserMessageException("날짜 변환 과정에 에러가 발생했습니다");
	}

	Calendar cal = Calendar.getInstance() ;
	cal.setTime(newDate);

	int dayNum = cal.get(Calendar.DAY_OF_WEEK) ;

	switch(dayNum){
	    case 1:
		day = DayOfTheWeek.SUNDAY.toString();
		break ;
	    case 2:
		day = DayOfTheWeek.MONDAY.toString();
		break ;
	    case 3:
		day = DayOfTheWeek.TUESDAY.toString();
		break ;
	    case 4:
		day = DayOfTheWeek.WEDNESDAY.toString();
		break ;
	    case 5:
		day = DayOfTheWeek.THURSDAY.toString();
		break ;
	    case 6:
		day = DayOfTheWeek.FRIDAY.toString();
		break ;
	    case 7:
		day = DayOfTheWeek.SATURDAY.toString();
		break ;
	}

	return day ;
}
```    

## DayOfWeek Enum class

```java
public enum DayOfTheWeek {
    MONDAY {
        @Override
        public String toString() {
            return "월요일";
        }
    },
    TUESDAY {
        @Override
        public String toString() {
            return "화요일";
        }
    },
    WEDNESDAY {
        @Override
        public String toString() {
            return "수요일";
        }
    },
    THURSDAY {
        @Override
        public String toString() {
            return "목요일";
        }
    },
    FRIDAY {
        @Override
        public String toString() {
            return "금요일";
        }
    },
    SATURDAY {
        @Override
        public String toString() {
            return "토요일";
        }
    },
    SUNDAY {
        @Override
        public String toString() {
            return "일요일";
        }
    };
}
```

## DateFormat Enum class

```java
public enum DateFormat {
    YYYYMMDD {
        @Override
        public String toString() {
            return "yyyy-MM-dd";
        }
    }
}
```
