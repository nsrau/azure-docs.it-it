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
ms.date: 03/15/2017
ms.author: glenga
ms.custom: welcome-email
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 6bb15dfd0e2c40b99d06ef2d695e3ba7ca78e91b
ms.lasthandoff: 03/22/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Creare la prima funzione nel portale di Azure

Questo argomento illustra come creare una semplice funzione "hello world" di Azure richiamata da una richiesta HTTP. Prima di creare una funzione nel portale di Azure, è necessario creare un'app per le funzioni nel Servizio app di Azure per l'esecuzione della funzione.

Per completare questa Guida introduttiva, è necessario disporre di un account di Azure. Sono disponibili [account gratuiti](https://azure.microsoft.com/free/). È anche possibile [provare Funzioni di Azure](https://azure.microsoft.com/try/app-service/functions/) senza che sia necessario la registrazione in Azure.

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Per altre informazioni, vedere [Creare un'app per le funzioni dal portale di Azure](functions-create-function-app-portal.md).

## <a name="create-a-function"></a>Creare una funzione
Questi passaggi consentono di creare una funzione nella nuova app per le funzioni usando la Guida introduttiva a Funzioni di Azure.

1. Nella scheda **Avvio rapido** fare clic su **WebHook e API**, scegliere un linguaggio per la funzione e quindi fare clic su **Crea funzione**. Verrà creata una nuova funzione predefinita nel linguaggio scelto.  
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

4. (Facoltativo) A questo punto dell'avvio rapido è possibile scegliere di seguire una demo introduttiva sulle funzionalità di Funzioni di Azure nel portale. Dopo aver completato o ignorato la presentazione, è possibile testare la nuova funzione inviando una richiesta HTTP.


## <a name="test-the-function"></a>Testare la funzione
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


