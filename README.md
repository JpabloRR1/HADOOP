[![Gitter chat](https://badges.gitter.im/gitterHQ/gitter.png)](https://gitter.im/big-data-europe/Lobby)

# Proyecto Hadoop con Docker: Contador de Palabras y Resolución de Sudokus

Este proyecto te guía paso a paso para levantar un clúster Hadoop usando Docker, y correr dos ejemplos usando MapReduce:

1. 📖 Contar palabras en un archivo de texto (usamos El Quijote).
2. 🧩 Resolver sudokus desde un archivo de texto.

Todo está pensado para que lo entiendas fácil.

---

## 🚀 Paso 1: Levantar el clúster Hadoop

Abre una terminal y métete en la carpeta donde tienes el repositorio `docker-hadoop`. Ahí corre el siguiente comando:

```
docker-compose up -d
```

## 🚀  ¿Ya se levantaron los contenedores?
Puedes checar con este comando:
```
docker ps
```

## 🧠 Paso 2: Entrar al nodo maestro (namenode)
Este nodo es el que administra el sistema Hadoop, o sea, donde vamos a trabajar.
Corre esto:
```
docker exec -it namenode bash
```

## 📁 Paso 3: Preparar el sistema de archivos HDFS
Hadoop usa un sistema propio de archivos, así que hay que crear una estructura básica:
```
hdfs dfs -mkdir -p /user/root/
```
```
hdfs dfs -ls /user/
```
# Ejemplo 1
## 📖 Contar Palabras con MapReduce (El Quijote y Llaneros)
Paso 4.1: Descargar los archivos necesarios
Descarga el archivo .jar de ejemplo:
👉 https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-mapreduce-examples/2.7.1/
Archivo: hadoop-mapreduce-examples-2.7.1-sources.jar

Descarga el texto plano de El Quijote (archivo .txt):
👉 https://gist.github.com/jsdario/6d6c69398cb0c73111e49f1218960f79
Archivo: el_quijote.txt

Guarda los dos archivos en la carpeta del proyecto docker-hadoop.

## Paso 4.2: Copiar los archivos al contenedor
Usa estos comandos:
```
docker cp hadoop-mapreduce-examples-2.7.1-sources.jar namenode:/tmp
```
```
docker cp el_quijote.txt namenode:/tmp
```

## Paso 4.3: Crear carpeta en HDFS y subir el archivo
Métete de nuevo al contenedor namenode:
```
docker exec -it namenode bash
```
Luego:
```
hdfs dfs -mkdir /user/root/input_contador
```
```
cd /tmp
```
```
hdfs dfs -put el_quijote.txt /user/root/input_contador
```
Con eso ya tenemos el archivo listo en Hadoop para ser procesado.

## Paso 4.4: Ejecutar el MapReduce
Ahora sí, lanza el contador de palabras con:
```
hadoop jar hadoop-mapreduce-examples-2.7.1-sources.jar org.apache.hadoop.examples.WordCount input_contador output_contador
```
Es una sola línea, y si todo salió bien, va a procesar el archivo y guardar los resultados en una carpeta de salida.

## Paso 4.5: Ver los resultados
Para verlos directamente en consola:
```
hdfs dfs -cat /user/root/output_contador/*
```

Si quieres guardar el resultado en un archivo .txt dentro del contenedor:
```
hdfs dfs -cat /user/root/output_contador/part-r-00000 > /tmp/quijote_wc.txt
```
```
exit
```
```
docker cp namenode:/tmp/quijote_wc.txt .
```

¡Listo! Ahora tienes el archivo quijote_wc.txt en tu compu, dentro de la carpeta docker-hadoop.

#Ejemplo 2
## 🧩 Resolver Sudokus con MapReduce
Paso 5.1: Descargar los archivos
Archivo .jar:
👉 hadoop-examples-0.20.205.0.jar 

Archivo de sudokus .dta:
👉 puzzle1.dta

Guárdalos también en la carpeta del proyecto docker-hadoop.

Mismos procedimientos que los anteriores por lo que no abra tanta explicacion. 

## Paso 5.2: Copiar los archivos al contenedor

```
docker cp hadoop-examples-0.20.205.0.jar namenode:/tmp
```
```
docker cp puzzle1.dta namenode:/tmp
```
## Paso 5.3: Ejecutar el resolver de Sudoku
Métete al contenedor:
```
docker exec -it namenode bash
```
```
cd /tmp
```
Ejecuta este comando:
```
hadoop jar hadoop-examples-0.20.205.0.jar sudoku puzzle1.dta > solucion_puzzle1.txt
```
Después salte:
```
exit
```
Y copia el archivo a tu compu:
```
docker cp namenode:/tmp/solucion_puzzle1.txt .
```
¡Listo! Ahora tienes el archivo con la solución en tu carpeta.





