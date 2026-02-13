2026-02-12 19:18

Status: #child 

Tags: [[Java]] [[JavaBasics]]


# Ciclo de ejecución Java

La ejecución de un código en java está dividida en tres partes:

1. ***compilación***: El código fuente de la aplicación es convertido a bytecode usando el "javac" compiler.
2. ***class loading***: El bytecode es cargado en memoria
3. ***bytecode execution***: El JVM ejecuta el bytecode y el programa corre.

El JVM es el responsable de TODO el ultimo proceso. Se encarga de traducir el bytecode en lenguaje máquina. Es un proceso complejo, a bajo nivel.

![[Pasted image 20260212201010.png]]


La JVM ejecuta bytecode Java en 6 fases principales: **Loading**, **Linking**, **Initializing**, **Instantiating**, **Finalizing** y **Unloading**. Es el ciclo de vida completo de una clase desde que se carga hasta que se elimina.



# References

