# 03_Mapper.xml映射文件之增删改

## 一、insert标签

全属性【**包含主键**】插入、返回受影响函数

```xml
public int add(Student info);

<insert id="add">
    insert into student(id,name,pwd,img,classid) values(#{id},#{name},#{pwd},#{img},#{classid})
</insert>
```

部分字段【**不包含主键**】插入，返回受影响函数，主键自动填充到插入的实体类对象

```xml
public int add(Student info);
<!--
	keyProperty:主键属性名
	keyColumn:主键列名
	useGeneratedKeys:是否自动增长（此方法只对有自动增长的数据库有效 如：mysql、sqlserver等）
-->
<insert id="add" keyProperty="id" keyColumn="id" useGeneratedKeys="true">
    insert into student(name,pwd,img,classid) values(#{name},#{pwd},#{img},#{classid})
</insert>

<!--
        Oracle中
        selectKey:查询主键的sql语句
        keyProperty:sql语句生成的结果需要填充的属性名
        order:AFTER、BEFORE
-->
<insert id="add">
	<selectKey keyProperty="id" order="BEFORE">
		select seq.nextVal from dual
	</selectKey>
        insert into student(id,name,pwd,img,classid) values(#{id},#{name},#{pwd},#{img},#{classid})
</insert>
```

## 二、删除标签

```xml
<!--多参数使用@param、或者直接使用对象、默认返回受影响函数-->
<delete id="deleteStudentById">
    delete from student where id=#{id}
</delete>
```

## 三、update标签

```xml
<update id="updateStudent">
    update student set name=#{id},pwd=#{pwd},img=#{img},classid=#{classid} where id=#{id}
</update>
```

## 四、Sql标签

```xml
<!--很长的sql语句可以使用sql标签让其对象化-->
<sql id="stu">
    select * from student
</sql>

<select id="getStudentAll" resultMap="studentResultMap">
    <include refid="stu"/> where 1=1
</select>
```

## 五、Cache：缓存的时候使用

```xml
<cache/>
```

































