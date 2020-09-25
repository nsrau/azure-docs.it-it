---
title: Attivare eventi in flussi di lavoro ML (anteprima)
titleSuffix: Azure Machine Learning
description: Configurare applicazioni, processi o flussi di lavoro di Machine Learning CI/CD guidati dagli eventi in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 7b1030c816bff5b50c0c47a16fa5f1812bb16b15
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250828"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Attivazione di applicazioni, processi o flussi di lavoro CI/CD basati su eventi di Azure Machine Learning (anteprima)

Questo articolo illustra come configurare applicazioni, processi o flussi di lavoro CI/CD basati su eventi di Azure Machine Learning, ad esempio messaggi di posta elettronica di notifica di errore o esecuzioni di pipeline ML, quando vengono rilevate determinate condizioni da [Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/).

Azure Machine Learning gestisce l'intero ciclo di vita del processo di apprendimento automatico, tra cui il training del modello, la distribuzione del modello e il monitoraggio. È possibile usare Griglia di eventi per rispondere a eventi di Azure Machine Learning come il completamento delle esecuzioni di training, la registrazione e distribuzione dei modelli e il rilevamento della deriva dei dati, usando architetture serverless moderne. È quindi possibile sottoscrivere e usare eventi quali modifica dello stato di esecuzione, completamento dell'esecuzione, registrazione del modello, distribuzione del modello e rilevamento della deriva dei dati in un'area di lavoro.

Quando usare Griglia di eventi per le azioni guidate dagli eventi:
* Invio di messaggi di posta elettronica in caso di errore e di completamento dell'esecuzione
* Utilizzo di una funzione di Azure dopo la registrazione di un modello
* Streaming di eventi da Azure Machine Learning a diversi endpoint
* Attivazione di una pipeline ML quando viene rilevata la deriva dei dati

> [!NOTE] 
> Attualmente, gli eventi runStatusChanged vengono attivati solo quando lo stato dell'esecuzione è **non riuscito**

## <a name="prerequisites"></a>Prerequisiti
Per usare Griglia di eventi è necessario accedere come collaboratore o proprietario all'area di lavoro di Azure Machine Learning per cui si intendono creare eventi.

## <a name="the-event-model--types"></a>Modello e tipi di eventi

Griglia di eventi di Azure legge gli eventi dalle origini, ad esempio Azure Machine Learning e altri servizi di Azure. Questi eventi vengono quindi inviati a gestori degli eventi come Hub eventi di Azure, Funzioni di Azure, App per la logica e altri. Il diagramma seguente illustra in che modo Griglia di eventi collega origini e gestori, ma non fornisce un elenco completo delle integrazioni supportate.

