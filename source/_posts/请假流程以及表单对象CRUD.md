---
title: 数据库的CRUD
date: 2021-08-20 16:58:20
author: lsy
readmore: true
tags: 
  - JAVA
  - CRUD
    
categories:
  -JAVA学习
  
---
俗话说:基础不牢，地动山摇.每个程序员CRUD都是必备操作.第一篇文章就复习CRUD吧!(SSM)
<!-- more -->
## 1.创建数据源,连接数据库
首先用Navicat随便建一个库.然后写配置文件连接到这个数据库.
![数据库](https://cdn.jsdelivr.net/gh/simpnohouse/blog_image/数据库.png)
## 2.取数逻辑
传统的页面取后台数据。需要些 controller,manager,dao 等一整套逻辑。

### model
创建一个对象来对应数据库各个属性.
```java
    public class OaStudent {
        private String ID;
        private String NAME;
        private String SEX;
    
        @Override
        public String toString() {
            return "OaStudent{" + "ID='" + ID + '\'' + ", NAME='" + NAME + '\'' + ", SEX='" + SEX + '\'' + '}';
        }
    
        public String getID() {
            return ID;
        }
    
        public void setID(String ID) {
            this.ID = ID;
        }
        public String getNAME() {
            return NAME;
        }
        public void setNAME(String NAME) {
            this.NAME = NAME;
        }
    
        public String getSEX() {
            return SEX;
        }
    
        public void setSEX(String SEX) {
            this.SEX = SEX;
        }
    }
```

### Dao层

首先创建一个接口,写上CRUD四个方法.(为了便于多态)
```java
    public interface OaStudentDao {
        void insert(OaStudent student);
        void delete(String id);
        void update(OaStudent student);
        List<OaStudent> find(OaStudent student);
    }
```
Dao层（数据持久层）加上@Repository注解，随后写这个接口的实例,并且继承MyBatisDaoImpl。重写接口方法和getNamespace方法。
在Mybatis中，namespace是用来绑定Dao接口的，即面向接口编程。
```java
    @Repository
    public class OaStudentDaoImpl extends MyBatisDaoImpl implements OaStudentDao {
    
        @Override
        public String getNamespace() {
            return OaStudentDaoImpl.class.getName();
        }
    
        @Override
        public  void insert(OaStudent student) {
            sqlSessionTemplate.selectList("insert1",student);
        }
    
        @Override
        public void delete(String id) {
            sqlSessionTemplate.selectList("delete1",id);
        }
    
        @Override
        public void update(OaStudent student) {
            sqlSessionTemplate.selectList("update1",student);
    
        }
    
        @Override
        public List<OaStudent> find(OaStudent student) {
            return sqlSessionTemplate.selectList("find1",student);
    
        }
    }
```

### Sever层

通过Sever层（负责设计业务模块的逻辑）来调用Dao层。这里只是简单的增删改查，没有复杂的逻辑，因此Server层直接调用Dao层就好。
接口如下：
```java
    public interface OaStudentManager {
        void insert(OaStudent student);
        void delete(String id);
        void update(OaStudent student);
        List<OaStudent> find(OaStudent student);
    
    }
```
定义接口的实例：没有复杂的逻辑，简单调用Dao层即可。
```java
    @Service
    public class OaStudentManagerImpl implements OaStudentManager {
    
        @Resource
        OaStudentDao oaStudentDao;
        @Override
        public void insert(OaStudent student) {
            oaStudentDao.insert(student);
        }
    
        @Override
        public void delete(String id) {
            oaStudentDao.delete(id);
        }
    
        @Override
        public void update(OaStudent student) {
            oaStudentDao.update(student);
        }
    
        @Override
        public List<OaStudent> find(OaStudent student) {
            return oaStudentDao.find(student);
        }
    }
```
### Controller层

通过Controller层来调用Server层，并通过URL作为映射来控制处理器所调用的方法。
```java

    @Controller
    @RequestMapping({"/studentlsy"})
    public class OaStudentController {
        @Resource
        private OaStudentManager oaStudentManager;
        @ResponseBody
        @RequestMapping("/insert")
        public void insert(@RequestBody OaStudent student){
            oaStudentManager.insert(student);
        }
        @ResponseBody
        @RequestMapping("/delete")
        public void delete(String ID){

        oaStudentManager.delete(ID);
    }

    @ResponseBody
    @RequestMapping("/update")
    public void update(@RequestBody OaStudent student){
        oaStudentManager.update(student);
    }
    @ResponseBody
    @RequestMapping("/find")
    public List<OaStudent> find(@RequestBody OaStudent student){
        return oaStudentManager.find(student);
    }
}
```
   
## 3.通过xml文件和Postman调用接口
### xml
在名为Mapper文件夹下新建一个后缀为xml的文件。


    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

通过namespace定位到Dao层，通过resultMap将数据库元素和传入元素建立映射。
```html

    <mapper namespace="com.zz.oa.dao.OaStudentDao">
    <resultMap id="BaseResultMap" type="com.zz.oa.model.OaStudent">
        <id column="ID" property="ID" jdbcType="VARCHAR"/>
         <result column="NAME" property="NAME" jdbcType="VARCHAR"/>
         <result column="SEX" property="SEX" jdbcType="VARCHAR"/>
    </resultMap>
 ```  
写上URL所对应的SQL语句。
```html
    <insert id="insert1" parameterType="com.zz.oa.model.OaStudent">
        insert into GOVENGINE_LAND.LSY_STUDENT  values(#{ID,jdbcType=VARCHAR},#{NAME,jdbcType=VARCHAR},#{SEX,jdbcType=VARCHAR})
    </insert>
    
    <delete id="delete1" parameterType="string">
        delete from GOVENGINE_LAND.LSY_STUDENT where ID=#{ID,jdbcType=VARCHAR}
    </delete>

    <update id="update1" parameterType="com.zz.oa.model.OaStudent">
        update GOVENGINE_LAND.LSY_STUDENT set NAME=#{NAME,jdbcType=VARCHAR},SEX=#{SEX,jdbcType=VARCHAR} WHERE ID=#{ID,jdbcType=VARCHAR}
    </update>

    <select id="findsex" resultType="com.zz.oa.model.OaStudent">
        select * from GOVENGINE_LAND.LSY_STUDENT where SEX=#{SEX,jdbcType=VARCHAR}
    </select>
       <insert id="insert1" parameterType="com.zz.oa.model.OaStudent">
           insert into GOVENGINE_LAND.LSY_STUDENT  values(#{ID,jdbcType=VARCHAR},#{NAME,jdbcType=VARCHAR},#{SEX,jdbcType=VARCHAR})
       </insert>
   
       <delete id="delete1" parameterType="string">
           delete from  GOVENGINE_LAND.LSY_STUDENT where ID=#{ID,jdbcType=VARCHAR}
       </delete>
   
       <update id="update1" parameterType="com.zz.oa.model.OaStudent">
           update GOVENGINE_LAND.LSY_STUDENT set NAME=#{NAME,jdbcType=VARCHAR} WHERE ID=#{ID,jdbcType=VARCHAR}
       </update>
   
       <select id="find1" resultType="com.zz.oa.model.OaStudent">
           select * from GOVENGINE_LAND.LSY_STUDENT WHERE ID=#{ID,jdbcType=VARCHAR}
       </select>
       
    </mapper>
```
### postman
如图：
![postman](https://cdn.jsdelivr.net/gh/simpnohouse/blog_image/postman1.png)
![postman](https://cdn.jsdelivr.net/gh/simpnohouse/blog_image/postman2.png)
postman简单调用URL的方式就是这样啦，就不多举例了。注意前端网页的cookie，可能会受热部署的影响就行。

## 踩到的坑
作为一个新入行的菜鸟，遇到的傻傻的问题......
1、调用接口的时候，大小写要严格区分。
2、记得使用@ResponseBody注解，功能是把传入的JAVAobject对象转换成json格式。如果不加@Response注解，程序虽然有可能可以成功执行，但不会有js结果。
3、在mybatis框架里面热部署并不会对mapper（xml）起作用，如果对它进行修改后记得重启项目。
4、对于delete，因传入的参数是string,应该这样调用URL。
![postman3](https://cdn.jsdelivr.net/gh/simpnohouse/blog_image/postman3.png)
这样调用时，并不会成功传入ID值。
![postman4](https://cdn.jsdelivr.net/gh/simpnohouse/blog_image/postman4.png)
结果如图：
![postman4](https://cdn.jsdelivr.net/gh/simpnohouse/blog_image/ps5.png)
具体原因还是没能理解透彻。
5、开始debug的时候一直没有能成功传进去值,后面发现在xml文件中忘记写上jdbcType:

    ID=#{ID,jdbcType=VARCHAR}
但是查资料觉得jdbcType在这里似乎不加也没关系.这也是一个待解决的问题.