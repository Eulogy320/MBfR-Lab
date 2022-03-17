Protocolo de análisis de secuencias genómicas 
=======================================


# Descarga de secuencias 

Las secuencias se suben a plataformas de almacenamiento o *"Database"* (DB) por ejemplo GenBank. GenBank está subidido en varias bases de datos, de las cuales la que utilizamos corresponde al SRA - NCBI (*Sequence Read Archive - National Center for Biotechnology information*). 

| **Código** | **Descripción** |
|---------------|------------|
| Bioproject | Base de datos que reune la metadata de los projectos de investigación y las secuencias que son depositadas|
| Biosample  | Base de datos que almacena toda la información relacionada con las secuencias|
| SRA        | Es un repositorio donde se unifican las dos DB anteriores más otros recursos |
 
Para poder descargar una secuencia dede el GenBank debemos hacerlo mediante SRA-TOOL kit. A continuación se presenta una script en bash para descargar los archivos correspondientes a las secuenciaciones hechas por el laboratorio. 


```Bash
#!/bin/bash
#### Reemplazar las variables por las rutas en tú computador de donde se encuntran los archivos
################################## Declarar variables ###################################################
## Crear el archivo sample.txt con los códigos de indexación de SRA (NCBI)
Dir=/home/eulogy/Desktop/PROYECTO_11191026/genomica/programas/sratoolkit.2.11.3-ubuntu64/bin
samples=/home/eulogy/Desktop/PROYECTO_11191026/genomica/Script/fastq/test_4/samples.txt
dw=/home/eulogy/Desktop/PROYECTO_11191026/genomica/Script/fastq/test_4/SRA
##############################################################################################################
```

El archivo *sample.txt* contiene los código con los cuales las secuencias estás indexadas en el GenBank. 
e.g. [samples.txt](https://github.com/Eulogy320/MBfR-Lab/files/8268830/samples.txt)

```Bash
$ head sample.txt 
>
SRR6966215
SRR6966216
SRR6966217
SRR6966218
SRR6966219
```

Luego de declarar algunas variables preeliminares, procedemos a descargar las secuencias, con los códigos en el archivo anterior (txt simple).

```Bash
############################################# Descarga #######################################################
echo 
echo Bloque Descarga
echo

for sample in $(cat $samples)
do
   printf "\n  Descargando desde SRA la muestra: ${sample}\n"
    
    $Dir/prefetch "${sample}" --output-directory $dw                   
done

## Acabamos de definir un loop, que hace la misma operación por cada código dentro de sample.txt
           
echo 
echo Moviendo archivos a direcotio raiz
echo

cd $dw

find . -type f -name "*.sra" |while read file

do
  mv $file $dw

done

for d in $(cat $samples)

do 

  printf "\n  Borrando carpetas  ${d}\n"

    rm -r $dw/"${d}"
done

echo 
echo Fin
echo
```
