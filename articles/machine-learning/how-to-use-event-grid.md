---
title: Creare flussi di lavoro di apprendimento automatico basati su eventiCreate event driven machine learning workflows
titleSuffix: Azure Machine Learning
description: Informazioni su come usare la griglia di eventi con Azure Machine Learning per abilitare soluzioni basate su eventi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: fe6125682f669e453100488b7e0afc4c49409588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129703"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Creare flussi di lavoro di Machine Learning basati su eventi (anteprima)Create event driven machine learning workflows (Preview)

[Griglia di eventi](https://docs.microsoft.com/azure/event-grid/) di Azure supporta gli eventi di Azure Machine Learning.Azure Event Grid supports Azure Machine Learning events. È possibile sottoscrivere e utilizzare eventi quali lo stato dell'esecuzione modificato, il completamento dell'esecuzione, la registrazione del modello, la distribuzione dei modelli e il rilevamento della deriva dei dati all'interno di un'area di lavoro.

Per altre informazioni sui tipi di evento, vedere [Integrazione](concept-event-grid-integration.md) di Azure Machine Learning con Griglia di eventi e lo schema della griglia di eventi di Azure Machine Learning.For more information on event types, see Azure Machine Learning integration with Event Grid and the Azure Machine Learning event grid [schema](/azure/event-grid/event-schema-machine-learning).

Utilizzare Griglia di eventi per abilitare scenari comuni, ad esempio:Use Event Grid to enable common scenarios such as:

* Inviare messaggi di posta elettronica in caso di errore di esecuzione e completamento esecuzione
* Usare una funzione di azure dopo la registrazione di un modelloUse an azure function after a model is registered
* Streaming di eventi da Azure Machine Learning a vari endpoint
* Attivare una pipeline ML quando viene rilevata la derivaTrigger an ML pipeline when drift is detected

> [!NOTE] 
> Attualmente, gli eventi runStatusChanged attivano solo quando lo stato di esecuzione non è **riuscito**
>

## <a name="prerequisites"></a>Prerequisiti
* L'accesso come collaboratore o proprietario all'area di lavoro di Azure Machine Learning per cui verranno creati gli eventi.

### <a name="configure-eventgrid-using-the-azure-portal"></a>Configurare EventGrid tramite il portale di AzureConfigure EventGrid using the Azure portal

1. Aprire il portale di Azure e passare all'area di lavoro di Azure Machine Learning.Open the [Azure portal](https://portal.azure.com) and go to your Azure Machine Learning workspace.

1. Nella barra sinistra selezionare __Eventi__ e quindi **Sottoscrizioni eventi.** 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Selezionare il tipo di evento da utilizzare. Ad esempio, la schermata seguente ha selezionato __Modello registrato__, __Modello distribuito__, __Esegui completato__e Deriva set di __dati rilevato:__

    ![add-event-type (tipo di evento aggiuntivo)](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Selezionare l'endpoint in cui pubblicare l'evento. Nella schermata seguente, Hub eventi è l'endpoint selezionato:In the following screenshot, __Event hub__ is the selected endpoint:

    ![select-event-handler](./media/how-to-use-event-grid/select-event-handler.png)

Dopo aver confermato la selezione, fare clic su __Crea__. Dopo la configurazione, questi eventi verranno inviati all'endpoint.


### <a name="configure-eventgrid-using-the-cli"></a>Configurare EventGrid tramite l'interfaccia della riga di comando

È possibile installare [l'interfaccia della riga](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)di comando di Azure più recente oppure usare Azure Cloud Shell fornito come parte della sottoscrizione di Azure.You can either install the latest Azure CLI, or use the Azure Cloud Shell that is provided as part of your Azure subscription.

Per installare l'estensione Griglia di eventi, utilizzare il comando seguente dall'interfaccia della riga di comando:

```azurecli-interactive
az add extension --name eventgrid
```

L'esempio seguente illustra come selezionare una sottoscrizione di Azure e crea una nuova sottoscrizione di eventi per Azure Machine Learning:The following example demonstrates how to select an Azure subscription and creates e a new event subscription for Azure Machine Learning:

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

Quando iregistri degli eventi, puoi applicare filtri per attivarli solo su dati specifici dell'evento. Nell'esempio seguente, per gli eventi di modifica dello stato di esecuzione, è possibile filtrare in base ai tipi di esecuzione. L'evento si attiva solo quando vengono soddisfatti i criteri. Fare riferimento allo schema della griglia di eventi di [Azure Machine Learning](/azure/event-grid/event-schema-machine-learning) per informazioni sui dati degli eventi in base ai quali è possibile filtrare. 

1. Passare al portale di Azure e selezionare una nuova sottoscrizione o una sottoscrizione esistente. 

1. Selezionare la scheda dei filtri e scorrere verso il basso fino a Filtri avanzati. In **Chiave** e **Valore** specificare i tipi di proprietà in base ai quali filtrare. Qui è possibile visualizzare l'evento verrà attivato solo quando il tipo di esecuzione è un'esecuzione della pipeline o un'esecuzione di passaggi della pipeline.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="eventi filtro":::

## <a name="sample-scenarios"></a>Scenari di esempio

### <a name="use-a-logic-app-to-send-email-alerts"></a>Usare un'app per la logica per inviare avvisi tramite posta elettronicaUse a Logic App to send email alerts

Sfrutta [le app per](https://docs.microsoft.com/azure/logic-apps/) la logica di Azure per configurare i messaggi di posta elettronica per tutti gli eventi. Personalizza con le condizioni e specifica i destinatari per consentire la collaborazione e il riconoscimento tra i team che lavorano insieme.

1. Nel portale di Azure passare all'area di lavoro di Azure Machine Learning e selezionare la scheda Eventi nella barra sinistra. Da qui, selezionare __App per__la logica . 

    ![select-logic-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. Accedere all'interfaccia utente dell'app per la logica e selezionare Servizio di Machine Learning come tipo di argomento. 

    ![select-topic-type](./media/how-to-use-event-grid/select-topic-type.png)

1. Selezionare gli eventi per cui ricevere la notifica. Ad esempio, la schermata seguente __RunCompleted__.

    ![select-event-runcompleto](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Puoi usare il metodo di filtro nella sezione precedente o aggiungere filtri per attivare solo l'app per la logica in un sottoinsieme di tipi di evento. Nella schermata seguente viene utilizzato un __filtro di prefisso__ di __/datadriftID/runs/.__

    ![filter-eventi](./media/how-to-use-event-grid/filtering-events.png)

1. Successivamente, aggiungi un passaggio per utilizzare questo evento e cerca la posta elettronica. Esistono diversi account di posta elettronica che è possibile utilizzare per ricevere eventi. È inoltre possibile configurare le condizioni su quando inviare un avviso tramite posta elettronica.

    ![select-email-action](./media/how-to-use-event-grid/select-email-action.png)

1. Seleziona __Invia un'e-mail__ e inserisci i parametri. Nell'oggetto, è possibile includere il __tipo di evento__ e l'argomento per filtrare gli eventi. __Topic__ È inoltre possibile includere un collegamento alla pagina dell'area di lavoro per le esecuzioni nel corpo del messaggio. 

    ![configure-email-body (corpo di posta elettronica)](./media/how-to-use-event-grid/configure-email-body.png)

1. Per salvare questa azione, selezionare **Salva con nome** nell'angolo sinistro della pagina. Dalla barra di destra che appare, confermare la creazione di questa azione.

    ![confirm-logic-app-create](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>Usare un'app per la logica per attivare la riqualificazione dei flussi di lavoro quando si verifica una deriva dei datiUse a Logic App to trigger retraining workflows when data drift occurs

I modelli non sono aggiornati nel tempo e non rimangono utili nel contesto in cui è in esecuzione. Un modo per stabilire se è il momento di eseguire nuovamente il training del modello è il rilevamento della deriva dei dati. 

Questo esempio mostra come usare la griglia di eventi con un'app per la logica di Azure per attivare la riqualificazione. L'esempio attiva una pipeline di Azure Data Factory quando si verifica una deriva dei dati tra il training di un modello e la pubblicazione di set di dati.

Prima di iniziare, eseguire le azioni seguenti:

* Configurare un monitoraggio set di dati per [rilevare]( https://aka.ms/datadrift) la deriva dei dati in un'area di lavoroSet up a dataset monitor to detect data drift in a workspace
* Creare una pipeline di [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)pubblicata.

In questo esempio viene usata una pipeline di Data Factory semplice per copiare i file in un archivio BLOB ed eseguire una pipeline di Machine Learning pubblicata. Per altre informazioni su questo scenario, vedere come configurare un passaggio di [Machine Learning in Azure Data FactoryFor](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service) more information on this scenario, see how to set up a Machine Learning step in Azure Data Factory

![adf-mlpipeline-stage](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Iniziare con la creazione dell'app per la logica. Passare al [portale di Azure,](https://portal.azure.com)cercare App per la logica e selezionare crea.

    ![search-logic-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Inserire le informazioni richieste. Per semplificare l'esperienza, usare la stessa sottoscrizione e lo stesso gruppo di risorse della pipeline di Azure Data Factory e dell'area di lavoro di Azure Machine Learning.To simplify the experience, use the same subscription and resource group as your Azure Data Factory Pipeline and Azure Machine Learning workspace.

    ![set-up-logic-app-for-adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Dopo aver creato l'app per la logica, selezionare __Quando si verifica un evento risorsa Griglia eventi__. 

    ![select-event-grid-trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Accedi e inserisci i dettagli dell'evento. Impostare __Nome risorsa__ sul nome dell'area di lavoro. Impostare il __tipo di evento__ su __DatasetDriftDetected__.

    ![login-and-add-event](./media/how-to-use-event-grid/login-and-add-event.png)

1. Aggiungere un nuovo passaggio ed eseguire la ricerca di __Azure Data Factory.__ Selezionare __Crea un'esecuzione della pipeline__. 

    ![create-adfpipeline-run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Accedere e specificare la pipeline di Azure Data Factory pubblicata per l'esecuzione.

    ![specifica-adf-pipeline](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Salvare e creare l'app per la logica usando il pulsante **Salva** nella parte superiore sinistra della pagina. Per visualizzare l'app, passare all'area di lavoro nel portale di [Azure](https://portal.azure.com) e fare clic su **Eventi**.

    ![show-logic-app-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Ora la pipeline della data factory viene attivata quando si verifica la deriva. Visualizzare i dettagli sull'esecuzione della deriva dei dati e sulla pipeline di apprendimento automatico nel [nuovo portale dell'area di lavoro.](https://ml.azure.com) 

![visualizzazione nell'area di lavoro](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Usare Funzioni di Azure per distribuire un modello basato sui tagUse Azure Functions to deploy a model based on tags

Un oggetto modello di Azure Machine Learning contiene parametri su cui è possibile eseguire il pivot delle distribuzioni, ad esempio il nome del modello, la versione, il tag e la proprietà. L'evento di registrazione del modello può attivare un endpoint ed è possibile usare una funzione di Azure per distribuire un modello basato sul valore di tali parametri.

Per un esempio, [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) vedere il repository e seguire i passaggi nel file **Readme.**

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sugli eventi disponibili, vedere lo schema degli eventi di [Azure Machine LearningTo](/azure/event-grid/event-schema-machine-learning) learn more about available events, see the Azure Machine Learning event schema
