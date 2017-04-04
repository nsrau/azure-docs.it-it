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
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 221f1b9f0985bbaf0553f6ca01f0f048b9976315
ms.lasthandoff: 03/28/2017


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>Creare una nuova app per la logica che connette servizi SaaS
Questo argomento illustra come iniziare in pochi minuti a usare le [app per la logica di Azure](logic-apps-what-are-logic-apps.md). Verrà descritto un semplice flusso di lavoro che consente di inviare i tweet di interesse al proprio indirizzo di posta elettronica.

Per usare questo scenario, sono necessari:

* Una sottoscrizione di Azure
* Un account Twitter
* Un account Outlook.com o Office 365 Outlook

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>Creare una nuova app per la logica per inviare tramite posta elettronica i tweet

1. Accedere al [portale di Azure](https://portal.azure.com). 

2. Dal menu a sinistra scegliere **Nuovo** > **Enterprise Integration** > **App per la logica**.

    È anche possibile scegliere **Nuovo**, digitare `logic app` nella casella di ricerca e quindi premere INVIO. Scegliere **App per la logica** > **Crea**.

3. Immettere un nome per l'app per la logica, selezionare la sottoscrizione di Azure, creare o selezionare un gruppo di risorse di Azure, selezionare una località e scegliere **Crea**.

    Se si seleziona **Aggiungi al dashboard**, dopo la distribuzione verrà aperta automaticamente l'app per la logica.

4. Quando si apre l'app per la logica per la prima volta, è possibile selezionare un modello come punto di partenza.
Per ora fare clic su **Blank Logic App** (App per la logica vuota) per eseguire la creazione da zero. 

5. Il primo elemento da creare è il trigger, ovvero l'evento che avvia l'app per la logica. Nella casella di ricerca immettere **twitter** e selezionare **Quando viene pubblicato un nuovo tweet**. Accedere con il nome utente e la password dell'account Twitter.

6. Digitare quindi un termine di ricerca per attivare l'app per la logica.

   ![Ricerca di Twitter](media/logic-apps-create-a-logic-app/twittersearch.png)

    **Frequenza** e **Intervallo** determinano la frequenza con cui l'app per la logica verificherà la presenza di nuovi tweet, restituendo tutti i tweet di tale periodo di tempo.

7. Selezionare **Nuovo passaggio** e scegliere **Aggiungi un'azione** o **Aggiungi una condizione**.

    Selezionando **Aggiungi un'azione** è possibile eseguire una ricerca nei [connettori disponibili](../connectors/apis-list.md) per scegliere un'azione. 

8. Nella casella di ricerca immettere **outlook** e selezionare **Invia un messaggio di posta elettronica** per inviare un messaggio di posta elettronica dal proprio account Outlook a qualsiasi indirizzo di posta elettronica specificato.

   ![Azioni](media/logic-apps-create-a-logic-app/actions.png)

9. Ora è necessario inserire i parametri desiderati per il messaggio di posta elettronica: 

   ![Parametri](media/logic-apps-create-a-logic-app/parameters.png)

10. È infine possibile selezionare **Salva** per attivare l'app per la logica.

## <a name="manage-your-logic-app-after-creation"></a>Gestire l'app per la logica dopo la creazione

A questo punto l'app per la logica è operativa. L'app verificherà periodicamente la presenza di tweet con il termine di ricerca immesso. Quando troverà un tweet corrispondente, invierà un messaggio di posta elettronica. Infine, ecco come disabilitare l'applicazione e verificarne lo stato.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel menu a sinistra fare clic su **Altri servizi**. In **Enterprise Integration** scegliere **App per la logica**. Selezionare l'app per la logica.

    *    Per visualizzare lo stato, la cronologia di esecuzione e le informazioni generali dell'app, scegliere **Panoramica** dal menu delle app per la logica. Se non si trovano i dati previsti, scegliere **Aggiorna** sulla barra dei comandi.

    *    Per modificare l'app, scegliere **Progettazione app per la logica** dal menu delle app per la logica.

    *    Per disattivare temporaneamente l'app, scegliere **Panoramica** dal menu delle app per la logica. Sulla barra dei comandi fare clic su **Disabilita**.

    *    Per eliminare l'app, scegliere **Panoramica** dal menu delle app per la logica. 
    Sulla barra dei comandi scegliere **Elimina**. Immettere il nome dell'app per la logica e scegliere **Elimina**.

In meno di 5 minuti è stato possibile configurare una semplice app per la logica eseguita nel cloud. Per altre informazioni sull'uso delle funzionalità delle app per la logica, vedere [Usare le funzionalità delle app per la logica]. Per informazioni sulle definizioni stesse dell'app per la logica, vedere l'articolo relativo alla [creazione di definizioni di app per la logica](../logic-apps/logic-apps-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Usare le funzionalità delle app per la logica]: logic-apps-create-a-logic-app.md
