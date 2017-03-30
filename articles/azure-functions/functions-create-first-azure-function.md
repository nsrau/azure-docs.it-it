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
ms.date: 03/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: a797910e286cd2aacf5a8aa6c509e2b0f5f60276
ms.lasthandoff: 03/17/2017


---
# <a name="create-your-first-azure-function"></a>Creare la prima funzione di Azure

Questo argomento illustra come usare l'avvio rapido di Funzioni di Azure nel portale per creare una semplice funzione "hello world" richiamata da una richiesta HTTP. Per altre informazioni su Funzioni di Azure, vedere [Panoramica di Funzioni di Azure](functions-overview.md).

Per iniziare è necessario un account Azure. Sono disponibili [account gratuiti](https://azure.microsoft.com/free/). È anche possibile [provare Funzioni di Azure](https://azure.microsoft.com/try/app-service/functions/) senza dover effettuare la registrazione con Azure.

## <a name="create-a-function-from-the-portal-quickstart"></a>Creare una funzione dall'avvio rapido del portale

1. Passare al [portale di Funzioni di Azure](https://functions.azure.com/signin) e accedere con l'account Azure. 
 
2. In **Nome** digitare un nome univoco per la nuova app per le funzioni o accettare quello generato automaticamente, selezionare la voce preferita in **Area** e quindi fare clic su **Creazione e introduzione**. Un nome valido può contenere solo lettere, numeri e segni meno. Il carattere di sottolineatura (**_**) non è consentito.

3. Nella scheda **Avvio rapido** fare clic su **WebHook e API**, scegliere un linguaggio per la funzione e quindi fare clic su **Crea funzione**. Verrà creata una nuova funzione predefinita nel linguaggio scelto. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

4. (Facoltativo) A questo punto dell'avvio rapido è possibile scegliere di seguire una demo introduttiva sulle funzionalità di Funzioni di Azure nel portale. Dopo aver completato o ignorato la presentazione è possibile testare la nuova funzione inviando una richiesta HTTP.

## <a name="test-the-function"></a>Testare la funzione
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="watch-the-video"></a>Video
Il video seguente mostra come eseguire i passaggi di base di questa esercitazione. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 


## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


