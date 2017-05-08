---
title: Creare una funzione in Azure attivata da un webhook GitHub | Microsoft Docs
description: Usare Funzioni di Azure per creare una funzione senza server che viene richiamata da un webhook GitHub.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/18/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d4354546f3342d65353a86a4cec7d02547ab92e7
ms.lasthandoff: 04/22/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Creare una funzione attivata da un webhook GitHub

Informazioni su come creare una funzione che viene attivata da un webhook GitHub. 

![Creare un'app per le funzioni nel portale di Azure](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

Per questo argomento sono necessarie le risorse create nell'argomento [Creare la prima funzione dal portale di Azure](functions-create-first-azure-function.md).

È necessario anche un account GitHub. È possibile [iscriversi per ottenere un account GitHub gratuito](https://github.com/join), se non se ne ha già uno. 

Per completare tutti i passaggi di questo argomento, saranno sufficienti meno di cinque minuti.

## <a name="find-your-function-app"></a>Trovare l'app per le funzioni    

1. Accedere al [Portale di Azure](https://portal.azure.com/). 

2. Nella barra di ricerca nella parte superiore del portale digitare il nome dell'app per le funzioni e selezionarla dall'elenco.

## <a name="create-function"></a>Creare una funzione attivata da webhook GitHub

1. Nell'app per le funzioni fare clic sul pulsante **+** accanto a **Funzioni**, fare clic sul modello **GitHubWebHook** per il linguaggio desiderato e fare clic su **Crea**.
   
    ![Creare una funzione attivata da webhook GitHub nel portale di Azure.](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. Fare clic su **</> Recupera URL della funzione**, quindi copiare e salvare i valori. Eseguire la stessa operazione per **</> Recupera segreto GitHub**. Questi valori servono per configurare il webhook in GitHub. 

    ![Esaminare il codice funzione](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png) 
         
Viene successivamente creato un webhook nel repository GitHub. 

## <a name="configure-the-webhook"></a>Configurare il webhook
1. In GitHub passare a un repository di cui si è proprietari. È possibile usare anche qualsiasi repository biforcato.
 
2. Fare clic su **Impostazioni**, quindi su **Webhook** e infine su **Aggiungi webhook**.
   
    ![Aggiungere un webhook di GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

3. Incollare l'URL e il segreto della funzione in **Payload URL** (URL payload) e in **Secret** (Segreto) e selezionare **application/json** in **Content type** (Tipo di contenuto).

4. Fare clic su **Let me select individual events** (Consenti selezione di singoli eventi), selezionare **Issue comment** (Commento problema) e fare clic su **Add webhook** (Aggiungi webhook).
   
    ![Impostare l'URL del webhook e il segreto](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

Il webhook ora è configurato per attivare la funzione quando un nuovo commento al problema viene aggiunto. 

## <a name="test-the-function"></a>Testare la funzione
1. Nel repository GitHub aprire la scheda **Issues** (Problemi) in una nuova finestra del browser.

2. Nella nuova finestra fare clic su **New Issue** (Nuovo problema), digitare un titolo, quindi fare clic su **Submit new issue** (Invia nuovo problema). 

2. Nel problema, digitare un commento e fare clic su **Comment (Commento)**. 

3. Nell'altra finestra di GitHub fare clic su **Edit** (Modifica) accanto al nuovo webhook, scorrere verso il basso fino a **Recent Deliveries** (Recapiti recenti) e verificare che la funzione abbia elaborato una richiesta di un webhook. 
 
    ![Impostare l'URL del webhook e il segreto](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-triggered.png)

   La risposta dalla funzione dovrà contenere `New GitHub comment: <Your issue comment text>`.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


