---
created: 2025-04-17
---
둘 다 요청을 가로채는 **공통 처리 기능**을 할 수 있지만, 사용하는 **레벨과 목적, 기능**이 다름.

## ✅ Filter (javax.servlet.Filter)

### ➤ Servlet 수준에서 동작 (스프링 바깥의 기능)

- **서블릿 컨테이너(Tomcat)** 가 요청을 처리하기 전에 동작
    
- **DispatcherServlet 이전**에 실행됨
    

### ➤ 주요 특징:

- 스프링과 무관한 **서블릿 스펙 기반**
    
- 요청/응답을 직접 조작할 수 있음
    
- 인증, 로깅, XSS 필터링 등에서 사용
    

### ➤ 사용 위치:

```
Client → Filter → DispatcherServlet → Controller
```

---

## ✅ Interceptor (HandlerInterceptor)

### ➤ Spring MVC 수준에서 동작

- **DispatcherServlet 이후**, 실제 컨트롤러 실행 전/후에 동작
    

### ➤ 주요 특징:

- **스프링에서만 사용** 가능
    
- `HandlerMethod`에 접근 가능 → 어떤 컨트롤러/메서드가 호출되는지 알 수 있음
    
- 로그인 체크, 권한 체크, 공통 모델 설정 등에 유용
    

### ➤ 사용 위치:

```
Client → Filter → DispatcherServlet → Interceptor → Controller
```

---

## 🔍 차이 정리 (표)

|항목|Filter|Interceptor|
|---|---|---|
|속한 계층|서블릿 (웹 서버)|스프링 MVC|
|동작 시점|DispatcherServlet **이전**|DispatcherServlet **이후**|
|요청/응답 조작|가능 (request/response 필터링)|불가능 (주로 로직 확인/차단)|
|사용 목적|보안, 로깅, 캐싱, 헤더 설정 등|인증, 권한 검사, 요청 처리 전후 로직|
|대상|모든 요청 (`/*`)|스프링 MVC Handler (컨트롤러) 대상|
|구현 방식|`javax.servlet.Filter`|`HandlerInterceptor`|
|등록 방식|`@Component`, `FilterRegistrationBean`|`WebMvcConfigurer.addInterceptors`|

---

## ☕️ 비유로 이해해볼까?

- **Filter**: 건물 입구 보안 (누구든 들어오는지 검사, X-Ray 같은 역할)
    
- **Interceptor**: 회의실 앞 비서 (회의 참석자 맞는지 체크하고 기록도 남김)
    

---

## ✅ 예제 비교

### ✅ Filter 예제

```java
@WebFilter("/*")
public class MyFilter implements Filter {
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
        System.out.println("Filter 실행됨");
        chain.doFilter(request, response);
    }
}
```

### ✅ Interceptor 예제

```java
public class MyInterceptor implements HandlerInterceptor {
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        System.out.println("Interceptor preHandle 실행됨");
        return true; // false면 Controller 진입 못 함
    }
}
```

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new MyInterceptor())
                .addPathPatterns("/api/**");
    }
}
```

---

## 🔚 정리하자면

- **Filter**는 스프링 외부(서블릿 컨테이너)에서 **요청/응답 자체**를 다룰 때.
- **Interceptor**는 스프링 내부에서 **비즈니스 전/후 로직**을 다룰 때.
