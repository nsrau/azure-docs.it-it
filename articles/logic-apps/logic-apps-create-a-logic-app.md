---
title: Automatizzare i flussi di lavoro tra sistemi e servizi cloud - App per la logica di Azure | Microsoft Docs
description: Creare app per la logica per automatizzare i flussi di lavoro per scenari di integrazione di sistemi e di Enterprise Application Integration (EAI)
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
ms.date: 10/20/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 5906605192f9b03f612e6ca3a445434a23713d7f
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2017
---
# <a name="automate-your-first-workflow-to-process-data-with-a-logic-app"></a>Automatizzare il primo flusso di lavoro per elaborare i dati con un'app per la logica

Per integrare sistemi e servizi in modo più rapido per l'organizzazione, è possibile automatizzare i flussi di lavoro e i processi aziendali con [App per la logica di Azure](../logic-apps/logic-apps-what-are-logic-apps.md). Questa guida introduttiva illustra come sia facile creare ed eseguire un flusso di lavoro automatizzato creando un'app per la logica. L'app di esempio mostra come automatizzare un flusso di lavoro che controlla il feed RSS di un sito Web per verificare la presenza di nuovi elementi e invia un messaggio di posta elettronica per ogni elemento.

Questa app per la logica di esempio invia un messaggio di posta elettronica simile all'esempio seguente:

