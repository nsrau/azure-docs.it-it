---
title: 'Esegui script R: riferimento al moduloExecute R Script: Module Reference'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Execute R Script in Azure Machine Learning per eseguire il codice R.Learn how to use the Execute R Script module in Azure Machine Learning to run R code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: f038293b48956ac89314e426df3f5dc491954df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064212"
---
# <a name="execute-r-script"></a>Execute R Script

Questo articolo descrive come usare il modulo **Esegui script R** per eseguire il codice R nella pipeline di progettazione di Azure Machine Learning (anteprima).

Con R, è possibile eseguire attività che non sono attualmente supportate da moduli esistenti, ad esempio:With R, you can perform tasks that aren't currently supported by existing modules such as: 
- Creare trasformazioni dati personalizzate
- Usare le proprie metriche per valutare le stimeUse your own metrics to evaluate predictions
- Compilare modelli usando algoritmi non implementati come moduli autonomi nella finestra di progettazioneBuild models using algorithms that aren't implemented as standalone modules in the designer

## <a name="r-version-support"></a>Supporto della versione R

Azure Machine Learning designer uses the CRAN (Comprehensive R Archive Network) distribution of R. La versione attualmente utilizzata è CRAN 3.5.1.

## <a name="supported-r-packages"></a>Pacchetti R supportati

L'ambiente R è preinstallato con oltre 100 pacchetti. Per un elenco completo, vedere la sezione [Pacchetti R preinstallati](#pre-installed-r-packages).

È anche possibile aggiungere il codice seguente a qualsiasi modulo **Execute R Script** e visualizzare i pacchetti installati.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Installazione di pacchetti R
Per installare pacchetti R `install.packages()` aggiuntivi, usare il metodo . Assicurarsi di specificare il repository CRAN. I pacchetti vengono installati per ogni modulo **Execute R Script** e non vengono condivisi tra altri moduli Execute R Script.Packages are installed for each Execute R Script module, and aren't shared across other **Execute R Script** modules.

In questo esempio viene illustrato come installare lo zoo:
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
 > [!NOTE]
  > Si prega di verificare se il pacchetto esiste già prima di installarlo per evitare la ripetizione dell'installazione. Come `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` nel codice di esempio precedente. L'installazione ripetuta può causare il timeout della richiesta del servizio Web.Repeat installing may cause web service request timeout.     

## <a name="upload-files"></a>Caricare file
**Lo script Execute R** supporta il caricamento di file tramite Azure Machine Learning R SDK.

L'esempio seguente mostra come caricare un file di immagine nello **script Execute R**:
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # Generate a jpeg graph
  img_file_name <- "rect.jpg"
  jpeg(file=img_file_name)
  example(rect)
  dev.off()

  upload_files_to_run(names = list(file.path("graphic", img_file_name)), paths=list(img_file_name))


  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

Al termine dell'esecuzione della pipeline, è possibile visualizzare l'anteprima dell'immagine nel pannello di destra del modulo

