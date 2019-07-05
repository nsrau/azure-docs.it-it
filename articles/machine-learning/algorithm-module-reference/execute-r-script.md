---
title: 'Modulo Execute R Script: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Execute R Script nel servizio Azure Machine Learning per eseguire il codice R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bfddcd3db4825ea1875474aa16544aa15412bdea
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518053"
---
# <a name="execute-r-script"></a>Execute R Script

Questo articolo descrive come usare il **Execute R Script** modulo per eseguire codice R in un esperimento interfaccia visiva.

Con R, è possibile eseguire le attività che non sono attualmente supportate per i moduli esistenti, ad esempio: 
- Creare trasformazioni dati personalizzate
- Usare le metriche personalizzate per valutare le stime
- Compilare modelli usando algoritmi non implementati come moduli autonomi in interfaccia visiva

## <a name="r-version-support"></a>Supporto della versione di R

L'interfaccia visiva di servizio di Azure Machine Learning utilizza la distribuzione CRAN (Comprehensive R Archive Network) di R. La versione attualmente in uso è CRAN 3.5.1.

## <a name="supported-r-packages"></a>Pacchetti R supportati

L'ambiente R è preinstallato con oltre 100 pacchetti. Per un elenco completo, vedere la sezione [i pacchetti R preinstallate](#pre-installed-r-packages).

È anche possibile aggiungere il codice seguente a qualsiasi **Execute R Script** modulo e visualizzare i pacchetti installati.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Installazione di pacchetti R
Per installare pacchetti R aggiuntivi, usare il `install.packages()` (metodo). Assicurarsi di specificare il repository CRAN. I pacchetti vengono installati per ogni **Execute R Script** modulo che non sono condivise tra altri **Execute R Script** moduli.

Questo esempio viene illustrato come installare lo Zoo di:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="how-to-configure-execute-r-script"></a>Jak nakonfigurovat Execute R Script

Il **Execute R Script** modulo contiene codice di esempio che è possibile usare come punto di partenza. Per configurare il **Execute R Script** modulo, fornire un set di input e l'esecuzione di codice.

![Modulo R](media/module/execute-r-script.png)

Set di dati archiviati nell'interfaccia visiva vengono convertiti automaticamente in un frame di dati R, quando caricati con questo modulo.

1.  Aggiungere il **Execute R Script** modulo nell'esperimento.

    > [!NOTE]
    > Tutti i dati passati per il **Execute R Script** modulo viene convertito in R `data.frame` formato.

1. Connettere tutti gli input necessari per lo script. Gli input sono facoltativi e possono includere dati e codice R aggiuntivo.

    * **Dataset1**: Fare riferimento il primo input come `dataframe1`. Il set di dati di input deve essere formattato come CSV, TSV, ARFF o è possibile connettere un set di dati di Azure Machine Learning.

    * **Dataset2**: Fare riferimento il secondo input come `dataframe2`. Questo set di dati deve inoltre essere formattato come un volume CSV, TSV, file ARFF, o come un set di dati di Azure Machine Learning.

    * **Script Bundle**: Il terzo input accetta i file con estensione ZIP. Il file compresso può contenere più file e più tipi di file.

1. Nel **lo script R** casella di testo digitare o incollare uno script R valido.

    Per aiutarti a iniziare a usare, il **lo Script R** casella di testo viene prepopolata con codice di esempio, che è possibile modificare o sostituire.

    * Lo script deve contenere una funzione denominata `azureml_main`, ovvero il punto di ingresso per questo modulo.

    * La funzione di punto di ingresso può contenere fino a due argomenti di input: `Param<dataframe1>` e `Param<dataframe2>`
 
    > [!NOTE]
    >  Il codice R esistente potrebbe essere necessario piccole modifiche per l'esecuzione in un esperimento di interfaccia visiva. Ad esempio, i dati di input forniti in formato CSV devono essere esplicitamente convertiti in un set di dati prima di usarla nel codice. Tipi di dati e di colonna usati nel linguaggio R presentano differiscono alcuni tra i tipi di dati e di colonna utilizzati nell'interfaccia visual.

1.  **Valore di inizializzazione casuale**: Digitare un valore da usare nell'ambiente R come valore di inizializzazione casuale. Questo parametro è equivalente alla chiamata `set.seed(value)` nel codice R.  

1. Eseguire l'esperimento.  

## <a name="results"></a>Risultati

Il **Execute R Script** moduli possono restituire più output, ma devono essere fornite come frame di dati R. I frame di dati vengono convertiti automaticamente al set di dati di interfaccia visiva per la compatibilità con altri moduli.

