# 统计实体类中多个需要计算的参数

###### 本文着重讲流的Collectors.toMap用法，了解流的原理可以或者更多的用法可以去这里 https://www.ibm.com/developerworks/cn/java/j-lo-java8streamapi/index.html



```java
// 我们已 List<Map<String, Object>> 形式储存数据 或者 List<Entity> 形式分别示例

// 首先 List<Map<String, Object>>
List<Map<String, Object>> debtDueListForMap = Lists.newArrayList();
		Map<String,Object> param1 = Maps.newHashMap();
		param1.put("supplierId",1);
		param1.put("payableAmount",1);
		param1.put("paidAmount",2);
		param1.put("unpaidAmount",3);

		Map<String,Object> param2 = Maps.newHashMap();
		param2.put("supplierId",1);
		param2.put("payableAmount",1);
		param2.put("paidAmount",2);
		param2.put("unpaidAmount",3);

		Map<String,Object> param3 = Maps.newHashMap();
		param3.put("supplierId",1);
		param3.put("payableAmount",1);
		param3.put("paidAmount",2);
		param3.put("unpaidAmount",3);

		debtDueListForMap.add(param1);
		debtDueListForMap.add(param2);
		debtDueListForMap.add(param3);


```

```java
package ev;

import lombok.Builder;
import lombok.Data;

import java.math.BigDecimal;

@Data
@Builder
public class TestEntity {

    // 供应商
    private Long supplierId;

    // 应付金额
    private BigDecimal payableAmount;

    // 已付金额
    private BigDecimal paidAmount;

    // 未付金额
    private BigDecimal unpaidAmount;


}

```

```java
// 实体 
/**
         * Collectors.toMap 的用法
         */
        // 统计supplierId 相同的已付金额
        Map<Long, BigDecimal> supplierIdEntity = debtDueListForEntity
                .stream()
                .collect(Collectors.toMap(TestEntity::getSupplierId // key
                        , TestEntity::getPaidAmount // value
                        , BigDecimal::add // 相同的supplierId value值累加
                ));
        // 统计supplierId 所有的需要金额计算的参
        List<TestEntity> supplierList = new ArrayList<>(debtDueListForEntity
                .stream()
                .collect(Collectors.toMap(TestEntity::getSupplierId // key
                        , v -> v // value
                        , (v1, v2) -> {
                            // v1 v2 为相同supplierId下的需要统计的实体，讲v2的数据累加进v1中仍然返回v1
                            v1.setPaidAmount(v1.getPaidAmount().add(v2.getPaidAmount()));
                            		                v1.setUnpaidAmount(v1.getUnpaidAmount().add(v2.getUnpaidAmount()));
                            v1.setPayableAmount(v1.getPayableAmount().add(v2.getPayableAmount()));
                            return v1;
                        }
                ))
                .values());

// map
 // 统计supplierId 相同的已付金额
        Map<String, BigDecimal> supplierIdEntity = debtDueListForEntity
                .stream()
                .collect(Collectors.toMap(k->k.get("supplierId").toString() // key
                        ,v->MathUtils.getBigDecimal(v.get("payableAmount")) // value
                        , BigDecimal::add // 相同的supplierId value值累加
                ));

// 统计supplierId 所有的需要金额计算的参
Map<String, Map<String, Object>> supplierIdMap = debtDueListForMap
				.stream()
				.collect(Collectors.toMap(k -> k.get("supplierId").toString(), v -> v, (v1, v2) -> {
					v1.put("payableAmount", MathUtils.getBigDecimal(v1.get("payableAmount")).add(MathUtils.getBigDecimal(v2.get("payableAmount"))));
					v1.put("paidAmount", MathUtils.getBigDecimal(v1.get("paidAmount")).add(MathUtils.getBigDecimal(v2.get("paidAmount"))));
					v1.put("unpaidAmount", MathUtils.getBigDecimal(v1.get("unpaidAmount")).add(MathUtils.getBigDecimal(v2.get("unpaidAmount"))));
					return v1;
				}));




```

```java
/**
* 将Object 类型的转换为BigDecimal
*/
package com.ev.framework.utils;

import java.math.BigDecimal;
import java.math.BigInteger;

public class MathUtils {
	public static BigDecimal getBigDecimal(Object value) {
		BigDecimal ret = null;
		if (value != null) {
			if (value instanceof BigDecimal) {
				ret = (BigDecimal) value;
			} else if (value instanceof String) {
				ret = new BigDecimal((String) value);
			} else if (value instanceof BigInteger) {
				ret = new BigDecimal((BigInteger) value);
			} else if (value instanceof Number) {
				ret = new BigDecimal(((Number) value).doubleValue());
			} else {
				throw new ClassCastException("Not possible to coerce [" + value + "] from class " + value.getClass()
						+ " into a BigDecimal.");
			}
		}
		return ret;
	}

}

```

