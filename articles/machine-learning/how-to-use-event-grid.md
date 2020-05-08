---
title: Attivare eventi in flussi di lavoro ML
titleSuffix: Azure Machine Learning
description: Informazioni su come attivare applicazioni, processi o flussi di lavoro di integrazione continua/recapito continuo basati su eventi in base agli eventi Azure Machine Learning per semplificare il ciclo di vita di ML.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: 236cc46bb6f9e5ed95e4a49068ac41ae77a736f5
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982871"
---
# <a name="trigger-event-driven-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Attivazione di applicazioni, processi o flussi di lavoro di integrazione continua/recapito continuo in base a eventi di Azure Machine Learning (anteprima)

Questo articolo illustra come configurare le applicazioni, i processi o i flussi di lavoro di integrazione continua/recapito continuo basati su eventi in base a eventi di Azure Machine Learning, ad esempio messaggi di posta elettronica di notifica di errore o esecuzioni di pipeline ML, quando vengono rilevate determinate condizioni da [griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/). 

Azure Machine Learning gestisce l'intero ciclo di vita del processo di Machine Learning, tra cui il training del modello, la distribuzione del modello e il monitoraggio. È possibile usare griglia di eventi per rispondere a eventi di Azure Machine Learning, ad esempio il completamento delle esecuzioni di training, la registrazione e la distribuzione dei modelli e il rilevamento della deviazione dei dati, usando architetture senza server moderne. In un'area di lavoro è quindi possibile sottoscrivere e utilizzare eventi quali lo stato dell'esecuzione, il completamento, la registrazione del modello, la distribuzione del modello e il rilevamento della tendenza dei dati.

Quando usare griglia di eventi per le azioni guidate dagli eventi:
* Invia messaggi di posta elettronica in caso di errore di esecuzione ed esecuzione completata
* Usare una funzione di Azure dopo la registrazione di un modello
* Streaming di eventi da Azure Machine Learning a diversi endpoint
* Attivare una pipeline ML quando viene rilevata la deriva

> [!NOTE] 
> Attualmente, gli eventi runStatusChanged vengono attivati solo quando lo stato dell'esecuzione **non è riuscito**

## <a name="prerequisites"></a>Prerequisiti
Per utilizzare griglia di eventi, è necessario disporre dell'accesso come collaboratore o proprietario all'area di lavoro Azure Machine Learning per cui si creeranno gli eventi.

## <a name="the-event-model--types"></a>Tipi di & del modello di eventi

Griglia di eventi di Azure legge gli eventi da origini, ad esempio Azure Machine Learning e altri servizi di Azure. Questi eventi vengono quindi inviati ai gestori eventi, ad esempio hub eventi di Azure, funzioni di Azure, app per la logica e altri. Il diagramma seguente illustra il modo in cui griglia di eventi connette le origini e i gestori, ma non è un elenco completo delle integrazioni supportate.

