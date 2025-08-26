### ¿Qué es el polimorfismo?
**Explicación:** Permite que diferentes clases usen el mismo método con comportamientos distintos.

**Importancia:** Hace el código más extensible y permite procesar diferentes fuentes de datos con una interfaz común.

**Ejemplo aplicativo (Extractor de datos):**
```python
class DataExtractor:
    def extract(self):
        raise NotImplementedError

class S3Extractor(DataExtractor):
    def extract(self):
        print("Extrayendo datos desde S3...")

class FTPExtractor(DataExtractor):
    def extract(self):
        print("Extrayendo datos desde FTP...")

def run_pipeline(extractor: DataExtractor):
    extractor.extract()

run_pipeline(S3Extractor())
run_pipeline(FTPExtractor())
# Salida esperada:
# Extrayendo datos desde S3...
# Extrayendo datos desde FTP...
```

---

### ¿Qué es una tupla y cuál es la diferencia con una lista?
**Explicación:** Una tupla es inmutable; una lista es mutable.

**Importancia:** Las tuplas se usan para representar configuraciones que no deben cambiar, listas para flujos dinámicos.

**Ejemplo aplicativo (conexión y pasos de ETL):**
```python
conexion = ("db_host", 5432, "data_warehouse")  # tupla
etapas = ["extract", "transform", "load"]       # lista
etapas.append("report")
print(conexion)
print(etapas)
# Salida esperada:
# ('db_host', 5432, 'data_warehouse')
# ['extract', 'transform', 'load', 'report']
```

---


### ¿Qué es un set ?
**Explicación:** Un set es una colección no ordenada, no indexada y que no permite elementos duplicados

**Importancia:** Sirve para detectar duplicados rápidamente.

**Ejemplo aplicativo (comparar columnas esperadas):**
```python
columnas = ["id", "nombre", "email", "id", "email"]
columnas_unicas = list(set(columnas))
print(columnas_unicas)
# Salida esperada:
# ['id', 'nombre', 'email']
```

---

### ¿Qué es list comprehension?
**Explicación:** Es una forma corta de crear listas a partir de otras estructuras.

**Importancia:** Mejora legibilidad y rendimiento en transformaciones simples.

**Ejemplo aplicativo (limpiar nombres de columnas):**
```python
columnas = [" Monto ", "Fecha_Venta", "Cliente "]
columnas_limpias = [columna.strip().lower() for columna in columnas]
print(columnas_limpias)
# Salida esperada:
# ['monto', 'fecha_venta', 'cliente']
```

---

### ¿Para qué se usa `map`?
**Explicación:** Aplica una función a cada elemento de una unica columna.

**Importancia:** Útil para normalizar o transformar grandes volúmenes de datos.

**Ejemplo aplicativo (crear un flag de acuerdo a un campo):**
```python
import pandas as pd

# Función definida
def es_corporativo(email):
    return email.endswith("@empresa.com")

# DataFrame de ejemplo
df = pd.DataFrame({
    "nombre": ["Ana", "Luis", "Carlos"],
    "email": ["ana@empresa.com", "luis@gmail.com", "carlos@empresa.com"]
})

# Aplicación de map() sobre una columna
df["es_corporativo"] = df["email"].map(es_corporativo)
print(df)

# Salida esperada:
#   nombre              email  es_corporativo
# 0     Ana   ana@empresa.com            True
# 1    Luis    luis@gmail.com           False
# 2  Carlos  carlos@empresa.com          True
```

---

### ¿Qué es una función lambda?
**Explicación:** Aplica una función por fila usando una o varias columnas.
 
**Importancia:** Útil para normalizar o transformar grandes volúmenes de datos.

**Ejemplo aplicativo (calcular el igv usando varias columnas):**
```python
# Función definida
def calcular_precio_final(row):
    if row["graba_igv"]:
        return round(row["precio"] * 1.18, 2)
    return row["precio"]

df = pd.DataFrame({
    "producto": ["A", "B", "C"],
    "precio": [100, 200, 300],
    "graba_igv": [True, False, True]
})

# apply por fila con axis=1
df["precio_final"] = df.apply(calcular_precio_final, axis=1)
print(df)
# Salida esperada:
#  producto  precio  graba_igv  precio_final
# 0        A     100       True         118.0
# 1        B     200      False         200.0
# 2        C     300       True         354.0

```

---

### ¿Qué es un generator?
**Explicación:** Un generator es una función que permite iterar valores uno a uno de forma perezosa (lazy), sin cargar todo el conjunto en memoria.
Se define como una función normal pero usa la palabra clave yield en lugar de return.

**Importancia:** Es eficiente en memoria ya que no carga todos los datos a la vez.

