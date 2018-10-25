---
title: Monitorare gli eventi di Servizi multimediali di Azure con Griglia di eventi e l'interfaccia della riga di comando | Microsoft Docs
description: Questo articolo illustra come eseguire la sottoscrizione a Griglia di eventi per monitorare gli eventi di Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 8145b4eb3c39511eb9cd0ed052c36b8338191d4f
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389497"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Creare e monitorare eventi di Servizi multimediali con Griglia di eventi e l'interfaccia della riga di comando di Azure

La griglia di eventi di Azure è un servizio di gestione degli eventi per il cloud. In questo articolo si userà l'interfaccia della riga di comando di Azure per sottoscrivere gli eventi per l'account di Servizi multimediali di Azure. Si attiveranno quindi gli eventi per visualizzare i risultati. In genere, si inviano eventi a un endpoint che elabora i dati dell'evento e intraprende azioni. In questo articolo gli eventi vengono inviati a un'app Web che raccoglie e visualizza i messaggi.

## <a name="prerequisites"></a>Prerequisiti

- Avere una sottoscrizione di Azure attiva.
- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md).

    Assicurarsi di ricordare i valori usati per il nome del gruppo di risorse e il nome dell'account di Servizi multimediali.

- Installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Questo articolo richiede l'interfaccia della riga di comando di Azure 2.0 o versioni successive. Eseguire `az --version` per trovare la versione in uso. È possibile usare anche [Azure Cloud Shell](https://shell.azure.com/bash).

## <a name="create-a-message-endpoint"></a>Creare un endpoint del messaggio

Prima di sottoscrivere gli eventi per l'account di Servizi multimediali, creare l'endpoint per il messaggio dell'evento. L'endpoint richiede in genere azioni basate sui dati degli eventi. In questo articolo si distribuisce un'[app Web preesistente](https://github.com/Azure-Samples/azure-event-grid-viewer) che visualizza i messaggi di evento. La soluzione distribuita include un piano di servizio app, un'app Web del servizio app e codice sorgente da GitHub.

1. Selezionare **Distribuisci in Azure** per distribuire la soluzione nella sottoscrizione. Nel portale di Azure specificare i valori per i parametri.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Per il completamento della distribuzione possono essere necessari alcuni minuti. Dopo il completamento della distribuzione, visualizzare l'app Web per assicurarsi che sia in esecuzione. In un Web browser passare a: `https://<your-site-name>.azurewebsites.net`

Se si passa al sito "Azure Event Grid Viewer" ("Visualizzatore Griglia di eventi di Azure"), è possibile osservare come non contenga ancora alcun evento.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](http://portal.azure.com) e avviare **CloudShell** per eseguire i comandi dell'interfaccia della riga di comando, come illustrato nei passaggi successivi.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessaria la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione in uso. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

## <a name="set-the-azure-subscription"></a>Impostare la sottoscrizione di Azure

Nel comando seguente specificare l'ID della sottoscrizione di Azure che si vuole usare per l'account di Servizi multimediali. È possibile visualizzare un elenco di sottoscrizioni a cui è possibile accedere passando a [Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli-interactive
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

## <a name="subscribe-to-media-services-events"></a>Eseguire la sottoscrizione a eventi di Servizi multimediali

Si esegue la sottoscrizione a un articolo per indicare a Griglia di eventi gli eventi di cui tenere traccia. L'esempio seguente sottoscrive l'account di Servizi multimediali creato e passa l'URL del sito Web creato come endpoint per la notifica degli eventi. 

Sostituire `<event_subscription_name>` con un nome univoco per la sottoscrizione di eventi. Per la `<resource_group_name>` e `<ams_account_name>`, specificare i valori usati durante la creazione dell'account di Servizi multimediali. Per `<endpoint_URL>`, specificare l'URL dell'app Web e aggiungere `api/updates` all'URL della home page. Se durante la sottoscrizione si specifica l'endpoint, Griglia di eventi gestisce il routing degli eventi verso l'endpoint. 

1. Ottenere l'ID risorsa

    ```azurecli-interactive
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Ad esempio: 

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Eseguire la sottoscrizione agli eventi

    ```azurecli-interactive
    az eventgrid event-subscription create \
    --resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Ad esempio: 

    ```
    az eventgrid event-subscription create --resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > È possibile che venga visualizzato un avviso relativo a un handshake di convalida. Attendere qualche minuto e per consentire all'handshake di eseguire la convalida.

A questo punto, attivare gli eventi per vedere come Griglia di eventi distribuisce il messaggio nell'endpoint.

## <a name="send-an-event-to-your-endpoint"></a>Inviare un evento all'endpoint

È possibile attivare gli eventi per l'account di Servizi multimediali tramite l'esecuzione di un processo di codifica. È possibile seguire [questa guida introduttiva](stream-files-dotnet-quickstart.md) per codificare un file e iniziare a inviare gli eventi. 

Visualizzare nuovamente l'app Web e notare che all'app è stato inviato un evento di convalida della sottoscrizione. Griglia di eventi invia l'evento di convalida in modo che l'endpoint possa verificare che voglia ricevere i dati dell'evento. L'endpoint deve impostare `validationResponse` su `validationCode`. Per altre informazioni, vedere [Event Grid security and authentication](../../event-grid/security-authentication.md) (Sicurezza e autenticazione di Griglia di eventi). È possibile visualizzare il codice dell'app Web per vedere come convalida la sottoscrizione.

> [!TIP]
> Selezionare l'icona a forma di occhio per espandere i dati dell'evento. Non aggiornare la pagina se si vuole visualizzare tutti gli eventi.

![Visualizzare l'evento della sottoscrizione](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Passaggi successivi

[Caricare, codificare ed eseguire lo streaming](stream-files-tutorial-with-api.md)

