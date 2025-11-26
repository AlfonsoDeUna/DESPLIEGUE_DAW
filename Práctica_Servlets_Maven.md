Crear una aplicación web con Maven
Desde una ventana de ms-dos o bash de linux nos vamos al directorio donde queramos crear el proyecto y escribimos

mvn archetype:create -DgroupId=chuidiang.ejemplos -DartifactId=ServletMaven -DarchetypeArtifactId=maven-archetype-webapp
donde chuidiang.ejemplos es el nombre del paquete que queramos para nuestras clases y ServletMaven es el nombre que queramos para nuestro proyecto.

Esto creará un directorio ServletMaven con la siguiente estructura de subdirectorios/ficheros por debajo
```
SERVLETMAVEN
├───pom.xml
└───src
    └───main
        ├───resources
        └───webapp
            ├───index.jsp
            └───WEB-INF
                └───web.xml
```

El fichero pom.xml es propio de maven. El fichero index.jsp es una simple página jsp con un "Hola Mundo". El fichero web.xml tiene la información mínima para ese fichero
```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>
</web-app>
```

Añadir dependencias a nuestra aplicación web con maven
Para que maven sea capaz de compilar nuestro proyecto, debemos añadirle los jar de los que dependemos. Mínimo necesitamos la dependencia del jar donde están la API de Servlet de Sun. Podríamos poner otras dependencias, como el conector de Mysql-Java.

Maven, por defecto, compila con java 1.3. Suele ser buena idea indicarle que lo haga con java 5 o la versión más moderna que tengamos.

Tanto para añadir la dependencia de la API Servlet de Sun como para indicar que compile con java 5, editamos el fichero pom.xml y añadimos las siguientes líneas

```xml
<project>
  ...  
  <dependencies>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>servlet-api</artifactId>
      <version>2.4</version>
      <scope>provided</scope>
    </dependency>
    ...
  </dependencies>
  <build>
      ...
      <plugins>
         <plugin>
             <groupId>org.apache.maven.plugins</groupId>
             <artifactId>maven-compiler-plugin</artifactId>
             <configuration>
                 <source>1.5</source>
                 <target>1.5</target>
             </configuration>
         </plugin>
      </plugins>

  </build>
</project>
```

Una de las maravillas de maven es que no necesitamos tener en ningún sitio de nuestro ordenador el jar con la API de Servlet. Cuando compilemos la primera vez, maven buscará este jar (y cualquier otro de los que dependa y esté en el repositorio de jars de ibiblio) de internet y se lo bajará automáticamente.

Hay que tener cuidad con la versión de servlet-api que se pone aquí. Debe ser compatible con la que viene con Tomcat. De hecho, podemos poner en el fichero pom.xml que no incluya el servlet-api.jar dentro del fichero war que se generará más adelante.

```xml
<project>
  [...]
  <dependencies>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>servlet-api</artifactId>
      <version>2.4</version>
      <scope>provided</scope>  <!-- Se cambia compile por provided -->
    </dependency>
    ...
  </dependencies>
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-war-plugin</artifactId>
        <version>2.0</version>
        <configuration>      <!-- Se añade esto en la configuracion -->
          <archive>
            <manifest>
              <addClasspath>false</addClasspath>
            </manifest>
          </archive>
        </configuration>
      </plugin>
      [...]
    </plugins>
  </build>
  [...]  
</project>
```

Creamos los fuentes de nuestro Servlet
Debajo del directorio main, creamos un subdirectorio java y a partir de ahí ponemos nuestros fuentes. En nuestro caso crearemos un sudirectorio chuidiang, debajo uno ejemplos y finalmente dentro de este nuestro ServletMaven.java. La estructura de directorios quedará así

```
SERVLETMAVEN
└───src
    └───main
        ├───java
        │   └───chuidiang
        │       └───ejemplos
        │           └───ServletMaven.java
        ├───resources
        └───webapp
            ├───chuidiang
            │   └───ejemplos
            └───WEB-INF
```

Nuestro programa java será un simple "Hola Mundo", tal que así

```java
package chuidiang.ejemplos;

import java.io.*;

import javax.servlet.*;
import javax.servlet.http.*;

public class ServletMaven extends HttpServlet {

	private static final long serialVersionUID = 1L;

	public void doGet(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {
		// Obtenemos un objeto Print Writer para enviar respuesta
		res.setContentType("text/html");
		PrintWriter pw = res.getWriter();
		pw.println("<html><head>");
		pw.println("<TITLE>Servlet Maven</TITLE>");
		pw.println("</head><body>");
		pw.println("<p>Hola Mundo</p>");
		pw.println("</body>");
		pw.close();
	}
	public void doPost(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {
		doGet(req,res);
	}
}
```

He implementado ambos métodos doGet() y doPost() porque inicialmente implementé sólo doPost() y obtuve el error desde el navegador de que HTTP GET no está soportado. El motivo es que si la petición del navegador se hace con el método GET y el Servlet sólo implementa el método doPost(), no puede responder a una petición GET y obtenemos el error.

La implementación de doPost() simplemente redirige la llamada a doGet().

Preparar el fichero web.xml
El el fichero web.xml debemos poner las líneas para nuestro Servlet

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
	<display-name>Archetype Created Web Application</display-name>

   <servlet>
        <servlet-name>ServletMaven</servlet-name>
	<servlet-class>chuidiang.ejemplos.ServletMaven</servlet-class>
    </servlet>

    <servlet-mapping>
	    <servlet-name>ServletMaven</servlet-name>
	    <url-pattern>/ServletMaven</url-pattern>
    </servlet-mapping>

</web-app>
```

Compilar el proyecto
Una vez preparado todo, el comando de maven

``` 
mvn package
```
crea un subdirectorio target, compilará todo el proyecto y meterá los .class en el subdirectorio target, generará toda la estructura de directorios para el despliegue y finalmente la empaqueta en un fichero ServletMaven.war y meterá todo esto también en target.

El resultado final puede ser como este
```
SERVLETMAVEN
├───.settings
├───src
│   └───main
│       ├───java
│       │   └───chuidiang
│       │       └───ejemplos
│       ├───resources
│       └───webapp
│           ├───chuidiang
│           │   └───ejemplos
│           └───WEB-INF
└───target
    ├───ServletMaven.war
    ├───classes
    │   └───chuidiang
    │       └───ejemplos
    │           └───ServletMaven.class
    └───ServletMaven
        ├───chuidiang
        │   └───ejemplos
        │       └───ServletMaven.java
        ├───META-INF
        └───WEB-INF
            ├───web.xml
            ├───classes
            │   └───chuidiang
            │       └───ejemplos
            │           └───ServletMaven.class
            └───lib
                └───servlet-api-2.4.jar
```

Desplegar el proyecto
Ahora, en el directorio webapp de donde tengamos instalado Tomcat, podemos optar por copiar el directorio ServletMaven que hay justo debajo de target, o bien copiar el fichero ServletMaven.war. En mi caso me he decidido por esto último. He copiado el fichero ServletMaven.war en el directorio webapp de Tomcat.

Para probarlo, abro el navegador y escribo
```
http://localhost:8080/ServletMaven/ServletMaven
```
y obtengo el "Hola Mundo"
