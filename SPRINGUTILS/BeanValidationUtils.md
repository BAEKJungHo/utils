```java
@Component
public class BeanValidationUtils {

    /**
     * @Valid, @Validated 어노테이션이 적용된 BindingResult 에러 메시지 출력
     * @param bindingResult
     */
    public void showUserMessageBindingResultErrors(BindingResult bindingResult) {
        if(bindingResult.hasErrors()) {
            bindingResult.getAllErrors().forEach(error -> {
                throw new ShowUserMessageException(error.getDefaultMessage());
            });
        }
    }

}
```
