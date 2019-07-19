---
title: 'Esegui script R: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Execute R script nel servizio Azure Machine Learning per eseguire il codice R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 710d64b445953ae3124830931c8cbb9315d32b83
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875719"
---
# <a name="execute-r-script"></a>Execute R Script

Questo articolo descrive come usare il modulo **Execute R script** per eseguire codice r nell'esperimento dell'interfaccia visiva.

Con R è possibile eseguire attività che non sono attualmente supportate dai moduli esistenti, ad esempio: 
- Creare trasformazioni di dati personalizzate
- Usare metriche personalizzate per valutare le stime
- Compilare modelli usando algoritmi che non sono implementati come moduli autonomi nell'interfaccia visiva

## <a name="r-version-support"></a>Supporto della versione di R

L'interfaccia visiva del servizio Azure Machine Learning usa la distribuzione CRAN (complete R Archive Network) di R. La versione attualmente utilizzata è CRAN 3.5.1.

## <a name="supported-r-packages"></a>Pacchetti R supportati

L'ambiente R è preinstallato con oltre 100 pacchetti. Per un elenco completo, vedere la sezione [pacchetti R pre-installati](#pre-installed-r-packages).

È anche possibile aggiungere il codice seguente a qualsiasi modulo **Execute R script** e visualizzare i pacchetti installati.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Installazione di pacchetti R
Per installare altri pacchetti R, usare il `install.packages()` metodo. Assicurarsi di specificare il repository CRAN. I pacchetti vengono installati per ogni modulo **Execute r script** e non sono condivisi tra altri moduli **Execute r script** .

Questo esempio illustra come installare Zoo:
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

## <a name="how-to-configure-execute-r-script"></a>Come configurare Execute R script

Il modulo **Execute R script** contiene codice di esempio che è possibile usare come punto di partenza. Per configurare il modulo **Execute R script** , fornire un set di input e codice da eseguire.

![Modulo R](media/module/execute-r-script.png)

I set di dati archiviati nell'interfaccia visiva vengono convertiti automaticamente in un frame di dati R quando vengono caricati con questo modulo.

1.  Aggiungere il modulo **Execute R script (Esegui script R** ) all'esperimento.

    > [!NOTE]
    > Tutti i dati passati al modulo **Execute r script** vengono convertiti nel formato `data.frame` R.

1. Connettere gli input richiesti dallo script. Gli input sono facoltativi e possono includere dati e codice R aggiuntivo.

    * **DataSet1**: Fare riferimento al primo input `dataframe1`come. Il set di dati di input deve essere formattato come CSV, TSV, ARFF oppure è possibile connettere un set di dati Azure Machine Learning.

    * **Dataset2**: Fare riferimento al secondo input `dataframe2`come. Questo set di dati deve essere formattato anche come file CSV, TSV, ARFF o come set di dati Azure Machine Learning.

    * **Bundle di script**: Il terzo input accetta file ZIP. Il file compresso può contenere più file e più tipi di file.

1. Nella casella di testo **script r** Digitare o incollare uno script r valido.

    Per iniziare, la casella di testo **script R** viene prepopolata con il codice di esempio, che è possibile modificare o sostituire.

    * Lo script deve contenere una funzione denominata `azureml_main`, che rappresenta il punto di ingresso per questo modulo.

    * La funzione del punto di ingresso può contenere un massimo di due `Param<dataframe1>` argomenti di input: e`Param<dataframe2>`
 
    > [!NOTE]
    >  Il codice R esistente potrebbe richiedere modifiche minime da eseguire in un esperimento dell'interfaccia visiva. Ad esempio, i dati di input forniti in formato CSV devono essere convertiti in modo esplicito in un set di dati prima di poterli usare nel codice. I tipi di dati e di colonna usati nel linguaggio R variano anche in qualche modo dai tipi di dati e di colonna usati nell'interfaccia visiva.

1.  Valore di **inizializzazione casuale**: Digitare un valore da usare all'interno dell'ambiente R come valore di inizializzazione casuale. Questo parametro equivale a chiamare `set.seed(value)` nel codice R.  

1. Eseguire l'esperimento.  

## <a name="results"></a>Risultati

I moduli **Execute R script** possono restituire più output, ma devono essere specificati come frame di dati R. I frame di dati vengono convertiti automaticamente nei set di dati dell'interfaccia visiva per la compatibilità con altri moduli.

I messaggi e gli errori standard di R vengono restituiti al log del modulo.

## <a name="sample-scripts"></a>Script di esempio

Esistono diversi modi per estendere l'esperimento usando uno script R personalizzato.  In questa sezione viene fornito il codice di esempio per le attività comuni.


### <a name="add-r-script-as-an-input"></a>Aggiungere uno script R come input

Il modulo **Execute r script** supporta i file di script r arbitrari come input. A tale scopo, è necessario caricarli nell'area di lavoro come parte del file ZIP.

1. Per caricare un file ZIP contenente codice R nell'area di lavoro, fare clic su **nuovo**, quindi su **set di dati**e infine selezionare **da file locale** e l'opzione **file zip** .  

