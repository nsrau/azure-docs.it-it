---
title: Creare una funzione che viene eseguita in una pianificazione | Microsoft Docs
description: Informazioni su come creare una funzione in Azure eseguita in base a una pianificazione definita.
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 10461bddeb4d5adf4a6e4f65159ba2653dbef8a4
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
#  <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Creare una funzione in Azure attivata da un timer

Informazioni su come usare Funzioni di Azure per creare una funzione eseguita in base a una pianificazione definita. 

![Creare un'app per le funzioni nel portale di Azure](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

Per completare tutti i passaggi di questo argomento, saranno sufficienti meno di cinque minuti.

## <a name="prerequisites"></a>Prerequisiti 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

In questo argomento si creerà una funzione attivata da un timer in un'app per le funzioni esistente. 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>Creare una funzione attivata da un timer

1. Espandere l'app per le funzioni, fare clic sul pulsante **+** accanto a **Funzioni** e fare clic sul modello **TimerTrigger** per il linguaggio desiderato. Usare quindi le impostazioni come indicato nella tabella e fare clic su **Crea**:

    | Impostazione      |  Valore consigliato   | Descrizione                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Dare un nome alla funzione** | TimerTriggerCSharp1 | Definisce il nome della funzione attivata dal timer.
    | **[Pianificazione](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 */1 * * * * | [Espressione CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) a sei campi che pianifica la funzione in modo che venga eseguita ogni minuto. |

    Viene creata una funzione nel linguaggio scelto che verrà eseguita ogni minuto. 

4. Verificare l'esecuzione visualizzando le informazioni di traccia scritte nei log. 

    ![Visualizzatore log di Funzioni nel portale di Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

È possibile ora modificare la pianificazione della funzione in modo che venga eseguita con meno frequenza, ad esempio ogni ora. 

## <a name="update-the-timer-schedule"></a>Aggiornare la pianificazione del timer

1. Espandere la funzione e fare clic su **Integrazione**. È a questo punto che si definiscono i binding di input e di output e si imposta la pianificazione. 

2. Nel campo **Pianificazione** immettere il nuovo valore `0 0 */1 * * *` e quindi fare clic su **Salva**.  

![Pianificazione del timer di aggiornamento di Funzioni nel portale di Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

È ora disponibile una funzione che viene eseguita ogni ora. 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi
È stata creata una funzione eseguita in base a una pianificazione. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Per altre informazioni sui trigger timer, vedere [Pianificare l'esecuzione di codice con Funzioni di Azure](functions-bindings-timer.md). 




