# R - Data Manipulation - Guía práctica

Este tutorial asume que el usuario maneja conocimientos básicos de programación.

```R
df <- read.csv("df.csv", sep=";", dec=",")

head(df,5) # Selecciona las primeras 5 filas
str(df) # Muestra la estructura de la base
```
**Estructura general de la base**
* La base tiene 100 proyectos con tamaños diferentes, cada proyecto está asociado a un empleado, y cada empleado está asociado a un único jefe.
* Los 100 proyectos se realizaron dentro de un total de 10 períodos.
* Cada proyecto puede ser de tres tipos: *X*, *Y*, o *Z*.
* Existen 12 empleados y 3 jefes (*A*, *B*, *C*)


**Funciones básicas para la manipulación de datos**

|Función | ¿Qué hace?|
| :---:|:---:|
|`select()` | Selecciona columnas|
|`filter()` | Filtra filas|
|`arrange()` | Reordena|
|`mutate()` | Crea nuevas columnas|
|`summarize()`| Sintetiza valores|
|`group_by()`| Permite agrupar operaciones|


Con el fin de entender cómo aprender y entender cómo funciona cada función del R, es necesario acostumbrarse a ir a la documentación:

|Ejemplo| ¿Qué hace?|
|:---:| :---|
|`?select()`| Va directamente a la documentación especificada|
|`??select()`| Realiza un buscar con la palabra o función a seguir de los signos de interrogación|


# Slicing, filtrar, y seleccionar

Slicing es una forma de seleccionar/filtrar un dataframe según los valores/condiciones que uno requiera, determinado por el/los índice/s de filas y columnas separados por una coma. Por ejemplo.
```r
df[3,] # Selecciona la fila 3 y todas la columnas de df
df[, 4] # Selecciona todas las filas de df y la columna 4
df[2,4] # Selecciona la fila número 2, y la columna número 4
df[3,1] # Selecciona la fila número 3, y la columna número 1
df[1:3,4] # Selecciona el rango de filas 1 a 3, y la columna 4
df[4,2:4] # Selecciona la fila 4, y el rango de columnas 2 a 4
df[1:4,3:5] # Selecciona el rango de filas 1 a 4, y el rango de columnas 3 a 5.
df[1:3, c(1,5,2:4)] # Selecciona el rango de filas 1 a 3, las columnas 1,5, y el rango de rolumnas 2 a 4
df[c(4,10,50,60,1:3), c(1,3,2,4:5)] # Selecciona las filas 4,10,50,60, el rango de filas 1 a 3, las columnas 1,3,2 y el rango de columnas 4 a 5
df[c(1,4,3), c("Period", "Boss", "Employee")] # Selecciona la fila 1,4,3 con las columnas Period, Boss y Employee
```
`c()` es la definición de un vector en R y se denota con una `c` de *concatenate*. Se pueden realizar combinaciones de selecciones de números de columnas como uno prefiera. En tanto, siendo `a` y `b` vectores con valores enteros de una misma cantidad de elementos, `df[a,b]`  extraerá las filas que indiquen el vector `a`, y las columnas que indiquen el vector `b`. 

De forma adicional, se pueden seleccionar las columnas por su nombre, así como también filtrar según determinados valores con vectores `BOOLEAN`:
```r
df$Period # Seleccionamos el vector Period del dataframe df
df$Period == 3 # Si Period es igual a 3 retornará TRUE, y FALSE en caso contrario.
x <- df$Period # Asignamos dicho vector x 
df[x,] # Selecciona todas las filas donde el vector x sea TRUE
df[df$Period == 3,] # Equivalente a df[x,]

df$Project_Performance # Seleccionamos el vector Project_Performance del dataframe df
df$Project_Performance > 0.5 # Si el Performance del proyecto es mayor a 0.5 retornará TRUE, y FALSE en caso contrario
df[df$Project_Performance > 0.5,] # Selecciona todas las filas donde se cumpla la condición correspondiente

df[df$Period == 3 & df$Project_Performance > 0.5,] # Selecciona todas las filas donde el Period == 3 Y que el Performance del proyecto sea mayor a 0.5
df[df$Period == 3 & df$Project_Performance > 0.5,c("Employee")] # Selecciona todos los ids de los empleados que durante el período 3 hayan tenido un performance mayor a 0.5 en su proyecto

df[df$Period == 3 | df$Project_Performance > 0.5,] # Selecciona todas las filas donde el Period == 3 Ó que el Performance del proyecto sea mayor a 0.5
df[df$Period == 3 | df$Project_Performance > 0.5,c("Employee", "Boss")] # Selecciona todos los ids de los empleados, y su jefe respectivo, que hayan tenido algún proyecto durante el período 3 ó que hayan tenido un performance mayor a 0.5 en cualquier período

# NOTA: En caso de utilizar & y | en una misma condición, se evalúa primero &. En consecuencia, se hace necesario evaluar el uso de paréntesis en caso de utilizar ambas operaciones en una misma condición.
```


