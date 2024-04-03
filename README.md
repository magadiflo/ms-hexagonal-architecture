# [Arquitectura Hexagonal con Spring Boot — Parte 1 al 4](https://medium.com/@oliveraluis11/arquitectura-hexagonal-con-spring-boot-parte-1-57b797eca69c)

Tutorial tomado de la página web de **medium** del autor **Luis Antonio**.

---

## Dependencias

````xml
<!--Spring Boot 3.2.4-->
<!--Java 21-->
<!--spring-cloud.version 2023.0.1-->
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>

    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
````