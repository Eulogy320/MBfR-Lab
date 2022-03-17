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

Una vez que las secuencias están en el directorio de trabajo se procede a convertir el formato del archivo y remover los partidores. 
Esto se logra con el archivo fasterq-dump.py contenido en la carpeta BIN del SRA-toolkit

```Bash
############################################### Tratamiento de secuencias ###########################################
############################################### Conversión de SRA a FATQ ###########################################
## Este código gestiona la conversión de la extensión del formato de la secuencias desde .SRA a fastq

mkdir $dw/fastq 
for sample in $(cat $samples)
do

   printf "\n SRA to FASTQ la muestra : ${sample}\n"
  
    $Dir/fasterq-dump --split-files $dw/"${sample}".sra --outdir $dw/fastq

done
```

Luego de la conversión del formato de las secuencias, debemos sacar de ellas las lecturas que pudieron quedar de partidores, para eso utilizaremos la siguiente línea de código. 

```Bash
############################# TRIMMING ##############################################
#trimming primers

for sample in $(cat $samples)
do

    printf "\n  Trimming sample: ${sample}\n"
    cutadapt -g GTGCCAGCMGCCGCGGTAA -G CCGYCAATTYMTTTRAGTTT \
             -o $dw/fastq/trimmed/${sample}_1_trimmed.fastq.gz -p $dw/fastq/trimmed/${sample}_2_trimmed.fastq.gz \
              $dw/fastq/${sample}_1.fastq $dw/fastq/${sample}_2.fastq \
             > $dw/fastq/trimmed/${sample}-cutadapt.log 2>&1
done

echo 
echo Fin
echo
########################################################################################
```

Estas secuencias son secuenciación de AMPLICONES de regiones 16/18s (procarioentes  y eucariontes) en la misma muestra, por tanto debemos separarlas.

```Bash
################################## BLAST #####################################################

######################### INSTALLAR MagicBlast ##############################################
 
echo instalando magicblast
conda install -c bioconda magicblast
```