> [!div class="mx-imgBorder"]
> ![Immagine caricata](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Come configurare Execute R Script

Il modulo **Execute R Script** contiene codice di esempio che è possibile usare come punto di partenza. Per configurare il modulo **Execute R Script,** fornire un set di input e codice da eseguire.

![Modulo R](media/module/execute-r-script.png)

I set di dati archiviati nella finestra di progettazione vengono convertiti automaticamente in un frame di dati R quando vengono caricati con questo modulo.

1.  Aggiungere il modulo **Execute R Script** alla pipeline.

  

1. Collegare tutti gli input necessari per lo script. Gli input sono facoltativi e possono includere dati e codice R aggiuntivo.

    * **Set di dati1**: `dataframe1`fare riferimento al primo input come . Il set di dati di input deve essere formattato come CSV, TSV, ARFF oppure è possibile connettere un set di dati di Azure Machine Learning.The input dataset must be formatted as a CSV, TSV, ARFF, or you can connect an Azure Machine Learning dataset.

    * **Dataset2**: Fare riferimento `dataframe2`al secondo input come . Questo set di dati deve anche essere formattato come file CSV, TSV, ARFF o come set di dati di Azure Machine Learning.This dataset also must be formatted as a CSV, TSV, ARFF file, or as an Azure Machine Learning dataset.

    * **Pacchetto di script**: Il terzo input accetta i file zip. Il file compresso può contenere più file e più tipi di file.

1. Nella casella di testo **Script R** digitare o incollare uno script R valido.

    Per iniziare, la casella di testo **R Script** è precompilata con codice di esempio, che è possibile modificare o sostituire.
    
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

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

 * Lo script deve contenere una funzione denominata `azureml_main`, che è il punto di ingresso per questo modulo.

 * La funzione del punto di ingresso `Param<dataframe1>` può contenere fino a due argomenti di input: e`Param<dataframe2>`
 
   > [!NOTE]
    > Ai dati passati al modulo Execute `dataframe1` `dataframe2` **R Script** viene fatto riferimento come e `dataset1`, `dataset2`che è diverso dalla finestra di progettazione di Azure Machine Learning (il riferimento alla finestra di progettazione come , ). Verificare che i dati di input siano referenati correttamente nello script.  
 
    > [!NOTE]
    >  Il codice R esistente potrebbe richiedere modifiche minori per l'esecuzione in una pipeline di progettazione. Ad esempio, i dati di input forniti in formato CSV devono essere convertiti in modo esplicito in un set di dati prima di poterlo usare nel codice. I tipi di dati e colonna utilizzati nel linguaggio R differiscono anche in alcuni modi dai tipi di dati e colonna usati nella finestra di progettazione.

1.  **Seed casuale**: Digitare un valore da usare all'interno dell'ambiente R come valore di seme casuale. Questo parametro è equivalente alla chiamata di `set.seed(value)` nel codice R.  

1. Inviare la pipeline.  

## <a name="results"></a>Risultati

I moduli Execute R Script possono restituire più output, ma devono essere forniti come frame di dati R.The **Execute R Script** modules can return multiple outputs, but they must be provided as R data frames. I frame di dati vengono convertiti automaticamente in dataset nella finestra di progettazione per garantire la compatibilità con altri moduli.

I messaggi standard e gli errori di R vengono restituiti al registro del modulo.

Se è necessario stampare i risultati nello script R, è possibile trovare i risultati stampati nella **70_driver_log** sotto **Uscite:** scheda log nel pannello di destra del modulo.

## <a name="sample-scripts"></a>Script di esempio

Esistono molti modi per estendere la pipeline usando uno script R personalizzato.  In questa sezione viene fornito codice di esempio per le attività comuni.


### <a name="add-r-script-as-an-input"></a>Aggiungere uno script R come inputAdd R script as an input

Il modulo **Execute R Script** supporta file di script R arbitrari come input. A tale scopo, è necessario caricarli nell'area di lavoro come parte del file .IP.

1. Per caricare un file zip contenente codice R nell'area di lavoro, passare alla pagina **Asset dataset,** fare clic su **Crea set**di dati e quindi selezionare Da **file locale** e l'opzione Tipo di set di dati **file.**  

1. Verificare che il file compresso sia disponibile nell'elenco **Set di dati** personali nella categoria Set **di** dati nell'albero del modulo sinistro.

1.  Connettere il set di dati alla porta di input **del pacchetto di script.**

1. Tutti i file contenuti nel file zip sono disponibili durante la fase di esecuzione della pipeline. 

    Se il file di pacchetto di script contiene una struttura di directory, la struttura viene mantenuta. Tuttavia, è necessario modificare il codice per anteporre la directory **./Script Bundle** al percorso.

### <a name="process-data"></a>Elaborazione dei dati

L'esempio seguente mostra come ridimensionare e normalizzare i dati di input:The following sample shows how to scale and normalize input data:

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

### <a name="read-a-zip-file-as-input"></a>Leggere un file zip come input

In questo esempio viene illustrato come utilizzare un set di dati in un file zip come input per il modulo **Execute R Script.**

1. Creare il file di dati in formato CSV e denominarlo "mydatafile.csv".
1. Creare un file zip e aggiungere il file CSV all'archivio.
1. Caricare il file compresso nell'area di lavoro di Azure Machine Learning.Upload the zipped file to your Azure Machine Learning workspace. 
1. Connettere il set di dati risultante all'input **ScriptBundle** del modulo **Execute R Script.**
1. Utilizzo del codice seguente per leggere i dati CSV dal file compresso.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replica relè di righe

In questo esempio viene illustrato come replicare i record positivi in un set di dati per bilanciare l'esempio:This sample shows how to replicate positive records in a dataset to balance the sample:

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

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Passare oggetti R tra i moduli Execute R ScriptPass R objects between Execute R Script modules

È possibile passare gli oggetti R tra le istanze del modulo **Execute R Script** usando il meccanismo di serializzazione interno. In questo esempio si presuppone che si `A` desideri spostare l'oggetto R denominato tra due moduli **Execute R Script.**

1. Aggiungere il primo modulo Execute R Script alla pipeline e digitare il codice seguente `A` nella casella di testo R Script per creare un oggetto serializzato come colonna nella tabella dati di output del modulo:Add the first Execute R **Script** module to your pipeline, and type the following code in the R **Script** text box to create a serialized object as a column in the module's output Data Table:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    La conversione esplicita in tipo Integer viene eseguita `Raw` perché la funzione di serializzazione restituisce i dati nel formato R, che non è supportato dalla finestra di progettazione.

1. Aggiungere una seconda istanza del modulo **Execute R Script** e connetterlo alla porta di output del modulo precedente.

1. Digitare il codice seguente nella casella `A` di testo Script **R** per estrarre l'oggetto dalla tabella dati di input. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Pacchetti R preinstallati

L'elenco corrente dei pacchetti R preinstallati disponibili per l'uso:

|              |            | 
|--------------|------------| 
| Pacchetto      | Versione    | 
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
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| caret        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| classe        | 7.3-15     | 
| interfaccia della riga di comando          | 1.1.0      | 
| clipr        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
| compilatore     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| set di dati     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evaluate     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0.8-71     | 
| Fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| generics     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| graphics     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| griglia         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0,8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| labeling     | 0,3        | 
| lattice      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matrice       | 1.2-17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0,7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallelo     | 3.5.1      | 
| pillar       | 1.4.1      | 
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
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recipes      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0,1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| spaziale      | 7.3-11     | 
| splines      | 3.5.1      | 
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
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfunfun         | 0,8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 
