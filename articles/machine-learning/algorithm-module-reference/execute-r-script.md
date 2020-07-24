---
title: 'Esegui script R: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Execute R script in Azure Machine Learning per eseguire il codice R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/27/2020
ms.openlocfilehash: 3559ae5c246129aa369cb49e7749e499002f1dc6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87048183"
---
# <a name="execute-r-script-module"></a>Modulo Execute R script

Questo articolo descrive come usare il modulo Execute R script per eseguire il codice R nella pipeline di Azure Machine Learning Designer (anteprima).

Con R è possibile eseguire attività che attualmente non supportano i moduli esistenti, ad esempio: 
- Creare trasformazioni dati personalizzate
- Usare metriche personalizzate per valutare le stime
- Compilare modelli usando algoritmi che non sono implementati come moduli autonomi nella finestra di progettazione

## <a name="r-version-support"></a>Supporto della versione di R

Azure Machine Learning Designer usa la distribuzione CRAN (complete R Archive Network) di R. La versione attualmente utilizzata è CRAN 3.5.1.

## <a name="supported-r-packages"></a>Pacchetti R supportati

L'ambiente R è preinstallato con più di 100 pacchetti. Per un elenco completo, vedere la sezione [pacchetti R preinstallati](#preinstalled-r-packages).

È anche possibile aggiungere il codice seguente a qualsiasi modulo Execute R script per visualizzare i pacchetti installati.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
> [!NOTE]
> Se la pipeline contiene più moduli Execute R script che richiedono pacchetti non inclusi nell'elenco preinstallato, installare i pacchetti in ogni modulo. 

## <a name="installing-r-packages"></a>Installazione di pacchetti R
Per installare altri pacchetti R, usare il `install.packages()` metodo. I pacchetti vengono installati per ogni modulo Execute R script. Non sono condivisi tra altri moduli Execute R script.

> [!NOTE]
> Specificare il repository CRAN quando si installano i pacchetti, ad esempio `install.packages("zoo",repos = "http://cran.us.r-project.org")` .

Questo esempio illustra come installare Zoo:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
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
 > Prima di installare un pacchetto, controllare se esiste già, in modo che non si ripeta un'installazione. Le installazioni ripetute potrebbero causare il timeout delle richieste del servizio Web.     

## <a name="uploading-files"></a>Caricamento di file
Il modulo Execute R script supporta il caricamento di file tramite il Azure Machine Learning R SDK.

L'esempio seguente illustra come caricare un file di immagine in Execute R script:
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
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

Al termine dell'esecuzione della pipeline, è possibile visualizzare l'anteprima dell'immagine nel riquadro di destra del modulo.

