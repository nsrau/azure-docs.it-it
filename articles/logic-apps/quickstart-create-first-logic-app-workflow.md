---
title: 'Avvio rapido: Creare il primo flusso di lavoro di App per la logica - Portale di Azure'
description: Creare il primo flusso di lavoro di App per la logica automatizzato nel portale di Azure usando questa guida di avvio rapido. Informazioni sui concetti di base dell'integrazione di sistemi e di soluzioni Enterprise Application Integration (EAI) nelle app per la logica.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/30/2020
ms.openlocfilehash: 7ec4515d5f1d096cfb83f06c398da4c1254279f6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099148"
---
# <a name="quickstart-create-your-first-logic-apps-workflow---azure-portal"></a>Avvio rapido: Creare il primo flusso di lavoro di App per la logica - Portale di Azure

Questa guida di avvio rapido illustra come creare il primo flusso di lavoro in [App per la logica di Azure](logic-apps-overview.md) tramite il [portale di Azure](https://portal.azure.com). Questa guida introduttiva illustra anche i concetti di base del servizio App per la logica, ad esempio come creare una nuova app per la logica, aggiungere un trigger e un'azione all'app per la logica e testare l'app per la logica. Seguire questa guida di avvio rapido per compilare un'app per la logica di esempio che controlla regolarmente un feed RSS e invia una notifica tramite posta elettronica per i nuovi elementi. Lo screenshot seguente illustra il flusso di lavoro generale di questa app per la logica di esempio:

![Screenshot della finestra di progettazione di App per la logica, che mostra l'app per la logica di esempio in cui il trigger è un feed RSS e l'azione è l'invio di un messaggio.](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

<a name="prerequisites"></a>

Per informazioni su come creare e gestire la prima app per la logica tramite altre interfacce e app, vedere le altre guide di avvio rapido di App per la logica seguenti: 

* [Creare e gestire app per la logica usando l'interfaccia della riga di comando di Azure](quickstart-logic-apps-azure-cli.md)
* [Creare e gestire app per la logica in Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)
* [Creare e gestire app per la logica in Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, [iscriversi per un account Azure gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un account di posta elettronica da un servizio supportato da App per la logica, ad esempio Office 365 Outlook o Outlook.com. Per altri provider di posta elettronica supportati, [vedere l'elenco dei connettori](/connectors/).

    > [!IMPORTANT]
    > Se si usa il [connettore Gmail](/connectors/gmail/), tenere presente che solo gli account G Suite Business possono usare questo connettore senza restrizioni nelle app per la logica. Se si dispone di un account Gmail consumer, è possibile usare questo connettore solo con servizi approvati da Google specifici oppure è possibile [creare un'app client Google da usare per l'autenticazione con il connettore Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Per altre informazioni, vedere [Informative sulla privacy e sulla sicurezza dei dati per i connettori Google in App per la logica di Azure](../connectors/connectors-google-data-security-privacy-policy.md).

<a name="create-logic-app"></a>

## <a name="create-your-logic-app"></a>Creare l'app per la logica

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Nella casella di ricerca del portale di Azure immettere `logic apps` e selezionare **App per la logica**.

   ![Screenshot del portale di Azure che mostra la casella di ricerca con "logic apps" ("app per la logica") come termine di ricerca e "App per la logica" come risultato della ricerca selezionato.](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. Nella pagina **App per la logica** selezionare **Aggiungi**.

   ![Screenshot della pagina del servizio App per la logica nel portale di Azure che mostra l'elenco di app per la logica e il pulsante "Aggiungi" selezionato.](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. Nel riquadro **App per la logica** specificare i dettagli e le impostazioni di base per l'app per la logica. Creare un nuovo [gruppo di risorse](../azure-resource-manager/management/overview.md#terminology) ai fini di questa app per la logica di esempio.
    
   | Proprietà | Valore | Descrizione |
   |----------|-------|-------------|
   | **Nome** | <*logic-app-name*> | Nome dell'app per la logica, che deve essere univoco tra le aree. Il nome può contenere solo lettere, numeri, trattini (`-`), caratteri di sottolineatura (`_`), parentesi (`(`, `)`) e punti (`.`). Questo esempio usa "My-First-Logic-app". |
   | **Sottoscrizione** | <*nome sottoscrizione di Azure*> | Nome della sottoscrizione di Azure. |
   | **Gruppo di risorse** | <*Azure-resource-group-name*> | Nome del [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md#terminology) in cui verrà creata l'app per la logica. Il nome del gruppo di risorse deve essere univoco tra le aree. Questo esempio usa "My-First-LA-RG". |
   | **Posizione** | <*Area di Azure*> | Area di Azure in cui archiviare le informazioni sull'app per la logica. Questo esempio usa "Stati Uniti occidentali". |
   | **Log Analytics** | Disattivato | Impostazione per la registrazione diagnostica, che è impostata su **No** per impostazione predefinita. Per questo esempio, mantenere l'impostazione **No**. |
   ||||

   ![Screenshot della pagina di creazione dell'app per la logica, che mostra il riquadro con i dettagli per la nuova app per la logica.](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

1. Al termine, selezionare **Rivedi e crea**. Verificare i dettagli specificati e selezionare **Crea**.

1. Dopo che Azure distribuisce correttamente l'app, fare clic su **Vai alla risorsa**. In alternativa, è possibile trovare e selezionare l'app per la logica digitando il nome nella casella di ricerca.

   ![Screenshot della pagina di distribuzione della risorsa, che mostra il pulsante "Vai alla risorsa" selezionato.](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Viene visualizzata la finestra Progettazione app per la logica, che mostra una pagina con un video introduttivo e i trigger più usati. In **Modelli** selezionare **App per la logica vuota**.

   ![Screenshot della finestra Progettazione app per la logica, che mostra la raccolta di modelli e il modello "App per la logica vuota" selezionato.](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

A questo punto, [aggiungere un trigger all'app per la logica](#add-rss-trigger).

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Aggiungere il trigger RSS

Ogni app per la logica deve essere avviata con un [trigger](../logic-apps/logic-apps-overview.md#how-do-logic-apps-work), che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger trova nuovi elementi viene attivato e il motore di App per la logica crea un'istanza dell'app per la logica che viene avviata e che esegue il flusso di lavoro. Se il trigger non trova nuovi elementi, non viene attivato e non crea e non esegue un'istanza del flusso di lavoro in corrispondenza di questo controllo.

In questo esempio dell'avvio rapido, dopo aver [creato un'app per la logica](#create-your-logic-app), si aggiunge un trigger che verifica la presenza di nuovi elementi in un feed RSS e viene attivato quando rileva nuovi elementi. È anche possibile creare app per la logica con diversi tipi di trigger, come illustrato nell'esercitazione per la [creazione di flussi di lavoro automatici basati su approvazione](tutorial-process-mailing-list-subscriptions-workflow.md).

1. In **Progettazione app per la logica** nella casella di ricerca selezionare **Tutti**.

1. Per trovare il connettore RSS, nella casella di ricerca immettere `rss`. Dall'elenco **Trigger** selezionare il trigger RSS **Quando viene pubblicato un elemento del feed**.

   ![Screenshot che mostra la finestra Progettazione app per la logica con "rss" nella casella di ricerca e il trigger RSS "Quando viene pubblicato un elemento del feed" selezionato.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Fornire l'URL del feed RSS per il trigger. Definire quindi la pianificazione del trigger impostando l'intervallo e la frequenza.

   | Proprietà | Valore | Descrizione |
   | -------- | ----- | ----------- |
   | **URL feed RSS** | <*RSS-feed-URL*> | URL del feed RSS che si vuole monitorare. Questo esempio usa il feed RSS di Wall Street Journal all'indirizzo `https://feeds.a.dj.com/rss/RSSMarketsMain.xml`. Tuttavia, ai fini di questo esempio, è possibile usare qualsiasi feed RSS che non richieda l'autorizzazione HTTP. Scegliere un feed RSS con una frequenza di pubblicazione elevata, in modo da poter testare l'app per la logica con facilità in un secondo momento. |
   | **Interval** | 1 | Numero di intervalli di attesa tra i controlli del feed RSS. Questo esempio usa intervalli di 1 minuto. |
   | **Frequenza** | Minuto | Unità di tempo per ogni intervallo tra i controlli del feed RSS. Questo esempio usa intervalli di 1 minuto. |
   ||||

   ![Screenshot che mostra la finestra Progettazione app per la logica con le impostazioni del trigger RSS, tra cui URL RSS, frequenza e intervallo.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

1. Per il momento, comprimere i dettagli del trigger facendo clic all'interno della barra del titolo.

   ![Screenshot che mostra la finestra Progettazione app per la logica con la forma dell'app per la logica compressa.](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Salvare l'app per la logica selezionando **Salva** sulla barra degli strumenti della finestra di progettazione.

L'app per la logica è ora attiva, ma non esegue alcuna operazione oltre a controllare il feed RSS. A questo punto, [aggiungere un'azione](#add-email-action) per definire cosa accade quando viene attivato il trigger.

<a name="add-email-action"></a>

## <a name="add-the-send-email-action"></a>Aggiungere l'azione di "invio messaggio di posta elettronica"

Dopo aver [aggiunto un trigger per l'app per la logica](#add-rss-trigger), è necessario aggiungere un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) per determinare la risposta quando l'app per la logica controlla il feed RSS e viene individuato un nuovo elemento. È anche possibile creare app per la logica con azioni molto più complesse, come illustrato nell'esercitazione per l'[elaborazione di messaggi di posta elettronica con App per la logica, Funzioni di Azure e Archiviazione di Azure](/tutorial-process-email-attachments-workflow.md).

> [!NOTE]
> Questo esempio usa Office 365 Outlook come servizio di posta elettronica. Se si usa un altro servizio di posta elettronica supportato nell'app per la logica, l'interfaccia utente potrebbe avere un aspetto diverso. Tuttavia, i concetti di base per la connessione a un altro servizio di posta elettronica restano invariati.

1. Nel trigger **Quando viene pubblicato un elemento del feed** selezionare **Nuovo passaggio**.

   ![Screenshot della finestra Progettazione app per la logica, che mostra un flusso di lavoro con il pulsante "Nuovo passaggio" selezionato.](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. In **Scegliere un'azione** e nella casella di ricerca selezionare **Tutte**.

1. Nella casella di ricerca immettere `send an email` per trovare i connettori che offrono questa azione. Per filtrare l'elenco di azioni in base a un'app o a un servizio specifico, è possibile selezionare prima l'app o il servizio.

   Ad esempio, se si usa un account aziendale o dell'istituto di istruzione Microsoft e si vuole usare Office 365 Outlook, selezionare **Office 365 Outlook**. Se invece si usa un account Microsoft personale, è possibile selezionare Outlook.com. Questo esempio continua con Office 365 Outlook:

   ![Screenshot della finestra Progettazione app per la logica, che mostra il passaggio dell'azione con il connettore di posta elettronica "Office 365 Outlook" selezionato.](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   È ora possibile trovare e selezionare più facilmente l'azione che si vuole usare, ad esempio `send an email`:

   ![Screenshot della finestra Progettazione app per la logica, che mostra l'elenco di azioni filtrate per il connettore di posta elettronica "Office 365 Outlook".](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. Se il connettore di posta elettronica selezionato richiede di autenticare l'identità, completare il passaggio adesso. Per il corretto funzionamento di questo esempio, è necessario creare una connessione tra l'app per la logica e il servizio di posta elettronica. 

    > [!NOTE]
    > Questo esempio illustra l'autenticazione manuale per il connettore Office 365 Outlook. Altri connettori potrebbero tuttavia supportare tipi di autenticazione diversi.
    > È anche possibile gestire l'autenticazione per le app per la logica in modi diversi, a seconda del caso d'uso. Ad esempio, quando si usano modelli di Azure Resource Manager per la distribuzione, è possibile impostare parametri per migliorare la sicurezza degli input che cambiano di frequente, ad esempio i dettagli della connessione. Per altre informazioni, vedere gli argomenti seguenti:
   > * [Parametri del modello per la distribuzione](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [Autorizzare le connessioni OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Eseguire l'autenticazione dell'accesso con le identità gestite](../logic-apps/create-managed-service-identity.md)
   > * [Eseguire l'autenticazione delle connessioni per la distribuzione di app per la logica](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. Nell'azione **Invia un messaggio di posta elettronica** specificare le informazioni da includere nella notifica tramite posta elettronica.

   1. Nella casella **A** immettere l'indirizzo di posta elettronica del destinatario. Per questo esempio, usare il proprio indirizzo di posta elettronica.

        > [!NOTE]
        > Quando si fa clic all'interno della casella **A** e di altre caselle di input nella finestra Progettazione app per la logica, viene visualizzato l'elenco **Aggiungi contenuto dinamico**. Questo esempio userà il contenuto dinamico in un passaggio successivo. L'elenco **Aggiungi contenuto dinamico** mostra gli output del passaggio precedente disponibili che è possibile usare come input per l'azione corrente.

   1. Nella casella **Oggetto** immettere l'oggetto per la notifica tramite posta elettronica. Per questo esempio, immettere il testo seguente con uno spazio vuoto finale: `New RSS item: `

      ![Screenshot della finestra Progettazione app per la logica, che mostra l'azione "Invia un messaggio di posta elettronica" e il cursore all'interno della casella della proprietà "Oggetto".](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. Dall'elenco **Aggiungi contenuto dinamico** selezionare **Titolo feed** , che viene fornito come output dal trigger **Quando viene pubblicato un elemento del feed**. La notifica tramite posta elettronica usa questo output per ottenere il titolo dell'elemento del feed RSS.

      ![Screenshot della finestra Progettazione app per la logica, che mostra l'azione "Invia un messaggio di posta elettronica" e il cursore all'interno della casella della proprietà "Oggetto" con un elenco di contenuto dinamico aperto e l'output "Titolo feed" selezionato.](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > Nell'elenco del contenuto dinamico, se non viene visualizzato alcun output dal trigger **Quando viene pubblicato un elemento del feed** , accanto all'intestazione dell'azione selezionare **Mostra più**.
      > 
      > ![Screenshot della finestra Progettazione app per la logica, che mostra l'elenco di contenuto dinamico aperto e la voce "Mostra più" selezionata per il trigger.](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      Al termine dell'operazione, l'oggetto del messaggio sarà simile al seguente:

      ![Screenshot della finestra Progettazione app per la logica, che mostra l'azione "Invia un messaggio di posta elettronica" e un esempio di oggetto del messaggio con la proprietà "Titolo feed" inclusa.](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      Se nella finestra di progettazione viene visualizzato un ciclo "For each", è stato selezionato un token per una matrice, ad esempio il token **categories-Item**. Per questi tipi di token la finestra di progettazione aggiunge automaticamente questo ciclo intorno all'azione che fa riferimento a tale token. In questo modo, l'app per la logica esegue la stessa azione su ogni elemento della matrice. Per rimuovere il ciclo, selezionare i **puntini di sospensione** ( **...** ) nella barra del titolo del ciclo e quindi scegliere **Elimina**.

   1. Nella casella **Corpo** immettere il contenuto del corpo del messaggio di posta elettronica. In questo esempio il contenuto include tre proprietà con un testo descrittivo per ognuna di esse: `Title:`, la proprietà **Titolo del feed** ; `Date published:`, la proprietà **Data di pubblicazione del feed** e `Link:`, la proprietà **Collegamento al feed primario**. Per aggiungere righe vuote in una casella di modifica, premere MAIUSC+INVIO.

      | Proprietà | Descrizione |
      |----------|-------------|
      | **Titolo feed** | Titolo dell'elemento |
      | **Data di pubblicazione del feed** | Data e ora di pubblicazione dell'elemento |
      | **Collegamento al feed primario** | URL dell'elemento |
      |||

      ![Screenshot della finestra Progettazione app per la logica, che mostra l'azione "Invia un messaggio di posta elettronica" e le proprietà selezionate all'interno della casella "Corpo".](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

1. Salvare l'app per la logica. Selezionare **Salva** dal menu della finestra di progettazione.

A questo punto, [verificare che l'app per la logica funzioni](#test-logic-app).

<a name="test-logic-app"></a>

## <a name="run-your-logic-app"></a>Eseguire l'app per la logica

Dopo aver creato l'app per la logica di esempio, verificare che il flusso di lavoro sia configurato correttamente. È possibile attendere che l'app per la logica controlli il feed RSS in base alla pianificazione specificata. In alternativa, è possibile eseguire manualmente l'app per la logica selezionando **Esegui** sulla barra degli strumenti della finestra Progettazione app per la logica, come illustrato nello screenshot seguente. 

Se il feed RSS include nuovi elementi, l'app per la logica invia un messaggio di posta elettronica per ogni nuovo elemento. In caso contrario, l'app per la logica attende l'intervallo successivo prima di controllare di nuovo il feed RSS. 

![Screenshot della finestra Progettazione app per la logica, che mostra il pulsante "Esegui" selezionato sulla barra degli strumenti.](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

Lo screenshot seguente mostra una notifica tramite posta elettronica di esempio da questa app per la logica di esempio. Il messaggio di posta elettronica include i dettagli per ogni elemento del feed RSS selezionato nella finestra di progettazione, nonché il testo descrittivo aggiunto per ogni elemento.

![Screenshot di Outlook, che mostra un messaggio di posta elettronica di esempio ricevuto quando viene visualizzato un nuovo elemento del feed RSS con titolo dell'elemento, data di pubblicazione e collegamento.](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Se non si ricevono messaggi di posta elettronica di notifica dall'app per la logica come previsto:

* Controllare la cartella della posta indesiderata dell'account di posta elettronica per verificare che il messaggio non sia stato filtrato in modo errato.
* Assicurarsi che nel feed RSS siano stati pubblicati nuovi elementi dall'ultimo controllo pianificato o manuale.

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver testato questa app per la logica di esempio, pulire l'app per la logica e le eventuali risorse correlate eliminando il gruppo di risorse creato per questo esempio.

> [!NOTE]
> Quando si [elimina un'app per la logica](manage-logic-apps-with-azure-portal.md#delete-logic-apps), non vengono più create istanze di nuove esecuzioni. Tutte le esecuzioni in corso e in sospeso vengono annullate. Se si dispone di migliaia di esecuzioni, l'annullamento potrebbe richiedere molto tempo.

1. Nella casella di ricerca di Azure immettere `resource groups` e quindi selezionare **Gruppi di risorse**.

   ![Screenshot che mostra la casella di ricerca del portale di Azure con il termine di ricerca "resource groups" ("gruppi di risorse").](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. Trovare e selezionare il gruppo di risorse dell'app per la logica. Nel riquadro **Panoramica** selezionare **Elimina gruppo di risorse**.

   ![Screenshot che mostra il portale di Azure in cui sono selezionati il gruppo di risorse e il pulsante per "Elimina gruppo di risorse".](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Quando viene visualizzato il riquadro di conferma immettere il nome del gruppo di risorse e selezionare **Elimina**.

   ![Screenshot che mostra il portale di Azure con il riquadro di conferma e il nome immesso per il gruppo di risorse da eliminare.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata creata la prima app per la logica nel portale di Azure per controllare gli aggiornamenti di un feed RSS in base a una pianificazione e inviare una notifica tramite posta elettronica per ogni nuovo elemento del feed. 

Per informazioni su come creare flussi di lavoro basati su pianificazione più avanzati in App per la logica, vedere l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Controllare il traffico con un'app per la logica basata su utilità di pianificazione](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
