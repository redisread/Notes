创建Spring项目









首先



简单使用Spring





## Maven配置文件

setting.xml

![image-20210622145909909](https://i.loli.net/2021/06/22/QnxUwyKf8tADFGo.png)



pom.xml

![image-20210622145829524](https://i.loli.net/2021/06/22/iXOZRu2Pmn5erqH.png)















[深入理解 Spring 控制反转与依赖注入_Leo的博客-CSDN博客_spring控制反转和依赖注入](https://leehao.blog.csdn.net/article/details/80803865)









## JDBC

> JDBC(Java DataBase Connectivity, 简称JDBC)是Java中用于规范应用程序如何来访问数据库的应用程序接口(API),它提供了查询和更新数据库中数据的方法

[(16条消息) 使用 JDBC 连接MySQL_Leo的博客-CSDN博客](https://blog.csdn.net/lihao21/article/details/80694503)

![image-20210623140840804](../AppData/Roaming/Typora/typora-user-images/image-20210623140840804.png)



引入依赖

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.31</version>
</dependency>
```



创建数据库

```mysql
CREATE TABLE EMPLOYEES (
    EMP_ID BIGINT NOT NULL AUTO_INCREMENT,
    NAME VARCHAR(252),
    DEPARTMENT VARCHAR(128),
    SALARY BIGINT,
    JOINED_ON TIMESTAMP,
    PRIMARY KEY (EMP_ID)
);

INSERT INTO EMPLOYEES (EMP_ID, NAME, DEPARTMENT, SALARY, JOINED_ON) VALUES (1, 'Nataraja G', 'Documentation', 10000, CURRENT_TIMESTAMP);
INSERT INTO EMPLOYEES (EMP_ID, NAME, DEPARTMENT, SALARY, JOINED_ON) VALUES (2, 'Amar M', 'Entertainment', 12000, CURRENT_TIMESTAMP);
INSERT INTO EMPLOYEES (EMP_ID, NAME, DEPARTMENT, SALARY, JOINED_ON) VALUES (3, 'Nagesh Y', 'Admin', 25000, CURRENT_TIMESTAMP);
INSERT INTO EMPLOYEES (EMP_ID, NAME, DEPARTMENT, SALARY, JOINED_ON) VALUES (4, 'Vasu V', 'Security', 2500, CURRENT_TIMESTAMP);
```



编写代码

```java
package org.example;

import java.sql.*;

/**
 * Hello world!
 *
 */
public class App {
    public static void main(String[] args) {
        System.out.println("MySQL JDBC Example.");
        Connection conn = null;
        String url = "jdbc:mysql://496e43891749bbb1.c.cloudtogo.cn:35236/test?autoReconnect=true&useSSL=false";
        String driver = "com.mysql.jdbc.Driver";
        String userName = "root";
        String password = "123456";
        Statement stmt = null;
        ResultSet rs = null;
        try {
            Class.forName(driver);
            conn = DriverManager.getConnection(url, userName, password);
            stmt = conn.createStatement();
            String sql = "select * from EMPLOYEES";
            rs = stmt.executeQuery(sql);
            while (rs.next()) {
                int id = rs.getInt("emp_id");
                String name = rs.getString("name");
                System.out.println("id = " + id + ", name = " + name);
            }
            // 关闭资源
            rs.close();
            stmt.close();
            conn.close();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (rs != null) {
                try {
                    rs.close();
                } catch (SQLException sqlEx) { } // ignore
            }

            if (stmt != null) {
                try {
                    stmt.close();
                } catch (SQLException sqlEx) { } // ignore
            }
        }
    }
}
```

执行

![image-20210623141743450](https://i.loli.net/2021/06/23/qnJoW6Tbt3Pjpvm.png)







[(16条消息) spring boot 中@SpringBootApplication详解_小一佳-CSDN博客](https://blog.csdn.net/qq_33863843/article/details/80824737)



[J2EE总体的学习计划_Leo的博客-CSDN博客](https://leehao.blog.csdn.net/article/details/1900028)

