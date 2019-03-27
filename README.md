# Data Manipulation - Guía práctica

Este tutorial asume que el usuario maneja conocimientos básicos de programación.

```R
install.packages("tidyverse") # Instala los paquetes necesarios
library("tidyverse") # Se abren los paquetes
set.seed(100)

# Creamos el dataframe o base de datos
df <- merge(data.frame(idProject = 1:100,
                       ProjectType = sample(c("X", "Y", "Z"),100,replace=TRUE),
                 Period = sample(1:5, 100, replace=TRUE),
                 Employee = sample(1:12, 100, replace=TRUE),
                 Project_Performance = sample(-100:100,100,replace=TRUE)/100,
                 Project_Size = sample(100:1000,100, replace=TRUE)),
            data.frame(Boss = c("A", "A", "A","A", "A", "B","B", "B", "B","C", "C", "C"),
                Employee = c(1,2,3,4,5,6,7,8,9,10,11,12), stringsAsFactors = FALSE),
            by = "Employee", all.x=TRUE)

head(df,5) # head() Selecciona las primeras 5 filas
str(df) # Muestra la estructura de la base
```

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


Ejercicios. Calcular:
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

Documentación útil:
`?round`
`?n`
`?summarise`

Resultados esperados:
1)
  Boss  Promedio
  <chr>    <dbl>
1 A         0.03
2 B         0   
3 C         0.11
  
2)
  Period Promedio
   <int>    <dbl>
1      1     0.14
2      2     0.11
3      3     0.03
4      4    -0.06
5      5    -0.05

3)
  Promedio
1     0.15

4)
  Promedio
1    -0.01

5)
  Promedio
1      NaN

6)
  Boss  n_Count
  <chr>   <int>
1 A          44
2 B          33
3 C          23

7)
  Boss  n_Count p_Count
  <chr>   <int>   <dbl>
1 A          44    0.44
2 B          33    0.33
3 C          23    0.23

8)
   Employee Employee_Performance
      <int>                <dbl>
 1        1                 0.17
 2        2                -0.07
 3        3                 0   
 4        4                 0.17
 5        5                -0.17
 6        6                -0.04
 7        7                -0.07
 8        8                 0.19
 9        9                -0.25
10       10                -0.09
11       11                 0.1 
12       12                 0.4 

9)
  Boss  Boss_Performance
  <chr>            <dbl>
1 A                 0.05
2 B                -0.05
3 C                 0.12

10)
  Boss  Boss_Leadership
  <chr>           <dbl>
1 A                0.02
2 B               -0.04
3 C                0.14
