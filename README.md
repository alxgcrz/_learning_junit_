# JUnit 5

JUnit es un framework Java para implementar test en Java. A diferencia de versiones anteriores, JUnit 5 se compone de tres sub-proyectos:

- **JUnit Platform**. Sirve como base sobre la cual se pueden ejecutar diferentes frameworks de pruebas. Es la plataforma que inicia el motor de pruebas y ejecuta las pruebas. A su vez se compone de:

  - **Launcher API**: permite a los clientes (IDEs, herramientas de construcción, etcétera...) descubrir y ejecutar pruebas.

  - **Test Engine API**: permite a los desarrolladores crear sus propios motores de pruebas que se pueden integrar con la plataforma. Por ejemplo se puede utilizar para ejecutar pruebas escritas en Spock, Cucumber, FitNesse, etcétera..., siempre y cuando haya un motor de pruebas compatible.

  - **Console Launcher**: una herramienta de línea de comandos para ejecutar pruebas.

- **JUnit Jupiter**: es la combinación del nuevo modelo de programación y la implementación del motor de pruebas para JUnit 5.

- **JUnit Vintage**: proporciona compatibilidad con versiones anteriores, permitiendo que las pruebas escritas con JUnit 3 y JUnit 4 se ejecuten en JUnit 5. Requiere que JUnit 4.12 esté presente en el class path o el module path.

JUnit 5 requiere **Java 8 (o superior)**.

