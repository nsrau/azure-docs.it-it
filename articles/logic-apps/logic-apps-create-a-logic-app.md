---
title: Creare il primo flusso di lavoro automatizzato tra sistemi e servizi cloud - App per la logica di Azure | Microsoft Docs
description: Automatizzare i processi aziendali e i flussi di lavoro per scenari di integrazione di sistemi e di Enterprise Application Integration (EAI) creando ed eseguendo app per la logica
author: ecfan
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
ms.date: 09/18/2017
ms.author: LADocs; estfan
ms.openlocfilehash: d62255ba6e3d5bdfbd792a47f3a92d4c88876742
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-logic-app-for-automating-workflows-and-processes-through-the-azure-portal"></a>Creare la prima app per la logica per l'automazione di flussi di lavoro e processi con il portale di Azure

Senza scrivere codice, è possibile integrare sistemi e servizi creando ed eseguendo flussi di lavoro automatizzati con [App per la logica di Azure](../logic-apps/logic-apps-what-are-logic-apps.md). Per mostrare come è semplice automatizzare le attività con un flusso di lavoro, questa esercitazione consente di creare un'app per la logica di base che controlla un feed RSS per verificare se è presente nuovo contenuto in un sito Web e invia un messaggio di posta elettronica per ogni nuovo elemento nel feed. 

