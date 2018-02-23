---
title: Simulazione in parallelo in R con Azure Batch
description: Esercitazione - Istruzioni dettagliate per eseguire una simulazione finanziaria Monte Carlo in Azure Batch usando il pacchetto R doAzureParallel
services: batch
author: jiata
manager: jkabat
ms.assetid: 
ms.service: batch
ms.devlang: r
ms.topic: tutorial
ms.date: 01/23/2018
ms.author: jiata
ms.custom: mvc
ms.openlocfilehash: 83b4c18959cd18d920fcb9822b933dbea9f1b936
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="tutorial-run-a-parallel-r-simulation-with-azure-batch"></a>Esercitazione: Eseguire una simulazione in parallelo in R con Azure Batch 

Eseguire i carichi di lavoro R paralleli su larga scala usando [doAzureParallel](http://www.github.com/Azure/doAzureParallel), un pacchetto R leggero che permette di usare Azure Batch direttamente dalla sessione R. Il pacchetto doAzureParallel è basato sul diffuso pacchetto R [foreach](http://cran.r-project.org/web/packages/foreach/index.html). Il pacchetto doAzureParallel invia ogni iterazione del ciclo foreach come attività di Azure Batch.

Questa esercitazione mostra come distribuire un pool di Batch ed eseguire un processo R in parallelo in Azure Batch direttamente da RStudio. Si apprenderà come:
 

> [!div class="checklist"]
> * Installare doAzureParallel e configurarlo per accedere agli account Batch e di archiviazione
> * Creare un pool di Batch come back-end parallelo per la sessione R
> * Eseguire una simulazione di esempio in parallelo nel pool

## <a name="prerequisites"></a>Prerequisiti

* Una distribuzione [R](https://www.r-project.org/) installata, ad esempio [Microsoft R Open](https://mran.microsoft.com/open). Usare R versione 3.3.1 o successive.

* [RStudio](https://www.rstudio.com/), edizione commerciale o edizione [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) open source. 

* Un account Azure Batch e un account di archiviazione di Azure. Per creare questi account, vedere le guide introduttive di Batch usando il [portale di Azure](quick-create-portal.md) o l'[interfaccia della riga di comando di Azure](quick-create-cli.md). 

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 
## <a name="install-doazureparallel"></a>Installare doAzureParallel

Nella console di RStudio installare il [pacchetto doAzureParallel di Github](http://www.github.com/Azure/doAzureParallel). I comandi seguenti scaricano e installano il pacchetto e le relative dipendenze nella sessione R corrente: 

```R
# Install the devtools package  
install.packages("devtools") 
  
# Install the doAzureParallel package 
devtools::install_github("Azure/doAzureParallel") 
 
# Load the doAzureParallel library 
library(doAzureParallel) 
```
L'installazione può richiedere alcuni minuti.

Per configurare doAzureParallel con le credenziali degli account ottenute in precedenza, generare un file di configurazione denominato *credentials.json* nella directory di lavoro: 

```R
generateCredentialsConfig("credentials.json") 
``` 

Immettere in questo file i nomi e le chiavi degli account Batch e di archiviazione. Lasciare invariata l'impostazione `githubAuthenticationToken`.

Al termine, il file delle credenziali sarà simile al seguente: 

```json
{
  "batchAccount": {
    "name": "mybatchaccount",
    "key": "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==",
    "url": "https://mybatchaccount.mybatchregion.batch.azure.com"
  },
  "storageAccount": {
    "name": "mystorageaccount",
    "key": "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=="
  },
  "githubAuthenticationToken": ""
}
```

Salvare il file. Eseguire quindi il comando seguente per impostare le credenziali per la sessione R corrente: 

```R
setCredentials("credentials.json") 
```

## <a name="create-a-batch-pool"></a>Creare un pool di Batch 

Il pacchetto doAzureParallel include una funzione per generare un pool (cluster) di Azure Batch per l'esecuzione di processi R paralleli. I nodi eseguono una [macchina virtuale di data science di Azure](../machine-learning/data-science-virtual-machine/overview.md) basata su Ubuntu. In questa immagine sono preinstallati Microsoft R Open e i pacchetti R più diffusi. È possibile visualizzare o personalizzare alcune impostazioni del cluster, come il numero e le dimensioni dei nodi. 

Per generare un file JSON di configurazione del cluster nella directory di lavoro: 
 
```R
generateClusterConfig("cluster.json")
``` 
 
Aprire il file per visualizzare la configurazione predefinita, che include tre nodi dedicati e tre nodi [per priorità bassa](batch-low-pri-vms.md). Queste impostazioni sono solo esempi che è possibile provare o modificare. I nodi dedicati sono riservati per il pool. I nodi per priorità bassa vengono offerti a un prezzo ridotto usando la capacità in eccesso delle VM in Azure. I nodi per priorità bassa non sono disponibili se Azure non ha capacità sufficiente. 

Per questa esercitazione, modificare la configurazione in questo modo:

* Aumentare `maxTasksPerNode` a *2* per trarre vantaggio da entrambi i core in ogni nodo.
* Impostare `dedicatedNodes` su *0* per provare le VM con priorità bassa per Batch. Impostare `min` per `lowPriorityNodes` su *5*. Impostare `max` su *10* oppure scegliere numeri minori se necessario. 

Mantenere i valori predefiniti per le altre impostazioni e salvare il file. Dovrebbe essere simile a quello riportato di seguito:

```json
{
  "name": "myPoolName",
  "vmSize": "Standard_D2_v2",
  "maxTasksPerNode": 4,
  "poolSize": {
    "dedicatedNodes": {
      "min": 0,
      "max": 0
    },
    "lowPriorityNodes": {
      "min": 5,
      "max": 10
    },
    "autoscaleFormula": "QUEUE"
  },
  "containerImage": "rocker/tidyverse:latest",
  "rPackages": {
    "cran": [],
    "github": [],
    "bioconductor": []
  },
  "commandLine": []
}
```

Creare ora il cluster. Batch crea immediatamente il pool, ma richiede alcuni minuti per allocare e avviare i nodi di calcolo. Dopo che il cluster è disponibile, registrarlo come back-end parallelo per la sessione R. 

```R
# Create your cluster if it does not exist; this takes a few minutes
cluster <- makeCluster("cluster.json") 
  
# Register your parallel backend 
registerDoAzureParallel(cluster) 
  
# Check that the nodes are running 
getDoParWorkers() 
```

L'output mostra il numero di "ruoli di lavoro di esecuzione" per doAzureParallel. Questo numero corrisponde al numero di nodi moltiplicato per il valore di `maxTasksPerNode`. Se la configurazione del cluster è stata modificata come descritto sopra, il numero sarà *10*. 
 
## <a name="run-a-parallel-simulation"></a>Eseguire una simulazione in parallelo

Dopo aver creato il cluster, è possibile eseguire il ciclo foreach con il back-end parallelo registrato (pool di Azure Batch). Come esempio, eseguire una simulazione finanziaria Monte Carlo, prima in locale usando un ciclo foreach standard, quindi eseguendo foreach con Batch. Questo esempio è una versione semplificata della previsione del prezzo di un titolo azionario simulando un numero elevato di risultati diversi dopo cinque anni.

Si supponga che il titolo Contoso Corporation guadagni ogni giorno in media 1001 volte il prezzo di apertura, ma che abbia una volatilità (deviazione standard) pari a 0,01. Considerando un prezzo di partenza di 100 dollari, usare una simulazione di determinazione dei prezzi Monte Carlo per prevedere il prezzo del titolo Contoso dopo cinque anni.

Parametri per la simulazione Monte Carlo:

```R
mean_change = 1.001 
volatility = 0.01 
opening_price = 100 
```

Per simulare i prezzi di chiusura, definire la funzione seguente:

```R
getClosingPrice <- function() { 
  days <- 1825 # ~ 5 years 
  movement <- rnorm(days, mean=mean_change, sd=volatility) 
  path <- cumprod(c(opening_price, movement)) 
  closingPrice <- path[days] 
  return(closingPrice) 
} 
```

Eseguire prima di tutto 10.000 simulazioni in locale usando un ciclo foreach standard con la parola chiave `%do%`:

```R
start_s <- Sys.time() 
# Run 10,000 simulations in series 
closingPrices_s <- foreach(i = 1:10, .combine='c') %do% { 
  replicate(1000, getClosingPrice()) 
} 
end_s <- Sys.time() 
```


Tracciare un istogramma dei prezzi di chiusura per mostrare la distribuzione dei risultati:

```R
hist(closingPrices_s)
``` 

L'output è simile al seguente:

![Distribuzione dei prezzi di chiusura](media/tutorial-r-doazureparallel/closing-prices-local.png)
  
Una simulazione locale viene completata in pochi secondi o anche meno:

```R
difftime(end_s, start_s) 
```

Il tempo di esecuzione stimato per 10 milioni di risultati in locale, usando un'approssimazione lineare, è circa 30 minuti:

```R 
1000 * difftime(end_s, start_s, unit = "min") 
```


Eseguire ora il codice usando `foreach` con la parola chiave `%dopar%` per confrontare il tempo necessario per eseguire 10 milioni di simulazioni in Azure. Per parallelizzare la simulazione con Batch, eseguire 100 iterazioni di 100.000 simulazioni:

```R
# Optimize runtime. Chunking allows running multiple iterations on a single R instance.
opt <- list(chunkSize = 10) 
start_p <- Sys.time()  
closingPrices_p <- foreach(i = 1:100, .combine='c', .options.azure = opt) %dopar% { 
  replicate(100000, getClosingPrice()) 
} 
end_p <- Sys.time() 
```

La simulazione distribuisce le attività nei nodi nel pool di Batch. È possibile visualizzare l'attività nella mappa di calore per il pool nel portale di Azure. Passare a **Account Batch** > *AccountBatch*. Fare clic su **Pool** > *NomePool*. 

![Mappa di calore del pool che esegue attività R parallele](media/tutorial-r-doazureparallel/pool.png)

La simulazione viene completata dopo alcuni minuti. Il pacchetto unisce automaticamente i risultati e ne esegue il pool dai nodi. È quindi possibile usare i risultati nella sessione R. 

```R
hist(closingPrices_p) 
```

L'output è simile al seguente:

![Distribuzione dei prezzi di chiusura](media/tutorial-r-doazureparallel/closing-prices.png)

Quanto è durata la simulazione in parallelo? 

```R
difftime(end_p, start_p, unit = "min")  
```

Si osserverà che l'esecuzione della simulazione nel pool di Batch offre un miglioramento significativo delle prestazioni rispetto al tempo previsto per l'esecuzione della simulazione in locale. 

## <a name="clean-up-resources"></a>Pulire le risorse

Il processo viene eliminato automaticamente dopo il suo completamento. Quando il cluster non è più necessario, è possibile eliminarlo chiamando la funzione `stopCluster` nel pacchetto doAzureParallel:

```R
stopCluster(cluster)
```

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso a:

> [!div class="checklist"]
Installare doAzureParallel e configurarlo per accedere agli account Batch e di archiviazione
> * Creare un pool di Batch come back-end parallelo per la sessione R
> * Eseguire una simulazione di esempio in parallelo nel pool


Per altre informazioni su doAzureParallel, vedere la documentazione e gli esempi in GitHub.

> [!div class="nextstepaction"]
> [Pacchetto doAzureParallel](https://github.com/Azure/doAzureParallel/)




