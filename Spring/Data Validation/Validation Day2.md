

## 数据校验#2

- 自定义校验器
    - 创建注解类
```java
@Constraint(validatedBy = { IdCardValidator.class }) // 此处填入的是使用什么类去校验 -> IdCardValidator.class
@Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER })
@Retention(RUNTIME)
public @interface IdCard {

    String message() default "";

    Class<?>[] groups() default { };

    Class<? extends Payload>[] payload() default { };

    @Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER })
    @Retention(RUNTIME)
    @Documented
    public @interface List {
        IdCard[] value();
    }

}
```

- 创建校验器类实现接口 ConstraintValidator
```java
// 第一个泛型代表校验哪个类, 第二个泛型代表给哪个类型使用
public class IdCardValidator implements ConstraintValidator<IdCard, String> {

    @Override
    public void initialize(IdCard constraintAnnotation) {
    }

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        return IdCardUtils.isIDCard(value);
    }
}
```

- 实体类中添加校验注解
```java
@IdCard(message = "身份证输入不合法")
    private String idCardNumber;
```