![Panoramica - esempio di prima app per la logica](./media/logic-apps-create-a-logic-app/logic-app-overview.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'app per la logica vuota.
> * Aggiungere un trigger per avviare l'app per la logica quando viene pubblicato un elemento del feed RSS.
> * Aggiungere un'azione per l'invio di un messaggio di posta elettronica con le informazioni sull'elemento del feed RSS.
> * Eseguire e testare l'app per la logica.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free/). In alternativa, è possibile [iscriversi per ottenere una sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/pricing/purchase-options/).

* Un account di posta elettronica di [qualsiasi provider di posta elettronica supportato da App per la logica di Azure](../connectors/apis-list.md) per l'invio di notifiche. È ad esempio possibile usare Office 365 Outlook, Outlook.com, Gmail o un altro provider supportato. In questa esercitazione viene usato Office 365 Outlook.

  > [!TIP]
  > Se si ha un [account Microsoft](https://account.microsoft.com/account) personale, si ha un account Outlook.com. Se invece si ha un account aziendale o dell'istituto di istruzione di Azure, si ha un account Office 365 Outlook.

* Un collegamento al feed RSS del sito Web. Questo esempio usa il [feed RSS delle notizie principali dal sito Web CNN.com](http://rss.cnn.com/rss/cnn_topstories.rss): `http://rss.cnn.com/rss/cnn_topstories.rss`

## <a name="1-create-a-blank-logic-app"></a>1. Creare un'app per la logica vuota 

1. Accedere al [Portale di Azure](https://portal.azure.com "Portale di Azure").

2. Dal menu principale di Azure scegliere **Nuovo** > **Enterprise Integration** > **App per la logica**.

   ![Portale di Azure, Nuovo, Enterprise Integration, App per la logica](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. Creare l'app per la logica con le impostazioni specificate nella tabella.

   ![Specificare i dettagli dell'app per la logica](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | Impostazione | Valore consigliato | Descrizione | 
   | ------- | --------------- | ----------- | 
   | **Nome** | *nome-app-per-la-logica* | Specificare un nome univoco per l'app per la logica. | 
   | **Sottoscrizione** | *sottoscrizione-di-Azure* | Selezionare la sottoscrizione di Azure da usare. | 
   | **Gruppo di risorse** | *gruppo-di-risorse-di-Azure* | Creare o selezionare un gruppo di risorse di Azure, per organizzare o gestire le risorse di Azure correlate. | 
   | **Posizione** | *area-di-Azure* | Selezionare l'area del data center per la distribuzione dell'app per la logica. | 
   |||| 

4. Al termine, selezionare **Aggiungi al dashboard** e quindi scegliere **Crea**.

   È stata creata ora una risorsa di Azure per l'app per la logica. 
   Al termine della distribuzione dell'app per la logica, Progettazione app per la logica mostra i modelli comuni disponibili per poter iniziare più rapidamente.

   > [!NOTE] 
   > Quando si seleziona **Aggiungi al dashboard**, l'app per la logica viene visualizzata nel dashboard di Azure dopo la distribuzione e viene aperta automaticamente in Progettazione app per la logica. In caso contrario, è possibile trovare e aprire manualmente l'app per la logica.

5. Per il momento, in **Modelli** scegliere **App per la logica vuota** per poter creare l'app per la logica da zero.

   ![Selezionare il modello dell'app per la logica](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   Progettazione app per la logica mostra ora i [*connettori*](../connectors/apis-list.md) disponibili e i relativi [*trigger*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts), che è possibile usare per avviare il flusso di lavoro dell'app per la logica.

   ![Trigger dell'app per la logica](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="2-add-a-trigger-for-starting-the-workflow"></a>2. Aggiungere un trigger per avviare il flusso di lavoro

Ogni app per la logica deve iniziare con un [*trigger*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts). Il trigger viene attivato quando si verifica un evento specifico o nuovi dati soddisfano la condizione che è stata impostata. Il motore di App per la logica crea quindi un'istanza di app per la logica per l'esecuzione del flusso di lavoro. Ogni volta che il trigger viene attivato, il motore crea un'altra istanza separata che esegue il flusso di lavoro dell'app per la logica.

1. Nella casella di ricerca digitare "rss" come filtro. Selezionare il trigger: **RSS - Quando viene pubblicato un elemento del feed** 

   ![Selezionare il trigger: "RSS - Quando viene pubblicato un elemento del feed"](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. Specificare il collegamento per il feed RSS del sito Web che si vuole monitorare, ad esempio `http://rss.cnn.com/rss/cnn_topstories.rss`. Impostare l'intervallo e la frequenza per la ricorrenza. In questo esempio impostare queste proprietà per controllare il feed ogni giorno. 

   ![Configurare un trigger con feed RSS, frequenza e intervallo](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

3. Salvare il lavoro, per il momento. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.
Per comprimere e nascondere i dettagli del trigger, scegliere la barra del titolo del trigger.

   ![Salvare l'app per la logica](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   L'app per la logica è ora attiva ma, fino a quando non si aggiungono azioni al flusso di lavoro, non esegue alcuna operazione oltre a verificare la presenza di nuovi elementi nel feed RSS. 

## <a name="3-add-an-action-that-responds-to-the-trigger"></a>3. Aggiungere un'azione che risponde al trigger

Aggiungere ora un'[*azione*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts), ovvero un'attività eseguita dal flusso di lavoro dell'app per la logica. In questo esempio aggiungere un'azione per l'invio di un messaggio di posta elettronica in caso di comparsa di nuovi elementi nel feed RSS.

1. In Progettazione app per la logica, sotto il trigger, scegliere **+ Nuovo passaggio** > **Aggiungi un'azione**.

   ![Aggiungere un'azione](./media/logic-apps-create-a-logic-app/add-new-action.png)

   La finestra di progettazione mostra i [connettori disponibili](../connectors/apis-list.md), per consentire di selezionare un'azione da eseguire all'attivazione del trigger.

   ![Selezionare una voce nell'elenco di azioni](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. Nella casella di ricerca immettere "invia messaggio di posta elettronica" come filtro. Selezionare il connettore corrispondente al provider di posta elettronica in uso. Selezionare quindi l'azione di invio di un messaggio di posta elettronica relativa al connettore selezionato. ad esempio: 

   * Per un account aziendale o dell'istituto di istruzione di Azure, selezionare il connettore Office 365 Outlook. 
   * Per gli account Microsoft personali, selezionare il connettore Outlook.com. 
   * Per gli account Gmail, selezionare il connettore Gmail. 

   Si continuerà a usare il connettore Office 365 Outlook. 
   Se si usa un provider diverso, la procedura rimane invariata ma è possibile che l'interfaccia utente abbia un aspetto diverso. 

   ![Selezionare l'azione: "Office 365 Outlook - Invia un messaggio di posta elettronica"](./media/logic-apps-create-a-logic-app/actions.png)

3. Quando vengono richieste le credenziali, accedere con il nome utente e la password per l'account di posta elettronica. 

4. Fornire i dettagli specificati nella tabella e scegliere i campi da includere nel messaggio.

   | To | Passi | 
   | -- | ----- | 
   | Selezionare i campi disponibili per il flusso di lavoro. | Fare clic in una casella di modifica in modo che venga aperto l'elenco **Contenuto dinamico** oppure scegliere **Aggiungi contenuto dinamico**. | 
   | Visualizzare altri campi disponibili. | Nell'elenco **Contenuto dinamico** scegliere **Vedi altro** per ogni sezione.  | 
   | Aggiungere righe vuote in una casella di modifica. | Premere MAIUSC+INVIO. | 
   | Chiudere l'elenco **Contenuto dinamico**. | Scegliere di nuovo **Aggiungi contenuto dinamico**. | 
   ||| 

   ![Selezionare i dati da includere nel messaggio di posta elettronica](./media/logic-apps-create-a-logic-app/rss-action-setup.png)

   | Impostazione | Valore consigliato | Descrizione | 
   | ------- | --------------- | ----------- | 
   | **To** | *indirizzo-posta-elettronica-destinatario* | Immettere l'indirizzo di posta elettronica del destinatario. AI fini del test delle app è possibile indicare il proprio indirizzo di posta elettronica. | 
   | **Oggetto** | Nuovo post CNN: **Titolo feed** | Immettere il contenuto per l'oggetto del messaggio di posta elettronica. <p>Per questa esercitazione, immettere il testo suggerito e selezionare il campo **Titolo feed** del trigger, che mostra il titolo dell'elemento del feed. | 
   | **Corpo** | Titolo: **Titolo feed** <p>Data di pubblicazione: **Data di pubblicazione del feed** <p>Collegamento: **Collegamento al feed primario** | Immettere il contenuto per il corpo del messaggio di posta elettronica. <p>Per questa esercitazione, immettere il testo suggerito e quindi selezionare i campi del trigger: <p>- **Titolo feed**, che mostra di nuovo il titolo dell'elemento del feed </br>- **Data di pubblicazione del feed**, che mostra la data e l'ora di pubblicazione dell'elemento </br>- **Collegamento al feed primario**, che mostra l'URL per l'elemento del feed | 
   |||| 

   > [!NOTE] 
   > Se si seleziona un campo che archivia una matrice, la finestra di progettazione aggiunge automaticamente un ciclo "For each" intorno all'azione che fa riferimento alla matrice. In questo modo, l'app per la logica esegue l'azione su ogni elemento della matrice.

5. Al termine, salvare le modifiche. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

   ![App per la logica completata](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

   Se a questo punto si vuole testare l'app per la logica, passare alla sezione successiva.

## <a name="4-run-and-test-your-workflow"></a>4. Eseguire e testare il flusso di lavoro

1. Per eseguire manualmente l'app per la logica per il test, sulla barra degli strumenti della finestra di progettazione scegliere **Esegui**. In alternativa, è possibile lasciare che l'app per la logica controlli il feed RSS specificato in base alla pianificazione configurata.

   ![Eseguire l'app per la logica](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

   Se l'app per la logica trova nuovi elementi, invia un messaggio di posta elettronica che include i dati selezionati, ad esempio:

   ![Messaggio di posta elettronica inviato per un nuovo elemento del feed RSS](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

   Se l'app per la logica non trova alcun nuovo elemento, ignora l'azione di invio di un messaggio di posta elettronica e attende l'intervallo successivo prima di eseguire di nuovo il controllo. 

2. Per rivedere la cronologia delle esecuzioni e dei trigger, scegliere **Panoramica** dal menu dell'app per la logica.
Per visualizzare altri dettagli su un'esecuzione, scegliere la riga per tale esecuzione.

   ![Monitorare e visualizzare la cronologia di esecuzione e dei trigger dell'app per la logica](./media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > Se non si trovano i dati previsti, provare a scegliere **Aggiorna** sulla barra degli strumenti.

   Indipendentemente dal fatto che l'esecuzione abbia avuto esito positivo o negativo, la visualizzazione Dettagli esecuzione mostra i passaggi riusciti o non riusciti. 

   ![Visualizzare i dettagli per un'esecuzione dell'app per la logica](./media/logic-apps-create-a-logic-app/logic-app-run-details.png)

   Per altre informazioni sullo stato o sulla cronologia delle esecuzioni e dei trigger dell'app per la logica oppure per la diagnosi dell'app, vedere [Risolvere i problemi dell'app per la logica](../logic-apps/logic-apps-diagnosing-failures.md).

3. Per visualizzare gli input e gli output per ogni passaggio, espandere il passaggio da revisionare. Queste informazioni consentono di eseguire la diagnostica e il debug di problemi nell'app per la logica. ad esempio:

   ![Visualizzare i dettagli del passaggio](./media/logic-apps-create-a-logic-app/logic-app-run-details-expanded.png)

   Per altre informazioni, vedere [Monitorare l'app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md).

La prima app per la logica di base è stata creata ed eseguita. Questo esempio mostra come è facile creare flussi di lavoro che automatizzano i processi per l'integrazione di sistemi e servizi, senza necessità di scrivere codice.

> [!NOTE]
> L'esecuzione dell'app per la logica continua fino alla disattivazione dell'app. Per disattivare temporaneamente l'app, continuare con la sezione successiva.

## <a name="clean-up-resources"></a>Pulire le risorse

In questa esercitazione vengono usate risorse ed eseguite azioni che potrebbero generare addebiti sulla sottoscrizione di Azure. Al termine dell'esercitazione e dei test, assicurarsi di disabilitare o eliminare le risorse per evitare di incorrere in addebiti.

È possibile impedire l'esecuzione dell'app per la logica e l'invio di posta elettronica senza eliminare l'app. Scegliere **Panoramica** dal menu dell'app per la logica. Sulla barra degli strumenti scegliere **Disabilita**.

![Disabilitare l'app per la logica](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

## <a name="faq"></a>domande frequenti

**D:** Cos'altro è possibile fare con un'app per la logica? </br>
**R:** È possibile eseguire altre attività, come modificare, visualizzare la definizione JSON, esaminare il log attività o eliminare l'app per la logica.

Per trovare altre attività di gestione dell'app per la logica, esaminare questi comandi nel menu dell'app per la logica:

| Attività | Passi | 
| ---- | ----- | 
| Visualizzare lo stato dell'app, la cronologia delle esecuzioni e dei trigger e le informazioni generali | Scegliere **Panoramica**. | 
| Modificare l'app | Scegliere **Progettazione app per la logica**. | 
| Visualizzare la definizione JSON del flusso di lavoro dell'app | Scegliere **Visualizzazione codice app per la logica**. | 
| Visualizzare le operazioni eseguite nell'app per la logica | Scegliere **Log attività**. | 
| Visualizzare le versioni precedenti per l'app per la logica | Scegliere **Versioni**. | 
| Disattivare temporaneamente l'app | Scegliere **Panoramica** e quindi sulla barra degli strumenti scegliere **Disabilita**. | 
| Eliminare l'app | Scegliere **Panoramica** e quindi sulla barra degli strumenti scegliere **Elimina**. Immettere il nome dell'app per la logica e scegliere **Elimina**. | 
||| 

## <a name="get-support"></a>Supporto

* Per domande su App per la logica di Azure, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Si vuole contribuire al miglioramento di App per la logica di Azure e dei connettori? Votare o inviare le idee nel [sito UserVoice di App per la logica di Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'app per la logica con Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md)
* [Aggiungere condizioni ed eseguire flussi di lavoro](../logic-apps/logic-apps-use-logic-app-features.md)
*   [Modelli di app per la logica](../logic-apps/logic-apps-use-logic-app-templates.md)
* [Creare app per la logica da modelli di Azure Resource Manager](../logic-apps/logic-apps-arm-provision.md)
* [Misurazione dell'utilizzo di App per la logica](../logic-apps/logic-apps-pricing.md) 
* [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps)
