---
title: Creare il primo flusso di lavoro tra app cloud e servizi cloud - App per la logica di Azure | Microsoft Docs
description: Automatizzare i processi aziendali per scenari di integrazione di sistemi e di Enterprise Application Integration (EAI) creando ed eseguendo flussi di lavoro in App per la logica di Azure
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
keywords: flusso di lavoro, app cloud, servizi cloud, processi aziendali, integrazione di sistemi, enterprise application integration, EAI
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/31/2017
ms.author: jehollan; estfan; LADocs
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6634f0ee88e68f2fcb09fd7534a88677e8efa029
ms.lasthandoff: 04/03/2017

---

# <a name="create-your-first-logic-app-workflow-to-automate-processes-between-cloud-apps-and-cloud-services"></a>Creare il primo flusso di lavoro di app per la logica per automatizzare i processi tra app cloud e servizi cloud

Senza scrivere codice, è possibile automatizzare i processi aziendali in modo più semplice e rapido durante la creazione ed esecuzione di flussi di lavoro con [App per la logica di Azure](logic-apps-what-are-logic-apps.md). Questo primo esempio illustra come creare un flusso di lavoro di base per app per la logica che controlla un feed RSS alla ricerca di nuovi contenuti in un sito Web. Quando vengono visualizzati nuovi elementi nel feed del sito Web, l'app per la logica invia un messaggio di posta elettronica da un account Outlook o Gmail.

