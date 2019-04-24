---
title: Monitorare gli eventi di Servizi multimediali di Azure con Griglia di eventi e il portale | Microsoft Docs
description: Questo articolo illustra come eseguire la sottoscrizione a Griglia di eventi per monitorare gli eventi di Servizi multimediali di Azure.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: servizi multimediali di Azure, streaming, trasmissione, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: d4592c93cb7969c45a107d7365a1b9dabf11f412
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60326530"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Creare e monitorare eventi di Servizi multimediali con Griglia di eventi e il portale di Azure

La griglia di eventi di Azure è un servizio di gestione degli eventi per il cloud. Questo servizio utilizza [le sottoscrizioni di eventi](../../event-grid/concepts.md#event-subscriptions) per instradare i messaggi di evento ai sottoscrittori. Gli eventi di Servizi multimediali contengono tutte le informazioni necessarie per rispondere alle modifiche dei dati. Un evento di Servizi multimediali è riconoscibile perché la proprietà eventType inizia con "Microsoft.Media". Per altre informazioni, vedere [Schemi di eventi di Servizi multimediali](media-services-event-schemas.md).

In questo articolo si userà il portale di Azure per sottoscrivere gli eventi per l'account di Servizi multimediali di Azure. Si attiveranno quindi gli eventi per visualizzare i risultati. In genere, si inviano eventi a un endpoint che elabora i dati dell'evento e intraprende azioni. Nell'articolo, si inviano eventi a un'app web che raccoglie e visualizza i messaggi.

Al termine, i dati degli eventi saranno stati inviati all'app Web.

## <a name="prerequisites"></a>Prerequisiti 

* Avere una sottoscrizione di Azure attiva.
* Creare un nuovo account di Servizi multimediali in Azure, come descritto in [questa guida introduttiva](create-account-cli-quickstart.md).

## <a name="create-a-message-endpoint"></a>Creare un endpoint del messaggio

Prima di sottoscrivere gli eventi per l'account di Servizi multimediali, creare l'endpoint per il messaggio dell'evento. L'endpoint richiede in genere azioni basate sui dati degli eventi. In questo articolo si distribuisce un'[app Web preesistente](https://github.com/Azure-Samples/azure-event-grid-viewer) che visualizza i messaggi di evento. La soluzione distribuita include un piano di servizio app, un'app Web del servizio app e codice sorgente da GitHub.

1. Selezionare **Distribuisci in Azure** per distribuire la soluzione nella sottoscrizione. Nel portale di Azure specificare i valori per i parametri.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Per il completamento della distribuzione possono essere necessari alcuni minuti. Dopo il completamento della distribuzione, visualizzare l'app Web per assicurarsi che sia in esecuzione. In un Web browser passare a: `https://<your-site-name>.azurewebsites.net`

Se si passa al sito "Azure Event Grid Viewer" ("Visualizzatore Griglia di eventi di Azure"), è possibile osservare come non contenga ancora alcun evento.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Eseguire la sottoscrizione a eventi di Servizi multimediali

Si sottoscrive un argomento per indicare a Griglia di eventi gli eventi di cui si vuole tenere traccia e dove inviare tali eventi.

1. Nel portale selezionare l'account Servizi multimediali e fare clic su **Eventi**.
1. Per inviare gli eventi all'app per visualizzarli, usare un webhook per l'endpoint. 

   ![Selezionare il webhook](./media/monitor-events-portal/select-web-hook.png)

1. La sottoscrizione dell'evento è precompilata con i valori dell'account di Servizi multimediali. 
1. Selezionare "Web Hook" come **tipo di endpoint**.
1. In questo articolo si lascerà selezionata la casella di controllo **Esegui la sottoscrizione di tutti i tipi di eventi**. È tuttavia possibile deselezionarla e applicare un filtro per specifici tipi di evento. 
1. Fare clic sul collegamento **Seleziona un endpoint**.

    Per l'endpoint del webhook, specificare l'URL dell'app Web e aggiungere `api/updates` all'URL della home page. 

1. Premere **Confermare la selezione**.
1. Fare clic su **Crea**.
1. Specificare un nome per la sottoscrizione.

   ![Selezionare i log](./media/monitor-events-portal/create-subscription.png)

1. Visualizzare nuovamente l'app Web e notare che all'app è stato inviato un evento di convalida della sottoscrizione. 

    Griglia di eventi invia l'evento di convalida in modo che l'endpoint possa verificare che voglia ricevere i dati dell'evento. L'endpoint deve impostare `validationResponse` su `validationCode`. Per altre informazioni, vedere [Event Grid security and authentication](../../event-grid/security-authentication.md) (Sicurezza e autenticazione di Griglia di eventi). È possibile visualizzare il codice dell'app Web per vedere come convalida la sottoscrizione.

A questo punto, attivare gli eventi per vedere come Griglia di eventi distribuisce il messaggio nell'endpoint.

## <a name="send-an-event-to-your-endpoint"></a>Inviare un evento all'endpoint

È possibile attivare gli eventi per l'account di Servizi multimediali tramite l'esecuzione di un processo di codifica. È possibile seguire [questa guida introduttiva](stream-files-dotnet-quickstart.md) per codificare un file e iniziare a inviare gli eventi. Se è stata eseguita la sottoscrizione di tutti i tipi di evento, verrà visualizzata una schermata simile alla seguente:

> [!TIP]
> Selezionare l'icona a forma di occhio per espandere i dati dell'evento. Non aggiornare la pagina se si vuole visualizzare tutti gli eventi.

![Visualizzare l'evento della sottoscrizione](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Passaggi successivi

[Caricare, codificare ed eseguire lo streaming](stream-files-tutorial-with-api.md)
