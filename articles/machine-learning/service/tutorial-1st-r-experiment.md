---
title: 'Esercitazione: Eseguire il training del primo modello di Machine Learning con R'
titleSuffix: Azure Machine Learning
description: Questa esercitazione illustra i modelli di progettazione di base in Azure Machine Learning e si eseguirà il training di un modello di regressione logistica usando i pacchetti R azuremlsdk e caret per stimare la probabilità di un incidente d'auto mortale.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 11/04/2019
ms.openlocfilehash: 62c9ac0020db92c1540d0ecb4fa996d9b8405a58
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974258"
---
# <a name="tutorial-train-and-deploy-your-first-model-in-r-with-azure-machine-learning"></a>Esercitazione: Eseguire il training e distribuire il primo modello in R con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questa esercitazione illustra i modelli di progettazione di base in Azure Machine Learning.  Si eseguirà il training e la distribuzione di un modello **caret** per stimare la probabilità di un incidente d'auto mortale. Al termine dell'esercitazione, si avrà la conoscenza pratica dell'SDK R necessaria per passare allo sviluppo di esperimenti e flussi di lavoro più complessi.

In questa esercitazione si apprenderanno informazioni sulle attività seguenti:

> [!div class="checklist"]
> * Connettersi all'area di lavoro
> * Caricare i dati e preparare il training
> * Caricare i dati nell'archivio dati in modo che siano disponibili per il training remoto
> * Creare una risorsa di calcolo
> * Eseguire il training di un modello caret per stimare la probabilità di incidente mortale
> * Distribuire un endpoint di stima
> * Testare il modello da R

Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Prerequisiti

