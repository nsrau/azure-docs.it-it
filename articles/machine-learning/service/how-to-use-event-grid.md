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
ms.date: 11/04/2019
ms.openlocfilehash: b10b848918fda7a1f271a4e617b1706971f103d7
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622328"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Creare flussi di lavoro di machine learning basati su eventi (anteprima)

[Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/) supporta gli eventi del servizio Azure Machine Learning. Ad esempio, è possibile usare gli eventi dal completamento dell'esecuzione, dalla registrazione del modello, dalla distribuzione del modello e dal rilevamento della tendenza dei dati nell'ambito di un'area di lavoro.

Per ulteriori informazioni, vedere [Azure Machine Learning l'integrazione con griglia di eventi](concept-event-grid-integration.md) e lo [schema di griglia di eventi di Azure Machine Learning](/azure/event-grid/event-schema-machine-learning).

Usare griglia di eventi per abilitare scenari comuni, ad esempio:

* Attivazione di pipeline per la ripetizione del training
* Streaming di eventi da Azure Machine Learning a diversi endpoint

## <a name="prerequisites"></a>Prerequisiti

* Accesso come collaboratore o proprietario all'area di lavoro del servizio Azure Machine Learning per cui si creeranno gli eventi.
* Selezionare un endpoint del gestore eventi, ad esempio un webhook o un hub eventi. Per altre informazioni, vedere [gestori eventi](https://docs.microsoft.com/azure/event-grid/event-handlers). 

## <a name="register-resource-providers"></a>Registrare i provider di risorse

Se è stato usato griglia di eventi di Azure o Machine Learning prima del 1 2019 novembre, potrebbe essere necessario registrare nuovamente i provider di risorse prima di poter eseguire i passaggi descritti in questo documento. Per registrare nuovamente i provider, attenersi alla procedura seguente:

1. Passare alla portale di Azure e selezionare __sottoscrizioni__. Selezionare la sottoscrizione che si desidera utilizzare.
1. Selezionare __provider di risorse__e quindi cercare __EventGrid__.
1. Selezionare la voce __Microsoft. EventGrid__ , quindi selezionare __Re-Register__.

    ![ri-Register-Resource-Provider](media/how-to-use-event-grid/re-register-resource-provider.png)

1. Cercare __MachineLearningServices__, selezionare la voce e quindi selezionare __Re-Register__.

> [!TIP]
> Se non si dispone delle autorizzazioni necessarie per completare questi passaggi, rivolgersi all'amministratore della sottoscrizione per eseguirli.

## <a name="configure-machine-learning-events-using-the-azure-portal"></a>Configurare gli eventi di Machine Learning usando il portale di Azure

1. Aprire il [portale di Azure](https://portal.azure.com) e passare all'area di lavoro Azure Machine Learning.

1. Dalla barra a sinistra selezionare __eventi__ , quindi selezionare **sottoscrizioni di eventi**. 

    ![Select-Events-in-Workspace. png](media/how-to-use-event-grid/select-event.png)

1. Consente di selezionare il tipo di evento da utilizzare. La schermata seguente, ad esempio, ha selezionato __modello registrato__, __modello distribuito__, __esecuzione completata__e __Drift del set di dati rilevato__:

    ![Aggiungi-tipo-evento](media/how-to-use-event-grid/add-event-type.png)

1. Selezionare l'endpoint in cui pubblicare l'evento. Nella schermata seguente l' __Hub eventi__ è l'endpoint selezionato:

    ![Select-event-handler](media/how-to-use-event-grid/select-event-handler.png)

Una volta confermata la selezione, fare clic su __Crea__. Dopo la configurazione, questi eventi verranno inseriti nell'endpoint.

## <a name="set-up-azure-event-grid-using-cli"></a>Configurare griglia di eventi di Azure con CLI

È possibile installare la versione più recente dell'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)o usare il Azure cloud Shell fornito come parte della sottoscrizione di Azure.

Per installare l'estensione di griglia di eventi, usare il comando seguente dall'interfaccia della riga di comando:

```azurecli-interactive
az add extension --name eventgrid
```

Nell'esempio seguente viene illustrato come selezionare una sottoscrizione di Azure e quindi creare una nuova sottoscrizione di eventi per Azure Machine Learning:

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

## <a name="sample-scenarios"></a>Scenari di esempio

### <a name="use-a-logic-app-to-send-email-alerts"></a>Usare un'app per la logica per inviare avvisi di posta elettronica

Sfrutta le [app](https://docs.microsoft.com/azure/logic-apps/) per la logica di Azure per configurare i messaggi di posta elettronica per tutti gli eventi. Personalizzare con le condizioni e specificare i destinatari per consentire la collaborazione e la consapevolezza tra i team che interagiscono.

1. Nella portale di Azure passare all'area di lavoro Azure Machine Learning e selezionare la scheda eventi nella barra a sinistra. Da qui selezionare app per la __logica__. 

    ![Select-Logic-AP](media/how-to-use-event-grid/select-logic-ap.png)

1. Accedere all'interfaccia utente dell'app per la logica e selezionare Machine Learning servizio come tipo di argomento. 

    ![Select-Topic-Type](media/how-to-use-event-grid/select-topic-type.png)

1. Consente di selezionare gli eventi per i quali ricevere una notifica. Ad esempio, lo screenshot seguente __RunCompleted__.

    ![Select-Event-runcomplete](media/how-to-use-event-grid/select-event-runcomplete.png)

1. È anche possibile aggiungere filtri per attivare solo l'app per la logica in un subset di tipi di evento. Nello screenshot seguente viene usato un __filtro__ di prefisso __/datadriftID/runs/__ .

    ![Filtra-eventi](media/how-to-use-event-grid/filtering-events.png)

1. Successivamente, aggiungere un passaggio per utilizzare questo evento e cercare la posta elettronica. Sono disponibili diversi account di posta elettronica che è possibile usare per ricevere eventi. È anche possibile configurare le condizioni per l'invio di un avviso di posta elettronica.

    ![Select-email-Action](media/how-to-use-event-grid/select-email-action.png)

1. Selezionare __Invia un messaggio di posta elettronica__ e immettere i parametri. Nell'oggetto è possibile includere il tipo di __evento__ e l' __argomento__ per filtrare gli eventi. È anche possibile includere un collegamento alla pagina dell'area di lavoro per le esecuzioni nel corpo del messaggio. 

    ![Configure-email-body](media/how-to-use-event-grid/configure-email-body.png)

1. Per salvare questa azione, selezionare **Salva con nome** nell'angolo sinistro della pagina. Dalla barra a destra visualizzata, confermare la creazione di questa azione.

    ![conferma-logica-app-crea](media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>Usare un'app per la logica per attivare i flussi di lavoro di ripetizione del training quando si verifica la tendenza

I modelli diventano obsoleti nel tempo e non rimangono utili nel contesto in cui è in esecuzione. Un modo per stabilire se è il momento di ripetere il training del modello è il rilevamento della tendenza dei dati. 

Questo esempio illustra come usare griglia di eventi con un'app per la logica di Azure per attivare la ripetizione del training. Nell'esempio viene attivata una pipeline di Azure Data Factory quando si verifica la deviazione dei dati tra il training di un modello e la conservazione dei set di dati.

Prima di iniziare, eseguire le azioni seguenti:

* Configurare un monitoraggio del set di [dati per rilevare la tendenza dei dati]( https://aka.ms/datadrift) in un'area di lavoro
* Creare una [pipeline Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)pubblicata.

In questo esempio viene usata una semplice pipeline di Data Factory per copiare i file in un archivio BLOB ed eseguire una pipeline Machine Learning pubblicata. Per ulteriori informazioni su questo scenario, vedere How to set up a [Machine Learning Step in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![ADF-mlpipeline-stage](media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Iniziare con la creazione dell'app per la logica. Passare alla [portale di Azure](https://portal.azure.com), cercare app per la logica e selezionare Crea.

    ![ricerca-logica-app](media/how-to-use-event-grid/search-for-logic-app.png)

1. Immettere le informazioni richieste. Per semplificare l'esperienza, usare la stessa sottoscrizione e il gruppo di risorse della pipeline Azure Data Factory e dell'area di lavoro Azure Machine Learning.

    ![Impostazione-logica-app-for-ADF](media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Dopo aver creato l'app per la logica, selezionare __quando si verifica un evento della risorsa di griglia di eventi__. 

    ![Select-Event-Grid-trigger](media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Accedere e immettere i dettagli relativi all'evento. Impostare il __nome della risorsa__ sul nome dell'area di lavoro. Impostare il __tipo di evento__ su __DatasetDriftDetected__.

    ![Login-and-Add-Event](media/how-to-use-event-grid/login-and-add-event.png)

1. Aggiungere un nuovo passaggio e cercare __Azure Data Factory__. Selezionare __Crea un'esecuzione pipeline__. 

    ![Create-adfpipeline-Run](media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Accedere e specificare la pipeline Azure Data Factory pubblicata da eseguire.

    ![specifica-ADF-pipeline](media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Salvare e creare l'app per la logica usando il pulsante **Salva** nella parte superiore sinistra della pagina. Per visualizzare l'app, passare all'area di lavoro nel [portale di Azure](https://portal.azure.com) e fare clic su **eventi**.

    ![Show-Logic-app-webhook](media/how-to-use-event-grid/show-logic-app-webhook.png)

A questo punto la pipeline data factory viene attivata quando si verifica la tendenza. Visualizzare i dettagli sull'esecuzione della deviazione dati e sulla pipeline di machine learning nel [nuovo portale dell'area di lavoro](https://ml.azure.com). 

![visualizzazione in area di lavoro](media/how-to-use-event-grid/view-in-workspace.png)


### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Usare funzioni di Azure per distribuire un modello basato sui tag

Un oggetto modello di Azure Machine Learning contiene parametri in cui è possibile trasformare le distribuzioni tramite pivot, ad esempio il nome del modello, la versione, il tag e la proprietà. L'evento di registrazione del modello può attivare un endpoint ed è possibile usare una funzione di Azure per distribuire un modello in base al valore di tali parametri.

Per un esempio, vedere il repository [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) e seguire i passaggi nel file **Leggimi** .

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sugli eventi disponibili, vedere lo [schema dell'evento Azure Machine Learning](/azure/event-grid/event-schema-machine-learning)