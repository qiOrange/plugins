# Mysql 保存时验证名称重复

```sql
保存/和修改时传入实体参数即可
<select id="checkSave" resultType="int" parameterType="com.system.domain.RoleDO">
		SELECT COUNT(*) FROM
		sys_role
		<where>
			<if test="roleId != null and roleId != ''"> AND role_id <![CDATA[<>]]> #{roleId} </if>
			<if test="roleName != null and roleName != ''"> AND role_name = #{roleName} </if>
		</where>
</select>
```

```java
@Data
public class RoleDO {

    // 角色ID
    private Long roleId;

    // 角色名
    private String roleName;


}

/**
 * 角色
 */
@Mapper
public interface RoleDao {
	int checkSave(RoleDO role);
}


    public boolean checkSave(RoleDO role) {
        // 返回true 则可以保存
        return roleMapper.checkSave(role)==0;
    }

```