![Modello funzionale di Griglia di eventi di Azure](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Per ulteriori informazioni sulle origini eventi e i gestori eventi, vedere informazioni su [griglia](/azure/event-grid/overview)di eventi.

### <a name="event-types-for-azure-machine-learning"></a>Tipi di evento per Azure Machine Learning

Azure Machine Learning fornisce eventi nei vari punti del ciclo di vita di Machine Learning: 

| Tipo di evento | Descrizione |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Generato quando viene completata l'esecuzione di un esperimento di Machine Learning |
| `Microsoft.MachineLearningServices.ModelRegistered` | Generato quando un modello di apprendimento automatico viene registrato nell'area di lavoro |
| `Microsoft.MachineLearningServices.ModelDeployed` | Generato quando viene completata una distribuzione di un servizio di inferenza con uno o più modelli |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Generato quando viene completato un processo di rilevamento della deriva dei dati per due set di dati |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Generato quando lo stato di un'esecuzione viene modificato, attualmente generato solo quando uno stato di esecuzione è' failed ' |

### <a name="filter--subscribe-to-events"></a>Filtrare & sottoscrivere eventi

Questi eventi vengono pubblicati tramite griglia di eventi di Azure. Con portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure, i clienti possono sottoscrivere facilmente gli eventi [specificando uno o più tipi di evento e le condizioni di filtro](/azure/event-grid/event-filtering). 

Quando si configurano gli eventi, è possibile applicare filtri per attivare solo i dati specifici degli eventi. Nell'esempio seguente, per gli eventi di stato di esecuzione modificati è possibile filtrare in base ai tipi di esecuzione. L'evento viene attivato solo quando i criteri vengono soddisfatti. Fare riferimento allo [schema della griglia di eventi Azure Machine Learning](/azure/event-grid/event-schema-machine-learning) per informazioni sui dati degli eventi che è possibile filtrare in base a. 

Le sottoscrizioni per gli eventi Azure Machine Learning sono protette dal controllo degli accessi in base al ruolo (RBAC). Solo il [collaboratore o il proprietario](how-to-assign-roles.md#default-roles) di un'area di lavoro può creare, aggiornare ed eliminare sottoscrizioni di eventi.  I filtri possono essere applicati alle sottoscrizioni di eventi durante la [creazione](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) della sottoscrizione di eventi o in un secondo momento. 


1. Passare alla portale di Azure, selezionare una nuova sottoscrizione o una nuova sottoscrizione esistente. 

1. Selezionare la scheda filtri e scorrere verso il basso fino a filtri avanzati. Per la **chiave** e il **valore**, specificare i tipi di proprietà che si desidera filtrare in base a. Qui è possibile vedere che l'evento viene attivato solo quando il tipo di esecuzione è un'esecuzione di pipeline o un passaggio della pipeline.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="filtrare gli eventi":::


+ **Filtra per tipo di evento:** Una sottoscrizione di eventi può specificare uno o più Azure Machine Learning tipi di evento.

+ **Filtra per oggetto dell'evento:** Griglia di eventi di Azure supporta i filtri oggetto basati su __inizia con__ e __termina con__ corrispondenze, in modo che gli eventi con un oggetto corrispondente vengano recapitati al Sottoscrittore. Diversi eventi di Machine Learning hanno un formato soggetto diverso.

  | Tipo di evento | Formato oggetto | Oggetto di esempio |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **Filtro avanzato**: griglia di eventi di Azure supporta anche l'applicazione di filtri avanzati in base allo schema di eventi pubblicati. Azure Machine Learning dettagli dello schema di evento sono reperibili nello [schema di eventi di griglia di eventi di Azure per Azure Machine Learning](../event-grid/event-schema-machine-learning.md).  Alcuni esempi di filtri avanzati che è possibile eseguire includono:

  Per `Microsoft.MachineLearningServices.ModelRegistered` l'evento, per filtrare il valore del tag del modello:

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  Per altre informazioni su come applicare i filtri, vedere [filtrare gli eventi per griglia di eventi](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="consume-machine-learning-events"></a>USA eventi Machine Learning

Le applicazioni che gestiscono gli eventi di Machine Learning devono seguire alcune procedure consigliate:

> [!div class="checklist"]
> * Poiché è possibile configurare più sottoscrizioni per indirizzare gli eventi allo stesso gestore eventi, è importante non presupporre che gli eventi provengano da un'origine specifica, ma per controllare l'argomento del messaggio per assicurarsi che provenga dall'area di lavoro di Machine Learning prevista.
> * Controllare anche che il tipo di evento che si verifica, eventType, sia un oggetto che si è preparati a elaborare e non presupporre che tutti gli eventi che si ricevono siano dei tipi previsti.
> * Dal momento che i messaggi non arrivano in ordine e giungono con un po' di ritardo, usare i campi etag per verificare se le informazioni disponibili sugli oggetti sono ancora aggiornate.  È possibile usare anche i campi del sequencer per comprendere l'ordine degli eventi relativi a un oggetto specifico.
> * Ignorare i campi che non si conoscono. Questa procedura consentirà di rimanere flessibili alle nuove funzionalità che potrebbero essere aggiunte in futuro.
> * Operazione non riuscita o annullata Azure Machine Learning non attiverà alcun evento. Se, ad esempio, la distribuzione di un modello ha esito negativo, Microsoft. MachineLearningServices. ModelDeployed non verrà attivato. Prendere in considerazione tale modalità di errore durante la progettazione delle applicazioni. È sempre possibile usare Azure Machine Learning SDK, l'interfaccia della riga di comando o il portale per verificare lo stato di un'operazione e comprendere i motivi di errore dettagliati.

Griglia di eventi di Azure consente ai clienti di creare gestori di messaggi disaccoppiati, che possono essere attivati da eventi Azure Machine Learning. Di seguito sono riportati alcuni esempi significativi di gestori di messaggi:
* Funzioni di Azure
* App per la logica di Azure
* Hub eventi di Azure
* Pipeline Azure Data Factory
* Webhook generici, che possono essere ospitati nella piattaforma Azure o altrove

## <a name="set-up-in-azure-portal"></a>Configurare in portale di Azure

1. Aprire il [portale di Azure](https://portal.azure.com) e passare all'area di lavoro Azure Machine Learning.

1. Dalla barra a sinistra selezionare __eventi__ , quindi selezionare **sottoscrizioni di eventi**. 

    ![Select-Events-in-Workspace. png](./media/how-to-use-event-grid/select-event.png)

1. Consente di selezionare il tipo di evento da utilizzare. La schermata seguente, ad esempio, ha selezionato __modello registrato__, __modello distribuito__, __esecuzione completata__e __Drift del set di dati rilevato__:

    ![Aggiungi-tipo-evento](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Selezionare l'endpoint in cui pubblicare l'evento. Nella schermata seguente l' __Hub eventi__ è l'endpoint selezionato:

    ![Select-event-handler](./media/how-to-use-event-grid/select-event-handler.png)

Una volta confermata la selezione, fare clic su __Crea__. Dopo la configurazione, questi eventi verranno inseriti nell'endpoint.


### <a name="set-up-with-the-cli"></a>Configurare con l'interfaccia della riga di comando

È possibile installare la versione più recente dell'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)o usare il Azure cloud Shell fornito come parte della sottoscrizione di Azure.

Per installare l'estensione di griglia di eventi, usare il comando seguente dall'interfaccia della riga di comando:

```azurecli-interactive
az add extension --name eventgrid
```

L'esempio seguente illustra come selezionare una sottoscrizione di Azure e creare una nuova sottoscrizione di eventi per Azure Machine Learning:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>Esempi

### <a name="example-send-email-alerts"></a>Esempio: inviare avvisi di posta elettronica

Usare [app](https://docs.microsoft.com/azure/logic-apps/) per la logica di Azure per configurare i messaggi di posta elettronica per tutti gli eventi. Personalizzare con le condizioni e specificare i destinatari per consentire la collaborazione e la consapevolezza tra i team che interagiscono.

1. Nella portale di Azure passare all'area di lavoro Azure Machine Learning e selezionare la scheda eventi nella barra a sinistra. Da qui selezionare app per la __logica__. 

    ![Select-Logic-AP](./media/how-to-use-event-grid/select-logic-ap.png)

1. Accedere all'interfaccia utente dell'app per la logica e selezionare Machine Learning servizio come tipo di argomento. 

    ![Select-Topic-Type](./media/how-to-use-event-grid/select-topic-type.png)

1. Consente di selezionare gli eventi per i quali ricevere una notifica. Ad esempio, lo screenshot seguente __RunCompleted__.

    ![Select-Event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. È possibile usare il metodo di filtro nella sezione precedente o aggiungere filtri per attivare solo l'app per la logica in un subset di tipi di evento. Nello screenshot seguente viene usato un __filtro__ di prefisso __/datadriftID/runs/__ .

    ![Filtra-eventi](./media/how-to-use-event-grid/filtering-events.png)

1. Successivamente, aggiungere un passaggio per utilizzare questo evento e cercare la posta elettronica. Sono disponibili diversi account di posta elettronica che è possibile usare per ricevere eventi. È anche possibile configurare le condizioni per l'invio di un avviso di posta elettronica.

    ![Select-email-Action](./media/how-to-use-event-grid/select-email-action.png)

1. Selezionare __Invia un messaggio di posta elettronica__ e immettere i parametri. Nell'oggetto è possibile includere il tipo di __evento__ e l' __argomento__ per filtrare gli eventi. È anche possibile includere un collegamento alla pagina dell'area di lavoro per le esecuzioni nel corpo del messaggio. 

    ![Configure-email-body](./media/how-to-use-event-grid/configure-email-body.png)

1. Per salvare questa azione, selezionare **Salva con nome** nell'angolo sinistro della pagina. Dalla barra a destra visualizzata, confermare la creazione di questa azione.

    ![conferma-logica-app-crea](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>Esempio: trigger di spostamento dei dati ripetizione del training

I modelli diventano obsoleti nel tempo e non rimangono utili nel contesto in cui è in esecuzione. Un modo per stabilire se è il momento di ripetere il training del modello è il rilevamento della tendenza dei dati. 

Questo esempio illustra come usare griglia di eventi con un'app per la logica di Azure per attivare la ripetizione del training. Nell'esempio viene attivata una pipeline di Azure Data Factory quando si verifica la deviazione dei dati tra il training di un modello e la conservazione dei set di dati.

Prima di iniziare, eseguire le azioni seguenti:

* Configurare un monitoraggio del set di [dati per rilevare la tendenza dei dati]( https://aka.ms/datadrift) in un'area di lavoro
* Creare una [pipeline Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)pubblicata.

In questo esempio viene usata una semplice pipeline di Data Factory per copiare i file in un archivio BLOB ed eseguire una pipeline Machine Learning pubblicata. Per ulteriori informazioni su questo scenario, vedere How to set up a [Machine Learning Step in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![ADF-mlpipeline-stage](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Iniziare con la creazione dell'app per la logica. Passare alla [portale di Azure](https://portal.azure.com), cercare app per la logica e selezionare Crea.

    ![ricerca-logica-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Immettere le informazioni richieste. Per semplificare l'esperienza, usare la stessa sottoscrizione e il gruppo di risorse della pipeline Azure Data Factory e dell'area di lavoro Azure Machine Learning.

    ![Impostazione-logica-app-for-ADF](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Dopo aver creato l'app per la logica, selezionare __quando si verifica un evento della risorsa di griglia di eventi__. 

    ![Select-Event-Grid-trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Accedere e immettere i dettagli relativi all'evento. Impostare il __nome della risorsa__ sul nome dell'area di lavoro. Impostare il __tipo di evento__ su __DatasetDriftDetected__.

    ![Login-and-Add-Event](./media/how-to-use-event-grid/login-and-add-event.png)

1. Aggiungere un nuovo passaggio e cercare __Azure Data Factory__. Selezionare __Crea un'esecuzione pipeline__. 

    ![Create-adfpipeline-Run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Accedere e specificare la pipeline Azure Data Factory pubblicata da eseguire.

    ![specifica-ADF-pipeline](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Salvare e creare l'app per la logica usando il pulsante **Salva** nella parte superiore sinistra della pagina. Per visualizzare l'app, passare all'area di lavoro nel [portale di Azure](https://portal.azure.com) e fare clic su **eventi**.

    ![Show-Logic-app-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

A questo punto la pipeline data factory viene attivata quando si verifica la tendenza. Visualizzare i dettagli sull'esecuzione della deviazione dati e sulla pipeline di machine learning nel [nuovo portale dell'area di lavoro](https://ml.azure.com). 

![visualizzazione in area di lavoro](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>Esempio: distribuire un modello basato sui tag

Un oggetto modello di Azure Machine Learning contiene parametri in cui è possibile trasformare le distribuzioni tramite pivot, ad esempio il nome del modello, la versione, il tag e la proprietà. L'evento di registrazione del modello può attivare un endpoint ed è possibile usare una funzione di Azure per distribuire un modello in base al valore di tali parametri.

Per un esempio, vedere il [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) repository e seguire i passaggi nel file **Leggimi** .

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su griglia di eventi e concedi agli eventi Azure Machine Learning un tentativo:

- [Informazioni sulla griglia di eventi](../event-grid/overview.md)

- [Schema dell'evento per Azure Machine Learning](../event-grid/event-schema-machine-learning.md)

