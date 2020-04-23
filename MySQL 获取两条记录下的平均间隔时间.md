# MySQL 获取两条记录下的平均间隔时间

```sql
首先先建测试表
CREATE TABLE `test` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `device_id` int(11) DEFAULT NULL COMMENT '设备ID',
  `create_time` datetime DEFAULT NULL COMMENT '创建时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

INSERT INTO `test` VALUES ('1', '1', '2020-04-22 10:00:51');
INSERT INTO `test` VALUES ('2', '1', '2020-04-23 10:00:51');
INSERT INTO `test` VALUES ('3', '1', '2020-04-24 10:00:51');
INSERT INTO `test` VALUES ('4', '2', '2020-04-22 20:00:52');
INSERT INTO `test` VALUES ('5', '2', '2020-04-24 20:00:52');
INSERT INTO `test` VALUES ('6', '2', '2020-04-26 20:00:52');

id  create_time         device_id
1	2020-04-22 10:00:51	 1
2	2020-04-23 10:00:51	 1
3	2020-04-24 10:00:51	 1
4	2020-04-22 20:00:52	 2
5	2020-04-24 20:00:52	 2
6	2020-04-26 20:00:52	 2

*预期*
设备1
1和2相差24小时 
2和3相差24小时
（24+24）/3=16

设备2
4和5相差48小时 
5和6相差48小时
（48+48）/3=32

```

执行SQL语句

```sql
SELECT
	r1.deviceId,
	AVG(
		IFNULL(
			TIMESTAMPDIFF(
				HOUR,
				r2.createTime,
				r1.createTime
			),
			0
		)
	) AS avgTime
FROM
	(
		SELECT
			(@rownum := @rownum + 1) AS rownum,
			r1.*
		FROM
			(
				SELECT
					t.create_time AS createTime,
					t.device_id AS deviceId
				FROM
					test t
			) r1,
			(SELECT @rownum := 0) r
		ORDER BY
			r1.deviceId,
			r1.createTime DESC
	) r1
LEFT JOIN (
	SELECT
		(@tworownum := @tworownum + 1) AS rownum,
		r2.*
	FROM
		(
			SELECT
				t.create_time AS createTime,
				t.device_id AS deviceId
			FROM
				test t
		) r2,
		(SELECT @tworownum := 0) r
	ORDER BY
		r2.deviceId,
		r2.createTime DESC
) r2 ON r1.deviceId = r2.deviceId
AND r1.rownum = r2.rownum - 1
GROUP BY
	r1.deviceId
```

得出结果：

```sql
deviceId avgTime
1	16.0000
2	32.0000
```

与预期相符，成功！