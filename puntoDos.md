# Prueba Tecnica
## **Parte Dos**

### *Desarrollar un script de Python para la migración de datos a la nube AWS*.

#### _Una empresa tiene un gran volumen de datos almacenados en una base de datos on-premise. La empresa desea migrar estos datos a la nube AWS y necesita un script de Python que lea los datos de la base de datos on-premise, los transforme y los almacene en una base de datos en la nube AWS_

* Desarrollar un script de Python que lea los datos de la base de datos on-premise, los transforme y los almacene en una base de datos en la nube AWS
```python
import psycopg2
import boto3

# Conexión a la base de datos on-premise
on_prem_conn = psycopg2.connect(database="clientes_on-premise", user="usuario", password="contraseña", host="direccion_ip_on-premise", port="5432")

# Conexión a la tabla "clientes" en la base de datos on-premise
on_prem_cursor = on_prem_conn.cursor()
on_prem_cursor.execute("SELECT * FROM clientes")
rows = on_prem_cursor.fetchall()

# Transformación de datos
transformed_rows = []
for row in rows:
    transformed_row = {}
    transformed_row["id"] = row[0]
    transformed_row["nombre"] = row[1].title()
    transformed_row["direccion"] = row[2].upper()
    transformed_row["ciudad"] = row[1].title()
    transformed_row["pais"] = row[1].title()
    transformed_row["telefono"] = "+57" + row[3]
    transformed_rows.append(transformed_row)

# Conexión a la tabla en la nube AWS
aws_session = boto3.Session(profile_name='default')
dynamodb = aws_session.resource('dynamodb', region_name='us-west-2')
table = dynamodb.Table('cliente_aws')

# Almacenamiento de datos
with table.batch_writer() as batch:
    for row in transformed_rows:
        batch.put_item(Item=row)

# Cierre de conexiones
on_prem_cursor.close()
on_prem_conn.close()

```
* Proporcionar un archivo CSV de ejemplo y un archivo de especificaciones que indique cómo deben transformarse los datos
```python
archivo.csv
```
<!-- Clientes -->
| id_cliente | Ubicacion    | consumo_agua   | consumo_electricidad | 
|------------|-----------|------------|-----------------------------|
| 1          | Ciudad A      | 1000      | 500                    |
| 2          | Ciudad A     | 2500  |      800                    |
| 3          | Ciudad B    | 800    |     1200                    |
| 4          | Ciudad C    |   1000      |  1200                    |

### **Transformaciones de la tabla**
```python
import pandas as pd

# Cargar archivo CSV
df = pd.read_csv('archivo.csv')

# Función para convertir la columna de ubicación a formato estándar
def convertir_ubicacion(row):
    ubicacion = row['ubicacion'].upper()
    if 'CALLE' in ubicacion:
        ubicacion = ubicacion.replace('CALLE', 'C.')
    elif 'AVENIDA' in ubicacion:
        ubicacion = ubicacion.replace('AVENIDA', 'Av.')
    return ubicacion

# Aplicar la función a la columna ubicación
df['ubicacion'] = df.apply(convertir_ubicacion, axis=1)

# Función para calcular el consumo total de agua y electricidad
def calcular_consumo_total(row):
    consumo_agua = row['consumo_agua']
    consumo_electricidad = row['consumo_electricidad']
    return consumo_agua + consumo_electricidad

# Aplicar la función para calcular el consumo total
df['consumo_total'] = df.apply(calcular_consumo_total, axis=1)

# Guardar el resultado en un nuevo archivo CSV
df.to_csv('archivo_transformado.csv', index=False)

``` 
```python
archivo_transformado.csv
```

| cliente_id | ubicacion     | consumo_agua_actual | consumo_agua_anterior | consumo_electricidad_actual | consumo_electricidad_anterior | consumo_total_agua | consumo_total_electricidad |
|------------|---------------|---------------------|-----------------------|------------------------------|--------------------------------|---------------------|---------------------------|
| 1          | Bogotá, D.C.  | 150                 | 120                   | 200                          | 180                            | 30                  | 20                        |
| 2          | Medellín      | 80                  | 70                    | 120                          | 100                            | 10                  | 20                        |
| 3          | Cali          | 100                 | 90                    | 150                          | 140                            | 10                  | 10                        |
| 4          | Barranquilla  | 200                 | 190                   | 280                          | 250                            | 10                  | 30                        |
| 5          | Cartagena     | 90                  | 80                    | 130                          | 120                            | 10                  | 10                        |

* ### Explicar cómo se garantizará la seguridad y privacidad de los datos
    * Encriptación: Los datos deben ser encriptados durante la transferencia y almacenamiento en la nube. AWS ofrece diferentes opciones de encriptación, como la encriptación de objetos con Amazon S3, el cifrado de bases de datos con Amazon RDS, entre otras.
    * Acceso controlado: El acceso a los datos en la nube debe ser restringido solo a las personas que necesitan tener acceso a ellos. Se pueden establecer políticas de control de acceso y autenticación, utilizando herramientas como AWS Identity and Access Management (IAM).
    * Auditoría y monitoreo: Se deben establecer mecanismos para registrar y monitorear el acceso a los datos. AWS ofrece herramientas como Amazon CloudTrail y Amazon CloudWatch para auditar y monitorear el acceso a los recursos en la nube.
    * Copias de seguridad y recuperación de desastres: Se deben establecer políticas de copias de seguridad y recuperación de desastres para garantizar la disponibilidad y la integridad de los datos. AWS ofrece opciones de backup y recuperación de desastres, como Amazon S3 y Amazon Glacier
    * Cumplimiento normativo: Es importante cumplir con las regulaciones y estándares de seguridad aplicables, como HIPAA, GDPR, PCI, etc. AWS cumple con una amplia variedad de regulaciones y estándares de seguridad y ofrece herramientas para ayudar a los clientes a cumplir con estas regulaciones
* Proporcionar instrucciones claras sobre cómo configurar y ejecutar el script
    * Asegúrate de tener instalados Python, psycopg2 y pandas "
    
    * Descarga el archivo CSV de ejemplo y guárdalo en tu directorio de trabajo
    * Abre la terminal y navega a la ubicación donde guardaste el script.
    * Ejecuta el script en tu terminal con el comando
     ```python
        python script.py
     ```
* ### **Recomendaciones**
    * Realizar una optimización del código para reducir el tiempo de procesamiento. Por ejemplo, se pueden utilizar librerías como Pandas para optimizar la carga y procesamiento de los datos.
    * Realizar la migración en lotes más pequeños en lugar de migrar todo el conjunto de datos a la vez. Esto puede ayudar a reducir la carga del servidor y mejorar el rendimiento
    * Asegurarse de que el servidor y la base de datos en la nube tengan suficiente capacidad de almacenamiento y recursos de procesamiento para manejar grandes volúmenes de datos.
    * Utilizar técnicas de particionamiento para dividir el conjunto de datos en partes más pequeñas y procesarlas de forma paralela. Esto puede ayudar a reducir el tiempo de procesamiento
    * Realizar pruebas de rendimiento para identificar cuellos de botella y áreas problemáticas en el código y la infraestructura, y tomar medidas para solucionar estos problemas.
    * Monitorear el proceso de migración de forma regular para identificar problemas en tiempo real y tomar medidas para solucionarlos de manera oportuna


<!-- Mentiosn -->
[Jorge Diaz](github.com/jmdiaz23)