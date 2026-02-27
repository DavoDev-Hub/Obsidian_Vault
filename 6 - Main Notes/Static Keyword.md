2026-02-26 17:33

Status:

Tags: [[Java]] [[POO]]


# Static Keyword

En Java, el `static` keyword es un non-access modificador que puede ser aplicado a miembros de una clase: variables, métodos, clases anidadas, y bloques inicializadores. Cuando un miembro es declarado `static`, este pertenece a la clase por si misma, mas que a cualquier instancia especifica(objeto) de esa clase.

Piénsalo de esta manera:
* *Miembros de instancia:* son como las únicas características y comportamientos de un empleado individual (ej. cada empleado tiene su propio nombre, salario, y puede llevar a cabo un trabajo especifico).
* *Miembros estáticos:* Son como las características y comportamientos de la compañía entera (ej. la compañía tiene un nombre, un CEO, y una política común de RH )

##### 1. Variables `Static`  
***Un campo `static` es también conocido como class variable, es compartido por todas las instancias de una clase.*** Existe solo una copia de una variable estática en memoria, independientemente de cuantos objectos de la clase se creen.
*Características principales:*
* *Pertenece a la clase:* No a un objeto
* *Compartido:* Todos los objetos de la clase comparten la misma variable estática.
* *Eficiencia de memoria:* Solo existe una copia, guardando memoria para datos comunes.
* *Accesible via nombre de la clase:* La mejor practica es acceder a esta usando `className.staticVariable`. Tu puedes acceder a ella a traves de un objecto de referencia (`object.staticVariable`), pero esto no se recomienda porque puede ser engañoso.

```Java
class Company {
    String employeeName; // Instance variable
    static String companyName = "Tech Solutions Inc."; // Static variable
    static int employeeCount = 0; // Static variable to track total employees

    public Company(String name) {
        this.employeeName = name;
        employeeCount++; // Increment static counter every time a new employee (object) is created
    }

    public void displayEmployeeInfo() {
        System.out.println("Employee: " + employeeName + ", Company: " + companyName);
    }

    public static void displayCompanyInfo() {
        System.out.println("Current Company Name: " + companyName);
        System.out.println("Total Employees in Company: " + employeeCount);
    }
}

public class StaticVariableDemo {
    public static void main(String[] args) {
        Company emp1 = new Company("Alice");
        Company emp2 = new Company("Bob");
        Company emp3 = new Company("Charlie");

        emp1.displayEmployeeInfo(); // Employee: Alice, Company: Tech Solutions Inc.
        emp2.displayEmployeeInfo(); // Employee: Bob, Company: Tech Solutions Inc.

        System.out.println("\n--- Accessing Static Variables ---");
        // Accessing static variables directly via class name (RECOMMENDED)
        System.out.println("Company Name (via Class): " + Company.companyName);
        System.out.println("Employee Count (via Class): " + Company.employeeCount);

        // Modifying a static variable affects all instances
        Company.companyName = "Global Innovations Ltd."; // Changes for everyone!

        System.out.println("\n--- After Company Name Change ---");
        emp3.displayEmployeeInfo(); // Employee: Charlie, Company: Global Innovations Ltd.

        // Calling a static method
        Company.displayCompanyInfo();
    }
}
```

*¿Cuándo usar campos `static`?*
* Constantes: Valores que nunca cambian y son comunes para todas las *instancias* (ej.  `Math.PI`, `Integer.MAX_VALUE`). Úselo con final para constantes verdaderas: `public static final double PI = 3.14159;`

##### 1. Métodos `Static`  (variables)

_**Un método `static` pertenece a la clase y puede invocarse sin crear una instancia de la clase.**_ Solo puede acceder a otros miembros estáticos.

*Características principales*
- _Pertenece a la clase:_ No requiere objeto para ejecutarse
- _Acceso directo:_ `ClassName.staticMethod()`
- _Limitado:_ No accede a `this`, `super` ni miembros de instancia
- _Utilidad común:_ Perfecto para funciones helper o factories

```Java
class MathUtils {
    static double pi = 3.14159; // Variable estática auxiliar
    
    // Método estático para área de círculo
    static double circleArea(double radius) {
        return pi * radius * radius;
    }
    
    // Método estático para perímetro
    static double circlePerimeter(double radius) {
        return 2 * pi * radius;
    }
    
    // Método estático que usa otros métodos estáticos
    static void printCircleInfo(double radius) {
        System.out.println("Radio: " + radius);
        System.out.println("Área: " + circleArea(radius));
        System.out.println("Perímetro: " + circlePerimeter(radius));
    }
}

public class StaticMethodDemo {
    public static void main(String[] args) {
        System.out.println("--- Usando métodos estáticos ---");
        
        // Acceso directo por nombre de clase (RECOMENDADO)
        double area = MathUtils.circleArea(5.0);
        System.out.println("Área directa: " + area); // 78.53975
        
        // Método que usa otros métodos estáticos
        MathUtils.printCircleInfo(5.0);
        // Radio: 5.0
        // Área: 78.53975
        // Perímetro: 31.4159
        
        System.out.println("\n--- Comparación con instancia ---");
        // También funciona vía objeto (NO RECOMENDADO, confuso)
        MathUtils utils = new MathUtils();
        System.out.println("Área vía objeto: " + utils.circleArea(3.0)); // 28.27431
    }
}

```

**¿Cuándo usar métodos `static`?**

