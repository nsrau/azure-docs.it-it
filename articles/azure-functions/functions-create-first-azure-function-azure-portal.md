---
title: Creare una funzione dal portale di Azure | Documentazione Microsoft
description: "È possibile creare la prima funzione di Azure, un&quot;applicazione senza server, in meno di due minuti."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 8d7823b6ecbc1a762839fc620f76d0bf4687d8fa
ms.openlocfilehash: 5b33a36a3415efd2bc4673999806aa797f4a1c9f


---
# <a name="create-a-function-from-the-azure-portal"></a>Creare una funzione dal portale di Azure
## <a name="overview"></a>Panoramica
Funzioni di Azure offre un'esperienza di calcolo on demand guidata dagli eventi che estende la piattaforma applicativa di Azure con funzionalità per l'implementazione di codice attivato da eventi generati in altri servizi di Azure, prodotti SaaS e sistemi locali. Con Funzioni di Azure le applicazioni possono essere ridimensionate on demand e usano un modello di pagamento in base al consumo delle risorse. Funzioni di Azure permette di creare unità di codice pianificate o attivate implementate in vari linguaggi di programmazione. Per altre informazioni su Funzioni di Azure, vedere [Panoramica di Funzioni di Azure](functions-overview.md).

Questo argomento mostra come usare il portale di Azure per creare una semplice funzione Node.js "hello world" di Azure richiamata da un trigger HTTP. Per poter creare una funzione nel portale di Azure, è prima necessario creare in modo esplicito un'app per le funzioni nel servizio app di Azure. Per fare in modo che l'app per le funzioni venga creata automaticamente, vedere l' [altra esercitazione introduttiva su Funzioni di Azure](functions-create-first-azure-function.md), che offre un'esperienza introduttiva più semplice e include un video.

## <a name="create-a-function-app"></a>Creare un'app per le funzioni
Un'app per le funzioni ospita l'esecuzione delle funzioni in Azure. Seguire questa procedura per creare un'app per le funzioni nel portale di Azure.

Per poter creare la prima funzione, è necessario avere un account Azure attivo. Se non si ha già un account Azure, [sono disponibili account gratuiti](https://azure.microsoft.com/free/).

1. Passare al [portale di Azure](https://portal.azure.com) e accedere con il proprio account Azure.
2. Fare clic su **+Nuovo** > **Calcolo** > **App per le funzioni**, selezionare la **sottoscrizione**, immettere un **nome** univoco per identificare l'app per le funzioni e quindi specificare le impostazioni seguenti:
   
   * **[Gruppo di risorse](../azure-resource-manager/resource-group-overview.md)**: selezionare **Crea nuovo** e immettere un nome per il nuovo gruppo di risorse. È anche possibile scegliere un gruppo di risorse esistente. In tal caso, tuttavia, potrebbe non essere possibile creare un piano di servizio app basato sul consumo per l'app per le funzioni.
   * **[Piano di hosting](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)** può avere uno dei valori seguenti: 
     * **Piano a consumo**: tipo di piano predefinito per Funzioni di Azure. Quando si sceglie un piano a consumo, occorre anche scegliere la **località** e impostare il valore di **Allocazione memoria (MB)**. Per informazioni su come l'allocazione di memoria influisce sui costi, vedere i [prezzi di Funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/). 
     * **Piano di Servizio app**: se si sceglie un piano di servizio app è necessario creare un **piano di servizio app/località** o selezionarne uno esistente. Queste impostazioni determinano [la località, le funzionalità, i costi e le risorse di calcolo](https://azure.microsoft.com/pricing/details/app-service/) associati all'app.  
   * **Account di archiviazione**: ogni app per le funzioni richiede un account di archiviazione. È possibile scegliere un account di archiviazione esistente o crearne uno nuovo. 
     
     ![Creare una nuova app per le funzioni nel portale di Azure](./media/functions-create-first-azure-function-azure-portal/function-app-create-flow.png)

3. Fare clic su **Crea** per effettuare il provisioning della nuova app per le funzioni e distribuirla.  

Dopo aver effettuato il provisioning dell'app per le funzioni è possibile creare la prima funzione.

## <a name="create-a-function"></a>Creare una funzione
Seguire questa procedura per creare una funzione dall'avvio rapido di Funzioni di Azure.

1. Nella scheda **Avvio rapido** fare clic su **WebHook e API** e **JavaScript** e quindi scegliere **Crea funzione**. Verrà creata una nuova funzione Node.js predefinita. 
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

2. (Facoltativo) A questo punto dell'avvio rapido è possibile scegliere di seguire una demo introduttiva sulle funzionalità di Funzioni di Azure nel portale.    Dopo aver completato o ignorato la demo introduttiva, è possibile testare la nuova funzione usando il trigger HTTP.

## <a name="test-the-function"></a>Testare la funzione
Grazie al codice funzionale contenuto negli avvii rapidi di Funzioni di Azure, è possibile testare immediatamente la nuova funzione.

1. Nella scheda **Sviluppo** esaminare la finestra **Codice** e osservare che questo codice Node.js prevede una richiesta HTTP con un valore *name* passato nel corpo del messaggio o in una stringa di query. Durante l'esecuzione della funzione, questo valore viene restituito nel messaggio di risposta.
   
2. Fare clic su **Test** per visualizzare il pannello di richiesta di test HTTP predefinito per la funzione.
 
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-develop-tab-testing.png)

2. Nella casella di testo **Corpo della richiesta** modificare il valore della proprietà *name* specificando un nome personalizzato e quindi fare clic su **Esegui**. L'esecuzione viene attivata da una richiesta HTTP di test, le informazioni vengono scritte nei log in streaming e in **Output**viene visualizzata la risposta "hello". 

3. Per attivare l'esecuzione della stessa funzione da un'altra finestra o scheda del browser, copiare il valore di **URL funzione** dalla scheda **Sviluppo** e incollarlo nella barra degli indirizzi di un browser. Aggiungere quindi il valore della stringa di query `&name=yourname` all'URL e premere INVIO. Nei log vengono scritte le stesse informazioni e il browser visualizza la risposta "hello" come prima.

## <a name="next-steps"></a>Passaggi successivi
La guida introduttiva mostra un'esecuzione semplice di una funzione di base attivata da HTTP. Per altre informazioni sull'uso di Funzioni di Azure nelle app, vedere gli argomenti seguenti:

* [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)  
   Informazioni di riferimento per programmatori in merito alla codifica delle funzioni e alla definizione di trigger e associazioni.
* [Test di Funzioni di Azure](functions-test-a-function.md)  
   Descrive diversi strumenti e tecniche per il test delle funzioni.
* [Come aumentare le prestazioni di Funzioni di Azure](functions-scale.md)  
  Presenta i piani di servizio disponibili con Funzioni di Azure, tra cui il piano di servizio app basato sul consumo, e spiega come scegliere quello più appropriato. 
* [Che cos'è Servizio app di Azure?](../app-service/app-service-value-prop-what-is.md)  
   Funzioni di Azure sfrutta la piattaforma del servizio app di Azure per le funzionalità di base, ad esempio distribuzioni, variabili di ambiente e diagnostica. 

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO3-->