[Más información](https://junit.org/junit5/docs/current/user-guide/#overview-what-is-junit-5)

## [Writing Tests](https://junit.org/junit5/docs/current/user-guide/#writing-tests)

```java
import org.junit.jupiter.api.*;

import static org.junit.jupiter.api.Assertions.*;

class ExampleTest {

    @BeforeAll
    static void setupAll() {
        System.out.println("Ejecutado una vez antes de todas las pruebas.");
    }

    @BeforeEach
    void setup() {
        System.out.println("Ejecutado antes de cada prueba.");
    }

    @Test
    void testAddition() {
        assertEquals(2, 1 + 1, "1 + 1 debe ser igual a 2");
    }

    @Test
    void testSubtraction() {
        assertEquals(0, 1 - 1, "1 - 1 debe ser igual a 0");
    }

    @AfterEach
    void tearDown() {
        System.out.println("Ejecutado después de cada prueba.");
    }

    @AfterAll
    static void tearDownAll() {
        System.out.println("Ejecutado una vez después de todas las pruebas.");
    }
}
```

### [Annotations](https://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations)

JUnit se basa en [anotaciones](https://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations). Casi todas las anotaciones están en el paquete [`org.junit.jupiter.api`](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/package-summary.html) en el módulo _'junit-jupiter-api'_:

- `@Test`: indica que el método que la contiene es un test.

- `@ParameterizedTest`: indica que ese método es un [test parametrizado](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests).

- `@RepeatedTest`: indica que ese método es una plantilla para un [test repetible](https://junit.org/junit5/docs/current/user-guide/#writing-tests-repeated-tests).

- `@Before` (JUnit4) / `@BeforeEach` (JUnit5): indica que ese método anotado debe ser ejecutado **antes** que cada método anotado como `@Test`, `RepeatedTest`, `@ParameterizedTest` o `@TestFactory`.

- `@After` (JUnit4) / `@AfterEach` (JUnit5): indica que ese método anotado debe ser ejecutado **después** que cada método anotado como `@Test`, `RepeatedTest`, `@ParameterizedTest` o `@TestFactory`.

- `@BeforeClass` (JUnit4) / `@BeforeAll` (JUnit5): indica que ese método anotado debe ser ejecutado **antes** que cualquier otro método anotado como `@Test`, `RepeatedTest`, `@ParameterizedTest` o `@TestFactory`.

- `@AfterClass` (JUnit4) / `@AfterAll` (JUnit5): indica que ese método anotado debe ser ejecutado **después** de todos los métodos anotado como `@Test`, `RepeatedTest`, `@ParameterizedTest` o `@TestFactory`.

- `@Ignore` (JUnit 4) / `@Disabled` (JUnit5): se utiliza para [deshabilitar el test](https://junit.org/junit5/docs/current/user-guide/#writing-tests-disabling).

- `@Tag`: se utiliza para declarar [etiquetas](https://junit.org/junit5/docs/current/user-guide/#writing-tests-tagging-and-filtering) que permitan filtrar por tests.

- `@Timeout`: se utiliza para fallar un test, una factoría de test, una plantilla de test o ciclo de vida si su ejecución excede una duración determinada.

- `@ExtendWith`: se utiliza para [registrar](https://junit.org/junit5/docs/current/user-guide/#extensions-registration-declarative) extensiones de forma declarativa.

- `@DisplayName("cadena")` (JUnit5): Declara un nombre de visualización personalizado para la clase de prueba o el método de prueba. En lugar de usar esta anotación es recomendable utilizar nombres para los métodos lo suficientemente descriptivos como para que no sea necesario usar esta anotación.

#### [Meta-Annotations and Composed Annotations](https://junit.org/junit5/docs/current/user-guide/#writing-tests-meta-annotations)

Las anotaciones de JUnit Jupiter se pueden utilizar como meta-anotaciones. Eso significa que se pueden definir anotaciones compuestas personalizadas que heredarán automáticamente la semántica de las meta-anotaciones.

Por ejemplo, en vez de copiar y pegar la anotación `@Tag("fast")` (que permite etiquetar y agrupar pruebas), es posible crear una anotación personalizada como por ejemplo `@Fast`:

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import org.junit.jupiter.api.Tag;

@Target({ ElementType.TYPE, ElementType.METHOD })
@Retention(RetentionPolicy.RUNTIME)
@Tag("fast")
public @interface Fast { }
```

Esta anotación sería equivalente y podría utilizarse cualquiera de las dos anotaciones:

```java
@Fast
@Test
void myFastTest() {
    // ...
}

@Tag("fast")
@Test
void otherFastTest() {
    // ...
}
```

Incluso se podría ir un paso más allá introduciendo una anotación `@FastTest` personalizada que se puede usar como reemplazo directo de `@Tag("fast")` y `@Test`:

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.Test;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Tag("fast")
@Test
public @interface FastTest { }
```

### [Definitions](https://junit.org/junit5/docs/current/user-guide/#writing-tests-definitions)

- **Container**: un nodo en el árbol de prueba que contiene otros contenedores o pruebas como sus hijos (por ejemplo, una clase de prueba).

- **Test**: un nodo en el árbol de prueba que verifica el comportamiento esperado cuando se ejecuta (por ejemplo, un método `@Test`).

- **Lifecycle Method**: cualquier método que esté directamente anotado o meta-anotado con `@BeforeAll`, `@AfterAll`, `@BeforeEach` o `@AfterEach`.

- **Test Class**: cualquier clase de nivel superior, clase miembro estática o clase `@Nested` que contenga al menos un método de prueba, es decir, un contenedor. Las clases de prueba no deben ser abstractas y deben tener un único constructor.

- **Test Method**: cualquier método de instancia que esté directamente anotado o meta- anotado con `@Test`, `@RepeatedTest`, `@ParameterizedTest`, `@TestFactory` o `@TestTemplate`. Con la excepción de `@Test`, estos crean un contenedor en el árbol de pruebas que agrupa pruebas o, potencialmente (para `@TestFactory`), otros contenedores.

### [Test Classes and Methods](https://junit.org/junit5/docs/current/user-guide/#writing-tests-classes-and-methods)

Los métodos de prueba y los métodos de ciclo de vida pueden ser declarados localmente dentro de la clase de prueba actual, heredados de superclases o heredados de [interfaces](#test-interfaces-and-default-methods). Además, los métodos de prueba y los métodos de ciclo de vida **no deben ser abstractos y no deben retornar un valor** (excepto los métodos `@TestFactory`, que deben retornar un valor).

No es necesario que las clases de prueba, los métodos de prueba y los métodos de ciclo de vida sean `public`, pero **no deben ser privados**.

Generalmente se recomienda omitir el modificador `public` para clases de prueba, métodos de prueba y métodos de ciclo de vida a menos que exista una razón técnica para hacerlo.

Una clase estándard de prueba con todos los métodos del ciclo de vida:

```java
import static org.junit.jupiter.api.Assertions.fail;
import static org.junit.jupiter.api.Assumptions.assumeTrue;

import org.junit.jupiter.api.AfterAll;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Test;

class StandardTests {

    @BeforeAll
    static void initAll() {
    }

    @BeforeEach
    void init() {
    }

    @Test
    void succeedingTest() {
    }

    @Test
    void failingTest() {
        fail("a failing test");
    }

    @Test
    @Disabled("for demonstration purposes")
    void skippedTest() {
        // not executed
    }

    @Test
    void abortedTest() {
        assumeTrue("abc".contains("Z"));
        fail("test should have been aborted");
    }

    @AfterEach
    void tearDown() {
    }

    @AfterAll
    static void tearDownAll() {
    }

}
```

### [Display Names](https://junit.org/junit5/docs/current/user-guide/#writing-tests-display-names)

Las clases de prueba y los métodos de prueba pueden declarar nombres personalizados mediante `@DisplayName`, con espacios, caracteres especiales e incluso emojis, que se mostrarán en los informes de prueba y por los ejecutores de prueba y los IDE:

```java
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

@DisplayName("A special test case")
class DisplayNameDemo {

    @Test
    @DisplayName("Custom test name containing spaces")
    void testWithDisplayNameContainingSpaces() {
        // ...
    }

    @Test
    @DisplayName("╯°□°）╯")
    void testWithDisplayNameContainingSpecialCharacters() {
        // ...
    }

    @Test
    @DisplayName("😱")
    void testWithDisplayNameContainingEmoji() {
        // ...
    }

}
```

#### [Display Name Generators](https://junit.org/junit5/docs/current/user-guide/#writing-tests-display-name-generator)

JUnit Jupiter admite generadores de nombres personalizados que se pueden configurar mediante la anotación `@DisplayNameGeneration`. Los valores proporcionados a través de anotaciones `@DisplayName` siempre tienen prioridad sobre los nombres generados por `@DisplayNameGenerator`.

Esta anotación se utiliza para configurar una estrategia global de generación de nombres de visualización (_'display names'_) para todas las pruebas en una clase de prueba. Esto es útil cuando se requiere aplicar una convención de nombres de manera consistente sin tener que especificar `@DisplayName` individualmente en cada método de prueba.

### [Assertions](https://junit.org/junit5/docs/current/user-guide/#writing-tests-assertions)

JUnit Jupiter incluye muchos de los métodos de aserción que tiene JUnit 4 y añade algunos que se prestan bien para ser utilizados con lambdas de Java 8. Todas las aserciones de JUnit Jupiter son métodos estáticos en la clase [org.junit.jupiter.api.Assertions](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/Assertions.html).

Algunos de las aserciones más comunes son los siguientes:

- **assertEquals(expected, actual, message)**

- **assertNotEquals(unexpected, actual, message)**

- **assertTrue(condition, message)**

- **assertFalse(condition, message)**

- **assertNull(object, message)**

- **assertNotNull(object, message)**

- **assertThrows(expectedType, executable)**

- **assertAll(executables...)**

```java
import org.junit.jupiter.api.*;

import static org.junit.jupiter.api.Assertions.*;

class CommonAssertionsTest {

    @Test
    @DisplayName("Prueba de assertEquals y assertNotEquals")
    void testEquals() {
        assertEquals(2, 1 + 1, "1 + 1 debe ser igual a 2");
        assertNotEquals(3, 1 + 1, "1 + 1 no debe ser igual a 3");
    }

    @Test
    @DisplayName("Prueba de assertTrue y assertFalse")
    void testTrueFalse() {
        assertTrue(5 > 1, "5 debe ser mayor que 1");
        assertFalse(5 < 1, "5 no debe ser menor que 1");
    }

    @Test
    @DisplayName("Prueba de assertNull y assertNotNull")
    void testNull() {
        String nullString = null;
        String nonNullString = "JUnit";
        assertNull(nullString, "El objeto debe ser nulo");
        assertNotNull(nonNullString, "El objeto no debe ser nulo");
    }

    @Test
    @DisplayName("Prueba de assertThrows")
    void testThrows() {
        assertThrows(ArithmeticException.class, () -> {
            int result = 1 / 0;
        }, "Debe lanzar ArithmeticException");
    }

    @Test
    @DisplayName("Prueba de assertAll")
    void testAll() {
        assertAll("Pruebas de suma",
            () -> assertEquals(4, 2 + 2, "2 + 2 debe ser igual a 4"),
            () -> assertTrue(4 > 0, "4 debe ser mayor que 0"),
            () -> assertFalse(4 < 0, "4 no debe ser menor que 0")
        );
    }
}
```

#### [Kotlin Assertion Support](https://junit.org/junit5/docs/current/user-guide/#writing-tests-assertions-kotlin)

JUnit Jupiter también viene con algunos métodos de aserción que se prestan bien para ser utilizados en Kotlin. Todas las asercioness de JUnit Jupiter en Kotlin son funciones de nivel superior en el paquete _'org.junit.jupiter.api'_.

#### [Third-party Assertion Libraries](https://junit.org/junit5/docs/current/user-guide/#writing-tests-assertions-third-party)

Aunque las facilidades de aserción proporcionadas por JUnit Jupiter son suficientes para muchos escenarios de prueba, hay ocasiones en las que se desea o se requiere más potencia y funcionalidades adicionales, como los _'matchers'_.

En tales casos, el equipo de JUnit recomienda el uso de bibliotecas de aserción de terceros como [AssertJ](https://assertj.github.io/doc/), [Hamcrest](https://hamcrest.org/JavaHamcrest/), [Truth](https://truth.dev/), etc...:

```java
import org.junit.jupiter.api.Test;

import java.util.Arrays;
import java.util.List;

import static org.assertj.core.api.Assertions.*;

public class ExampleTest {

    @Test
    void testWithAssertJ() {
        // Given
        List<Integer> numbers = Arrays.asList(1, 2, 3);

        // Assertions with AssertJ
        assertThat(numbers).hasSize(3)
                           .contains(2)
                           .doesNotContain(4);
    }
}
```

### [Assumptions](https://junit.org/junit5/docs/current/user-guide/#writing-tests-assumptions)

Los _"assumptions"_ (suposiciones) en JUnit se utilizan para realizar **pruebas condicionales**. Permiten que una prueba se ejecute solo si se cumplen ciertas condiciones. Si la condición no se cumple, la prueba se marca como _"skipped"_ (omitida) en lugar de _"failed"_ (fallida). Esto es útil para situaciones donde la prueba no es relevante o no puede ejecutarse en ciertos entornos o configuraciones.

JUnit Jupiter viene con un subconjunto de los métodos de suposición que JUnit 4 proporciona y añade algunos que se prestan bien para ser utilizados con expresiones lambda y referencias de métodos de Java 8. Todas las suposiciones de JUnit Jupiter son métodos estáticos en la clase `org.junit.jupiter.api.Assumptions`.

Algunas de las más comunes serían:

- **`Assumptions.assumeTrue(condition)`**: la prueba se ejecuta solo si la condición es verdadera.

- **`Assumptions.assumeFalse(condition)`**: la prueba se ejecuta solo si la condición es falsa.

- **`Assumptions.assumeTrue(condition, "Message if condition is false")`**: la prueba se ejecuta solo si la condición es verdadera, con un mensaje si no se cumple.

- **`assumingThat(boolean condition, Executable executable)`**: ejecuta un bloque de código solo si la condición es verdadera.

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assumptions.assumeTrue;
import static org.junit.jupiter.api.Assumptions.assumingThat;

import example.util.Calculator;

import org.junit.jupiter.api.Test;

class AssumptionsDemo {

    private final Calculator calculator = new Calculator();

    @Test
    void testOnlyOnCiServer() {
        assumeTrue("CI".equals(System.getenv("ENV")));
        // remainder of test
    }

    @Test
    void testOnlyOnDeveloperWorkstation() {
        assumeTrue("DEV".equals(System.getenv("ENV")),
            () -> "Aborting test: not on developer workstation");
        // remainder of test
    }

    @Test
    void testInAllEnvironments() {
        assumingThat("CI".equals(System.getenv("ENV")),
            () -> {
                // perform these assertions only on the CI server
                assertEquals(2, calculator.divide(4, 2));
            });

        // perform these assertions in all environments
        assertEquals(42, calculator.multiply(6, 7));
    }

}
```

### [Disabling Tests](https://junit.org/junit5/docs/current/user-guide/#writing-tests-disabling)

Se pueden deshabilitar clases de prueba completas o métodos de prueba individuales mediante la anotación `@Disabled`.

Esta anotación puede declararse sin proporcionar un motivo; sin embargo, el equipo de JUnit recomienda que los desarrolladores proporcionen una breve explicación de por qué se ha deshabilitado una clase o método de prueba.

Clase de prueba deshabilitada:

```java
import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Test;

@Disabled("Disabled until bug #99 has been fixed")
class DisabledClassDemo {

    @Test
    void testWillBeSkipped() {
    }

}
```

Método de prueba deshabilitado en una clase de prueba:

```java
import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Test;

class DisabledTestsDemo {

    @Disabled("Disabled until bug #42 has been resolved")
    @Test
    void testWillBeSkipped() {
    }

    @Test
    void testWillBeExecuted() {
    }

}
```

Esta anotación `@Disabled` no se hereda, por lo que subclases que hereden de claes deshabilitadas o con métodos deshabilitados deberán redeclarar de nuevo esta anotación.

### [Conditional Test Execution](https://junit.org/junit5/docs/current/user-guide/#writing-tests-conditional-execution)

La API _"ExecutionCondition"_ en JUnit Jupiter permite a los desarrolladores decidir si un conjunto de pruebas o una prueba individual debe ejecutarse o no, basándose en ciertas condiciones. Estas condiciones pueden ser definidas de forma programática.

Un ejemplo básico de una condición es _"DisabledCondition"_, que se utiliza con la anotación `@Disabled`. Esta anotación se usa para desactivar una prueba, evitando que se ejecute.

Además de esta anotación, JUnit Jupiter incluye varias otras anotaciones en el paquete ['org.junit.jupiter.api.condition'](https://junit.org/junit5/docs/5.2.0/api/org/junit/jupiter/api/condition/package-summary.html) que permiten activar o desactivar pruebas de manera declarativa, es decir, simplemente aplicando una anotación a las pruebas o contenedores de pruebas.

Es recomendable indicar los motivos por los que una prueba está desactivada. Para ello, muchas de estas anotaciones tienen un atributo `disabledReason` donde se puede especificar el motivo.

Cualquiera de las anotaciones condicionales listadas en las secciones siguientes también puede usarse como meta-anotación para crear una anotación compuesta personalizada, como ya se explicó en [meta-anotaciones](#meta-annotations-and-composed-annotations):

```java
@Test
@EnabledOnOs(MAC)
void onlyOnMacOs() {
    // ...
}

// Meta-anotación entre '@Test' y '@EnabledOnOs(MAC)'
@TestOnMac
void testOnMac() {
    // ...
}

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Test
@EnabledOnOs(MAC)
@interface TestOnMac {}
```

A menos que se indique lo contrario, cada una de las anotaciones condicionales enumeradas en las siguientes secciones **solo puede declararse una vez en una interfaz de prueba, clase de prueba o método de prueba**. Si una anotación condicional está presente directamente, indirectamente o como meta-anotación múltiples veces en un mismo elemento, solo se usará la primera anotación descubierta por JUnit.

#### [Operating System and Architecture Conditions](https://junit.org/junit5/docs/current/user-guide/#writing-tests-conditional-execution-os)

Un contenedor o prueba se puede habilitar o deshabilitar en un sistema operativo, arquitectura o combinación de ambos en particular a través de las anotaciones `@EnabledOnOs` y `@DisabledOnOs`.

Ejecución condicional basada en el **sistema operativo**:

```java
@Test
@EnabledOnOs(MAC)
void onlyOnMacOs() {
    // ...
}

@Test
@EnabledOnOs({ LINUX, MAC })
void onLinuxOrMac() {
    // ...
}

@Test
@DisabledOnOs(WINDOWS)
void notOnWindows() {
    // ...
}
```

Ejecución condicional basada en **arquitectura**:

```java
@Test
@EnabledOnOs(architectures = "aarch64")
void onAarch64() {
    // ...
}

@Test
@DisabledOnOs(architectures = "x86_64")
void notOnX86_64() {
    // ...
}

@Test
@EnabledOnOs(value = MAC, architectures = "aarch64")
void onNewMacs() {
    // ...
}

@Test
@DisabledOnOs(value = MAC, architectures = "aarch64")
void notOnNewMacs() {
    // ...
}
```

#### [Java Runtime Environment Conditions](https://junit.org/junit5/docs/current/user-guide/#writing-tests-conditional-execution-jre)

Un contenedor o prueba puede habilitarse o deshabilitarse en versiones particulares del Entorno de Ejecución de Java (JRE) mediante las anotaciones `@EnabledOnJre` y `@DisabledOnJre` o en un rango particular de versiones del JRE mediante las anotaciones `@EnabledForJreRange` y `@DisabledForJreRange`.

El rango predeterminado establece "JRE.JAVA_8" como el límite inferior (mínimo) y "JRE.OTHER" como el límite superior (máximo), lo cual permite el uso de rangos semiabiertos.

```java
@Test
@EnabledOnJre(JAVA_8)
void onlyOnJava8() {
    // ...
}

@Test
@EnabledOnJre({ JAVA_9, JAVA_10 })
void onJava9Or10() {
    // ...
}

@Test
@EnabledForJreRange(min = JAVA_9, max = JAVA_11)
void fromJava9to11() {
    // ...
}

@Test
@EnabledForJreRange(min = JAVA_9)
void fromJava9toCurrentJavaFeatureNumber() {
    // ...
}

@Test
@EnabledForJreRange(max = JAVA_11)
void fromJava8To11() {
    // ...
}

@Test
@DisabledOnJre(JAVA_9)
void notOnJava9() {
    // ...
}

@Test
@DisabledForJreRange(min = JAVA_9, max = JAVA_11)
void notFromJava9to11() {
    // ...
}

@Test
@DisabledForJreRange(min = JAVA_9)
void notFromJava9toCurrentJavaFeatureNumber() {
    // ...
}

@Test
@DisabledForJreRange(max = JAVA_11)
void notFromJava8to11() {
    // ...
}
```

#### [Native Image Conditions](https://junit.org/junit5/docs/current/user-guide/#writing-tests-conditional-execution-native)

Un contenedor o prueba puede habilitarse o deshabilitarse dentro de una imagen nativa de GraalVM mediante las anotaciones `@EnabledInNativeImage` y `@DisabledInNativeImage`. Estas anotaciones se utilizan típicamente cuando se ejecutan pruebas dentro de una imagen nativa usando los complementos de Gradle y Maven del proyecto "GraalVM Native Build Tools".

```java
@Test
@EnabledInNativeImage
void onlyWithinNativeImage() {
    // ...
}

@Test
@DisabledInNativeImage
void neverWithinNativeImage() {
    // ...
}
```

#### [System Property Conditions](https://junit.org/junit5/docs/current/user-guide/#writing-tests-conditional-execution-system-properties)

Un contenedor o prueba puede habilitarse o deshabilitarse en función del valor de una **propiedad con nombre del sistema JVM** mediante las anotaciones `@EnabledIfSystemProperty` y `@DisabledIfSystemProperty`. El valor proporcionado a través del atributo _"matches"_ se interpretará como una expresión regular.

A partir de JUnit Jupiter 5.6, estas anotaciones son **anotaciones repetibles**. En consecuencia, estas anotaciones pueden declararse múltiples veces en una interfaz de prueba, clase de prueba o método de prueba.

```java
@Test
@EnabledIfSystemProperty(named = "os.arch", matches = ".*64.*")
void onlyOn64BitArchitectures() {
    // ...
}

@Test
@DisabledIfSystemProperty(named = "ci-server", matches = "true")
void notOnCiServer() {
    // ...
}
```

#### [Environment Variable Conditions](https://junit.org/junit5/docs/current/user-guide/#writing-tests-conditional-execution-environment-variables)

Un contenedor o prueba puede habilitarse o deshabilitarse en función del valor de una **variable de entorno nombrada del sistema operativo subyacente** mediante las anotaciones `@EnabledIfEnvironmentVariable` y `@DisabledIfEnvironmentVariable`. El valor proporcionado a través del atributo _"matches"_ se interpretará como una expresión regular.

A partir de JUnit Jupiter 5.6, estas anotaciones son **anotaciones repetibles**. En consecuencia, estas anotaciones pueden declararse múltiples veces en una interfaz de prueba, clase de prueba o método de prueba.

```java
@Test
@EnabledIfEnvironmentVariable(named = "ENV", matches = "staging-server")
void onlyOnStagingServer() {
    // ...
}

@Test
@DisabledIfEnvironmentVariable(named = "ENV", matches = ".*development.*")
void notOnDeveloperWorkstation() {
    // ...
}
```

#### [Custom Conditions](https://junit.org/junit5/docs/current/user-guide/#writing-tests-conditional-execution-custom)

Como alternativa a la implementación de una _"ExecutionCondition"_, un contenedor o prueba puede habilitarse o deshabilitarse en función de un método de condición configurado mediante las anotaciones `@EnabledIf` y `@DisabledIf`.

Un método de condición debe tener un tipo de retorno booleano y puede aceptar sin argumentos o un solo argumento de tipo _"ExtensionContext"_.

```java
@Test
@EnabledIf("customCondition")
void enabled() {
    // ...
}

@Test
@DisabledIf("customCondition")
void disabled() {
    // ...
}

boolean customCondition() {
    return true;
}
```

Alternativamente, el método de condición puede ubicarse **fuera de la clase de prueba**. En este caso, se debe hacer referencia a él por su nombre completo:

```java
package example;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.condition.EnabledIf;

class ExternalCustomConditionDemo {

    @Test
    @EnabledIf("example.ExternalCondition#customCondition")
    void enabled() {
        // ...
    }

}

class ExternalCondition {

    static boolean customCondition() {
        return true;
    }

}
```

Hay varios casos en los que un método de condición debería ser estático:

- cuando `@EnabledIf` o `@DisabledIf` se usan a nivel de clase

- cuando se usa `@EnabledIf` o `@DisabledIf` en un método `@ParameterizedTest` o `@TestTemplate`

- cuando el método de condición está ubicado en una clase externa

A menudo ocurre que puede utilizar un **método estático existente** en una clase de utilidad como condición personalizada.

Por ejemplo, _"java.awt.GraphicsEnvironment"_ proporciona el método `public static boolean isHeadless()` que puede usarse para determinar si el entorno actual no admite una pantalla gráfica. Por lo tanto, si tienes una prueba que depende del soporte gráfico, puedes deshabilitarla cuando dicho soporte no esté disponible de la siguiente manera:

```java
@DisabledIf(value = "java.awt.GraphicsEnvironment#isHeadless",
    disabledReason = "headless environment")
```

### [Tagging and Filtering](https://junit.org/junit5/docs/current/user-guide/#writing-tests-tagging-and-filtering)

Las clases y métodos de prueba se pueden **etiquetar** via la anotación `@Tag`. Esto permite agrupar tests bajo ciertas etiquetas, facilitando la selección y ejecución de subconjuntos específicos de tests según sea necesario.

En JUnit 4, estas etiquetas se mapean a `@Category`.

```java
import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.Test;

@Tag("fast")
@Tag("model")
class TaggingDemo {

    @Test
    @Tag("taxes")
    void testingTaxCalculation() {
    }

}
```

La sintaxis de las etiquetas debe seguir ciertas [reglas](https://junit.org/junit5/docs/current/user-guide/#running-tests-tag-syntax-rules).

### [Test Execution Order](https://junit.org/junit5/docs/current/user-guide/#writing-tests-test-execution-order)

Por defecto, las clases y métodos de prueba se ordenarán utilizando un algoritmo que es determinista pero intencionalmente no obvio. Esto asegura que ejecuciones subsecuentes de un conjunto de pruebas ejecuten las clases y métodos de prueba en el mismo orden, permitiendo así construcciones repetibles.

Aunque las verdaderas pruebas unitarias normalmente no deberían depender del orden en el que se ejecutan, hay ocasiones en las que es necesario imponer un orden de ejecución de un método de prueba específico, como por ejemplo en pruebas de integración o pruebas funcionales.

#### [Method Order](https://junit.org/junit5/docs/current/user-guide/#writing-tests-test-execution-order-methods)

Para controlar el orden en el que se ejecutan los métodos de prueba, anota tu clase de prueba o interfaz de prueba con `@TestMethodOrder` y especifica la implementación de ["MethodOrderer"](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/MethodOrderer.html) deseada.

Se puede implementar un _"MethodOrderer"_ personalizado o usar una de las implementaciones integradas.

El siguiente ejemplo demuestra cómo garantizar que los métodos de prueba se ejecuten en el orden especificado mediante la anotación `@Order`:

```java
import org.junit.jupiter.api.MethodOrderer.OrderAnnotation;
import org.junit.jupiter.api.Order;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.TestMethodOrder;

@TestMethodOrder(OrderAnnotation.class)
class OrderedTestsDemo {

    @Test
    @Order(1)
    void nullValues() {
        // perform assertions against null values
    }

    @Test
    @Order(2)
    void emptyValues() {
        // perform assertions against empty values
    }

    @Test
    @Order(3)
    void validValues() {
        // perform assertions against valid values
    }

}
```

#### [Class Order](https://junit.org/junit5/docs/current/user-guide/#writing-tests-test-execution-order-classes)

Aunque las clases de prueba típicamente no deben depender del orden en que se ejecutan, hay ocasiones en las que es deseable aplicar un orden específico de ejecución a las clases de prueba. Puedes desear ejecutar las clases de prueba en un orden aleatorio para asegurarte de que no existan dependencias accidentales entre ellas, por ejemplo.

Se puede implementar un ["ClassOrderer"](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/ClassOrderer.html) personalizado o utilizar una de las implementaciones integradas.

El siguiente ejemplo demuestra cómo garantizar que las clases de prueba `@Nested` se ejecuten en el orden especificado mediante la anotación `@Order`.

```java
import org.junit.jupiter.api.ClassOrderer;
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Order;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.TestClassOrder;

@TestClassOrder(ClassOrderer.OrderAnnotation.class)
class OrderedNestedTestClassesDemo {

    @Nested
    @Order(1)
    class PrimaryTests {

        @Test
        void test1() {
        }
    }

    @Nested
    @Order(2)
    class SecondaryTests {

        @Test
        void test2() {
        }
    }
}
```

### [Test Instance Lifecycle](https://junit.org/junit5/docs/current/user-guide/#writing-tests-test-instance-lifecycle)

Para permitir que los métodos de prueba individuales se ejecuten de **manera aislada** y evitar **efectos secundarios inesperados** debido al estado mutable de la instancia de prueba, JUnit **crea una nueva instancia** de cada clase de prueba antes de ejecutar cada método de prueba.

Este ciclo de vida de instancia de prueba "por método" es el **comportamiento predeterminado** en JUnit Jupiter y es análogo a todas las versiones anteriores de JUnit.

Si se requiere que JUnit Jupiter ejecute todos los métodos de prueba en **la misma instancia de prueba**, se debe anotar la clase de prueba con `@TestInstance(Lifecycle.PER_CLASS)`:

```java
import org.junit.jupiter.api.*;

@TestInstance(TestInstance.Lifecycle.PER_CLASS)
public class MyClassTest {

    private int counter = 0;

    @BeforeAll
    void init() {
        // Este método se ejecuta una vez antes de todos los métodos de prueba
        System.out.println("Inicialización de la clase de prueba...");
    }

    @BeforeEach
    void setUp() {
        // Este método se ejecuta antes de cada método de prueba
        counter++;
        System.out.println("Preparando para ejecutar el método de prueba " + counter);
    }

    @Test
    void testMethod1() {
        // Método de prueba 1
        System.out.println("Ejecutando método de prueba 1...");
        Assertions.assertEquals(1, counter);
    }

    @Test
    void testMethod2() {
        // Método de prueba 2
        System.out.println("Ejecutando método de prueba 2...");
        Assertions.assertEquals(2, counter);
    }

    @AfterEach
    void tearDown() {
        // Este método se ejecuta después de cada método de prueba
        System.out.println("Finalizando la ejecución del método de prueba " + counter);
    }

    @AfterAll
    void cleanUp() {
        // Este método se ejecuta una vez después de todos los métodos de prueba
        System.out.println("Limpieza después de todos los métodos de prueba");
    }
}
```

Al usar este modo, se creará una nueva instancia de prueba una vez por clase de prueba. Por lo tanto, si los métodos de prueba dependen del estado almacenado en variables de instancia, es posible que se necesite restablecer ese estado en los métodos `@BeforeEach` o `@AfterEach`.

El modo "por clase" tiene algunos beneficios adicionales sobre el modo predeterminado "por método". Específicamente, con el modo "por clase", es posible declarar los métodos `@BeforeAll` y `@AfterAll` en métodos no estáticos, así como en métodos por defecto de interfaces. Por lo tanto, el modo "por clase" también hace posible utilizar métodos `@BeforeAll` y `@AfterAll` en clases de prueba `@Nested`.

#### [Changing the Default Test Instance Lifecycle](https://junit.org/junit5/docs/current/user-guide/#writing-tests-test-instance-lifecycle-changing-default)

Si una clase de prueba o una interfaz de prueba no está anotada con `@TestInstance`, JUnit Jupiter utilizará un modo de ciclo de vida predeterminado. El modo predeterminado estándar es **_"PER_METHOD"_**; sin embargo, es posible cambiar el valor predeterminado para la ejecución de un plan de prueba completo.

Para establecer el modo de ciclo de vida de la instancia de prueba predeterminado en **_"Lifecycle.PER_CLASS"_** a través del archivo de configuración de la plataforma JUnit, cree un archivo llamado **"junit-platform.properties"** en la raíz de la ruta de clase (por ejemplo, _"src/test/resources"_) con el siguiente contenido:

```txt
junit.jupiter.testinstance.lifecycle.default = per_class
```

Existen otras formas de para configurar el modo, pero esta es la recomendable ya que este archivo de configuración puede ser incluido en un sistema de control de versiones junto con el proyecto, lo que permite su uso dentro de los entornos de desarrollo integrado (IDE) y en el software de construcción.

### [Nested Tests](https://junit.org/junit5/docs/current/user-guide/#writing-tests-nested)

TODO

### [Dependency Injection for Constructors and Methods](https://junit.org/junit5/docs/current/user-guide/#writing-tests-dependency-injection)

TODO

### [Test Interfaces and Default Methods](https://junit.org/junit5/docs/current/user-guide/#writing-tests-test-interfaces-and-default-methods)

TODO

---

## Referencias

- <https://junit.org/junit5/>
- <https://github.com/junit-team/junit5-samples>
- <https://www.baeldung.com/junit>
- <https://www.tutorialspoint.com/junit/index.htm>
- <https://www.vogella.com/tutorials/JUnit/article.html>

## Licencia

[![Licencia de Creative Commons](https://i.creativecommons.org/l/by-sa/4.0/80x15.png)](http://creativecommons.org/licenses/by-sa/4.0/)
Esta obra está bajo una [licencia de Creative Commons Reconocimiento-Compartir Igual 4.0 Internacional](http://creativecommons.org/licenses/by-sa/4.0/).
