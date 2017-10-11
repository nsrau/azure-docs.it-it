---
title: Aggiungere il connettore Office 365 Outlook nelle app per la logica | Microsoft Docs
description: Creare app per la logica con il connettore Office 365 per consentire l'interazione con Office 365. Ad esempio, per creare, modificare e aggiornare contatti ed elementi del calendario.
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2f6cc2c-bba2-493a-b0ba-841785462a80
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 5335dae62e61659b68e8befb4ed0d404dffb800c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Guida introduttiva al connettore Outlook di Office 365
Il connettore Office 365 Outlook consente l'interazione con Outlook in Office 365. Usare questo connettore per creare, modificare e aggiornare i contatti e gli elementi del calendario e anche per ottenere, inviare e rispondere ai messaggio di posta elettronica.

Con Office 365 Outlook è possibile:

* Creare il flusso di lavoro usando le funzionalità di posta elettronica e calendario in Office 365. 
* Usare i trigger per avviare il flusso di lavoro quando è presente un nuovo messaggio di posta elettronica, quando viene aggiornato un elemento del calendario e altro ancora.
* Usare azioni per inviare un messaggio di posta elettronica, creare un nuovo evento del calendario e altro ancora. Ad esempio, quando è presente un nuovo oggetto in Salesforce (trigger), inviare un messaggio di posta elettronica a Office 365 Outlook (azione). 

Questo argomento illustra come usare il connettore Office 365 Outlook in un'app per la logica ed elenca i trigger e le azioni.

> [!NOTE]
> Questa versione dell'articolo si applica alla la disponibilità generale delle app per la logica.
> 
> 

Per altre informazioni sulle app per la logica, vedere [Cosa sono le app per la logica](../logic-apps/logic-apps-what-are-logic-apps.md) e [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-office-365"></a>Connettersi a Office 365
Prima che l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una connessione fornisce la connettività tra un'app per la logica e un altro servizio. Ad esempio, per connettersi a Office 365 Outlook, è necessaria prima di tutto una *connessione* a Office 365. Per creare una connessione, immettere le credenziali che si usano normalmente per accedere al servizio a cui si vuole connettersi. Pertanto, per creare la connessione a Office 365 Outlook, immettere le credenziali dell'account Office 365 Outlook.

## <a name="create-the-connection"></a>Creare la connessione
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Usare un trigger
Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. I trigger eseguono il "polling" del servizio agli intervalli e con la frequenza desiderati. [Altre informazioni sui trigger](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Nell'app per la logica digitare "office 365" per ottenere l'elenco dei trigger:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Selezionare **Office 365 Outlook - All'avvio imminente di un prossimo evento**. Se esiste già una connessione, selezionare un calendario dall'elenco a discesa.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Se viene chiesto di effettuare l'accesso, immettere i dettagli di accesso per creare la connessione. La sezione [Creare la connessione](connectors-create-api-office365-outlook.md#create-the-connection) di questo argomento elenca i passaggi necessari. 
   
   > [!NOTE]
   > In questo esempio l'app per la logica viene eseguita quando viene aggiornato un evento del calendario. Per vedere i risultati del trigger, aggiungere un'altra azione che invia un SMS al proprio cellulare. Ad esempio, aggiungere l'azione di Twilio *Send message* (Invia messaggio) che invia un SMS 15 minuti prima dell'avvio dell'evento del calendario. 
   > 
   > 
3. Selezionare il pulsante **Modifica** e impostare i valori **Frequenza** e **Intervallo**. Ad esempio, se si vuole che il trigger esegua il poll ogni 15 minuti, impostare **Frequenza** su **Minuto** e **Intervallo** su **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. Scegliere **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare le modifiche. L'app per la logica viene salvata e può essere attivata automaticamente.

## <a name="use-an-action"></a>Usare un'azione
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Altre informazioni sulle azioni](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Selezionare il segno più. Sono disponibili varie opzioni: **Aggiungi un'azione**, **Aggiungi una condizione** e le opzioni in **Altro**.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Selezionare **Aggiungi un'azione**.
3. Nella casella di testo digitare "office 365" per ottenere l'elenco di tutte le azioni disponibili.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. Nell'esempio scegliere **Office 365 Outlook - Crea contatto**. Se esiste già una connessione, scegliere l'**ID cartella**, il **nome** e le altre proprietà:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Se viene richiesto di inserire le informazioni di connessione, immettere i dettagli per creare la connessione. La sezione [Creare la connessione](connectors-create-api-office365-outlook.md#create-the-connection) di questo argomento descrive queste proprietà. 
   
   > [!NOTE]
   > In questo esempio si crea un nuovo contatto in Office 365 Outlook. Per creare il contatto è possibile usare l'output di un altro trigger. Ad esempio, aggiungere il trigger di SalesForce *When an object is created* (Quando viene creato un oggetto). Aggiungere quindi l'azione di Office 365 Outlook *Crea contatto* che usa i campi di SalesForce per creare il nuovo contatto in Office 365. 
   > 
   > 
5. Scegliere **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare le modifiche. L'app per la logica viene salvata e può essere attivata automaticamente.

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/office365connector/). 

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md). Esplorare gli altri connettori disponibili nelle app per la logica nell' [elenco delle API](apis-list.md).

