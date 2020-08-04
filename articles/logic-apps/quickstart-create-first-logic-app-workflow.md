---
title: Creare un flusso di lavoro di integrazione automatizzato
description: 'Avvio rapido: creare il primo flusso di lavoro automatizzato usando App per la logica di Azure per soluzioni di integrazione di sistemi e soluzioni Enterprise Application Integration (EAI)'
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/23/2020
ms.openlocfilehash: 980e3e036257bbf5aa9743025bbfb55065176a39
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133300"
---
# <a name="quickstart-create-your-first-automated-integration-workflow-by-using-azure-logic-apps---azure-portal"></a>Avvio rapido: Creare il primo flusso di lavoro di integrazione automatizzato con App per la logica di Azure - Portale di Azure

Questa guida di avvio rapido introduce i concetti generali di base sulla creazione del primo flusso di lavoro usando [App per la logica di Azure](logic-apps-overview.md), ad esempio la creazione di un'app per la logica vuota, l'aggiunta di un trigger e un'azione e il test dell'app per la logica. In questa guida di avvio rapido viene creata un'app per la logica che verifica la presenza di nuovi elementi nel feed RSS di un sito Web a intervalli regolari. Se sono presenti nuovi elementi, l'app per la logica invia un messaggio di posta elettronica per ogni elemento. Al termine, a livello generale l'app per la logica dovrebbe avere un flusso di lavoro simile al seguente:

![Immagine concettuale che mostra un esempio generale di flusso di lavoro dell'app per la logica.](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

Per questo scenario, sono necessari una sottoscrizione di Azure o l'[iscrizione per un account Azure gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) e un account di posta elettronica di un servizio supportato da App per la logica di Azure, ad esempio Office 365 Outlook, Outlook.com o Gmail. Per altri servizi di posta elettronica supportati, [vedere l'elenco dei connettori](/connectors/). In questo esempio l'app per la logica usa un account Office 365 Outlook. Se si usa un servizio di posta elettronica diverso, la procedura generale complessiva è la stessa, ma l'interfaccia utente potrebbe essere leggermente diversa.