> [!div class="mx-imgBorder"]
> ![Anteprima dell'immagine caricata](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Come configurare Execute R script

Il modulo Execute R script contiene codice di esempio che è possibile usare come punto di partenza. Per configurare il modulo Execute R script, fornire un set di input e codice da eseguire.

![Diagramma degli input per un modulo R](media/module/execute-r-script.png)

I set di dati archiviati nella finestra di progettazione vengono convertiti automaticamente in un frame di dati R quando vengono caricati con questo modulo.

1.  Aggiungere il modulo **Execute R script** alla pipeline.  

1. Connettere gli input necessari per lo script. Gli input sono facoltativi e possono includere dati e codice R aggiuntivo.

    * **DataSet1**: fa riferimento al primo input come `dataframe1` . Il set di dati di input deve essere formattato come file CSV, TSV o ARFF. In alternativa, è possibile connettere un set di dati Azure Machine Learning.

    * **Dataset2**: fa riferimento al secondo input come `dataframe2` . Questo set di dati deve anche essere formattato come file CSV, TSV o ARFF o come set di dati Azure Machine Learning.

    * **Bundle di script**: il terzo input accetta i file con estensione zip. Un file compresso può contenere più file e più tipi di file.

1. Nella casella di testo **script r** Digitare o incollare uno script r valido.

    > [!NOTE]
    > Prestare attenzione durante la scrittura dello script. Verificare che non siano presenti errori di sintassi, ad esempio l'uso di variabili non dichiarate o di moduli o funzioni non importati. Prestare particolare attenzione all'elenco dei pacchetti preinstallati alla fine di questo articolo. Per usare i pacchetti non elencati, installarli nello script. Un esempio è `install.packages("zoo",repos = "http://cran.us.r-project.org")`.
    
    Per iniziare, la casella di testo **script R** viene prepopolata con il codice di esempio, che è possibile modificare o sostituire.
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main,
    # which is the entry point for this module.

    # Note that functions dependent on the X11 library,
    # such as "View," are not supported because the X11 library
    # is not preinstalled.
    
    # The entry point function MUST have two input arguments.
    # If the input port is not connected, the corresponding
    # dataframe argument will be null.
    #   Param<dataframe1>: a R DataFrame
    #   Param<dataframe2>: a R DataFrame
    azureml_main <- function(dataframe1, dataframe2){
    print("R script run.")

    # If a .zip file is connected to the third input port, it's
    # unzipped under "./Script Bundle". This directory is added
    # to sys.path.

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

    La funzione del punto di ingresso deve avere gli argomenti di input `Param<dataframe1>` e `Param<dataframe2>` , anche quando questi argomenti non vengono usati nella funzione.

    > [!NOTE]
    > Ai dati passati al modulo Execute R script viene fatto riferimento come `dataframe1` e `dataframe2` , che è diverso da Azure Machine Learning Designer (il riferimento alla finestra di progettazione come `dataset1` `dataset2` ). Verificare che nello script venga fatto riferimento correttamente ai dati di input.  
 
    > [!NOTE]
    > Il codice R esistente potrebbe richiedere modifiche minime per l'esecuzione in una pipeline di progettazione. Ad esempio, i dati di input forniti in formato CSV devono essere convertiti in modo esplicito in un set di dati prima di poterli usare nel codice. I tipi di dati e di colonna usati nel linguaggio R variano anche in qualche modo dai tipi di dati e di colonna usati nella finestra di progettazione.

1.  Per il valore di **inizializzazione casuale**, immettere un valore da usare all'interno dell'ambiente R come valore di inizializzazione casuale. Questo parametro è equivalente alla chiamata di `set.seed(value)` nel codice R.  

1. Inviare la pipeline.  

## <a name="results"></a>Risultati

I moduli Execute R script possono restituire più output, ma devono essere specificati come frame di dati R. I frame di dati vengono convertiti automaticamente in set di dati nella finestra di progettazione per la compatibilità con altri moduli.

I messaggi e gli errori standard di R vengono restituiti al log del modulo.

Se è necessario stampare i risultati nello script R, è possibile trovare i risultati stampati in **70_driver_log** nella scheda **output + log** nel riquadro di destra del modulo.

## <a name="sample-scripts"></a>Script di esempio

Esistono diversi modi per estendere la pipeline usando script R personalizzati. In questa sezione viene fornito il codice di esempio per le attività comuni.


### <a name="add-an-r-script-as-an-input"></a>Aggiungere uno script R come input

Il modulo Execute R script supporta i file di script R arbitrari come input. Per usarli, è necessario caricarli nell'area di lavoro come parte del file zip.

1. Per caricare un file con estensione zip che contiene codice R nell'area di lavoro, passare alla pagina **set** di risorse. Selezionare **Crea set di dati**, quindi selezionare **da file locale** e l'opzione tipo di set di dati **file** .  

1. Verificare che il file compresso sia disponibile nell'elenco **set di impostazioni personali** sotto la categoria **set** di elementi nell'albero del modulo sinistro.

1.  Connettere il set di dati alla porta di input del **bundle di script** .

1. Tutti i file nel file zip sono disponibili durante la fase di esecuzione della pipeline. 

    Se il file del bundle di script contiene una struttura di directory, la struttura viene mantenuta. È tuttavia necessario modificare il codice per anteporre il **bundle directory./script** al percorso.

### <a name="process-data"></a>Elaborazione dei dati

Nell'esempio seguente viene illustrato come ridimensionare e normalizzare i dati di input:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a .zip file is connected to the third input port, it's
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # Find the maximum and minimum values of the width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # Calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # Apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Leggere un file con estensione zip come input

Questo esempio illustra come usare un set di dati in un file con estensione zip come input per il modulo Execute R script.

1. Creare il file di dati in formato CSV e denominarlo **mydatafile.csv**.
1. Creare un file con estensione zip e aggiungere il file CSV all'archivio.
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

È possibile passare gli oggetti R tra le istanze del modulo Execute R Script usando il meccanismo di serializzazione interno. Questo esempio presuppone che si voglia spostare l'oggetto R denominato `A` tra due moduli Execute R script.

1. Aggiungere il primo modulo **Execute R script** alla pipeline. Immettere quindi il codice seguente nella casella di testo **script R** per creare un oggetto serializzato `A` come colonna nella tabella dati di output del modulo:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    La conversione esplicita in un tipo Integer viene eseguita perché la funzione di serializzazione restituisce i dati nel `Raw` formato R, che non è supportato dalla finestra di progettazione.

1. Aggiungere una seconda istanza del modulo **Execute R script** e connetterla alla porta di output del modulo precedente.

1. Digitare il codice seguente nella casella di testo **script R** per estrarre l'oggetto `A` dalla tabella dati di input. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="preinstalled-r-packages"></a>Pacchetti R preinstallati

Sono attualmente disponibili i pacchetti R preinstallati seguenti:

| Pacchetto      | Versione    | 
|--------------|------------| 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| boot         | 1,3-22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| accento circonflesso        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| classe        | 7.3-15     | 
| cli          | 1.1.0      | 
| clipr        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
| compiler     | 3.5.1      | 
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
| FS           | 1.3.1      | 
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
| gtools       | 3.8.1 indica      | 
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
| labeling     | 0.3        | 
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
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
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
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| spatial      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
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
| tools        | 3.5.1      | 
| tseries      | 0,10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
