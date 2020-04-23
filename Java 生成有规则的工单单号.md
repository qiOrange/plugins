# Java 生成有规则的工单单号

```java
public static String getFormateDate(Date date, String pattern) {
        SimpleDateFormat dateFormat = setFormat(pattern);
        String timeString = dateFormat.format(date);
        return timeString;
    }

//17位时间戳工单号 年月日 时分秒 毫秒
public static String getWorkOrderno() {
    
    String workOrderno = DateFormatUtil.getFormateDate(new Date(), "yyyyMMddHHmmssSSS");
    return workOrderno;
}

/** 
* 例：TEST0001 TEST0002
* @param prefix  单据前缀
	 * @param suffix 从数据库查出的最后一个生成该单据前缀下的单号
      * @param zeroNum 尾数0保留个数
     * 生成没有时间戳的编号 
     */
    public static String getWorkOrderno(String prefix, String suffix, Integer zeroNum) {
        if (zeroNum <= 0) {
            return null;
        }
        //16位工单号的前缀
        StringBuilder stringBuilder = new StringBuilder();
        for (int i = 0; i < zeroNum; i++) {
            if (i == 10) {
                break;
            }
            stringBuilder.append(0);
        }
        String sortNo = stringBuilder.toString();
        if (suffix != null) {
            String[] split = suffix.split("\\D");
            if (split.length > 0) {
                List<String> collect = Arrays.stream(split).filter(StringUtils::isNoneEmpty).collect(Collectors.toList());
                if (collect.size() == 1) {
                    String s = collect.get(0);
                    if (s.length() >= 10) {
                        return prefix + StringUtils.autoGenericCode(sortNo, zeroNum);
                    }
                    return prefix + StringUtils.autoGenericCode(s, zeroNum);
                }
            }
        }
        return prefix + StringUtils.autoGenericCode(sortNo, zeroNum);
    }

// 生成带有时间的单据号
/** 
* 例：TEST202004220001 TEST202004220002 TEST202004230001
* @param prefix  单据前缀
	 * @param suffix 从数据库查出的最后一个生成该单据前缀下的单号
      * @param zeroNum 尾数0保留个数
     * 生成没有时间戳的编号 
     */
    public static String getWorkOrderNo(String prefix, String suffix, Integer zeroNum) {
        if (zeroNum <= 0) {
            zeroNum = 4;
        }

        suffix = suffix == null ? "" : suffix;

        StringBuilder stringBuilder = new StringBuilder(StringUtils.isEmpty(prefix) ? "DJ" : prefix);
        stringBuilder.append(getFormateDate(new Date(), "yyyyMMdd"));
        if (suffix.contains(stringBuilder)) {
            String[] strings = suffix.split(stringBuilder.toString());
            System.err.println(strings.length);
            if (strings.length >= 2) {
                System.err.println(strings[1]);
                return stringBuilder.append(StringUtils.autoGenericCode(strings[1], zeroNum)).toString();
            }
        }
        for (int i = 0; i < zeroNum; i++) {
            if (i == 10) {
                break;
            }
            if (i == zeroNum - 1) {
                stringBuilder.append(1);
            } else {
                stringBuilder.append(0);
            }
        }
        return stringBuilder.toString();
    }
```







