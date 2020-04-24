---
title: Creare flussi di lavoro di machine learning basati su eventi
titleSuffix: Azure Machine Learning
description: Informazioni su come usare griglia di eventi con Azure Machine Learning per abilitare le soluzioni basate su eventi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: 2a1440dcda27a487c89be4ac63e624a2bb6b393a
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111879"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Creare flussi di lavoro di machine learning basati su eventi (anteprima)

[Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/) supporta eventi Azure Machine Learning. È possibile sottoscrivere e utilizzare eventi quali lo stato di esecuzione modificato, il completamento, la registrazione del modello, la distribuzione del modello e il rilevamento della tendenza dei dati all'interno di un'area di lavoro.

Per ulteriori informazioni sui tipi di evento, vedere [Azure Machine Learning l'integrazione con griglia di eventi](concept-event-grid-integration.md) e lo [schema di griglia di eventi di Azure Machine Learning](/azure/event-grid/event-schema-machine-learning).

Usare griglia di eventi per abilitare scenari comuni, ad esempio:

* Invia messaggi di posta elettronica in caso di errore di esecuzione ed esecuzione completata
* Usare una funzione di Azure dopo la registrazione di un modello
* Streaming di eventi da Azure Machine Learning a diversi endpoint
* Attivare una pipeline ML quando viene rilevata la deriva

> [!NOTE] 
> Attualmente, gli eventi runStatusChanged vengono attivati solo quando lo stato dell'esecuzione **non è riuscito**
>

## <a name="prerequisites"></a>Prerequisiti
* Accesso come collaboratore o proprietario all'area di lavoro Azure Machine Learning per cui si creeranno gli eventi.

### <a name="configure-eventgrid-using-the-azure-portal"></a>Configurare EventGrid usando il portale di Azure

1. Aprire il [portale di Azure](https://portal.azure.com) e passare all'area di lavoro Azure Machine Learning.

1. Dalla barra a sinistra selezionare __eventi__ , quindi selezionare **sottoscrizioni di eventi**. 

    ![Select-Events-in-Workspace. png](./media/how-to-use-event-grid/select-event.png)

1. Consente di selezionare il tipo di evento da utilizzare. La schermata seguente, ad esempio, ha selezionato __modello registrato__, __modello distribuito__, __esecuzione completata__e __Drift del set di dati rilevato__:

    ![Aggiungi-tipo-evento](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Selezionare l'endpoint in cui pubblicare l'evento. Nella schermata seguente l' __Hub eventi__ è l'endpoint selezionato:

    ![Select-event-handler](./media/how-to-use-event-grid/select-event-handler.png)

Una volta confermata la selezione, fare clic su __Crea__. Dopo la configurazione, questi eventi verranno inseriti nell'endpoint.


### <a name="configure-eventgrid-using-the-cli"></a>Configurare EventGrid usando l'interfaccia della riga di comando

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

## <a name="filter-events"></a>Filtrare gli eventi

Quando si configurano gli eventi, è possibile applicare filtri per attivare solo i dati specifici degli eventi. Nell'esempio seguente, per gli eventi di stato di esecuzione modificati è possibile filtrare in base ai tipi di esecuzione. L'evento viene attivato solo quando i criteri vengono soddisfatti. Fare riferimento allo [schema della griglia di eventi Azure Machine Learning](/azure/event-grid/event-schema-machine-learning) per informazioni sui dati degli eventi che è possibile filtrare in base a. 

1. Passare alla portale di Azure, selezionare una nuova sottoscrizione o una nuova sottoscrizione esistente. 

1. Selezionare la scheda filtri e scorrere verso il basso fino a filtri avanzati. Per la **chiave** e il **valore**, specificare i tipi di proprietà che si desidera filtrare in base a. Qui è possibile vedere che l'evento viene attivato solo quando il tipo di esecuzione è un'esecuzione di pipeline o un passaggio della pipeline.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="filtrare gli eventi":::

## <a name="sample-send-email-alerts"></a>Esempio: inviare avvisi di posta elettronica

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


## <a name="sample-trigger-retraining-when-data-drift-occurs"></a>Esempio: attivare la ripetizione del training quando si verifica la tendenza dei dati

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

## <a name="sample-deploy-a-model-based-on-tags"></a>Esempio: distribuire un modello basato sui tag

Un oggetto modello di Azure Machine Learning contiene parametri in cui è possibile trasformare le distribuzioni tramite pivot, ad esempio il nome del modello, la versione, il tag e la proprietà. L'evento di registrazione del modello può attivare un endpoint ed è possibile usare una funzione di Azure per distribuire un modello in base al valore di tali parametri.

Per un esempio, vedere il [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) repository e seguire i passaggi nel file **Leggimi** .

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sugli eventi disponibili, vedere lo [schema dell'evento Azure Machine Learning](/azure/event-grid/event-schema-machine-learning)
