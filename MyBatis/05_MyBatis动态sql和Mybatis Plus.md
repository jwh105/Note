# 05_MyBatis动态sql

## 一、${参数}和#{参数}区别

​	${参数}：不防止sql注入、参数可以是一条sql语句

​	#{参数}：防止sql注入、参数只能作为参数

## 二、if

```xml
<!--如果id不为空、就根据id查询、否则就不根据id查询-->
<select id="getStudentAll" resultMap="studentResultMap">
	select * from student where 1=1
	<if test="id!=null">
		and id=#{id}
    </if>
</select>
```

## 三、choose, when, otherwise

```xml
<!--如果id不为null、根据ID查询，否则根据name查询，如果name也为null，则根据otherwise的条件查询-->
<select id="getStudentAll" resultMap="studentResultMap">
	select * from student where 1=1
	<choose>
		<when test="id!=null">
			and id=#{id}
		</when>
		<when test="name!=null">
			and name=#{name}
		</when>
		<otherwise>
			and img is not null
		</otherwise>
	</choose>
</select>
```

## 四、trim, where, set

```xml
<!--【trim, where没🔨用】 因为可以用 查询语句后面接where1=1 固定格式来解决，-->
<!--set标签在修改的时候，非常有用，set里面的每个if后面都需要加,(逗号)，Mybatis会自动处理末尾逗号的问题-->
<update id="updateStudent">
    update student
    <set>
        <if test="name!=null">name=#{name},</if>
        <if test="name!=null">pwd=#{pwd},</if>
        <if test="name!=null">img=#{img},</if>
        <if test="name!=null">classid=#{classid},</if>
    </set>
    where id=#{id}
</update>
```

### 五、foreach

```xml
<!--查询多个id-->
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID in
  <foreach item="item" index="index" collection="list"
      open="(" separator="," close=")">
        #{item}
  </foreach>
</select>
```

## 六、mybatis-plus,mybatis-generator-gui代码生成器

## 七、Mybatis总结

```
优点：
1. 易于上手和掌握。
2. sql写在xml里，便于统一管理和优化。
3. 解除sql与程序代码的耦合。
4. 提供映射标签，支持对象与数据库的orm字段关系映射
5. 提供对象关系映射标签，支持对象关系组建维护
6. 提供xml标签，支持编写动态sql。
缺点：
1. sql工作量很大，尤其是字段多、关联表多时，更是如此。
2. sql依赖于数据库，导致数据库移植性差。
3. 由于xml里标签id必须唯一，导致DAO中方法不支持方法重载。
4. 字段映射标签和对象关系映射标签仅仅是对映射关系的描述，具体实现仍然依赖于sql。（比如配置了一对多Collection标签，如果sql里没有join子表或查询子表的话，查询后返回的对象是不具备对象关系的，即Collection的对象为null）
5. DAO层过于简单，对象组装的工作量较大。
6.  不支持级联更新、级联删除。
7. 编写动态sql时,不方便调试，尤其逻辑复杂时。
8 提供的写动态sql的xml标签功能简单（连struts都比不上），编写动态sql仍然受限，且可读性低。
9. 使用不当，容易导致N+1的sql性能问题。
10. 使用不当，关联查询时容易产生分页bug。
11. 若不查询主键字段，容易造成查询出的对象有“覆盖”现象。
12. 参数的数据类型支持不完善。（如参数为Date类型时，容易报没有get、set方法，需在参数上加@param）
13. 多参数时，使用不方便，功能不够强大。（目前支持的方法有map、对象、注解@param以及默认采用012索引位的方式）
14. 缓存使用不当，容易产生脏数据。
 
总结：
mybatis的优点其实也是mybatis的缺点，正因为mybatis使用简单，数据的可靠性、完整性的瓶颈便更多依赖于程序员对sql的使用水平上了。sql写在xml里，虽然方便了修改、优化和统一浏览，但可读性很低，调试也非常困难，也非常受限，无法像jdbc那样在代码里根据逻辑实现复杂动态sql拼接。mybatis简单看就是提供了字段映射和对象关系映射的jdbc，省去了数据赋值到对象的步骤而已，除此以外并无太多作为，不要把它想象成hibernate那样强大，简单小巧易用上手，方便浏览修改sql就是它最大的优点了。
```



























