- Funciones utilitarias: `Math.sqrt()`, `Arrays.sort()`, `String.valueOf()`
- Métodos factory: `Integer.valueOf()`, `Collections.emptyList()`
- Validaciones generales: `Objects.requireNonNull()`
- Helpers sin estado: cálculos puros, conversiones de formato

##### 3. Bloques Estáticos

_**Un bloque `static` se ejecuta una sola vez cuando la clase se carga en memoria, antes del main().**_ Perfecto para inicializaciones complejas de variables estáticas.

**Características principales:**

- _Ejecución única:_ Solo una vez por clase, independientemente de instancias
- _Automático:_ Se ejecuta al primer acceso a la clase
- _Múltiples bloques:_ Se ejecutan en orden de aparición
- _Excepciones:_ Si falla, la clase no se puede usar

```Java
class DatabaseManager {
    static String dbUrl;
    static int maxConnections;
    static String[] supportedDrivers;
    static boolean isInitialized = false;
    
    // Primer bloque estático: configuración básica
    static {
        System.out.println("=== BLOQUE 1: Configuración básica ===");
        dbUrl = "jdbc:mysql://localhost:3306/empresa_db";
        maxConnections = 20;
        System.out.println("DB URL configurada: " + dbUrl);
    }
    
    // Segundo bloque estático: drivers y validación
    static {
        System.out.println("=== BLOQUE 2: Drivers y validación ===");
        supportedDrivers = new String[] {
            "com.mysql.cj.jdbc.Driver",
            "org.postgresql.Driver"
        };
        
        // Validación de entorno
        String javaVersion = System.getProperty("java.version");
        if (javaVersion.startsWith("1.8") || javaVersion.startsWith("11")) {
            isInitialized = true;
            System.out.println("Java compatible: " + javaVersion);
        } else {
            throw new RuntimeException("Java version no soportada: " + javaVersion);
        }
    }
    
    static void showStatus() {
        System.out.println("\n=== ESTADO FINAL ===");
        System.out.println("DB URL: " + dbUrl);
        System.out.println("Max conexiones: " + maxConnections);
        System.out.println("Drivers: " + String.join(", ", supportedDrivers));
        System.out.println("Inicializado: " + isInitialized);
    }
}

public class StaticBlockDemo {
    public static void main(String[] args) {
        // Al llamar cualquier método estático, se ejecutan TODOS los bloques
        DatabaseManager.showStatus();
        /*
        Salida:
        === BLOQUE 1: Configuración básica ===
        DB URL configurada: jdbc:mysql://localhost:3306/empresa_db
        === BLOQUE 2: Drivers y validación ===
        Java compatible: 17.0.2
        === ESTADO FINAL ===
        DB URL: jdbc:mysql://localhost:3306/empresa_db
        Max conexiones: 20
        Drivers: com.mysql.cj.jdbc.Driver, org.postgresql.Driver
        Inicializado: true
        */
    }
}

```
**Cuándo usar bloques `static`?**

- Inicializar colecciones grandes o complejas
- Cargar configuraciones desde properties/JSON/YAML
- Validar requisitos del sistema al inicio
- Crear singletons o caches globales
- Registrar drivers JDBC automáticamente

##### 4. Clases Anidadas Estáticas

_**Una clase `static` anidada no necesita una instancia de la clase externa para existir ni acceder a sus miembros.**_ Organiza clases auxiliares de forma encapsulada.

**Características principales:**

- _Independiente:_ `Outer.Nested obj = new Outer.Nested()`
- _Sin `this` externo:_ No accede a campos de instancia de OuterClass
- _Top-level like:_ Se comporta como clase independiente
- _Mejor organización:_ Lógica relacionada en un solo archivo
```Java
class LinkedList {
    private Node head;
    private int size;
    
    // Clase anidada ESTÁTICA para nodos
    static class Node {
        String data;
        Node next;
        int index;
        
        Node(String data) {
            this.data = data;
            this.next = null;
            this.index = -1;
        }
        
        // Método estático auxiliar para crear nodos
        static Node createNode(String data, int index) {
            Node node = new Node(data);
            node.index = index;
            return node;
        }
        
        @Override
        public String toString() {
            return String.format("Node[index=%d, data='%s']", index, data);
        }
    }
    
    public void add(String data) {
        Node newNode = Node.createNode(data, size); // Usa método estático de nested class
        // lógica de LinkedList...
        size++;
    }
    
    public void printList() {
        Node current = head;
        while (current != null) {
            System.out.println(current);
            current = current.next;
        }
    }
}

public class StaticNestedDemo {
    public static void main(String[] args) {
        System.out.println("--- Creando nodo SIN instancia de LinkedList ---");
        // ¡Funciona sin crear LinkedList!
        LinkedList.Node node1 = new LinkedList.Node("Alice");
        LinkedList.Node node2 = LinkedList.Node.createNode("Bob", 1);
        
        System.out.println("Nodo 1: " + node1); // Node[index=-1, data='Alice']
        System.out.println("Nodo 2: " + node2); // Node[index=1, data='Bob']
        
        System.out.println("\n--- Usando en LinkedList ---");
        LinkedList list = new LinkedList();
        list.add("Charlie");
        // list.printList();
    }
}

```
**¿Cuándo usar clases anidadas `static`?**
- Estructuras de datos auxiliares: `Map.Entry`, `List.Node`
- Clases builder/helper específicas: `Optional.empty()`
- Enumeraciones relacionadas: `Thread.State`, `ProcessBuilder.Redirect`
- DTOs puros estrechamente acoplados a la clase padre



# References
[[Clases y Objetos]]
[[Métodos y Atributos en Java]]

