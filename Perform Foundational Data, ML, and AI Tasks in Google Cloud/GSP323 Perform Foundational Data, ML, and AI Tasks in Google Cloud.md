
En un laboratorio de desafío, se te asigna un escenario y un conjunto de tareas. En lugar de seguir instrucciones paso a paso, utilizarás las habilidades aprendidas en los laboratorios de la misión para descubrir cómo completar las tareas por tu cuenta. Un sistema de puntuación automatizado (que se muestra en esta página) le proporcionará comentarios sobre si ha completado sus tareas correctamente.

Cuando realiza un laboratorio de desafío, no se le enseñarán nuevos conceptos de Google Cloud. Se espera que amplíe las habilidades aprendidas, como cambiar los valores predeterminados y leer e investigar mensajes de error para corregir sus propios errores.

Duración: 1 Hora 20 minutos
Creditos: 5

Antes que nada debemos inicializar nuestros valores, para eso utilizamos el comando **export**, esto nor permitira que los comandos generales del SCRIP no cambien y podamos reutilizarlo unicamente modificando los valores específicos de nuestro entorno.


```bash
#Remplaza los valores aqui mostrados por los valores que muestra tu laboratorio
export REGION= 
export BigQuery_output_table=
```
Una vez que tengamos listos estos parametros debemos copiar y pergar el siguiente texto en la terminal.


```bash
#Este segmento de código crea un archivo llamado "s.py" agregando el contenido.
cat << EOF > s.py
input_string = "$BigQuery_output_table"
parts = input_string.split(':')
LAB_NAME = parts[1].split('.')[0]
CUSTOMERS = parts[1].split('.')[1]
print("LAB_NAME:", LAB_NAME)
print("CUSTOMERS:", CUSTOMERS)
EOF # Aqui termina la creación de nuestro archivo s.py

#Asignamos a la variable "output" el arvhivo s.py
output=$(python s.py)
LAB_NAME=$(echo "$output" | awk '/LAB_NAME:/ {print $2}')
CUSTOMERS=$(echo "$output" | awk '/CUSTOMERS:/ {print $2}')

#creamos el elemento Big Query mediante el comando "bq", las letras mk representan la instrucción "make"
bq mk $LAB_NAME

#A continuación creamos el "bucket"
gsutil mb gs://$DEVSHELL_PROJECT_ID-marking/
gsutil cp gs://cloud-training/gsp323/lab.csv  .
gsutil cp gs://cloud-training/gsp323/lab.schema .
gcloud dataflow jobs run Cloudhustler --gcs-location gs://dataflow-templates-$REGION/latest/GCS_Text_to_BigQuery --region $REGION --worker-machine-type e2-standard-2 --staging-location gs://$DEVSHELL_PROJECT_ID-marking/temp --parameters javascriptTextTransformGcsPath=gs://cloud-training/gsp323/lab.js,JSONPath=gs://cloud-training/gsp323/lab.schema,javascriptTextTransformFunctionName=transform,outputTable=$BigQuery_output_table,inputFilePattern=gs://cloud-training/gsp323/lab.csv,bigQueryLoadingTemporaryDirectory=gs://$DEVSHELL_PROJECT_ID-marking/bigquery_temp
gcloud dataproc clusters create cluster-b53a --region $REGION --master-machine-type e2-standard-2 --master-boot-disk-size 500 --num-workers 2 --worker-machine-type e2-standard-2 --worker-boot-disk-size 500 --image-version 2.1-debian11 --project $DEVSHELL_PROJECT_ID
```
Al dar enter se comenzara a procesar la solicitud.
Podemos esperar a que finalice el proceso, o podemos abrir una pestaña adicional para continuar con el proceso siguiente.
Recordemos tomar los valores de nuestro laboratorio.
``` bash
export API_KEY=
export Bucket_TASK_3=
export Bucket_TASK_4=
```
Una vez hayamos generado estos valores
