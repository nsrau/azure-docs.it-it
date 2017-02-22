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
ms.date: 02/09/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 7743bfb98552f7fa2334d8daa6a6f6935969f393
ms.openlocfilehash: d76630e0be4a021720d88e6d7e64cf2258f84266


---
# <a name="create-your-first-azure-function"></a>Creare la prima funzione di Azure
## <a name="overview"></a>Panoramica
Funzioni di Azure offre un'esperienza di calcolo on demand guidata dagli eventi che estende la piattaforma applicativa di Azure con funzionalità per l'implementazione di codice attivato da eventi generati in altri servizi di Azure, prodotti SaaS e sistemi locali. Con Funzioni di Azure le applicazioni possono essere ridimensionate on demand e usano un modello di pagamento in base al consumo delle risorse. Funzioni di Azure permette di creare unità di codice pianificate o attivate implementate in vari linguaggi di programmazione. Per altre informazioni su Funzioni di Azure, vedere [Panoramica di Funzioni di Azure](functions-overview.md).

Questo argomento illustra come usare l'avvio rapido di Funzioni di Azure nel portale per creare una semplice funzione JavaScript "hello world" richiamata da un trigger HTTP. È anche possibile guardare un breve video per scoprire come eseguire questi passaggi nel portale.

## <a name="watch-the-video"></a>Video
Il video seguente mostra come eseguire i passaggi di base di questa esercitazione. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>Creare una funzione dall'avvio rapido
Un'app per le funzioni ospita l'esecuzione delle funzioni in Azure. Seguire questi passaggi per creare un'app per le funzioni con la nuova funzione. L'app per le funzioni viene creata con una configurazione predefinita. Per un esempio di come creare in modo esplicito l'app per le funzioni, vedere [l'altra esercitazione di avvio rapido di Funzioni di Azure](functions-create-first-azure-function-azure-portal.md).

Per poter creare la prima funzione, è necessario avere un account Azure attivo. Se non si possiede già un account Azure, [sono disponibili account gratuiti](https://azure.microsoft.com/free/).

1. Passare al [portale di Funzioni di Azure](https://functions.azure.com/signin) e accedere con l'account Azure.
2. In **Nome** digitare un nome univoco per la nuova app per le funzioni o accettare quello generato automaticamente, selezionare la voce preferita in **Area** e quindi fare clic su **Creazione e introduzione**. Si noti che è necessario immettere un nome valido, che può contenere solo lettere, numeri e trattini. Il carattere di sottolineatura (**_**) non è consentito.
3. Nella scheda **Avvio rapido** fare clic su **WebHook e API** e **JavaScript** e quindi scegliere **Crea funzione**. Verrà creata una nuova funzione JavaScript predefinita. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (Facoltativo) A questo punto dell'avvio rapido è possibile scegliere di seguire una demo introduttiva sulle funzionalità di Funzioni di Azure nel portale. Dopo aver completato o ignorato la demo introduttiva, è possibile testare la nuova funzione usando il trigger HTTP.

## <a name="test-the-function"></a>Testare la funzione
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Feb17_HO2-->