# SELECT, FILTER, ARRANGE
```r
select(df, Period, Boss, Employee) # Selecciona las columnas Period, Boss, y Employee del dataframe df
select(df, idProject:Employee) # Selecciona las columnas desde idProject hasta Employee
select(df, -Period) # Selecciona todas las columnas menos la columna Period
select(df, ends_with("d")) # Selecciona todas las columnas cuyo nombre termine con "d"

# Variaciones útiles
?starts_with() # Selecciona columnas que comienzan con un character string
?ends_with() # Selecciona columnas que terminan con un character string
?contains() # Selecciona columnas que contienen un character string
?matches() # Selecciona columnas que coinciden con una regular expression

#Variaciones útiles
?select_if
?select_all
?select_at

filter(df, Period==3) # Selecciona todas las filas cuyo período sea igual a 3
filter(df, Period==3 & Boss == "A") # Selecciona todas las filas cuyo período sea igual a 3, y su jefe sea A
filter(df, Period==3 & (Boss == "A" | Boss == "C")) # Selecciona todas las filas cuyo período sea igual a 3, y su jefe sea A o C

#Variaciones útiles
?filter_if
?filter_all
?filter_at

arrange(df, idProject) # Ordena df de forma ascendente según la columna idProject
arrange(df, desc(idProject)) # Ordena df de forma descendente según la columna idProject

?subset # Otra metodología para filtrar y extraer columnas
subset(df, # dataframe
       subset = Period==3 & Employee == 10, # Condiciones para filtrar filas
       select = c(idProject, Project_Performance)) # Columnas a seleccionar

```


# MUTATE y SUMMARISE
```r
mutate(df, Nombre = Project_Performance*100) # Crea nuevas columnas
summarise(df, Promedio = mean(Project_Performance)) # Para cálculos de tendencia central
#NOTA: summarise() <> summarize()


summarise(group_by(df, Employee),Promedio = mean(Project_Performance)) # Promedio por empleado
summarise(group_by(df, Boss),Promedio = mean(Project_Performance)) # Promedio por Jefe
summarise(group_by(df, Employee, Boss),Promedio = mean(Project_Performance)) # Promedio por empleado y por jefe
```

Pipe Operator (`%>%`) inserta en el primer argumento de la función a seguir, el resultado anterior. Matemáticamente f(x) es lo mismo que `x %>% f`, o g(f(x)) es lo mismo que `x %>% f() %>% g()`
```r
df %>%
  group_by(Employee) %>%
  summarise(Promedio = mean(Project_Performance)) # Promedio por empleado
df %>%
  group_by(Boss, Employee) %>%
  summarise(Promedio = mean(Project_Performance)) # Promedio por empleado y por jefe

#Adicionalmente, se puede modificar la inserción en el primer argumento de la función a seguir, especificando la posición con un '.':
# f(y, z = x) es lo mismo que x %>% f(y, z = .)
# f(x, y = nrow(x), z = ncol(x)) es lo mismo que x %>% f(y = nrow(.), z = ncol(.))
```


# Ejercicios

1) La media simple de cada proyecto, por jefe
2) La media simple de cada proyecto, por período
3) La media simple de los proyectos tipo X
4) La media simple de los proyectos tipo Y ó Z
5) La media ponderada de los proyectos X y Z
6) La cantidad de proyectos asociado a cada jefe
7) El porcentaje de proyectos asociado a cada jefe, según el total de proyectos realizados
8) El desempeño de cada empleado, el cual está definido como el promedio ponderado del tamaño de los proyectos realizados por su nota respectiva
9) El desempeño de cada jefe, el cual está definido como el promedio ponderado del tamaño de los proyectos realizados asociados a sus empleados, por su nota respectiva
10) El nivel de liderazgo de cada jefe, el cual está definido como el promedio del desempeño de sus empleados asociados 

Documentación útil adicional: `?round` `?n`

### Resultados esperados

#### Ejercicio 1:

| Boss  |Promedio|
| :---: |   :---:|
| A     |    0.03|
| B     |    0   |
| C     |    0.11|
  
#### Ejercicio 2:
|  Period| Promedio|
| :---: |   :---:|
|      1|     0.14|
|      2|     0.11|
|      3|     0.03|
|      4|    -0.06|
|      5|    -0.05|

#### Ejercicio 3:

 | Promedio|
 |:---:|
|0.15|

#### Ejercicio 4:
  |Promedio|
  |:---:|
| -0.01|

#### Ejercicio 5:
  |Promedio|
|:---:|
|NaN|

#### Ejercicio 6:
|  Boss | n_Count|
| :---: |   :---:|
| A     |     44 |
| B     |     33 |
| C     |     23 |

#### Ejercicio 7:
|  Boss | p_Count|
| :---: |:---:|
| A     |   0.44|
| B     |   0.33|
| C     |   0.23|

#### Ejercicio 8:
|Employee |Employee_Performance|
|:---:|:---:|
|        1|                 0.17|
|        2|                -0.07|
|        3|                 0   |
|        4|                 0.17|
|        5|                -0.17|
|        6|                -0.04|
|        7|                -0.07|
|        8|                 0.19|
|        9|                -0.25|
|       10|                -0.09|
|       11|                 0.1 |
|       12|                 0.4 |

#### Ejercicio 9:
  |Boss|  Boss_Performance|
|:---:|:---:|
|A               |  0.05|
|B               | -0.05|
|C               |  0.12|

#### Ejercicio 10:
|  Boss | Boss_Leadership|
|:---:|:---:|
| A        |        0.02|
| B        |       -0.04|
| C        |        0.14|