Per creare ed eseguire un'app per la logica, sono necessari questi elementi:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free/). In alternativa, è possibile [iscriversi per ottenere una sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/pricing/purchase-options/).

  La sottoscrizione di Azure viene usata per la fatturazione dell'utilizzo delle app per la logica. Per altre informazioni, vedere le pagine relative alla [misurazione dell'utilizzo](../logic-apps/logic-apps-pricing.md) e ai [prezzi](https://azure.microsoft.com/pricing/details/logic-apps) per App per la logica di Azure.

Questo esempio richiede anche gli elementi seguenti:

* Account Outlook.com, Office 365 Outlook o Gmail

    > [!TIP]
    > Se si ha un [account Microsoft](https://account.microsoft.com/account) personale, si ha un account Outlook.com. Se invece si una un account Azure aziendale o dell'istituto di istruzione, si ha un account **Office 365 Outlook**.

* Un collegamento al feed RSS del sito Web. Questo esempio usa il feed RSS per il [sito Web MSDN Channel 9](https://channel9.msdn.com/): `https://s.ch9.ms/Feeds/RSS`

## <a name="add-a-trigger-that-starts-your-workflow"></a>Aggiungere un trigger che avvia il flusso di lavoro

Un [*trigger*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) è un evento che avvia il flusso di lavoro dell'app per la logica ed è il primo elemento necessario per l'app per la logica.

1. Accedere al [Portale di Azure](https://portal.azure.com "Portale di Azure").

2. Dal menu a sinistra scegliere **Nuovo** > **Enterprise Integration** > **App per la logica**, come mostrato qui:

     ![Portale di Azure, Nuovo, Enterprise Integration, App per la logica](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

   > [!TIP]
   > È anche possibile scegliere **Nuovo**, digitare `logic app` nella casella di ricerca e quindi premere INVIO. Scegliere quindi **App per la logica** > **Crea**.

3. Assegnare un nome all'app per la logica e selezionare la sottoscrizione di Azure. Creare quindi o selezionare un gruppo di risorse di Azure, che consente di organizzare e gestire le risorse di Azure correlate. Selezionare infine la località del data center per l'hosting dell'app per la logica. Quando si è pronti, scegliere **Aggiungi al dashboard** e quindi **Crea**.

     ![Dettagli dell'app per la logica](media/logic-apps-create-a-logic-app/logic-app-settings.png)

   > [!NOTE]
   > Quando si seleziona **Aggiungi al dashboard**, l'app per la logica viene visualizzata nel dashboard di Azure dopo la distribuzione e viene aperta automaticamente. Se l'app per la logica non viene visualizzata nel dashboard, nel riquadro **Tutte le risorse** scegliere **Vedi altri** e selezionare l'app per la logica. Dal menu a sinistra scegliere **More services** (Altri servizi). In **Enterprise Integration** scegliere **App per la logica**, quindi selezionare l'app per la logica.

4. Quando si apre l'app per la logica per la prima volta, la finestra di progettazione dell'app per la logica mostra i modelli disponibili per iniziare. Per il momento, scegliere **App per la logica vuota**, per consentire la creazione di un'app per la logica completamente nuova.

    Viene aperta la finestra di progettazione dell'app per la logica, che mostra i servizi disponibili e i *trigger* che possono essere usati nell'app per la logica.

5. Nella casella di ricerca digitare `RSS`, quindi selezionare questo trigger: **RSS - Quando viene pubblicato un elemento del feed** 

    ![Trigger di RSS](media/logic-apps-create-a-logic-app/rss-trigger.png)

6. Immettere il collegamento per il feed RSS del sito Web di cui si vuole tenere traccia. 

     È anche possibile cambiare i valori per **Frequenza** e **Intervallo**. 
     Queste impostazioni determinano la frequenza con cui l'app per la logica verifica la presenza di nuovi elementi e restituisce tutti gli elementi trovati durante tale periodo di tempo.

     Per questo esempio la verifica viene eseguita ogni giorno, alla ricerca di nuovi elementi inseriti nel sito Web MSDN Channel 9.

     ![Configurare un trigger con feed RSS, frequenza e intervallo](media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

7. Salvare il lavoro, per il momento. Sulla barra dei comandi della finestra di progettazione scegliere **Salva**.

   ![Salvare l'app per la logica](media/logic-apps-create-a-logic-app/save-logic-app.png)

   Dopo il salvataggio, l'app per la logica viene resa disponibile, ma attualmente verifica solo la presenza di nuovi elementi nel feed RSS specificato. 
   Per rendere più utile l'esempio, è possibile aggiungere un'azione eseguita dall'app per la logica dopo l'attivazione del trigger.

## <a name="add-an-action-that-responds-to-your-trigger"></a>Aggiungere un'azione che risponde al trigger

Un'[*azione*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) è un'attività eseguita dal flusso di lavoro dell'app per la logica. Dopo avere aggiunto un trigger all'app per la logica, è possibile aggiungere un'azione per eseguire operazioni con i dati generati da tale trigger. Per questo esempio, viene ora aggiunta un'azione per l'invio di un messaggio di posta elettronica in caso di comparsa di nuovi elementi nel feed RSS del sito Web.

1. Nella finestra di progettazione, scegliere **Nuovo passaggio** > **Aggiungi un'azione** sotto il trigger, come mostrato qui:

   ![Aggiungere un'azione](media/logic-apps-create-a-logic-app/add-new-action.png)

   La finestra di progettazione mostra i [connettori disponibili](../connectors/apis-list.md), per consentire di selezionare un'azione da eseguire all'attivazione del trigger.

2. In base all'account di posta elettronica, seguire la procedura per Outlook o Gmail.

   * Per inviare messaggi di posta elettronica dall'account Outlook, nella casella di ricerca immettere `outlook`. In **Servizi** scegliere **Outlook.com** per gli account Microsoft personali oppure **Office 365 Outlook** per gli account Azure aziendali o dell'istituto di istruzione. 
   In **Azioni** selezionare **Invia un messaggio di posta elettronica**.

       ![Selezionare l'azione "Invia un messaggio di posta elettronica" di Outlook](media/logic-apps-create-a-logic-app/actions.png)

   * Per inviare messaggi di posta elettronica dall'account Gmail, nella casella di ricerca immettere `gmail`. 
   In **Azioni** selezionare **Invia messaggio di posta elettronica**.

       ![Scegliere "Gmail - Invia messaggio di posta elettronica"](media/logic-apps-create-a-logic-app/actions-gmail.png)

3. Quando vengono richieste le credenziali, accedere con il nome utente e la password per l'account di posta elettronica. 

4. Specificare i dettagli per questa azione, ad esempio l'indirizzo di posta elettronica di destinazione, quindi scegliere i parametri per i dati da includere nel messaggio di posta elettronica, ad esempio:

   ![Selezionare i dati da includere nel messaggio di posta elettronica](media/logic-apps-create-a-logic-app/rss-action-setup.png)

    Se è stato scelto Outlook, l'app per la logica potrebbe avere un aspetto simile a questo esempio:

    ![App per la logica completata](media/logic-apps-create-a-logic-app/save-run-complete-logic-app.png)

5.    Salvare le modifiche. Sulla barra dei comandi della finestra di progettazione scegliere **Salva**.

6. È ora possibile eseguire manualmente l'app per la logica per i test. Sulla barra dei comandi della finestra di progettazione scegliere **Esegui**. In alternativa, è possibile lasciare che l'app per la logica verifichi il feed RSS specificato in base alla pianificazione configurata.

   Se l'app per la logica trova nuovi elementi, invia un messaggio di posta elettronica che include i dati selezionati. 
   Se non vengono trovati nuovi elementi, l'app per la logica ignora l'azione di invio del messaggio di posta elettronica.

7. Per monitorare e controllare la cronologia di esecuzione e dei trigger dell'app per la logica, dal menu dell'app per la logica scegliere **Panoramica**.

   ![Monitorare e visualizzare la cronologia di esecuzione e dei trigger dell'app per la logica](media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > Se non si trovano i dati previsti, provare a scegliere **Aggiorna** sulla barra dei comandi.

   Per altre informazioni sullo stato o sulla cronologia di esecuzione e dei trigger dell'app per la logica o per la diagnosi delle app per la logica, vedere [Risolvere i problemi dell'app per la logica](logic-apps-diagnosing-failures.md).

      > [!NOTE]
      > L'esecuzione dell'app per la logica continua fino alla disattivazione dell'app. Per disattivare temporaneamente l'app, dal menu dell'app per la logica scegliere **Panoramica**. Sulla barra dei comandi fare clic su **Disabilita**.

La prima app per la logica è stata configurata ed eseguita. È stato anche illustrato come creare con facilità flussi di lavoro per l'automazione dei processi e integrare le app cloud e i servizi cloud, senza scrivere codice.

## <a name="manage-your-logic-app"></a>Gestire l'app per la logica

Per gestire l'app, è possibile eseguire attività come la verifica dello stato, la modifica, la visualizzazione della cronologia, la disattivazione o l'eliminazione dell'app per la logica.

1. Accedere al [Portale di Azure](https://portal.azure.com "Portale di Azure").

2. Dal menu a sinistra scegliere **More services** (Altri servizi). In **Enterprise Integration** scegliere **App per la logica**. Selezionare l'app per la logica. 

   Nel menu dell'app per la logica sono disponibili queste attività di gestione delle app per la logica:

   |Attività|Passi| 
   |:---|:---| 
   | Visualizzare lo stato, la cronologia di esecuzione e le informazioni generali dell'app| Scegliere **Panoramica**.| 
   | Modificare l'app | Scegliere **Progettazione app per la logica**. | 
   | Visualizzare la definizione JSON del flusso di lavoro dell'app | Scegliere **Visualizzazione codice app per la logica**. | 
   | Visualizzare le operazioni eseguite nell'app per la logica | Scegliere **Log attività**. | 
   | Visualizzare le versioni precedenti per l'app per la logica | Scegliere **Versioni**. | 
   | Disattivare temporaneamente l'app | Scegliere **Panoramica** e quindi sulla barra dei comandi scegliere **Disabilita**. | 
   | Eliminare l'app | Scegliere **Panoramica** e quindi sulla barra dei comandi scegliere **Elimina**. Immettere il nome dell'app per la logica e scegliere **Elimina**. | 

## <a name="get-help"></a>Ottenere aiuto

Per porre domande, fornire risposte e ottenere informazioni sulle attività degli altri utenti di App per la logica di Azure, vedere il [forum su App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Per contribuire al miglioramento delle App per la logica di Azure e dei connettori, votare o inviare idee al [sito dei commenti e suggerimenti degli utenti di App per la logica di Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

*  [Aggiungere condizioni ed eseguire flussi di lavoro](../logic-apps/logic-apps-use-logic-app-features.md)
*     [Modelli di app per la logica](../logic-apps/logic-apps-use-logic-app-templates.md)
*  [Creare app per la logica da modelli di Azure Resource Manager](../logic-apps/logic-apps-arm-provision.md)

