# [Arquitectura Hexagonal con Spring Boot — Parte 1 al 4](https://medium.com/@oliveraluis11/arquitectura-hexagonal-con-spring-boot-parte-1-57b797eca69c)

Tutorial tomado de la página web de **medium** del autor **Luis Antonio**.

---

## ¿Qué es la arquitectura hexagonal o patrón de puertos  y adaptadores?

![01.arquitectura-hexagonal.png](./assets/01.arquitectura-hexagonal.png)

> `La Arquitectura Hexagonal` propone que nuestro dominio sea el núcleo de las capas y que este no se acople a nada
> externo. En lugar de hacer uso explícito y **mediante el principio de inversión de dependencias nos acoplamos a
> contratos** `(interfaces o puertos)` y no a `implementaciones concretas`.

`Puertos`, **definición de una interfaz pública.** Permiten comunicar cada una de las capas de la aplicación. *Por
ejemplo, si queremos acceder a algún dato de la capa de aplicación, desde la capa de infraestructura, pues lo hacemos a
través de un puerto.* Y entonces, **¿qué son los puertos?, en esta arquitectura serían sencillamente interfaces.**

`Adaptadores`, **especialización de un puerto para un contexto concreto.** Puerta de comunicación con aplicaciones
externas. Es decir, es cualquier cosa que tenga que ver con algo externo a nuestra aplicación, *por ejemplo: una
conexión con base de datos, una petición a un servicio REST externo, etc.*
Esto se realiza desde la capa de infraestructura.

## Recursos

Utilizaremos [JsonPlaceholder API](https://jsonplaceholder.typicode.com/) como recurso que nos ayudará a dar un ejemplo
de cómo sería consumir una API utilizando arquitectura hexagonal.

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

## Referencia para la estructura del proyecto

A la arquitectura hexagonal (Puertos y Adaptadores) que vamos a desarrollar la acompañaremos con
**vertical slicing (corte vertical)**.
Esta referencia lo podemos ver en el video siguiente **(click en la imagen)**:

[![02.vertical-slicing.png](./assets/02.vertical-slicing.png)](https://www.youtube.com/watch?v=eNFAJbWCSww&feature=youtu.be)

`Vertical Slicing`, es una práctica de arquitectura de software que implica organizar la estructura de directorios de un
proyecto de manera que cada directorio represente una característica o funcionalidad completa del sistema, desde la
interfaz de usuario hasta la capa de datos. En lugar de organizar los archivos por tipo (por ejemplo, todos los modelos
juntos, todas las vistas juntas, etc.), el "vertical slicing" agrupa los archivos relacionados con una característica
específica en un solo lugar.

Esta práctica ayuda a mantener el código relacionado estrechamente junto y facilita la comprensión y modificación de una
característica particular del sistema. Además, el "vertical slicing" facilita la escalabilidad del proyecto, ya que cada
característica puede ser desarrollada, probada y desplegada de forma independiente, lo que permite un desarrollo más
ágil y flexible.

Por ejemplo, en un proyecto web de comercio electrónico, podrías tener un directorio para la autenticación de usuarios,
otro para la gestión de productos, otro para el proceso de pago, etc. Cada directorio contendría todos los archivos
relacionados con esa característica específica, como modelos de datos, controladores, vistas, rutas, etc. Esto hace que
sea más fácil para los desarrolladores trabajar en una parte específica del sistema sin tener que navegar a través de
múltiples directorios para encontrar los archivos relacionados.

![03.vertical-slicing.png](./assets/03.vertical-slicing.png)

---

# DOMAIN

---

En esta sección implementaremos la capa de `dominio`, donde vamos a definir nuestros modelos y los repositorios.

## Domain - Modelos

La siguiente clase define únicamente los atributos necesarios para nuestro `Post` para poder realizar operaciones
como `inserción`, `actualización` o `eliminación`:

````java

@Getter
@Builder
public class PostCommand {
    private Long userId;
    private String body;
    private String title;
}
````

En esta otra clase se definen todos los atributos, incluidos el `id` del `Post`, dado que se usará esta clase para
poder recuperar información del post. A diferencia de la clase anterior, esta clase representa la información que
vamos a recuperar, mientras que la clase anterior, representa la información que se va a persistir.

````java

@AllArgsConstructor
@Getter
public class PostQuery {
    private Long id;
    private Long userId;
    private String body;
    private String title;
}
````

## Domain - Repositorios

A continuación se muestra la definición de los `puertos (interfaces)`, que más adelante los usaremos para poder
realizar inyección de dependencias.

````java
public interface PostCommandRepository {
    Optional<PostQuery> createPost(PostCommand postCommand);

    Optional<PostQuery> updatePost(PostCommand postCommand);

    void deletePost(Long id);
}
````

````java
public interface PostQueryRepository {
    List<PostQuery> findAllPosts();

    Optional<PostQuery> findPostById(Long id);

    List<PostQuery> searchPostsByParams(Map<String, String> params);
}
````

Como vemos, nuestros puertos serían:

- `PostCommandRepository`
- `PostQueryRepository`

Las implementaciones de estas `interfaces (puertos)`, es decir, los `adaptadores (implementación de interfaz)` se
encontrarán en la capa de `infraestructura`.