> [!IMPORTANT]
> Se si vuole usare il connettore Gmail, solo gli account G Suite Business possono usare questo connettore senza restrizioni nelle app per la logica. Se si dispone di un account Gmail consumer, è possibile usare questo connettore solo con servizi approvati da Google specifici oppure è possibile [creare un'app client Google da usare per l'autenticazione con il connettore Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Per altre informazioni, vedere [Informative sulla privacy e sulla sicurezza dei dati per i connettori Google in App per la logica di Azure](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="create-your-logic-app"></a>Creare l'app per la logica

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Nella casella di ricerca del portale di Azure immettere `logic apps` e selezionare **App per la logica**.

   ![Screenshot che mostra la casella di ricerca del portale di Azure con "logic apps" ("app per la logica") come termine di ricerca e "App per la logica" come risultato della ricerca selezionato.](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. Nella pagina **App per la logica** selezionare **Aggiungi**.

   ![Screenshot che mostra l'elenco delle app per la logica e il pulsante "Aggiungi" selezionato.](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. Nel riquadro **App per la logica** specificare i dettagli dell'app per la logica come illustrato di seguito.

   ![Screenshot che mostra il riquadro di creazione delle app per la logica con i dettagli per la nuova app per la logica.](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Proprietà | valore | Descrizione |
   |----------|-------|-------------|
   | **Nome** | <*logic-app-name*> | Il nome dell'app per la logica, che deve essere univoco tra le varie aree e può contenere solo lettere, numeri, trattini (`-`), caratteri di sottolineatura (`_`), parentesi (`(`, `)`) e punti (`.`). Questo esempio usa "My-First-Logic-app". |
   | **Sottoscrizione** | <*nome sottoscrizione di Azure*> | Il nome della sottoscrizione di Azure |
   | **Gruppo di risorse** | <*Azure-resource-group-name*> | Il nome del [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md), che deve essere univoco tra le varie aree e viene usato per organizzare le risorse correlate. Questo esempio usa "My-First-LA-RG". |
   | **Posizione** | <*Area di Azure*> | L'area in cui archiviare le informazioni sull'app per la logica. Questo esempio usa "Stati Uniti occidentali". |
   | **Log Analytics** | Disattivato | Lasciare l'impostazione **No** per la registrazione diagnostica. |
   ||||

1. Al termine, selezionare **Rivedi e crea**. Verificare i dettagli specificati e selezionare **Crea**.

1. Dopo che Azure distribuisce correttamente l'app, fare clic su **Vai alla risorsa**.

   ![Screenshot che mostra la pagina di distribuzione delle risorse e il pulsante selezionato per "Vai alla risorsa".](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   In alternativa, è possibile trovare e selezionare l'app per la logica digitando il nome nella casella di ricerca.

   Viene visualizzata la finestra Progettazione app per la logica, che mostra una pagina con un video introduttivo e i trigger più usati. In **Modelli** selezionare **App per la logica vuota**.

   ![Screenshot che mostra la raccolta di modelli Progettazione app per la logica e il modello "App per la logica vuota" selezionato.](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Aggiungere quindi un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) che attiva il flusso di lavoro quando nel feed RSS è presente un nuovo elemento. Ogni app per la logica deve essere avviata con un trigger, che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore di App per la logica di Azure crea un'istanza dell'app per la logica che avvia ed esegue il flusso di lavoro.

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Aggiungere il trigger RSS

1. In **Progettazione app per la logica** nella casella di ricerca selezionare **Tutti**.

1. Per trovare il connettore RSS, nella casella di ricerca immettere `rss`. Dall'elenco dei trigger selezionare il trigger RSS **Quando viene pubblicato un elemento del feed**.

   ![Screenshot che mostra la finestra Progettazione app per la logica con "rss" nella casella di ricerca e il trigger selezionato, ovvero "Quando viene pubblicato un elemento del feed".](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Specificare le informazioni per il trigger come descritto in questo passaggio:

   ![Screenshot che mostra la finestra Progettazione app per la logica con le impostazioni del trigger RSS, tra cui URL RSS, frequenza e intervallo.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Proprietà | valore | Descrizione |
   |----------|-------|-------------|
   | **URL feed RSS** | <*RSS-feed-URL*> | Collegamento per il feed RSS che si vuole monitorare. Questo esempio usa il feed RSS del Wall Street Journal all'indirizzo `https://feeds.a.dj.com/rss/RSSMarketsMain.xml`, ma se si vuole, è possibile usare l'URL del proprio feed RSS. |
   | **Interval** | 1 | Numero di intervalli di attesa tra i controlli |
   | **Frequenza** | Minuto | Unità di tempo per ogni intervallo tra i controlli |
   ||||

   La combinazione di intervallo e frequenza consente di definire la pianificazione per il trigger dell'app per la logica. Questa app per la logica controlla il feed ogni minuto.

1. Per comprimere i dettagli del trigger, fare clic sulla barra del titolo del trigger.

   ![Screenshot che mostra la finestra Progettazione app per la logica con la forma dell'app per la logica compressa.](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

L'app per la logica è ora attiva, ma non esegue alcuna operazione oltre a controllare il feed RSS. Aggiungere quindi un'azione per la risposta all'attivazione del trigger.

## <a name="add-the-send-email-action"></a>Aggiungere l'azione di "invio messaggio di posta elettronica"

Aggiungere un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) per l'invio di un messaggio di posta elettronica quando è disponibile un nuovo elemento nel feed RSS.

1. Nel trigger **Quando viene pubblicato un elemento del feed** selezionare **Nuovo passaggio**.

   ![Screenshot che mostra la finestra Progettazione app per la logica con "Nuovo passaggio".](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. In **Scegliere un'azione** e nella casella di ricerca selezionare **Tutte**.

1. Nella casella di ricerca immettere `send an email` per trovare i connettori che offrono questa azione. Per filtrare l'elenco di azioni in base a un'app o a un servizio specifico, è possibile selezionare prima l'app o il servizio.

   Ad esempio, se si usa un account aziendale o dell'istituto di istruzione Microsoft e si vuole usare Office 365 Outlook, selezionare **Office 365 Outlook**. Se invece si usa un account Microsoft personale, è possibile selezionare Outlook.com. Questo esempio continua con Office 365 Outlook:

   ![Screenshot che mostra la finestra Progettazione app per la logica e il connettore di Office 365 Outlook selezionato.](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   È ora possibile trovare e selezionare più facilmente l'azione che si vuole usare, ad esempio `send an email`:

   ![Screenshot che mostra la finestra Progettazione app per la logica e l'elenco con le azioni filtrate.](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. Se il connettore di posta elettronica selezionato richiede di autenticare l'identità, eseguire il passaggio per creare una connessione tra l'app per la logica e il servizio di posta elettronica.

   > [!NOTE]
   > In questo esempio specifico l'autenticazione dell'identità viene eseguita manualmente. Tuttavia, i connettori che richiedono l'autenticazione differiscono nei tipi di autenticazione supportati. Sono anche disponibili opzioni per configurare il modo in cui si vuole gestire l'autenticazione. Ad esempio, quando si usano modelli di Azure Resource Manager per la distribuzione, è possibile parametrizzare e migliorare la sicurezza degli input che si vogliono modificare spesso o facilmente, ad esempio le informazioni di connessione. Per altre informazioni, vedere gli argomenti seguenti:
   >
   > * [Parametri del modello per la distribuzione](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [Autorizzare le connessioni OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Eseguire l'autenticazione dell'accesso con le identità gestite](../logic-apps/create-managed-service-identity.md)
   > * [Eseguire l'autenticazione delle connessioni per la distribuzione di app per la logica](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. Nell'azione **Invia un messaggio di posta elettronica** specificare le informazioni da includere nel messaggio.

   1. Nella casella **A** immettere l'indirizzo di posta elettronica del destinatario. AI fini del test è possibile usare il proprio indirizzo di posta elettronica.

      Per il momento ignorare l'elenco **Aggiungi contenuto dinamico** che viene visualizzato. Quando si fa clic all'interno di alcune caselle di modifica, viene visualizzato questo elenco che mostra tutti gli output disponibili del passaggio precedente che è possibile usare come input per l'azione corrente.

   1. Nella casella **Oggetto** immettere questo testo seguito da uno spazio vuoto: `New RSS item: `

      ![Screenshot che mostra la finestra Progettazione app per la logica con l'azione "Invia un messaggio di posta elettronica" e il cursore all'interno della casella della proprietà "Oggetto".](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. Dall'elenco **Aggiungi contenuto dinamico** selezionare **Titolo feed**, che viene fornito come output dal trigger "Quando viene pubblicato un elemento del feed", rendendo il titolo dell'elemento RSS disponibile per l'uso.

      ![Screenshot che mostra la finestra Progettazione app per la logica con l'azione "Invia un messaggio di posta elettronica" e il cursore all'interno della casella della proprietà "Oggetto" con un elenco di contenuto dinamico aperto e l'output "Titolo feed" selezionato.](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > Nell'elenco del contenuto dinamico, se non viene visualizzato alcun output dal trigger "Quando viene pubblicato un elemento del feed", accanto all'intestazione dell'azione selezionare **Visualizza dettagli**.
      > 
      > ![Screenshot che mostra la finestra Progettazione app per la logica con l'elenco di contenuto dinamico aperto e la voce "Visualizza dettagli" selezionata per il trigger.](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      Al termine dell'operazione, l'oggetto del messaggio sarà simile al seguente:

      ![Screenshot che mostra la finestra Progettazione app per la logica con l'azione "Invia un messaggio di posta elettronica" e un esempio di oggetto del messaggio con la proprietà "Titolo feed" inclusa.](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      Se nella finestra di progettazione viene visualizzato un ciclo "For each", è stato selezionato un token per una matrice, ad esempio il token **categories-item**. Per questi tipi di token la finestra di progettazione aggiunge automaticamente questo ciclo intorno all'azione che fa riferimento a tale token. In questo modo, l'app per la logica esegue la stessa azione su ogni elemento della matrice. Per rimuovere il ciclo, selezionare i **puntini di sospensione** ( **...** ) nella barra del titolo del ciclo e quindi scegliere **Elimina**.

   1. Nella casella **Corpo** immettere questo testo e selezionare questi token per il corpo del messaggio di posta elettronica. Per aggiungere righe vuote in una casella di modifica, premere MAIUSC+INVIO.

      ![Screenshot che mostra la finestra Progettazione app per la logica con l'azione "Invia un messaggio di posta elettronica" e le proprietà selezionate all'interno della casella "Corpo".](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

      | Proprietà | Descrizione |
      |----------|-------------|
      | **Titolo feed** | Titolo dell'elemento |
      | **Data di pubblicazione del feed** | Data e ora di pubblicazione dell'elemento |
      | **Collegamento al feed primario** | URL dell'elemento |
      |||

1. Salvare l'app per la logica.

Testare quindi l'app per la logica.

## <a name="run-your-logic-app"></a>Eseguire l'app per la logica

Per avviare manualmente l'app per la logica, sulla barra degli strumenti della finestra di progettazione selezionare **Esegui**. In alternativa, attendere che l'app per la logica controlli il feed RSS in base alla pianificazione specificata (ogni minuto).

![Screenshot che mostra la finestra Progettazione app per la logica con il pulsante "Esegui" selezionato sulla barra degli strumenti.](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

Se il feed RSS include nuovi elementi, l'app per la logica invia un messaggio di posta elettronica per ogni nuovo elemento. In caso contrario, l'app per la logica attende l'intervallo successivo prima di controllare di nuovo. Se non si riceve alcun messaggio di posta elettronica, controllare la cartella della posta indesiderata.

Ecco un esempio di messaggio di posta elettronica inviato dall'app per la logica.

![Screenshot che mostra un messaggio di posta elettronica di esempio ricevuto quando viene visualizzato un nuovo elemento del feed RSS.](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Da un punto di vista tecnico, quando il trigger controlla il feed RSS e trova nuovi elementi, il trigger viene attivato e il motore di App per la logica di Azure crea un'istanza del flusso di lavoro dell'app per la logica che esegue le azioni nel flusso di lavoro. Se il trigger non trova nuovi elementi, non viene attivato e non crea un'istanza del flusso di lavoro.

Sono state completate la creazione e l'esecuzione della prima app per la logica con il portale di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando questo esempio non è più necessario, eliminare il gruppo di risorse contenente l'app per la logica e le risorse correlate.

1. Nella casella di ricerca di Azure immettere `resource groups` e quindi selezionare **Gruppi di risorse**.

   ![Screenshot che mostra la casella di ricerca del portale di Azure con il termine di ricerca "resource groups" ("gruppi di risorse").](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. Trovare e selezionare il gruppo di risorse dell'app per la logica. Nel riquadro **Panoramica** selezionare **Elimina gruppo di risorse**.

   ![Screenshot che mostra il portale di Azure in cui sono selezionati il gruppo di risorse e il pulsante per "Elimina gruppo di risorse".](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Quando viene visualizzato il riquadro di conferma immettere il nome del gruppo di risorse e selezionare **Elimina**.

   ![Screenshot che mostra il portale di Azure con il riquadro di conferma e il nome immesso per il gruppo di risorse da eliminare.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Quando si elimina un'app per la logica, non viene eseguita alcuna nuova istanza di esecuzione. Tutte le esecuzioni in corso e in sospeso vengono annullate. Se si dispone di migliaia di esecuzioni, l'annullamento potrebbe richiedere molto tempo.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata la prima app per la logica che verifica la presenza di aggiornamenti del feed RSS in base alla pianificazione specificata (ogni minuto) ed esegue un'azione (invio di un messaggio di posta elettronica) quando sono presenti aggiornamenti. Per altre informazioni, continuare con questa esercitazione che consente di creare flussi di lavoro più avanzati basati sulla pianificazione:

> [!div class="nextstepaction"]
> [Controllare il traffico con un'app per la logica basata su utilità di pianificazione](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
