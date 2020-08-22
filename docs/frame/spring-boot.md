# Spring-boot

## 全局统一

### 统一返回

```java
/**
 * 统一接口返回
 * @author lgc
 */
@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class ApiResult<T>{
    private int code;
    private String errMsg;
    private  T data;
}
```

### 统一返回封装

```java
package com.aladdin.cloud.common.response;

/**
 * 统一返回 builder
 *
 * @author lgc
 */
public class ApiResultBuilder<T> {
    private static int successCode = ApiErrEnum.SUCCESS.getCode();
    private static String successMsg = ApiErrEnum.SUCCESS.getErrMsg();

    /**
     * 不携带数据成功返回
     *
     * @return success
     */
    public static ApiResult<String> successWithOutData() {
        ApiResult<String> apiResult = new ApiResult<>();
        apiResult.setCode(successCode);
        apiResult.setErrMsg(successMsg);
        apiResult.setData("");
        return apiResult;
    }

    /**
     * 携带数据成功返回
     *
     * @param t   data
     * @param <T> T
     * @return success with data
     */
    public static <T> ApiResult<T> successWithData(T t) {
        ApiResult<T> apiResult = new ApiResult<>();
        apiResult.setCode(successCode);
        apiResult.setErrMsg(successMsg);
        apiResult.setData(t);
        return apiResult;
    }

    /**
     * 失败返回errEnum
     *
     * @param apiErrEnum errEnum
     * @param <T>        data
     * @return result
     */
    public static <T> ApiResult<T> error(ApiErrEnum apiErrEnum) {
        ApiResult<T> apiResult = new ApiResult<>();
        apiResult.setCode(apiErrEnum.getCode());
        apiResult.setErrMsg(apiErrEnum.getErrMsg());
        return apiResult;
    }

    /**
     * 失败附加错误信息
     *
     * @param apiErrEnum   baseErrEnum
     * @param attachErrMsg attach errMsg
     * @param <T>          data
     * @return result
     */
    public static <T> ApiResult<T> errorWithAttachErrMsg(ApiErrEnum apiErrEnum, String attachErrMsg) {
        ApiResult<T> apiResult = new ApiResult<>();
        apiResult.setCode(apiErrEnum.getCode());
        apiResult.setErrMsg(apiErrEnum.getErrMsg() + ":" + attachErrMsg);
        return apiResult;
    }

    /**
     * 重写
     *
     * @param apiErrEnum   apiEnum
     * @param attachErrMsg attachErrMsg
     * @param <T>          data
     * @return result
     */
    public static <T> ApiResult<T> errorOverrideAttachErrMsg(ApiErrEnum apiErrEnum, String attachErrMsg) {
        ApiResult<T> apiResult = new ApiResult<>();
        apiResult.setCode(apiErrEnum.getCode());
        apiResult.setErrMsg(attachErrMsg);
        return apiResult;
    }

    /**
     * 重写
     *
     * @param resultCode 返回代码
     * @param resultMsg  返回信息
     * @param <T>        data
     * @return result
     */
    public static <T> ApiResult<T> customerApiResult(int resultCode, String resultMsg) {
        ApiResult<T> apiResult = new ApiResult<>();
        apiResult.setCode(resultCode);
        apiResult.setErrMsg(resultMsg);
        return apiResult;
    }

}
```

### 统一异常拦截处理

```java
/**
 * @author lgc
 */
@RestControllerAdvice(basePackages = {"com.aladdin.cloud.mp.web"})
public class GlobalResponseBodyAdvice implements ResponseBodyAdvice<Object> {


    @Override
    public boolean supports(MethodParameter methodParameter, Class aClass) {
        return true;
    }

    @Override
    public Object beforeBodyWrite(Object o, MethodParameter methodParameter, MediaType mediaType,
                                  Class aClass, ServerHttpRequest serverHttpRequest, ServerHttpResponse serverHttpResponse) {
        if (!(o instanceof ApiResult)) {
            ApiResult ApiResult = new ApiResult(HttpStatus.OK.value(), HttpStatus.OK.getReasonPhrase(), o);
            if (o instanceof String) {
                return GsonUtil.toString(ApiResult);
            }
            return ApiResult;
        }
        return o;
    }

    @ExceptionHandler({MethodArgumentNotValidException.class})
    @ResponseStatus(HttpStatus.OK)
    @ResponseBody
    public ApiResult handleMethodArgumentNotValidException(MethodArgumentNotValidException ex) {
        BindingResult bindingResult = ex.getBindingResult();
        StringBuilder sb = new StringBuilder("Bad Request:");
        for (FieldError fieldError : bindingResult.getFieldErrors()) {
            sb.append(" ").append(fieldError.getDefaultMessage()).append(",");
        }
        sb.deleteCharAt(sb.length() - 1);
        String msg = sb.toString();
        return ApiResultBuilder.customerApiResult(HttpStatus.BAD_REQUEST.value(), msg);
    }

    @ExceptionHandler({ConstraintViolationException.class})
    @ResponseStatus(HttpStatus.OK)
    @ResponseBody
    public ApiResult handleConstraintViolationException(ConstraintViolationException ex) {
        return ApiResultBuilder.customerApiResult(ApiErrEnum.CLIENT_ERR.getCode(), ex.getMessage());
    }

    @ResponseBody
    @ResponseStatus(HttpStatus.OK)
    @ExceptionHandler({ValidationException.class})
    public ApiResult errorHandler(ValidationException exception) {
        exception.printStackTrace();
        return ApiResultBuilder.customerApiResult(ApiErrEnum.CLIENT_ERR.getCode(), exception.getMessage());
    }

    @ResponseBody
    @ExceptionHandler(value = Exception.class)
    public ApiResult errorHandler(Exception ex) {
        ex.printStackTrace();
        return ApiResultBuilder.error(ApiErrEnum.INTERNAL_ERR);
    }
}
```

## valid 参数校验&嵌套

```java
package com.aladdin.cloud.mp.domain.ao;

import lombok.*;
import org.hibernate.validator.constraints.Length;

import javax.validation.Valid;
import javax.validation.constraints.*;

/**
 * @author lgc
 */
@AllArgsConstructor
@NoArgsConstructor
@Data
@EqualsAndHashCode
@Builder
public class ValidAo {
    @NotBlank(message = "id required", groups = {GroupB.class})
    private String id;
    @NotBlank(message = "name required",groups = {GroupA.class})
    private String name;

    @NotBlank
    @Email(message = "{valid.mail}",groups = {GroupA.class})
    private String email;

    @NotNull(message = "age required",groups = {GroupA.class})
    @Min(18)
    @Max(120)
    private int age;

    @NotBlank(message = "address required",groups = {GroupA.class})
    @Length()
    private String address;

    @NotNull(message = "innerValidAo required",groups = {GroupA.class})
    @Valid
    private InnerValidAo innerValidAo;

    public interface GroupA {

    }

    public interface GroupB {

    }

}
```

### @valid @validated