![Modello funzionale di Griglia di eventi di Azure](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Per altre informazioni sulle origini degli eventi e sui gestori degli eventi, vedere [Informazioni su Griglia di eventi](/azure/event-grid/overview).

### <a name="event-types-for-azure-machine-learning"></a>Tipi di eventi per Azure Machine Learning

Azure Machine Learning offre eventi in vari punti del ciclo di vita dell'apprendimento automatico: 

| Tipo di evento | Descrizione |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Generato quando viene completata l'esecuzione di un esperimento di Machine Learning |
| `Microsoft.MachineLearningServices.ModelRegistered` | Generato quando un modello di Machine Learning viene registrato nell'area di lavoro |
| `Microsoft.MachineLearningServices.ModelDeployed` | Generato quando viene completata la distribuzione di un servizio di inferenza con uno o più modelli |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Generato quando viene completato un processo di rilevamento della deriva dei dati per due set di dati |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Generato quando lo stato di un'esecuzione viene modificato, attualmente generato solo quando lo stato di un'esecuzione è 'non riuscito' |

### <a name="filter--subscribe-to-events"></a>Filtrare e sottoscrivere eventi

Questi eventi vengono pubblicati tramite Griglia di eventi di Azure. Usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure, i clienti possono facilmente eseguire la sottoscrizione agli eventi [specificando uno o più tipi di eventi e filtrando le condizioni](/azure/event-grid/event-filtering). 

Quando si configurano gli eventi, è possibile applicare filtri per far sì che l'attivazione avvenga solo in base a dati degli eventi specifici. Nell'esempio seguente, per gli eventi il cui stato di esecuzione è modificato, è possibile filtrare per tipo di esecuzione. L'evento viene attivato solo quando i criteri vengono soddisfatti. Per informazioni sui dati degli eventi in base ai quali è possibile applicare filtri, fare riferimento a [Schema di Griglia di eventi di Azure Machine Learning](/azure/event-grid/event-schema-machine-learning). 

Le sottoscrizioni per gli eventi Azure Machine Learning sono protette dal controllo degli accessi in base al ruolo. Solo chi ha il ruolo di [collaboratore o proprietario](how-to-assign-roles.md#default-roles) di un'area di lavoro può creare, aggiornare ed eliminare le sottoscrizioni agli eventi.  È possibile applicare filtri alle sottoscrizioni di eventi durante la [creazione](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest&preserve-view=true) della sottoscrizione stessa o in un secondo momento. 


1. Accedere al portale di Azure, selezionare una nuova sottoscrizione o una sottoscrizione esistente. 

1. Selezionare la scheda filtri e scorrere verso il basso fino a Filtri avanzati. Per **Chiave** e **Valore**, specificare i tipi di proprietà in base ai quali si desidera filtrare. Qui è possibile vedere che l'evento viene attivato solo quando il tipo di esecuzione è un'esecuzione di pipeline o un'esecuzione passaggio della pipeline.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="filtrare gli eventi":::


+ **Filtrare in base al tipo di evento:** Una sottoscrizione di eventi può specificare uno o più tipi di eventi di Azure Machine Learning.

+ **Filtrare in base all'oggetto dell'evento:** Griglia di eventi supporta i filtri di oggetto che si basano sulle corrispondenze con __inizia con__ e __termina con__. In questo modo gli eventi con un oggetto corrispondente vengono recapitati al sottoscrittore. Diversi eventi di Machine Learning presentano diversi formati dell'oggetto.

  | Tipo di evento | Formato dell'oggetto | Oggetto di esempio |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **Filtro avanzato**: Griglia di eventi di Azure supporta anche i filtri avanzati basati sullo schema dell'evento pubblicato. Per i dettagli sullo schema degli eventi di Azure Machine Learning, vedere [Schema degli eventi di Griglia di eventi di Azure per Azure Machine Learning](../event-grid/event-schema-machine-learning.md).  Alcuni esempi di filtri avanzati che è possibile eseguire includono:

  Per l'evento `Microsoft.MachineLearningServices.ModelRegistered`, per filtrare il valore del tag del modello:

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  Per altre informazioni su come applicare i filtri, vedere [Filtrare gli eventi per Griglia di eventi](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="consume-machine-learning-events"></a>Usare gli eventi di Machine Learning

Per le applicazioni che gestiscono gli eventi di Machine Learning è consigliabile seguire alcune procedure:

> [!div class="checklist"]
> * Dal momento che vi possono essere più sottoscrizioni configurate per instradare gli eventi allo stesso gestore eventi, è importante non presupporre che gli eventi derivino da un'origine specifica, ma controllare l'argomento del messaggio per assicurarsi che provengano dall'area di lavoro di Machine Learning prevista.
> * Controllare anche che il tipo di evento che si verifica, eventType, sia un oggetto che si è preparati a elaborare e non presupporre che tutti gli eventi che si ricevono siano dei tipi previsti.
> * Dal momento che i messaggi non arrivano in ordine e giungono con un po' di ritardo, usare i campi etag per verificare se le informazioni disponibili sugli oggetti sono ancora aggiornate.  È possibile usare anche i campi del sequencer per comprendere l'ordine degli eventi relativi a un oggetto specifico.
> * Ignorare i campi che non si conoscono. Questa procedura consentirà di rimanere flessibili alle nuove funzionalità che potrebbero essere aggiunte in futuro.
> * Le operazioni non riuscite o annullate di Azure Machine Learning non attiveranno alcun evento. Ad esempio, se la distribuzione modello ha esito negativo, Microsoft.MachineLearningServices.ModelDeployed non viene attivato. Prendere in considerazione tale modalità di errore durante la progettazione delle applicazioni. È comunque possibile usare l'SDK di Azure Machine Learning, l'interfaccia della riga di comando o il portale per verificare lo stato di un'operazione e comprendere dettagliatamente i motivi dell'errore.

Griglia di eventi di Azure consente ai clienti di compilare gestori di messaggi disaccoppiati, che possono essere attivati dagli eventi di Azure Machine Learning. Di seguito sono riportati alcuni esempi significativi di gestori di messaggi:
* Funzioni di Azure
* App per la logica di Azure
* Hub eventi di Azure
* Pipeline di Azure Data Factory
* Webhook generici, che possono essere ospitati nella piattaforma Azure o altrove

## <a name="set-up-in-azure-portal"></a>Eseguire la configurazione nel portale di Azure

1. Aprire il [portale di Azure](https://portal.azure.com) e accedere all'area di lavoro di Azure Machine Learning.

1. Nella barra a sinistra selezionare __Eventi__, quindi selezionare **Sottoscrizioni di eventi**. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Selezionare il tipo di evento da usare. Nello screenshot seguente, ad esempio, è stato selezionato __Modello registrato__, __Modello distribuito__, __Esecuzione completata__ e __Deriva dei dati rilevata__:

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Selezionare l'endpoint in cui pubblicare l'evento. Nello screenshot seguente __Hub eventi__ è l'endpoint selezionato:

    ![gestore eventi](./media/how-to-use-event-grid/select-event-handler.png)

Dopo aver confermato la selezione, fare clic su __Crea__. Dopo la configurazione, questi eventi verranno inseriti nell'endpoint.


### <a name="set-up-with-the-cli"></a>Eseguire la configurazione con l'interfaccia della riga di comando

È possibile installare la versione più recente dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) o usare Azure Cloud Shell fornito come parte della sottoscrizione di Azure.

Per installare l'estensione Griglia di eventi, usare il comando seguente dall'interfaccia della riga di comando:

```azurecli-interactive
az add extension --name eventgrid
```

L'esempio seguente illustra come selezionare una sottoscrizione di Azure e creare una nuova sottoscrizione di eventi per Azure Machine Learning:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace. This example uses EventHub as a destination. 
az eventgrid event-subscription create --name {eventGridFilterName} \
  --source-resource-id /subscriptions/{subId}/resourceGroups/{RG}/providers/Microsoft.MachineLearningServices/workspaces/{wsName} \
  --endpoint-type eventhub \
  --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.EventHub/namespaces/n1/eventhubs/EH1 \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>Esempi

### <a name="example-send-email-alerts"></a>Esempio: inviare avvisi tramite posta elettronica

Usare [App per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/) per configurare i messaggi di posta elettronica per tutti gli eventi. Personalizzare usando le condizioni e specificare i destinatari per consentire la collaborazione tra team che lavorano insieme.

1. Nel portale di Azure accedere all'area di lavoro di Azure Machine Learning e selezionare la scheda eventi nella barra a sinistra. Da qui, selezionare __App per la logica__. 

    ![Select-Logic-app](./media/how-to-use-event-grid/select-logic-ap.png)

1. Accedere all'interfaccia utente App per la logica e selezionare il servizio Machine Learning come tipo di argomento. 

    ![argomento-tipo](./media/how-to-use-event-grid/select-topic-type.png)

1. Selezionare gli eventi per cui si vogliono ricevere avvisi. Ad esempio, come nello screenshot seguente, __RunCompleted__.

    ![Select-Event-run-complete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. È possibile usare il metodo di filtraggio nella sezione precedente o aggiungere filtri per attivare solo l'app per la logica in un subset di tipi di evento. Nello screenshot seguente viene usato un __filtro per prefisso__ di __/datadriftID/runs/__ .

    ![filter-events](./media/how-to-use-event-grid/filtering-events.png)

1. Successivamente, aggiungere un passaggio per usare questo evento e cercare la posta elettronica. Sono disponibili diversi account di posta elettronica che è possibile usare per ricevere eventi. È anche possibile configurare delle condizioni per l'invio di un avviso di posta elettronica.

    ![posta elettronica-azione](./media/how-to-use-event-grid/select-email-action.png)

1. Selezionare __Invia un messaggio di posta elettronica__ e specificare i parametri. Nell'oggetto è possibile includere __Tipo di evento__ e __Argomento__ per filtrare meglio gli eventi. È possibile includere anche un collegamento alla pagina dell'area di lavoro per le esecuzioni nel corpo del messaggio. 

    ![Configura-posta elettronica](./media/how-to-use-event-grid/configure-email-body.png)

1. Per salvare questa azione, selezionare **Salva con nome** nell'angolo sinistro della pagina. Dalla barra che viene visualizzata a destra, confermare la creazione dell'azione.

    ![conferma-logica-Creazione app](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>Esempio: ripetizione del training attivata dalla deriva dei dati

Nel corso del tempo i modelli diventano obsoleti e non sono più utili nel contesto in cui vengono eseguiti. Un metodo per stabilire se è il momento di ripetere il training del modello è rilevare la deriva dei dati. 

Questo esempio illustra come usare Griglia di eventi con un'app per la logica di Azure per attivare la ripetizione del training. Nell'esempio, quando si verifica la deriva dei dati tra il training di un modello e la gestione dei set di dati viene attivata una pipeline di Azure Data Factory.

Prima di iniziare, eseguire le azioni seguenti:

* Configurare il monitoraggio di un set di dati per [rilevare la deriva dei dati](how-to-monitor-datasets.md) in un'area di lavoro
* Creare una [pipeline di Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) pubblicata.

In questo esempio viene usata una semplice pipeline di Data Factory per copiare i file in un archivio BLOB ed eseguire una pipeline di Machine Learning pubblicata. Per altre informazioni su questo scenario, vedere come configurare un [Passaggio di Machine Learning in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![ADF-mlpipeline](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Iniziare con la creazione dell'app per la logica. Accedere al [portale di Azure](https://portal.azure.com), cercare le App per la logica e selezionare Crea.

    ![search-logic-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Inserire le informazioni richieste. Per semplificare l'esperienza, usare la stessa sottoscrizione e lo stesso gruppo di risorse della pipeline di Azure Data Factory e dell'area di lavoro di Azure Machine Learning.

    ![Impostazione-logica-app-ADF](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Dopo aver creato l'app per la logica, selezionare __Quando si verifica l'evento di una risorsa di Griglia di eventi__. 

    ![Select-eventgrid-trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Accedere e inserire i dettagli relativi all'evento. Impostare __Nome risorsa__ sul nome dell'area di lavoro. Impostare __Tipo di evento__ su __DatasetDriftDetected__.

    ![Login-Add-Event](./media/how-to-use-event-grid/login-and-add-event.png)

1. Aggiungere un nuovo passaggio e cercare __Azure Data Factory__. Selezionare __Crea un'esecuzione della pipeline__. 

    ![creazione-ADF-pipeline-esecuzione](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Accedere e specificare la pipeline di Azure Data Factory pubblicata da eseguire.

    ![specifica-adfpipeline](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Salvare e creare l'app per la logica usando il pulsante **salva** nell'area in alto a sinistra della pagina. Per visualizzare l'app, passare all'area di lavoro nel [portale di Azure](https://portal.azure.com) e fare clic su **Eventi**.

    ![Show-logicapp-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Ora la pipeline data factory viene attivata quando si verifica la deriva. Visualizzare i dettagli sull'esecuzione della deriva dei dati e sulla pipeline di Machine Learning nel [nuovo portale dell'area di lavoro](https://ml.azure.com). 

![Visualizza-area di lavoro](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>Esempio: distribuzione di un modello in base ai tag

Un oggetto modello di Azure Machine Learning contiene parametri in cui è possibile trasformare le distribuzioni tramite pivot, ad esempio il nome del modello, la versione, il tag e la proprietà. L'evento di registrazione del modello può attivare un endpoint: è possibile usare una funzione di Azure per distribuire un modello in base al valore di tali parametri.

Per un esempio, vedere il repository [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) e seguire i passaggi nel file **leggimi**.

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altre informazioni su Griglia di eventi e la possibilità di provare a usare gli eventi di Azure Machine Learning:

- [Informazioni sulla griglia di eventi](../event-grid/overview.md)

- [Schema degli eventi per Azure Machine Learning](../event-grid/event-schema-machine-learning.md)

