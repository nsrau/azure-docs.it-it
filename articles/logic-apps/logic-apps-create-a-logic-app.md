---
title: Creare flussi di lavoro con la prima app per la logica di Azure | Documentazione Microsoft
description: Introduzione alla connessione di app e servizi SaaS con la prima app per la logica
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: ce1e0cfe7c7f52d521ef3318376af75331323083
ms.openlocfilehash: 178a57624345a4b3e5d73e64ce4ccf81b8b90a88


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>Creare una nuova app per la logica che connette servizi SaaS
Questo argomento illustra come iniziare in pochi minuti a usare le [app per la logica di Azure](logic-apps-what-are-logic-apps.md). Verrà descritto un semplice flusso di lavoro che consente di inviare i tweet di interesse al proprio indirizzo di posta elettronica.

Per usare questo scenario, sono necessari:

* Una sottoscrizione di Azure
* Un account Twitter
* Una cassetta postale ospitata in Office 365 oppure Outlook.com

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>Creare una nuova app per la logica per inviare tramite posta elettronica i tweet
1. Nel [dashboard del portale di Azure](https://portal.azure.com)selezionare **Nuovo**. 
2. Nella barra di ricerca cercare "app per la logica" e quindi selezionare **App per la logica**. È anche possibile selezionare **Nuovo**, **Web e dispositivi mobili** e quindi **App per la logica**. 
3. Immettere un nome per l'app per la logica e selezionare una località, un gruppo di risorse e quindi **Crea**.  Se si seleziona **Aggiungi al dashboard** , al termine della distribuzione l'app per la logica verrà aperta automaticamente.  
4. Dopo aver aperto l'app per la logica per la prima volta, è possibile selezionare un modello come punto di partenza.  Per ora fare clic su **Blank Logic App** (App per la logica vuota) per eseguire la creazione da zero. 
5. Il primo elemento da creare è il trigger,  ovvero l'evento che avvierà l'app per la logica.  Cercare **twitter** nella casella di ricerca del trigger e selezionarlo.
6. Digitare ora un termine di ricerca da usare come trigger.  **Frequenza** e **Intervallo** determineranno la frequenza con cui l'app per la logica verificherà la presenza di nuovi tweet, restituendo tutti i tweet di tale periodo di tempo.
    ![Ricerca di Twitter](media/logic-apps-create-a-logic-app/twittersearch.png)
7. Selezionare il pulsante **Nuovo passaggio** e quindi scegliere **Aggiungi un'azione** o **Aggiungi una condizione**
8. Selezionando **Aggiungi un'azione**, è possibile eseguire una ricerca nei [connettori disponibili](../connectors/apis-list.md) per scegliere un'azione. È ad esempio possibile selezionare **Outlook.com - Send Email** (Outlook.com - Invia messaggio di posta elettronica) per inviare un messaggio di posta elettronica da un indirizzo di Outlook.com:  
    ![Actions](media/logic-apps-create-a-logic-app/actions.png)
9. Ora è necessario inserire i parametri per il messaggio di posta elettronica:  ![Parametri](media/logic-apps-create-a-logic-app/parameters.png)
10. È infine possibile selezionare **Salva** per attivare l'app per la logica.

## <a name="manage-your-logic-app-after-creation"></a>Gestire l'app per la logica dopo la creazione
A questo punto l'app per la logica è operativa. L'app verificherà periodicamente la presenza di tweet con il termine di ricerca immesso. Quando troverà un tweet corrispondente, invierà un messaggio di posta elettronica. Infine, ecco come disabilitare l'applicazione e verificarne lo stato.

1. Accedere al [portale di Azure](https://portal.azure.com)
2. Fare clic su **Sfoglia** sul lato sinistro dello schermo e selezionare **App per la logica**.
3. Fare clic sulla nuova app per la logica appena creata per visualizzare informazioni generali e sullo stato corrente.
4. Per modificare la nuova app per la logica, fare clic su **Modifica**.
5. Per disattivare l'app, fare clic su **Disabilita** nella barra dei comandi.
6. Visualizzare le cronologie di esecuzione e trigger per monitorare quando l'app per la logica è in esecuzione.  Per visualizzare i dati più recenti, fare clic su **Aggiorna** .

In meno di 5 minuti è stato possibile configurare una semplice app per la logica eseguita nel cloud. Per altre informazioni sull'uso delle funzionalità delle app per la logica, vedere [Usare le funzionalità delle app per la logica]. Per informazioni sulle definizioni stesse dell'app per la logica, vedere l'articolo relativo alla [creazione di definizioni di app per la logica](../logic-apps/logic-apps-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Usare le funzionalità delle app per la logica]: logic-apps-create-a-logic-app.md



<!--HONumber=Jan17_HO4-->


