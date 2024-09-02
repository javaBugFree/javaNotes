
**MySQL中的字段类型** 与 **Java中的数据类型** 的*对应关系*应该如下：
Java都使用*包装类*。因为默认值是null。可以清楚地知悉 是否给值了。

| MySQL中的字段类型                | Java中的属性类型 |
|:-------------------------------- |:---------------- |
| `tinyint` / `smallint` / `int`   | `Integer`        |
| `bigint`                         | `Long`           |
| `varchar` / `char` / `text 系列` | `String`         |
| `decimal`                        | `BigDecimal`     |
| `datetime`                       | `LocalDateTime`  |

**小数类型为** `decimal`，禁止使用 float 和 double。