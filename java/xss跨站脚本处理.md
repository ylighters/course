## xss跨站脚本处理

### 系统增加拦截器过滤特殊字符

### 后端过滤方法

```
// 1*/-->"/ifr%0Dame></sc%00ript></st%0yle></ti%00tle><tex%00tarea><sc%00ript>alert(81748)</sc%00ript>
value = value.replaceAll("%00", "");
value = value.replaceAll("eval\\((.*)\\)", "");
value = value.replaceAll("[\\\"\\\'][\\s]*javascript:(.*)[\\\"\\\']", "\"\"");
value = value.replaceAll("(?i)<script.*?>.*?<script.*?>", "");
value = value.replaceAll("(?i)<script.*?>.*?</script.*?>", "");
value = value.replaceAll("(?i)<.*?javascript:.*?>.*?</.*?>", "");
value = value.replaceAll("(?i)<.*?\\s+on.*?>.*?</.*?>", "");
value = value.replaceAll("(?i)<sc%00ript.*?>.*?<sc%00ript.*?>", "");
value = value.replaceAll("(?i)<.*?ript.*?>.*?</.*?ript.*?>", "");
value = value.replaceAll("(?i)<.*?ript.*?>.*?<.*?ript.*?>", "");
value = value.replaceAll("alert\\((.*)\\)", "");
```