1. Seguire le [istruzioni di installazione](https://azure.github.io/azureml-sdk-for-r/articles/installation.html) per:
    + Installare Anaconda
    + Installare `azuremlsdk`
    + Installare Azure Machine Learning SDK per Python

1. Scaricare i tre file di esercitazione da [GitHub](https://github.com/Azure/azureml-sdk-for-r/tree/master/vignettes/train-and-deploy-with-caret).  Salvarli in una directory **esercitazioni**.

2. Creare un'area di lavoro di Azure Machine Learning e scaricare il file di configurazione usando la procedura seguente.


### <a name="create-a-workspace"></a>Creare un'area di lavoro

Un'area di lavoro di Machine Learning è una risorsa cloud fondamentale usata per eseguire gli esperimenti, il training e la distribuzione di modelli di Machine Learning. Collega la sottoscrizione e il gruppo di risorse di Azure a un oggetto di facile utilizzo nell'SDK. Se si dispone già di un'area di lavoro di Azure Machine Learning, passare alla [sezione successiva](#config). Altrimenti crearne una adesso.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="config"></a> Scaricare il file config.json

1. Nella parte superiore della pagina dell'area di lavoro selezionare **Scarica config.json**.

    ![Scaricare il file config.json](media/tutorial-1st-r-experiment/download-config.png)  

1. Aggiungere questo file alla directory **esercitazioni**.

A questo punto è possibile eseguire l'esercitazione.

Per eseguire questa esercitazione, è consigliabile usare RStudio. Su RStudio scegliere File > Nuovo progetto > Directory esistente e selezionare la directory **esercitazioni** creata in precedenza.

> [!Important]
> Il resto di questo articolo contiene lo stesso contenuto visualizzato nel file **train-and-deploy-to-aci.Rmd**.  
> Se si ha familiarità con RMarkdown, è possibile usare il codice di tale file.  In alternativa, è possibile copiare e incollare i frammenti di codice da tale posizione o da questo articolo in uno script R o nella riga di comando.


## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo
La configurazione per il lavoro di sviluppo di questa esercitazione include le azioni seguenti:

* Installare i pacchetti necessari
* Connettersi a un'area di lavoro per consentire al computer locale di comunicare con risorse remote
* Creare un esperimento per tenere traccia delle esecuzioni
* Creare una destinazione di calcolo remota da usare per il training

### <a name="install-required-packages"></a>Installare i pacchetti necessari
Per questa esercitazione è necessario avere installato Azure ML SDK. Proseguire e importare il pacchetto **azuremlsdk**.

```R
library(azuremlsdk)
```

L'esercitazione usa i dati del [pacchetto **DAAG**](https://cran.r-project.org/package=DAAG). Installare il pacchetto, se necessario.

```R
install.packages("DAAG")
```

Gli script di training e assegnazione dei punteggi (`accidents.R` e `accident_predict.R`) presentano alcune dipendenze aggiuntive. Se si prevede di eseguire tali script in locale, assicurarsi di disporre anche dei pacchetti necessari.

### <a name="load-your-workspace"></a>Caricare l'area di lavoro
Creare un'istanza di oggetto area di lavoro dall'area di lavoro esistente. Il codice seguente caricherà i dettagli dell'area di lavoro dal file **config.json**. È anche possibile recuperare un'area di lavoro usando [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html).

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Creare un esperimento
Un esperimento di Azure ML tiene traccia di un raggruppamento di esecuzioni, in genere dello stesso script di training. Creare un esperimento per tenere traccia delle esecuzioni per il training del modello caret sui dati degli incidenti.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>Creare una destinazione di calcolo
Con l'ambiente di calcolo di Azure Machine Learning (AmlCompute), un servizio gestito, i data scientist possono eseguire il training di modelli di Machine Learning in cluster di macchine virtuali di Azure, ad esempio le macchine virtuali con supporto per GPU. In questa esercitazione verrà creato un cluster AmlCompute a nodo singolo come ambiente di training. Il codice seguente crea automaticamente il cluster di elaborazione se non esiste già nell'area di lavoro.

Potrebbe essere necessario attendere alcuni minuti per il provisioning del cluster di elaborazione, se non esiste già.

```R
cluster_name <- "rcluster"
compute_target <- get_compute(ws, cluster_name = cluster_name)
if (is.null(compute_target)) {
  vm_size <- "STANDARD_D2_V2" 
  compute_target <- create_aml_compute(workspace = ws,
                                       cluster_name = cluster_name,
                                       vm_size = vm_size,
                                       max_nodes = 1)
}

wait_for_provisioning_completion(compute_target)
```

## <a name="prepare-data-for-training"></a>Preparare i dati per il training
Questa esercitazione usa i dati del pacchetto **DAAG**. Questo set di dati include i dati provenienti da oltre 25.000 incidenti d'auto negli Stati Uniti, con variabili utili per stimare la probabilità di un incidente mortale. Prima di tutto, importare i dati in R e trasformarli in un nuovo dataframe `accidents` per l'analisi, quindi esportarli in un file `Rdata`.

```R
library(DAAG)
data(nassCDS)

accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>Caricare i dati nell'archivio dati
Caricare i dati nel cloud per renderli accessibili dall'ambiente di training remoto. Ogni area di lavoro di Azure Machine Learning include un archivio dati predefinito contenente le informazioni di connessione per il contenitore BLOB di Azure di cui viene effettuato il provisioning nell'account di archiviazione collegato all'area di lavoro. Il codice seguente consente di caricare i dati sugli incidenti creati in precedenza nell'archivio dati.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>Eseguire il training di un modello

Per questa esercitazione, adattare un modello di regressione logistica ai dati caricati usando il cluster di elaborazione remoto. Per inviare un processo, è necessario:

* Preparare lo script di training
* Creare un oggetto di stima
* Inviare il processo

### <a name="prepare-the-training-script"></a>Preparare lo script di training
Uno script di training denominato `accidents.R` è incluso nella stessa directory dell'esercitazione. Notare i dettagli seguenti **all'interno dello script di training** che permettono di sfruttare il servizio Azure Machine Learning per il training:

* Lo script di training usa un argomento `-d` per trovare la directory che contiene i dati di training. Quando si definisce e si invia il processo in un secondo momento, si fa riferimento all'archivio dati per questo argomento. Azure ML monta la cartella di archiviazione nel cluster remoto per il processo di training.
* Lo script di training registra l'accuratezza finale come metrica per il record esecuzione in Azure ML usando `log_metric_to_run()`. Azure ML SDK offre un set di API di registrazione per registrare diverse metriche durante le esecuzioni di training. Queste metriche vengono registrate e salvate in modo persistente nel record esecuzione dell'esperimento. È possibile accedere alle metriche in qualsiasi momento e visualizzarle nella pagina dei dettagli dell'esecuzione nello [studio](https://ml.azure.com). Per il set completo di metodi di registrazione `log_*()`, vedere il [riferimento](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation).
* Lo script di training salva il modello in una directory denominata **outputs**. La cartella `./outputs` riceve un trattamento speciale da Azure ML. Durante il training, i file scritti in `./outputs` vengono caricati automaticamente nel record esecuzione da Azure ML e salvati in modo persistente come artefatti. Salvando il modello sottoposto a training in `./outputs`, sarà possibile accedere e recuperare il file del modello anche dopo l'esecuzione e non sarà più possibile accedere all'ambiente di training remoto.

### <a name="create-an-estimator"></a>Creare un oggetto di stima

Un oggetto di stima di Azure ML incapsula le informazioni configurazione di esecuzione necessarie per l'esecuzione di uno script di training nella destinazione di calcolo. Le esecuzioni di Azure ML sono eseguite come processi in contenitori nella destinazione di calcolo specificate. Per impostazione predefinita, l'immagine Docker compilata per il processo di training includerà R, Azure ML SDK e un set di pacchetti R usati di frequente. Vedere l'elenco completo dei pacchetti predefiniti inclusi qui.

Per creare l'oggetto di stima, definire quanto segue:

* La directory contenente gli script necessari per il training (`source_directory`). Tutti i file in questa directory vengono caricati nel nodo o nei nodi del cluster per l'esecuzione. La directory deve contenere lo script di training e gli eventuali script aggiuntivi necessari.
* Lo script di training che sarà eseguito (`entry_script`).
* La destinazione di calcolo (`compute_target`), in questo caso il cluster AmlCompute creato in precedenza.
* I parametri richiesti dallo script di training (`script_params`). Azure ML eseguirà lo script di training come script della riga di comando con `Rscript`. In questa esercitazione si specifica un argomento per lo script, il punto di montaggio della directory dei dati, al quale è possibile accedere con `ds$path(target_path)`.
* Eventuali dipendenze di ambiente necessarie per il training. L'immagine Docker predefinita compilata per il training contiene già i tre pacchetti (`caret`, `e1071` e `optparse`) necessari nello script di training.  Non è quindi necessario specificare altre informazioni. Se si usano pacchetti R che non sono inclusi per impostazione predefinita, usare il parametro `cran_packages` dell'oggetto di stima per aggiungere altri pacchetti CRAN. Per il set completo di opzioni configurabili, vedere il riferimento [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html).

```R
est <- estimator(source_directory = ".",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>Inviare il processo nel cluster remoto

Infine, inviare il processo da eseguire nel cluster. `submit_experiment()` restituisce un oggetto Run da usare per interfacciarsi con l'esecuzione. In totale, la prima esecuzione richiede **circa 10 minuti**. Tuttavia, per le esecuzioni successive viene riusata la stessa immagine Docker, purché le dipendenze dello script rimangano invariate.  In questo caso, l'immagine viene memorizzata nella cache e il tempo di avvio del contenitore è molto più breve.

```R
run <- submit_experiment(exp, est)
```

È possibile visualizzare i dettagli dell'esecuzione in RStudio Viewer. Facendo clic sul collegamento "Visualizzazione Web" si visualizza Azure Machine Learning Studio, dove è possibile monitorare l'esecuzione nell'interfaccia utente.

```R
view_run_details(run)
```

Il training del modello avviene in background. Attendere il completamento del training del modello prima di eseguire altro codice.

```R
wait_for_run_completion(run, show_output = TRUE)
```

Gli utenti che hanno accesso all'area di lavoro possono inviare più esperimenti in parallelo: Azure ML eseguirà la pianificazione delle attività nel cluster di elaborazione. È anche possibile configurare il cluster per passare automaticamente a più nodi e ridimensionarsi quando non sono presenti altre attività di calcolo nella coda. Questa configurazione è una soluzione conveniente per consentire ai team di condividere le risorse di calcolo.

## <a name="retrieve-training-results"></a>Recuperare i risultati del training
Dopo il completamento del training del modello, è possibile accedere agli artefatti del processo salvati in modo persistente nel record esecuzione, tra cui le metriche registrate e il modello finale sottoposto a training.

### <a name="get-the-logged-metrics"></a>Ottenere le metriche registrate
Nello script di training `accidents.R` è stata registrata una metrica del modello: l'accuratezza delle stime nei dati di training. È possibile visualizzare le metriche in [Studio](https://ml.azure.com) o estrarle nella sessione locale sotto forma di elenco R come indicato di seguito:

```R
metrics <- get_run_metrics(run)
metrics
```

Se sono stati eseguiti più esperimenti (ad esempio usando variabili, algoritmi o iperparametri differenti) è possibile usare le metriche di ogni esecuzione per confrontare i modelli e scegliere quello da usare nell'ambiente di produzione.

### <a name="get-the-trained-model"></a>Ottenere il modello sottoposto a training
È possibile recuperare il modello sottoposto a training ed esaminare i risultati nella sessione di R locale. Il codice seguente consente di scaricare il contenuto della directory `./outputs` contenente il file del modello.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

Si notano alcuni fattori che contribuiscono a un aumento nella stima della probabilità di morte:

* maggiore velocità di impatto 
* conducente di sesso maschile
* passeggero più anziano
* passeggero

Si nota una minore probabilità di morte con i fattori seguenti:

* presenza di airbag
* presenza di cinture di sicurezza
* collisione frontale 

L'anno di produzione del veicolo non influisce in modo significativo.

È possibile usare questo modello per eseguire nuove stime:

```R
newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=16,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

## predicted probability of death for these variables, as a percentage
as.numeric(predict(accident_model,newdata, type="response")*100)
```

## <a name="deploy-as-a-web-service"></a>Distribuire come servizio Web

Con il modello è possibile stimare il rischio di morte in seguito a una collisione. Usare Azure ML per distribuire il modello come servizio di stima. In questa esercitazione si distribuirà il servizio Web in [Istanze di Azure Container](https://docs.microsoft.com/azure/container-instances/) (ACI).

### <a name="register-the-model"></a>Registrare il modello

Per prima cosa, registrare il modello scaricato nell'area di lavoro con [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html). Un modello registrato può essere qualsiasi raccolta di file, ma in questo caso l'oggetto modello R è sufficiente. Azure ML userà il modello registrato per la distribuzione.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>Definire le dipendenze di inferenza
Per creare un servizio Web per il modello, è prima di tutto necessario creare uno script di assegnazione dei punteggi (`entry_script`), uno script R che accetterà i valori delle variabili come input (in formato JSON) e restituirà una stima derivata dal modello. Per questa esercitazione, usare il file di punteggio `accident_predict.R` disponibile. Lo script di assegnazione dei punteggi deve contenere un metodo `init()` che carica il modello e restituisce una funzione che usa il modello per eseguire una stima basata sui dati di input. Per altri dettagli, vedere la [documentazione](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details).

Definire quindi un **ambiente** di Azure ML per le dipendenze del pacchetto dello script. Con un ambiente, è necessario specificare i pacchetti R (da CRAN o altrove) necessari per l'esecuzione dello script. È anche possibile specificare i valori delle variabili di ambiente a cui lo script può fare riferimento per modificarne il comportamento. Per impostazione predefinita, Azure ML compilerà la stessa immagine Docker predefinita usata con l'oggetto di stima per il training. Siccome l'esercitazione non prevede requisiti particolari, creare un ambiente senza attributi speciali.

```R
r_env <- r_environment(name = "basic_env")
```

Se invece si vuole usare un'immagine Docker personalizzata per la distribuzione, specificare il parametro `custom_docker_image`. Per il set completo di opzioni configurabili per la definizione di un ambiente, vedere il riferimento [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html).

A questo punto si dispone di tutto il necessario per creare un elemento **inference config** per incapsulare le dipendenze dell'ambiente e dello script di punteggio.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>Distribuire in ACI
In questa esercitazione si distribuirà il servizio in ACI. Questo codice esegue il provisioning di un singolo contenitore per rispondere alle richieste in ingresso, il che è adatto per test e carichi leggeri. Per altre opzioni configurabili, vedere [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html). Per le distribuzioni a livello di produzione, è anche possibile [distribuire nel servizio Azure Kubernetes](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks/deploy-to-aks.html).

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

A questo punto è possibile distribuire il modello come servizio Web. La distribuzione **può richiedere alcuni minuti**. 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>Testare il servizio distribuito

Ora che il modello è distribuito come servizio, è possibile testare il servizio da R usando [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html).  Specificare un nuovo set di dati da stimare, convertirlo in JSON e inviarlo al servizio.

```R
library(jsonlite)

newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=22,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

prob <- invoke_webservice(aci_service, toJSON(newdata))
prob
```

È anche possibile recuperare l'endpoint HTTP del servizio Web di assegnazione dei punteggi, che accetta le chiamate del client REST. È possibile condividere questo endpoint con tutti coloro che desiderano testare il servizio Web oppure integrarlo in un'applicazione.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>Pulire le risorse

Eliminare le risorse quando non sono più necessarie. Non eliminare le risorse che si pensa di usare di nuovo. 

Eliminare il servizio Web:
```R
delete_webservice(aci_service)
```

Eliminare il modello registrato:
```R
delete_model(model)
```

Eliminare il cluster di elaborazione:
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>Eliminare tutto

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

È anche possibile mantenere il gruppo di risorse ma eliminare una singola area di lavoro. Visualizzare le proprietà dell'area di lavoro e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato completato il primo esperimento di Azure Machine Learning in R, vedere altre informazioni su [Azure Machine Learning SDK per R](https://azure.github.io/azureml-sdk-for-r/index.html).

