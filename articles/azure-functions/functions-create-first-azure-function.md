---
title: Creare la prima funzione di Azure | Documentazione Microsoft
description: "È possibile creare la prima funzione di Azure, un&quot;applicazione senza server, in meno di due minuti."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 4a1669e7-233e-4ea2-9b83-b8624f2dbe59
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/08/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e446766c0f3d19a8cce585d708e8e7a809593408


---
# <a name="create-your-first-azure-function"></a>Creare la prima funzione di Azure
## <a name="overview"></a>Overview
Funzioni di Azure offre un'esperienza di calcolo on demand guidata dagli eventi che estende la piattaforma applicativa di Azure con funzionalità per l'implementazione di codice attivato da eventi generati in altri servizi di Azure, prodotti SaaS e sistemi locali. Con Funzioni di Azure le applicazioni possono essere ridimensionate on demand e usano un modello di pagamento in base al consumo delle risorse. Funzioni di Azure permette di creare unità pianificate o attivate del codice implementate in un'ampia gamma di linguaggi di programmazione. Per altre informazioni su Funzioni di Azure, vedere [Panoramica di Funzioni di Azure](functions-overview.md).

Questo argomento illustra come usare l'avvio rapido di Funzioni di Azure nel portale per creare una semplice funzione Node.js "hello world" richiamata da un trigger HTTP. È anche possibile guardare un breve video per scoprire come eseguire questi passaggi nel portale.

## <a name="watch-the-video"></a>Video
Il video seguente mostra come eseguire i passaggi di base di questa esercitazione. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>Creare una funzione dall'avvio rapido
Un'app per le funzioni ospita l'esecuzione delle funzioni in Azure. Seguire questi passaggi per creare una nuova app per le funzioni, nonché la nuova funzione. La nuova app per le funzioni viene creata con una configurazione predefinita. Per un esempio di come creare in modo esplicito l'app per le funzioni, vedere [l'altra esercitazione di avvio rapido di Funzioni di Azure](functions-create-first-azure-function-azure-portal.md).

Per poter creare la prima funzione, è necessario avere un account Azure attivo. Se non si possiede già un account Azure, [sono disponibili account gratuiti](https://azure.microsoft.com/free/).

1. Passare al [portale di Funzioni di Azure](https://functions.azure.com/signin) e accedere con l'account Azure.
2. In **Nome** digitare un nome univoco per la nuova app per le funzioni o accettare quello generato automaticamente, selezionare la voce preferita in **Area** e quindi fare clic su **Creazione e introduzione**. 
3. Nella scheda **Avvio rapido** fare clic su **WebHook e API** e **JavaScript** e quindi scegliere **Crea funzione**. Verrà creata una nuova funzione Node.js predefinita. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (Facoltativo) A questo punto dell'avvio rapido è possibile scegliere di seguire una demo introduttiva sulle funzionalità di Funzioni di Azure nel portale.    Dopo aver completato o ignorato la demo introduttiva, è possibile testare la nuova funzione usando il trigger HTTP.

## <a name="test-the-function"></a>Testare la funzione
Grazie al codice funzionale contenuto negli avvii rapidi di Funzioni di Azure, è possibile testare immediatamente la nuova funzione.

1. Nella scheda **Sviluppo** esaminare la finestra **Codice** e osservare che questo codice Node.js prevede una richiesta HTTP con un valore *name* passato nel corpo del messaggio o in una stringa di query. Durante l'esecuzione della funzione, questo valore viene restituito nel messaggio di risposta.
   
    ![](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)
2. Scorrere verso il basso fino alla casella di testo **Corpo della richiesta**, modificare il valore della proprietà *name* specificando un nome personalizzato e quindi fare clic su **Esegui**. Si noterà che l'esecuzione viene attivata da una richiesta HTTP di test, le informazioni vengono scritte nei log in streaming e in **Output**viene visualizzata la risposta "hello". 
3. Per attivare l'esecuzione della stessa funzione da un'altra finestra o scheda del browser, copiare il valore di **URL funzione** dalla scheda **Sviluppo** e incollarlo nella barra degli indirizzi di un browser, quindi aggiungere il valore della stringa di query `&name=yourname` e premere INVIO. Nei log vengono scritte le stesse informazioni e il browser visualizza la risposta "hello" come prima.

## <a name="next-steps"></a>Passaggi successivi
L'avvio rapido mostra un'esecuzione molto semplice di una funzione di base attivata da HTTP. Per altre informazioni su come sfruttare tutte le potenzialità di Funzioni di Azure nelle proprie app, vedere gli argomenti seguenti.

* [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)  
   Informazioni di riferimento per programmatori in merito alla codifica delle funzioni e alla definizione di trigger e associazioni.
* [Test di Funzioni di Azure](functions-test-a-function.md)  
   Descrive diversi strumenti e tecniche per il test delle funzioni.
* [Come aumentare le prestazioni di Funzioni di Azure](functions-scale.md)  
   Descrive i piani di servizio disponibili con Funzioni di Azure, tra cui il piano di servizio dinamico, e come scegliere quello più appropriato. 
* [Che cos'è Servizio app di Azure?](../app-service/app-service-value-prop-what-is.md)  
   Funzioni di Azure sfrutta la piattaforma Servizio app di Azure per funzionalità di base come distribuzioni, variabili di ambiente e diagnostica. 

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO2-->


