2026-02-12 18:35

Status: #child 

Tags: [[Java]] [[JavaBasics]]


# Sentencias de control

#### sentencia IF and else 

Nos ayuda a ejecutar código si se cumple cierta evaluación y otro código si no se cumple.

```java
void applyBrakes() {
    // the "if" clause: bicycle must be moving
    if (isMoving){
        // the "then" clause: decrease current speed
        currentSpeed--;
    } else{
	    currentSpeed++;
    }
}
```

#### sentencias while and do-while 

	La sentencia while ejecuta un código mientras la sentencia dentro de los parentesis se cumpla. La diferencia que hay entre do-while y while, es que do-while ejecuta todo el código al menos una vez, y el while solo si la sentencia se cumple en el momento. 
```java
while (expression) {
     statement(s)
}
```

```java
do {
     statement(s)
} while (expression);
```

#### Sentencia for

Proporciona iteraciones controladas mediante un rango especifico, es la mas utilizada ya que siempre tendremos un control de rango al que se quiere ejecutar una sentencia.

```java
for (initialization; termination; increment) {
    statement(s)
}
```


#### Switch
El `switch` en Java selecciona un bloque de código según el valor exacto de una variable. Es ideal para múltiples opciones fijas, más limpio que cadenas de `if-else`.

```java
int quarter = ...; // any value

String quarterLabel = null;
switch (quarter) {
    case 0: quarterLabel = "Q1 - Winter"; 
            break;
    case 1: quarterLabel = "Q2 - Spring"; 
            break;
    case 2: quarterLabel = "Q3 - Summer"; 
            break;
    case 3: quarterLabel = "Q3 - Summer"; 
            break;
    default: quarterLabel = "Unknown quarter";
};
```



# References

[[Variables]]