# ContextUtils

```java
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import org.springframework.web.context.ContextLoader;
import org.springframework.web.context.WebApplicationContext;
import org.springframework.web.context.request.RequestContextHolder;
import org.springframework.web.context.request.ServletRequestAttributes;

public class ContextUtil {
    /**
     * 빈을 직접 얻습니다.
     * 필요한 빈(Bean)이나 서블릿(Servlet) 객체에 직접 접근 하는 방법입니다.
     *  빈을 직접 얻기 위해서는 ContextLoader 로부터 현재의 WebApplicationContext 를 얻은 후 getBean() 메소드로 빈을 얻을 수 있습니다.
     * @param beanName
     * @return
     */
    public static Object getBean(String beanName) {
        WebApplicationContext context = ContextLoader.getCurrentWebApplicationContext();
        return context.getBean(beanName);
    }

    /**
     * HttpServletReqeust 객체를 직접 얻습니다.
     * @return
     */
    public static HttpServletRequest getRequest() {
        ServletRequestAttributes attr = (ServletRequestAttributes)RequestContextHolder.currentRequestAttributes();
        return attr.getRequest();
    }

    /**
     * HttpServletResponse 객체를 직접 얻습니다.
     * @return
     */
    public static HttpServletResponse getResponse() {
        ServletRequestAttributes attr = (ServletRequestAttributes)RequestContextHolder.currentRequestAttributes();
        return attr.getResponse();
    }

    /**
     * HttpSession 객체를 직접 얻습니다.
     *
     * @param gen 새 세션 생성 여부
     * @return
     */
    public static HttpSession getSession(boolean gen) {
        return ContextUtil.getRequest().getSession(gen);
    }

    /**
     * REQUEST 영역에서 가져오기
     *
     * @param key
     * @return
     */
    public static Object getAttrFromRequest(String key) {
        ServletRequestAttributes attr = (ServletRequestAttributes)RequestContextHolder.currentRequestAttributes();
        return attr.getAttribute(key, ServletRequestAttributes.SCOPE_REQUEST);
    }

    /**
     * REQUEST 영역에 객체 저장
     *
     * @param key
     * @param obj
     */
    public static void setAttrToRequest(String key, Object obj) {
        ServletRequestAttributes attr = (ServletRequestAttributes)RequestContextHolder.currentRequestAttributes();
        attr.setAttribute(key, obj, ServletRequestAttributes.SCOPE_REQUEST);
    }

    /**
     * SESSION 영역에서 가져오기
     *
     * @param key
     * @return
     */
    public static Object getAttrFromSession(String key) {
        ServletRequestAttributes attr = (ServletRequestAttributes)RequestContextHolder.currentRequestAttributes();
        return attr.getAttribute(key, ServletRequestAttributes.SCOPE_SESSION);
    }

    /**
     * Session 영역에 객체 저장
     *
     * @param key
     * @param obj
     */
    public static void setAttrToSession(String key, Object obj) {
        ServletRequestAttributes attr = (ServletRequestAttributes)RequestContextHolder.currentRequestAttributes();
        attr.setAttribute(key, obj, ServletRequestAttributes.SCOPE_SESSION);
    }
}
```

* Object getBean(String beanName) - 빈 이름으로 필요한 빈 객체를 직접 가져옵니다. LoginDAO 가 @Repository("LoginDAO") 로 빈 이름이 지어져 있다면 다음과 같이 가져올 수 있습니다. LoginDAO loginDao = (LoginDAO)ContextUtil.getBean("LoginDAO");

* HttpServletRequest getRequest() - HttpServletRequest 객체를 직접 얻습니다.

* HttpServletResponse getResponse() - HttpServletResponse 객체를 직접 얻습니다.

* HttpSession getSession(boolean gen) - HttpSession 객체를 직접 얻습니다. 내부적으로 HttpServletRequest 객체를 먼저 구한 다음에 그 객체로부터 HttpSession 객체를 얻습니다.

* Object getAttrFromRequest(String key) - REQUEST 범위에 저장된 객체를 가져옵니다.

* void setAttrToRequest(String key, Object obj) - REQUEST 범위에 객체를 저장합니다.

* Object getAttrFromSession(String key) - SESSION 범위에 저장된 객체를 가져옵니다.

* void setAttrToSession(String key, Object obj) - SESSION 범위에 객체를 저장합니다.
