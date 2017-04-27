---
title: Creare la prima funzione nel portale di Azure | Microsoft Docs
description: Benvenuti in Azure. Creare la prima funzione di Azure nel portale di Azure.
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
ms.date: 04/10/2017
ms.author: glenga
ms.custom: welcome-email
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7542280ca6dbe1a8d110155e521228d675c0d994
ms.lasthandoff: 04/12/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Creare la prima funzione nel portale di Azure

Questo argomento illustra come usare le funzioni di Azure per creare una funzione "hello world" richiamata da una richiesta HTTP. Prima di creare una funzione nel portale di Azure, è necessario creare un'app per le funzioni per l'esecuzione senza server della funzione.

Per completare questa Guida introduttiva, è necessario disporre di un account di Azure. Sono disponibili [account gratuiti](https://azure.microsoft.com/free/). È anche possibile [provare Funzioni di Azure](https://azure.microsoft.com/try/app-service/functions/) senza che sia necessario la registrazione in Azure.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al [Portale di Azure](https://portal.azure.com/).

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Per altre informazioni, vedere [Creare un'app per le funzioni dal portale di Azure](functions-create-function-app-portal.md).

## <a name="create-a-function"></a>Creare una funzione
Questi passaggi consentono di creare una funzione nella nuova app per le funzioni usando la Guida introduttiva a Funzioni di Azure.

1. Fare clic sul pulsante **Nuovo** quindi su **WebHook e API**, scegliere un linguaggio per la funzione e fare clic su **Crea funzione**. Viene creata una funzione nel linguaggio prescelto usando il modello di funzione HTTP attivato.  
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

Dopo aver creato la funzione, è possibile eseguirne il test inviando una richiesta HTTP.

## <a name="test-the-function"></a>Testare la funzione

Poiché i modelli di funzione contengono codice funzionante, è possibile eseguire il test della funzione direttamente nel portale.

1. Nell'app per le funzioni, fare clic sulla nuova funzione ed esaminare il codice del modello. Notare che la funzione prevede una richiesta HTTP con un valore *name* passato nel corpo del messaggio o in una stringa di query. Durante l'esecuzione della funzione, questo valore viene restituito nel messaggio di risposta. L'esempio riportato di seguito è una funzione JavaScript.
   
2. Fare clic su **Esegui** per eseguire la funzione. L'esecuzione viene attivata da una richiesta HTTP di test, le informazioni vengono scritte nei log e in **Output** viene visualizzata la risposta "hello" nella scheda **Test**.
 
    ![](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

3. Nella casella di testo **Corpo della richiesta** modificare il valore della proprietà *name* specificando un nome personalizzato e quindi fare nuovamente clic su **Esegui**. Questa volta la risposta in **Output** contiene il nome dell'utente.   

4. Per attivare l'esecuzione della stessa funzione da uno strumento di test HTTP o da un'altra finestra del browser, fare clic su **</> Recupera URL della funzione** copiare l'URL di richiesta e incollarlo nella barra degli indirizzi del browser o dello strumento. Aggiungere quindi il valore della stringa di query `&name=yourname` all'URL ed eseguire la richiesta. Le stesse informazioni vengono scritte nei log e la stessa stringa è contenuta nel corpo del messaggio di risposta.

    ![](./media/functions-create-first-azure-function/function-app-browser-testing.png)


## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