1. Verificare che il file compresso sia disponibile nell'elenco **set di impostazioni salvati** .

1.  Connettere il set di dati alla porta di input del **bundle di script** .

1. Tutti i file contenuti nel file ZIP sono disponibili durante la fase di esecuzione dell'esperimento. 

    Se il file del bundle di script contiene una struttura di directory, la struttura viene mantenuta. Tuttavia, è necessario modificare il codice per anteporre il **bundle directory./script** al percorso.

### <a name="process-data"></a>Elaborazione dei dati

Nell'esempio seguente viene illustrato come ridimensionare e normalizzare i dati di input:

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

### <a name="read-a-zip-file-as-input"></a>Leggi un file ZIP come input

Questo esempio illustra come usare un set di dati in un file ZIP come input per il modulo **Execute R script** .

1. Creare il file di dati in formato CSV e denominarlo "DataFile. csv".
1. Creare un file ZIP e aggiungere il file CSV all'archivio.
1. Caricare il file compresso nell'area di lavoro Azure Machine Learning. 
1. Connettere il set di dati risultante all'input **ScriptBundle** del modulo **Execute R script** .
1. Usare il codice seguente per leggere i dati CSV dal file compresso.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replicare righe

In questo esempio viene illustrato come replicare i record positivi in un set di dati per bilanciare l'esempio:

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

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Passare oggetti R tra i moduli Execute R script

È possibile passare oggetti R tra le istanze del modulo **Execute R script** usando il meccanismo di serializzazione interno. Questo esempio presuppone che si voglia spostare l'oggetto R denominato `A` tra due moduli **Execute R script** .

1. Aggiungere il primo modulo **Execute R script** all'esperimento e digitare il codice seguente nella casella di testo **script r** per creare un oggetto `A` serializzato come colonna nella tabella dati di output del modulo:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    La conversione esplicita in un tipo Integer viene eseguita perché la funzione di serializzazione restituisce `Raw` i dati nel formato R, che non è supportato dall'interfaccia visiva.

1. Aggiungere una seconda istanza del modulo **Execute R script** e connetterla alla porta di output del modulo precedente.

1. Digitare il codice seguente nella casella di testo **script R** per estrarre l' `A` oggetto dalla tabella dati di input. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Pacchetti R pre-installati

Elenco corrente dei pacchetti R preinstallati disponibili per l'uso:

|              |            | 
|--------------|------------| 
| Pacchetto      | Version    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0,1-3      | 
| BH           | 1.69.0-1   | 
| bindingr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bicime       | 1.0-6      | 
| boot         | 1,3-22     | 
| Scopa        | 0.5.2      | 
| chiamante        | 3.2.0      | 
| cursore        | 6.0-84     | 
| catools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| classe        | 7.3-15     | 
| cli          | 1.1.0      | 
| clipr        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| ColorSpace   | 1.4-1      | 
| compilatore     | 3.5.1      | 
| pastello       | 1.3.4      | 
| CURL         | 3.3        | 
| Data. Table   | 1.12.2     | 
| datasets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evaluate     | 0,14       | 
| fansi        | 0.4.0      | 
| Forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| esterna      | 0.8-71     | 
| FS           | 1.3.1      | 
| GData        | 2.18.0     | 
| Generics     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| Glue         | 1.3.1      | 
| Gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| grafica     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| griglia         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| Haven        | 2.1.0      | 
| più alta        | 0,8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| IPRED        | 0.9-9      | 
| iteratori    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | -2 23E-15    | 
| maglia        | 1,23       | 
| etichette     | 0.3        | 
| Reticolo      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1,5        | 
| Markdown     | 1          | 
| MASSA         | 7.3-51.4   | 
| Matrix       | 1.2-17     | 
| Metodi      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| MIME         | 0,7        | 
| ModelMetrics | 1.2.2      | 
| modello di       | 0.1.4      | 
| Munsell      | 0.5.0      | 
| nLME         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallelo     | 3.5.1      | 
| Pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progress     | 1.2.2      | 
| ps           | 1.3.0      | 
| ronzio        | 0.3.2      | 
| quadprog     | 1,5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| lettura        | 1.3.1      | 
| readxl       | 1.3.1      | 
| ricette      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticolare   | 1,12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1,13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0,1        | 
| rvest        | 0.3.4      | 
| scale       | 1.0.0      | 
| Selezionare      | 0.4-1      | 
| spaziali      | 7.3-11     | 
| spline      | 3.5.1      | 
| QUADRATO      | 2017.10-1  | 
| Statistiche        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringa      | 1.4.3      | 
| stringa      | 1.3.1      | 
| sopravvivenza     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| Riordina        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043,102   | 
| tinytex      | 0,13       | 
| strumenti        | 3.5.1      | 
| tseries      | 0,10-47    | 
| TTR          | 0.23-4     | 
| UTF8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| Baffo      | 0.3-2      | 
| con        | 2.1.2      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| XTS          | 0.11-2     | 
| YAML         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 