Registro del modulo vengono restituiti i messaggi standard e gli errori da R.

## <a name="sample-scripts"></a>Script di esempio

Esistono molti modi in cui è possibile estendere l'esperimento con script R personalizzato.  Questa sezione presenta il codice di esempio per attività comuni.


### <a name="add-r-script-as-an-input"></a>Aggiungere lo script R come input

Il **Execute R Script** modulo supporta i file di script R arbitrari come input. A tale scopo, essi devono essere caricati nell'area di lavoro come parte del file ZIP.

1. Per caricare un file ZIP contenente codice R nell'area di lavoro, fare clic su **New**, fare clic su **set di dati**, quindi selezionare **dal file locale** e il **file con estensione Zip**opzione.  

1. Verificare che il file compresso sia disponibile nel **Saved Datasets** elenco.

1.  Connettere il set di dati per il **Bundle di Script** porta di input.

1. Tutti i file contenuti nel file ZIP sono disponibili durante la fase di esecuzione dell'esperimento. 

    Se il file di bundle di script contiene una struttura di directory, viene mantenuta la struttura. Tuttavia, è necessario modificare il codice per la directory di anteporre **. / Script Bundle** al percorso.

### <a name="process-data"></a>Elaborazione dei dati

L'esempio seguente illustra come ridimensionare e normalizzare i dati di input:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.
# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Leggere un file con estensione ZIP come input

In questo esempio viene illustrato come utilizzare un set di dati in un file ZIP come input per il **Execute R Script** modulo.

1. Creare il file di dati in formato CSV e denominarlo "mydatafile".
1. Creare un file ZIP e aggiungere il file CSV nell'archivio.
1. Caricare il file compresso all'area di lavoro di Azure Machine Learning. 
1. Connettere il set di dati risultante per il **ScriptBundle** input delle **Execute R Script** modulo.
1. Usando il codice seguente per leggere i dati CSV dal file compresso.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replicare righe

Questo esempio viene illustrato come eseguire la replica positivo record in un set di dati per bilanciare l'esempio:

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Passare oggetti R tra moduli Execute R Script

È possibile passare oggetti R tra istanze del **Execute R Script** modulo mediante il meccanismo di serializzazione interno. Questo esempio si presuppone che si desidera spostare l'oggetto R denominato `A` tra due **Execute R Script** moduli.

1. Aggiungere il primo **Execute R Script** per l'esperimento e digitare il seguente codice nel modulo le **lo Script R** casella di testo per creare un oggetto serializzato `A` come tabella di dati di output di una colonna nel modulo:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    La conversione esplicita di tipo integer viene eseguita perché la funzione di serializzazione restituisce i dati in R `Raw` formato, che non è supportato per l'interfaccia visiva.

1. Aggiungere una seconda istanza di **Execute R Script** modulo e connetterla alla porta di output del modulo precedente.

1. Digitare il codice seguente nel **lo Script R** casella di testo per estrarre l'oggetto `A` dalla tabella di dati di input. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Pacchetti R pre-installato

L'elenco corrente di pre-installati i pacchetti R disponibili per l'uso:

|              |            | 
|--------------|------------| 
| Pacchetto      | Version    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| boot         | 1.3-22     | 
| Scopa        | 0.5.2      | 
| callr        | 3.2.0      | 
| Punto di inserimento        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| class        | 7.3-15     | 
| cli          | 1.1.0      | 
| clipr        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| spazio colore   | 1.4-1      | 
| compiler     | 3.5.1      | 
| crayon       | 1.3.4      | 
| CURL         | 3.3        | 
| data.table   | 1.12.2     | 
| datasets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evaluate     | 0,14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| straniero      | 0.8-71     | 
| ADFS           | 1.3.1      | 
| gdata        | 2.18.0     | 
| generics     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| Grafica     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| Griglia         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| valutazione        | 2.1.0      | 
| highr        | 0,8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| Iteratori    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1,23       | 
| L'assegnazione di etichette     | 0,3        | 
| reticolo      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1,5        | 
| Markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matrix       | 1.2-17     | 
| Metodi      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| MIME         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| Parallelo     | 3.5.1      | 
| Pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progress     | 1.2.2      | 
| ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| RandomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| ricette      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| r markdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0,1        | 
| rvest        | 0.3.4      | 
| Scale       | 1.0.0      | 
| selectr      | 0.4-1      | 
| Spaziali      | 7.3-11     | 
| spline      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| Statistiche        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| strumenti        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| Utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| baffo      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 