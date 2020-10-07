---
title: Inviare eventi di archiviazione BLOB a un endpoint Web - Modello
description: Usare Griglia di eventi di Azure e un modello di Azure Resource Manager per creare un account di archiviazione BLOB e sottoscrivere i relativi eventi. Inviare eventi a un webhook.
ms.date: 07/07/2020
ms.topic: quickstart
ms.openlocfilehash: fc6216142c6bec99cb12bf4e0bdae920f27f1bf2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91598350"
---
# <a name="quickstart-route-blob-storage-events-to-web-endpoint-by-using-an-arm-template"></a>Avvio rapido: Instradare gli eventi di archiviazione BLOB a un endpoint Web usando un modello di Resource Manager

La griglia di eventi di Azure è un servizio di gestione degli eventi per il cloud. Questo articolo descrive come usare un modello di Azure Resource Manager per creare un account di archiviazione BLOB, sottoscrivere i relativi eventi e attivare un evento per visualizzare il risultato. In genere, si inviano eventi a un endpoint che elabora i dati dell'evento e intraprende azioni. Per maggiore semplicità, tuttavia, in questo articolo gli eventi vengono inviati a un'app Web che raccoglie e visualizza i messaggi.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

### <a name="create-a-message-endpoint"></a>Creare un endpoint del messaggio

Prima di sottoscrivere gli eventi per l'account di archiviazione BLOB, creare l'endpoint per il messaggio dell'evento. L'endpoint richiede in genere azioni basate sui dati degli eventi. Per semplificare questa guida introduttiva, si distribuisce un'[app Web preesistente](https://github.com/Azure-Samples/azure-event-grid-viewer) che visualizza i messaggi di evento. La soluzione distribuita include un piano di servizio app, un'app Web del servizio app e codice sorgente da GitHub.

1. Selezionare **Distribuisci in Azure** per distribuire la soluzione nella sottoscrizione. Nel portale di Azure specificare i valori per i parametri.

    [Distribuzione in Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)
1. Per il completamento della distribuzione possono essere necessari alcuni minuti. Dopo il completamento della distribuzione, visualizzare l'app Web per assicurarsi che sia in esecuzione. In un Web browser passare a: `https://<your-site-name>.azurewebsites.net`

1. Viene visualizzato il sito, ma nessun evento è ancora stato pubblicato.

   ![Visualizzare il nuovo sito](./media/blob-event-quickstart-portal/view-site.png)

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-event-grid-subscription-and-storage/).

:::code language="json" source="~/quickstart-templates/101-event-grid-subscription-and-storage/azuredeploy.json":::

Nel modello sono definite due risorse di Azure:

* [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): crea un account di archiviazione di Azure.
* [**Microsoft.EventGrid/systemTopics**](/azure/templates/microsoft.eventgrid/systemtopics): crea un argomento di sistema con il nome specificato per l'account di archiviazione.
* [**Microsoft.EventGrid/systemTopics/eventSubscriptions**](/azure/templates/microsoft.eventgrid/systemtopics/eventsubscriptions): crea una sottoscrizione di Griglia di eventi di Azure per l'argomento di sistema.

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare il collegamento seguente per accedere ad Azure e aprire un modello. Il modello crea un insieme di credenziali delle chiavi e un segreto.

    [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json)

2. Specificare l'**endpoint**: fornire l'URL dell'app Web e aggiungere `api/updates` all'URL della home page.
3. Selezionare **Acquista** per distribuire il modello.

  In questo caso per distribuire il modello si usa il portale di Azure. È anche possibile usare Azure PowerShell, l'interfaccia della riga di comando di Azure e un'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../azure-resource-manager/templates/deploy-powershell.md).

> [!NOTE]
> È possibile trovare altri esempi di modelli di Griglia di eventi di Azure [qui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid&pageNumber=1&sort=Popular).

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Visualizzare nuovamente l'app Web e notare che all'app è stato inviato un evento di convalida della sottoscrizione. Selezionare l'icona a forma di occhio per espandere i dati dell'evento. Griglia di eventi invia l'evento di convalida in modo che l'endpoint possa verificare che voglia ricevere i dati dell'evento. L'app Web include il codice per convalidare la sottoscrizione.

![Visualizzare l'evento della sottoscrizione](./media/blob-event-quickstart-portal/view-subscription-event.png)

A questo punto, attivare un evento per vedere come la griglia di eventi distribuisce il messaggio nell'endpoint.

Per attivare un evento per l'account di archiviazione BLOB, caricare un file. Non sono necessari contenuti specifici per il file. Gli articoli presuppongono che si abbia un file denominato testfile.txt, ma è possibile usare qualsiasi file.

Quando si carica il file nell'archiviazione BLOB di Azure, Griglia di eventi invia un messaggio all'endpoint configurato durante la sottoscrizione. Il messaggio è in formato JSON e contiene una matrice con uno o più eventi. Nell'esempio seguente il messaggio JSON contiene una matrice con un solo evento. Visualizzare l'app Web. Si noterà che è stato ricevuto un evento creato da un BLOB.

![Visualizzazione dei risultati](./media/blob-event-quickstart-portal/view-results.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, [eliminare il gruppo di risorse](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group
).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui modelli di Azure Resource Manager, vedere gli articoli seguenti:

* [Documentazione di Azure Resource Manager](../azure-resource-manager/index.yml)
* [Definire risorse nei modelli di Azure Resource Manager](/azure/templates/)
* [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/)
* [Modelli di Griglia di eventi di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid).
