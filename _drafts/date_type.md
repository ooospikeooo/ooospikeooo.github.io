---
layout: post
title: Date Type
date: 2022-04-28 9:17:00 +0900
category: [java, javascript]
tags: [java, javascript, mysql]
---

# Date Type
* Java Date type
    * java.util.Date
        * java 8 이후에는 사용않길 권고
    * java.sql.Date
        * sql에서 Date정보를 읽어올 때 사용한다.
        * java.util.Date를 상속받음
        * 년,월,일 의 정보만 있음
* Javascript Date Type
    * 기본적으로 Wed Apr 27 2022 15:30:00 GMT+0900 (한국 표준시) 의 형식으로 출력됨
    * 아래와 같이 출력형식을 바꿀 수 있다.->format 함수 추가해줘야함.
    ```javascript
    const d = new Date();
    document.getElementById("demo").innerHTML = d.toISOString();
    // -> 2022-04-29T15:30:00.000Z
    d.format("yyyy-MM-dd HH:mm:ss");
    // -> 2022-04-29 15:30:00
    ```
* Mysql Date Type

# Date 정보 전달 과정
* javascript Date -> json(string) -> java Date -> string -> db Date

# String -> java.util.date 
* ConversionService에서 converter를 찾아서 convert 해줌
* TypeConverterDelegate.class
    ````java
    public <T> T convertIfNecessary(String propertyName, Object oldValue, Object newValue, Class<T> requiredType, TypeDescriptor typeDescriptor) throws IllegalArgumentException {
        Object convertedValue = newValue;
        PropertyEditor editor = this.propertyEditorRegistry.findCustomEditor(requiredType, propertyName);
        ConversionFailedException firstAttemptEx = null;
        ConversionService conversionService = this.propertyEditorRegistry.getConversionService();
        TypeDescriptor elementType;
        if (editor == null && conversionService != null && newValue != null && typeDescriptor != null) {
            elementType = TypeDescriptor.forObject(newValue);
            TypeDescriptor targetTypeDesc = typeDescriptor;
            if (conversionService.canConvert(elementType, typeDescriptor)) {
                try {
                    return conversionService.convert(convertedValue, elementType, targetTypeDesc);
                } catch (ConversionFailedException var15) {
                    firstAttemptEx = var15;
                }
            }
        }

        //...

    }
    ````
    <!-- TODO: FormatterRegistry는 어디서 연결되어있나? -->
    <!-- TODO: 왜 'T'이렇게 묶어야될까? -->
    * String이 ```` 2018-12-15T10:00:00 ````이면 pattern은 ````yyyy-MM-dd'T'HH:mm:ss```` 이렇게 'T'로 묶어야 되었음
    * Spring MVC초기화 과정에서 세팅이 덮어씌여질 수 있으므로 주의해야함.