**Ejemplo aplicativo:**
```python
import pandas as pd

def square_numbers(nums):
    for i in nums:
        yield(i*i)


my_nums = square_numbers([1,2,3,4,5])


# Salida esperada:
# <generator object square_numbers at 0x1004dc500>

print next(my_nums)
print next(my_nums)
print next(my_nums)
print next(my_nums)
print next(my_nums)
print next(my_nums)
print my_nums


# Salida esperada
# 1
# 4
# 9   
# 16
# 25
#File "/Users/coreyschafer/Projects/Demos/Python/Generators/square_nums.py", line 14, in <module>
#    print next(my_nums)
#StopIteration
#[Finished in 0.1s with exit code 1]
#[cmd: ['python', '-u', '/Users/coreyschafer/Projects/Demos/Python/Generators/square_nums.py']]
#[dir: /Users/coreyschafer/Projects/Demos/Python/Generators]

for num in my_nums:
    print num

# 1
# 4
# 9
# 16
# 25

df = pd.DataFrame({"monto": [200, 50], "cantidad": [2, 5]})
df["total"] = df["monto"] * df["cantidad"]
df = df[df["total"] > 100]
print(df)
# Salida esperada:
#    monto  cantidad  total
# 0    200         2    400
```

---

### ¿Esos índices vienen solos o uno lo puede definir o cambiar?
**Explicación:** Se genera por defecto, pero puede personalizarse.

**Importancia:** Usar una clave como índice mejora consultas y mergeo.

**Ejemplo aplicativo:**
```python
import pandas as pd

df = pd.DataFrame({"id_transaccion": [1], "valor": [100]})
df = df.set_index("id_transaccion")
print(df.index)
df.reset_index(inplace=True)
print(df)
# Salida esperada:
# Int64Index([1], dtype='int64', name='id_transaccion')
#    id_transaccion  valor
# 0               1    100
```

---

### ¿Cómo buscar un dato de un DataFrame de pandas por su índice?
**Explicación:** Se usa `.loc[indice]` para acceder directamente.

**Importancia:** Permite obtener una fila por su clave única.

**Ejemplo aplicativo:**
```python
import pandas as pd

df = pd.DataFrame({"id": [20230901], "valor": [100]})
df.set_index("id", inplace=True)
registro = df.loc[20230901]
print(registro)
# Salida esperada:
# valor    100
# Name: 20230901, dtype: int64
```

---

### ¿Para qué sirve la sentencia `loc` ?
**Explicación:** `loc` accede por etiquetas.

**Importancia:** Filtrar por columna/columnas con condiciones y actualiza valores condicionalmente.

**Ejemplo aplicativo:**
```python
import pandas as pd

df = pd.DataFrame({
    "cliente": ["Ana", "Luis", "Sofía", "Mateo", "Claudia"],
    "monto": [120, 300, 150, 80, 210],
    "ciudad": ["Lima", "Lima", "Cusco", "Piura", "Lima"],
    "estado": ["activo", "inactivo", "activo", "activo", "inactivo"]
})

print(df)

# Salida esperada:
#  cliente  monto ciudad    estado
# 0     Ana    120   Lima     activo
# 1    Luis    300   Lima   inactivo
# 2   Sofía    150  Cusco     activo
# 3   Mateo     80  Piura     activo
# 4 Claudia    210   Lima   inactivo

df.loc[(df["estado"] == "activo") & (df["ciudad"] == "Lima")] #acceso por etiqueta + filtros

# Salida esperada:
#  cliente  monto ciudad  estado
# 0     Ana    120   Lima  activo

df.loc[df["ciudad"] == "Cusco", "estado"] = "inactivo" #Cambiar el estado de todos los clientes de Cusco a inactivo

# Salida esperada
#  cliente  monto ciudad    estado
#...
#2   Sofía    150  Cusco   inactivo
#...

```

---

### ¿Para qué sirve la sentencia `iloc`?
**Explicación:** `iloc` accede por posición.

**Importancia:** Accede a una celda por posición (Excel-like) y recorre en ordiles filas y columnas especificas.

**Ejemplo aplicativo:**
```python
import pandas as pd

df = pd.DataFrame({
    "cliente": ["Ana", "Luis", "Sofía", "Mateo", "Claudia"],
    "monto": [120, 300, 150, 80, 210],
    "ciudad": ["Lima", "Lima", "Cusco", "Piura", "Lima"],
    "estado": ["activo", "inactivo", "activo", "activo", "inactivo"]
})

print(df)

# Salida esperada:
#  cliente  monto ciudad    estado
# 0     Ana    120   Lima     activo
# 1    Luis    300   Lima   inactivo
# 2   Sofía    150  Cusco     activo
# 3   Mateo     80  Piura     activo
# 4 Claudia    210   Lima   inactivo

df.iloc[2] #Obtener la 3ra fila (posición 2)

# Salida esperada:
# cliente    Sofía
# monto        150
# ciudad     Cusco
# estado   inactivo
# Name: 2, dtype: object

df.iloc[[0, 2], [1, 2]] #Obtener filas 0, 2 y columnas 1 (monto) y 2 (ciudad)

#Salida esperada
#   monto ciudad
# 0    120   Lima
# 2    150  Cusco

```