![Messaggio di posta elettronica inviato per un nuovo elemento del feed RSS](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

Ecco il flusso di lavoro generale dell'app per la logica creata:

![Panoramica - esempio di app per la logica](./media/logic-apps-create-a-logic-app/logic-app-simple-overview.png)

In questa guida introduttiva si apprende come:

> [!div class="checklist"]
> * Creare un'app per la logica vuota.
> * Aggiungere un trigger per avviare il flusso di lavoro quando compare un nuovo elemento nel feed RSS.
> * Aggiungere un'azione per inviare un messaggio di posta elettronica con informazioni dettagliate sull'elemento del feed RSS.
> * Eseguire il flusso di lavoro dell'app per la logica.

Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account di Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Un account di posta elettronica di qualsiasi provider di posta elettronica supportato da App per la logica di Azure per l'invio di notifiche. È ad esempio possibile usare Office 365 Outlook, Outlook.com o Gmail. Per altri connettori di posta elettronica supportati, [vedere l'elenco dei connettori](https://docs.microsoft.com/connectors/). Questa guida introduttiva usa Office 365 Outlook.

  > [!TIP]
  > Se si ha un [account Microsoft](https://account.microsoft.com/account) personale, si ha un account Outlook.com. Se invece si ha un account aziendale o dell'istituto di istruzione di Azure, si ha un account Office 365 Outlook.

* Un collegamento al feed RSS del sito Web. Questo esempio usa il [feed RSS delle notizie principali del sito Web Reuters](http://feeds.reuters.com/reuters/topNews): `http://feeds.reuters.com/reuters/topNews`

La guida introduttiva non richiede l'uso di codice, ma App per la logica supporta altri scenari che usano il codice, ad esempio l'esecuzione di codice personalizzato da un'app per la logica con [Funzioni di Azure](../azure-functions/functions-overview.md).

## <a name="create-a-blank-logic-app"></a>Creare un'app per la logica vuota 

1. Accedere al [Portale di Azure](https://portal.azure.com "Portale di Azure"). 

2. Dal menu principale di Azure scegliere **Nuovo** > **Enterprise Integration** > **App per la logica**.

   ![Portale di Azure, Nuovo, Enterprise Integration, App per la logica](./media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. Creare l'app per la logica con le impostazioni specificate nella tabella riportata dopo l'immagine:

   ![Specificare i dettagli dell'app per la logica](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | Impostazione | Valore consigliato | Descrizione | 
   | ------- | --------------- | ----------- | 
   | **Nome** | *nome-app-per-la-logica* | Specificare un nome univoco per l'app per la logica. | 
   | **Sottoscrizione** | *nome-sottoscrizione-di-Azure* | Selezionare la sottoscrizione di Azure da usare. | 
   | **Gruppo di risorse** | *nome-gruppo-di-risorse-di-Azure* | Creare un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) per questa app per la logica e per organizzare tutte le risorse associate a questa app. | 
   | **Posizione** | *area-data-center-di-Azure* | Selezionare l'area del data center per la distribuzione dell'app per la logica, ad esempio Stati Uniti occidentali. | 
   | **Log Analytics** | Off | Attivare la registrazione diagnostica per l'app per la logica. Per questa guida introduttiva lasciare tuttavia l'impostazione **Off**. | 
   |||| 

4. Al termine, selezionare **Aggiungi al dashboard**. L'app per la logica verrà così visualizzata nel dashboard di Azure e aperta dopo la distribuzione. Scegliere **Create**.

   > [!NOTE]
   > Se non si vuole aggiungere l'app per la logica, per continuare è necessario trovare e aprire manualmente l'app per la logica dopo la distribuzione.

   Dopo che Azure distribuisce l'app per la logica, Progettazione app per la logica si apre mostrando una pagina con un video introduttivo. 
   Sotto il video è possibile trovare i modelli di app per la logica comuni. 
   Questa guida introduttiva crea l'app per la logica da zero. 

5. Andare oltre il video introduttivo e i trigger comuni. In **Modelli** scegliere **App per la logica vuota**.

   ![Scegliere il modello App per la logica vuota](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   Progettazione app per la logica mostra i connettori disponibili e i relativi trigger, che consentono di avviare i flussi di lavoro dell'app per la logica.

   ![Trigger dell'app per la logica](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="add-a-trigger-to-detect-new-items"></a>Aggiungere un trigger per rilevare i nuovi elementi

Ogni flusso di lavoro di un'app per la logica inizia con un [trigger](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts). Il trigger viene attivato quando si verifica un evento specifico o nuovi dati soddisfano la condizione impostata. Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica che si avvia ed esegue il flusso di lavoro.

1. Nella casella di ricerca immettere "rss" come filtro. Selezionare il trigger: **RSS - Quando viene pubblicato un elemento del feed** 

   ![Selezionare il trigger: "RSS - Quando viene pubblicato un elemento del feed"](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. Specificare il collegamento per il feed RSS che si vuole monitorare, ad esempio `http://feeds.reuters.com/reuters/topNews`. Impostare l'intervallo e la frequenza per la ricorrenza. Questo esempio controlla il feed ogni cinque minuti.

   ![Configurare un trigger con feed RSS, frequenza e intervallo](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

   App per la logica crea una connessione al feed RSS.

   > [!TIP]
   > Per semplificare la visualizzazione nella finestra di progettazione, è possibile comprimere e nascondere i dettagli di una forma facendo semplicemente clic all'interno della barra del titolo della forma.

3. Salvare il lavoro. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**. 

   ![Salvare l'app per la logica](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   L'app per la logica è ora attiva, ma non esegue alcuna operazione oltre a controllare il feed RSS. Aggiungere quindi un'azione che risponde all'attivazione del trigger.

## <a name="add-an-action-to-send-email"></a>Aggiungere un'azione per inviare un messaggio di posta elettronica

Ora che si ha un trigger, aggiungere un'[azione](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) per l'invio di un messaggio di posta elettronica quando appare un nuovo elemento nel feed RSS. Il flusso di lavoro esegue questa operazione dopo l'attivazione del trigger.

1. In Progettazione app per la logica, sotto il trigger, scegliere **+ Nuovo passaggio** > **Aggiungi un'azione**.

   ![Aggiungere un'azione](./media/logic-apps-create-a-logic-app/add-new-action.png)

   La finestra di progettazione mostra le azioni che l'app per la logica può eseguire all'attivazione del trigger.

   ![Selezionare una voce nell'elenco di azioni](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. Nella casella di ricerca immettere "invia messaggio di posta elettronica" come filtro. Trovare e selezionare il connettore di posta elettronica da usare. Selezionare quindi l'azione "invia messaggio di posta elettronica" per il connettore. Ad esempio: 

   * Per un account aziendale o dell'istituto di istruzione di Azure, selezionare Office 365 Outlook. 
   * Per gli account Microsoft personali, selezionare Outlook.com. 
   * Per gli account Gmail, selezionare Gmail. 

   Questa guida introduttiva usa Office 365 Outlook. 
   Se si usa un provider di posta elettronica diverso, la procedura rimane invariata, ma è possibile che l'interfaccia utente abbia un aspetto diverso. 

   ![Selezionare l'azione: "Office 365 Outlook - Invia un messaggio di posta elettronica"](./media/logic-apps-create-a-logic-app/actions.png)

3. Quando vengono richieste le credenziali, accedere con il nome utente e la password per l'account di posta elettronica. 

   App per la logica crea una connessione all'account di posta elettronica.

4. Specificare ora i dati da includere nel messaggio di posta elettronica. 

   1. Nella casella **A** immettere l'indirizzo di posta elettronica del destinatario. 
   AI fini del test delle app è possibile indicare il proprio indirizzo di posta elettronica.

   2. Nella casella **Oggetto** immettere l'oggetto del messaggio. 
   Per questo esempio immettere "Nuovo elemento RSS":

      ![Immettere l'oggetto del messaggio di posta elettronica](./media/logic-apps-create-a-logic-app/logic-app-add-subject.png)

      Quando si fa clic all'interno della casella di modifica, viene visualizzato l'elenco **Aggiungi contenuto dinamico**, che consente di selezionare i campi dati disponibili da includere nell'operazione. 
      Se l'elenco di contenuti dinamici non si apre, sotto la relativa casella di modifica scegliere **Aggiungi contenuto dinamico**.

   3. Nell'elenco **Aggiungi contenuto dinamico** selezionare **Titolo feed** per includere il titolo dell'elemento nel messaggio di posta elettronica.

      ![Immettere l'oggetto del messaggio di posta elettronica](./media/logic-apps-create-a-logic-app/logic-app-select-field.png)

      Al termine dell'operazione, l'oggetto del messaggio sarà simile al seguente:

      ![Titolo del feed aggiunto](./media/logic-apps-create-a-logic-app/added-feed-title.png)

      > [!NOTE] 
      > Se si seleziona un campo che contiene una matrice, ad esempio **categorie - Elemento**, la finestra di progettazione aggiunge automaticamente un ciclo "For each" intorno all'azione che fa riferimento a tale campo. In questo modo, l'app per la logica può eseguire l'azione su ogni elemento della matrice. 
      > 
      > Per rimuovere il ciclo, scegliere i puntini di sospensione (**...**) nella barra del titolo del ciclo e quindi scegliere **Elimina**.

   4. Nella casella **Corpo** immettere il contenuto del corpo del messaggio di posta elettronica. 
   Per questo esempio, immettere il testo e selezionare questi campi:

      ![Aggiungere il contenuto per il corpo del messaggio di posta elettronica](./media/logic-apps-create-a-logic-app/logic-app-complete.png)

      | Campo | Descrizione | 
      | ----- | ----------- | 
      | **Titolo feed** | Mostra il titolo dell'elemento. | 
      | **Data di pubblicazione del feed** | Mostra la data e l'ora di pubblicazione dell'elemento. | 
      | **Collegamento al feed primario** | Mostra l'URL dell'elemento. | 
      ||| 

      > [!TIP]
      > Per aggiungere righe vuote in una casella di modifica, premere MAIUSC+INVIO. 
      
5. Salvare il lavoro. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

   ![App per la logica completata](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

## <a name="run-your-logic-app-workflow"></a>Eseguire il flusso di lavoro dell'app per la logica

Per avviare manualmente l'app per la logica, sulla barra degli strumenti della finestra di progettazione scegliere **Esegui**. In alternativa, è possibile attendere l'esecuzione dell'app per la logica in base alla pianificazione configurata.

![Eseguire l'app per la logica](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

Se il feed RSS include nuovi elementi, l'app per la logica invia un messaggio di posta elettronica per ogni nuovo elemento. Ecco ad esempio un messaggio di posta elettronica di Outlook inviato dall'app per la logica:

![Messaggio di posta elettronica inviato per un nuovo elemento del feed RSS](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

Se il feed non contiene nuovi elementi, l'app per la logica ignora il passaggio di invio del messaggio di posta elettronica e attende l'intervallo successivo prima di eseguire di nuovo il controllo. 

> [!TIP]
> Se non si riceve alcun messaggio di posta elettronica, controllare la cartella della posta indesiderata. In caso contrario, se non si è certi che l'app per la logica sia stata eseguita correttamente, vedere [Risolvere i problemi dell'app per la logica](../logic-apps/logic-apps-diagnosing-failures.md).

La prima app per la logica è stata creata ed eseguita. Questa guida introduttiva ha mostrato come creare in modo facile e veloce flussi di lavoro automatizzati per l'integrazione di sistemi e servizi.

## <a name="clean-up-resources"></a>Pulire le risorse

L'app per la logica continua a essere eseguita e potrebbe comportare l'addebito di costi nella sottoscrizione di Azure fino a quando non la si disattiva o elimina. Quando si creano connessioni per l'app per la logica, le connessioni rimangono presenti anche dopo avere eliminato l'app per la logica. 

Al termine, assicurarsi di disabilitare o eliminare le risorse non necessarie, per evitare di incorrere in addebiti. Per eliminare tutte le risorse create per questa guida introduttiva, eliminare il gruppo di risorse di Azure creato per l'app per la logica. 

### <a name="delete-resource-group"></a>Eliminare un gruppo di risorse

Per eliminare tutti gli elementi correlati all'app per la logica, eliminare il gruppo di risorse creato per questa guida introduttiva e tutte le risorse correlate. Leggere altre informazioni su [come gestire i gruppi di risorse di Azure](../azure-resource-manager/resource-group-portal.md#manage-resources).

1. Nel menu di Azure scegliere **Gruppi di risorse**.

2. Scegliere il gruppo di risorse da eliminare. Nel menu del gruppo di risorse scegliere **Panoramica**, se l'opzione non è già selezionata. 

3. Esaminare tutte le risorse nel gruppo da eliminare. Al termine, scegliere **Elimina gruppo di risorse** sulla barra degli strumenti del gruppo di risorse.

### <a name="turn-off-logic-app"></a>Disattivare l'app per la logica

Per interrompere l'esecuzione dell'app per la logica senza eliminare il lavoro, disabilitare l'app. 

Scegliere **Panoramica** dal menu dell'app per la logica. Sulla barra degli strumenti scegliere **Disabilita**.

  ![Disabilitare l'app per la logica](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Se il menu dell'app per la logica non viene visualizzato, provare a tornare al dashboard di Azure e riaprire l'app per la logica.

### <a name="delete-logic-app"></a>Eliminare l'app per la logica

È possibile eliminare solo l'app per la logica, ma conservare tutte le altre risorse correlate, ad esempio le connessioni create.

1. Scegliere **Panoramica** dal menu dell'app per la logica. Sulla barra degli strumenti scegliere **Elimina**. 

   ![Eliminare l'app per la logica](./media/logic-apps-create-a-logic-app/delete-logic-app.png)

   > [!TIP]
   > Se il menu dell'app per la logica non viene visualizzato, provare a tornare al dashboard di Azure e riaprire l'app per la logica.

2. Assicurarsi di voler eliminare l'app per la logica e quindi scegliere **Elimina**.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare flussi di lavoro di app per la logica da modelli predefiniti](../logic-apps/logic-apps-create-logic-apps-from-templates.md)