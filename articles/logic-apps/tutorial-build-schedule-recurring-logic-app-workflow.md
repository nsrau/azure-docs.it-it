---
title: Creare flussi di lavoro di automazione basati su pianificazione con Azure
description: 'Esercitazione: Creare un flusso di lavoro di automazione ricorrente, basato su pianificazione, che si integra tra servizi cloud tramite App per la logica di Azure.'
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2020
ms.openlocfilehash: aad271875abb9024a1ecc7f45018c04d8c79ce95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842564"
---
# <a name="tutorial-create-schedule-based-and-recurring-automation-workflows-with-azure-logic-apps"></a>Esercitazione: Creare flussi di lavoro di automazione ricorrenti e basati su pianificazione con App per la logica di Azure

Questa esercitazione illustra come creare un'[app per la logica](../logic-apps/logic-apps-overview.md) e automatizzare un flusso di lavoro che viene eseguito in base a una pianificazione ricorrente. Nello specifico, l'app per la logica di esempio controlla il tempo di percorrenza tra due luoghi, tenendo conto del traffico, e viene eseguita la mattina di ogni giorno feriale. Se il tempo supera un limite specifico, l'app per la logica invia un messaggio di posta elettronica con il tempo di percorrenza e il tempo aggiuntivo necessario per arrivare a destinazione. Il flusso di lavoro include vari passaggi, che iniziano con un trigger basato su pianificazione, seguito da un'azione di Bing Maps, un'azione di operazioni sui dati, un'azione di flusso di controllo e un'azione di notifica tramite posta elettronica.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un'app per la logica vuota.
> * Aggiungere un trigger di ricorrenza che specifica la pianificazione per l'app per la logica.
> * Aggiungere un'azione di Bing Maps che ottiene il tempo di percorrenza di un itinerario.
> * Aggiungere un'azione che crea una variabile, converte il tempo di percorrenza da secondi a minuti e archivia il risultato nella variabile.
> * Aggiungere una condizione che confronta il tempo di viaggio con un limite specificato.
> * Aggiungere un'azione che invia un messaggio di posta elettronica se il tempo di percorrenza supera il limite.

Al termine, a livello generale l'app per la logica dovrebbe avere un flusso di lavoro simile al seguente:

![Screenshot della panoramica generale di un esempio di flusso di lavoro di app per la logica.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Prerequisiti

* Un account e una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un account di posta elettronica di un provider supportato da App per la logica, ad esempio un account Office 365 Outlook, Outlook.com o Gmail. Per altri provider, [vedere qui l'elenco dei connettori](/connectors/). Questa guida di avvio rapido usa Office 365 Outlook con un account aziendale o dell'istituto di istruzione. Se si usa un account di posta elettronica diverso, la procedura generale resta invariata, ma l'interfaccia utente potrebbe essere leggermente diversa.

  > [!IMPORTANT]
  > Se si vuole usare il connettore Gmail, solo gli account G Suite Business possono usare questo connettore senza restrizioni nelle app per la logica. Se si dispone di un account Gmail consumer, è possibile usare questo connettore solo con servizi approvati da Google specifici oppure è possibile [creare un'app client Google da usare per l'autenticazione con il connettore Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Per altre informazioni, vedere [Informative sulla privacy e sulla sicurezza dei dati per i connettori Google in App per la logica di Azure](../connectors/connectors-google-data-security-privacy-policy.md).

* Per ottenere il tempo di viaggio per un itinerario, è necessaria una chiave di accesso per l'API di Bing Maps. Per ottenere questa chiave, seguire i passaggi per [ottenere una chiave di Bing Maps](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key).

## <a name="create-your-logic-app"></a>Creare l'app per la logica

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure. Nella home page di Azure selezionare **Crea una risorsa**.

1. Nel menu di Azure Marketplace selezionare **Integrazione** > **App per la logica**.

   ![Screenshot che mostra il menu di Azure Marketplace con le opzioni "Integrazione" e "App per la logica" selezionate.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. Nel riquadro **App per la logica** specificare le informazioni riportate qui sull'app per la logica da creare.

   ![Screenshot che mostra il riquadro di creazione dell'app per la logica e le informazioni da fornire per la nuova app per la logica.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Proprietà | Valore | Descrizione |
   |----------|-------|-------------|
   | **Sottoscrizione** | <*nome sottoscrizione di Azure*> | Il nome della sottoscrizione di Azure. In questo esempio viene utilizzato `Pay-As-You-Go`. |
   | **Gruppo di risorse** | LA-TravelTime-RG | Il nome del [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) usato per organizzare le risorse correlate. Questo esempio crea un nuovo gruppo di risorse denominato `LA-TravelTime-RG`. |
   | **Nome** | LA-TravelTime | Il nome dell'app per la logica, che può contenere solo lettere, numeri, trattini (`-`), caratteri di sottolineatura (`_`), parentesi (`(`, `)`) e punti (`.`). In questo esempio viene utilizzato `LA-TravelTime`. |
   | **Posizione** | Stati Uniti occidentali | L'area in cui archiviare le informazioni sull'app per la logica. In questo esempio viene utilizzato `West US`. |
   | **Log Analytics** | Disattivato | Lasciare l'impostazione **No** per la registrazione diagnostica. |
   ||||

1. Al termine, selezionare **Rivedi e crea**. Una volta convalidate le informazioni sull'app per la logica in Azure, selezionare **Crea**.

1. Dopo la distribuzione dell'app in Azure, selezionare **Vai alla risorsa**.

   Azure apre il riquadro di selezione del modello App per la logica, che visualizza un video introduttivo, i trigger di uso comune e i criteri del modello di app per la logica.

1. Scorrere verso il basso dopo le sezioni del video e dei trigger comuni fino alla sezione **Modelli** e selezionare **App per la logica vuota**.

   ![Screenshot che mostra il riquadro di selezione del modello App per la logica con l'opzione "App per la logica vuota" selezionata.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Aggiungere quindi il [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) Ricorrenza, che esegue il flusso di lavoro in base alla pianificazione selezionata. Ogni app per la logica deve essere avviata con un trigger, che viene attivato quando si verifica un determinato evento o quando nuovi dati soddisfano una condizione specifica. Per altre informazioni, vedere [Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md) (Creare la prima app per la logica).

## <a name="add-the-recurrence-trigger"></a>Aggiungere il trigger Ricorrenza

1. Nella casella di ricerca di Progettazione app per la logica immettere `recurrence` e selezionare il trigger denominato **Ricorrenza**.

   ![Screenshot che mostra la casella di ricerca di Progettazione app per la logica, che contiene il termine di ricerca "ricorrenza" e il trigger "Ricorrenza" selezionato nell'elenco "Trigger".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Nella forma **Ricorrenza** selezionare il pulsante con i **puntini di sospensione** ( **...** ) e quindi selezionare **Rinomina**. Rinominare il trigger con questa descrizione: `Check travel time every weekday morning`

   ![Screenshot che mostra il pulsante con i puntini di sospensione selezionato, l'elenco "Impostazioni" aperto e il comando "Rinomina" selezionato.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. All'interno del trigger cambiare queste proprietà come descritto e illustrato qui.

   ![Screenshot che mostra le modifiche apportate all'intervallo e alla frequenza del trigger.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Interval** | Sì | 1 | Numero di intervalli di attesa tra i controlli |
   | **Frequenza** | Sì | Week | Unità di tempo da usare per la ricorrenza |
   |||||

1. In **Intervallo** e **Frequenza** aprire l'elenco **Aggiungi nuovo parametro** e selezionare queste proprietà da aggiungere al trigger.

   * **In questi giorni**
   * **A queste ore**
   * **A questi minuti**

   ![Screenshot che mostra l'elenco "Aggiungi nuovo parametro" aperto e queste proprietà selezionate: "In questi giorni", "A queste ore" e "In questi minuti".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Impostare ora i valori per le proprietà aggiuntive, come illustrato e descritto qui.

   ![Screenshot che mostra le proprietà aggiuntive impostate sui valori come descritto nella tabella seguente.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Proprietà | Valore | Descrizione |
   |----------|-------|-------------|
   | **In questi giorni** | Lunedì, Martedì, Mercoledì, Giovedì, Venerdì | Questa impostazione è disponibile solo se l'opzione **Frequenza** è impostata su **Settimana**. |
   | **A queste ore** | 7, 8, 9 | Questa impostazione è disponibile solo se l'opzione **Frequenza** è impostata su **Settimana** o su **Giorno**. Per questa ricorrenza, selezionare le ore del giorno. Questo esempio viene eseguito alle ore `7`, `8` e `9`. |
   | **A questi minuti** | 0, 15, 30, 45 | Questa impostazione è disponibile solo se l'opzione **Frequenza** è impostata su **Settimana** o su **Giorno**. Per questa ricorrenza, selezionare i minuti del giorno. Questo esempio viene eseguito ogni 15 minuti a partire dall'ora esatta. |
   ||||

   Il trigger viene attivato ogni giorno feriale, ogni 15 minuti, a partire dalle 7:00 fino alle 9:45. La casella **Anteprima** mostra la pianificazione della ricorrenza. Per altre informazioni, vedere [Pianificare attività e flussi di lavoro](../connectors/connectors-native-recurrence.md) e [Trigger e azioni dei flussi di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Per nascondere i dettagli del trigger per il momento, fare clic all'interno della barra del titolo della forma per comprimerla.

   ![Screenshot che mostra la forma compressa del trigger.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

L'app per la logica è ora disponibile nel portale di Azure ma non esegue alcuna azione oltre all'attivazione del trigger in base alla pianificazione specificata. Aggiungere quindi un'azione per la risposta all'attivazione del trigger.

## <a name="get-the-travel-time-for-a-route"></a>Ottenere il tempo di viaggio per un itinerario

Ora che si ha un trigger, aggiungere un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) che ottiene il tempo di viaggio tra due luoghi. App per la logica fornisce un connettore per l'API di Bing Maps che consente di ottenere facilmente queste informazioni. Prima di iniziare questa attività, assicurarsi di disporre di una chiave API di Bing Maps, come descritto nei prerequisiti di questa esercitazione.

1. In Progettazione app per la logica, sotto il trigger Ricorrenza, selezionare **Nuovo passaggio**.

1. In **Scegliere un'operazione** selezionare **Standard**. Nella casella di ricerca immettere `bing maps` e selezionare l'azione **Get route** (Ottieni percorso).

   ![Screenshot che mostra l'elenco "Scegliere un'operazione" filtrato in base alle azioni "Bing Maps" e l'azione "Get route" selezionata.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Se non si ha una connessione esistente a Bing Maps, viene chiesto di crearne una. Specificare i dettagli della connessione come illustrato e descritto, quindi selezionare **Crea**.

   ![Screenshot che mostra la casella di connessione di Bing Maps con il nome della connessione e la chiave dell'API di Bing Maps specificati.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Connection Name** (Nome connessione) | Sì | BingMapsConnection | Specificare un nome per la connessione. In questo esempio viene utilizzato `BingMapsConnection`. |
   | **Chiave API** | Sì | <*Bing-Maps-API-key*> | Immettere la chiave dell'API di Bing Maps ricevuta in precedenza. Se non si ha una chiave di Bing Maps, leggere le informazioni su [come ottenere una chiave](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key). |
   |||||

1. Rinominare l'azione con questa descrizione: `Get route and travel time with traffic`.

1. Nell'azione aprire l'elenco **Aggiungi nuovo parametro** e selezionare queste proprietà.

   * **Optimize** (Ottimizza)
   * **Unità distanza**
   * **Travel mode** (Modalità di viaggio)

   ![Screenshot che mostra l'azione "Get route" con le proprietà "Optimize", "Distance unit" e "Travel mode" selezionate.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Immettere ora i valori per le proprietà illustrati e descritti qui.

   ![Screenshot che mostra i valori delle proprietà aggiuntivi per l'azione "Get route".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Punto di tragitto 1** | Sì | <*luogo-di-partenza*> | L'origine del percorso. Questo esempio specifica un indirizzo di partenza. |
   | **Punto di tragitto 2** | Sì | <*luogo-di-arrivo*> | La destinazione del percorso. Questo esempio specifica un indirizzo di destinazione. |
   | **Optimize** (Ottimizza) | No | timeWithTraffic | Parametro per ottimizzare l'itinerario, ad esempio distanza, tempo di viaggio con il traffico corrente e così via. Selezionare il valore del parametro **timeWithTraffic**. |
   | **Unità distanza** | No | <*preferenza*> | Unità di distanza per l'itinerario. Questo esempio usa **Miglia** come unità. |
   | **Travel mode** (Modalità di viaggio) | No | Driving (Guida) | Modalità di viaggio lungo l'itinerario. Selezionare la modalità **Driving** (Guida). |
   |||||

   Per altre informazioni su questi parametri e valori, vedere [Calcolare un percorso](/bingmaps/rest-services/routes/calculate-a-route).

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

Creare quindi una variabile per poter convertire e archiviare il tempo di viaggio corrente sotto forma di minuti, invece che di secondi. In questo modo, è possibile evitare di ripetere la conversione e usare il valore più facilmente nei passaggi successivi. 

## <a name="create-a-variable-to-store-travel-time"></a>Creare una variabile in cui archiviare il tempo di percorrenza

A volte può essere necessario eseguire operazioni sui dati nel flusso di lavoro e quindi usare i risultati in operazioni successive. Per salvare i risultati in modo da riutilizzarli o farvi riferimento facilmente, è possibile creare variabili in cui archiviarli dopo l'elaborazione. È possibile creare le variabili solo al livello principale nell'app per la logica.

Per impostazione predefinita, l'azione **Get route** (Ottieni percorso) restituisce il tempo di percorrenza corrente con il traffico, in secondi, dalla proprietà **Travel Duration Traffic** (Durata percorrenza con traffico). Convertendo e archiviando questo valore sotto forma di minuti, sarà possibile riutilizzarlo più facilmente in un secondo momento senza convertirlo di nuovo.

1. Nella finestra di progettazione, in **Get route** (Ottieni percorso), selezionare **Nuovo passaggio**.

1. In **Scegliere un'operazione** selezionare **Predefinita**. Nella casella di ricerca immettere `variables` e selezionare l'azione **Inizializza variabile**.

   ![Screenshot che mostra l'azione "Inizializza variabile" selezionata.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Rinominare l'azione con questa descrizione: `Create variable to store travel time`

1. Specificare queste informazioni per la variabile come illustrato in questa tabella e nei passaggi seguenti:

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Nome** | Sì | travelTime | Nome della variabile In questo esempio viene utilizzato `travelTime`. |
   | **Tipo** | Sì | Integer | Tipo di dati della variabile |
   | **Valore** | No | Un'espressione che converte il tempo di percorrenza corrente da secondi a minuti (vedere la procedura sotto la tabella). | Valore iniziale della variabile |
   |||||

   1. Per creare l'espressione per la proprietà **Valore**, fare clic all'interno della casella in modo da visualizzare l'elenco di contenuto dinamico. Se necessario, allargare la finestra del browser fino a visualizzare l'elenco dinamico. Nell'elenco di contenuto dinamico selezionare **Espressione** per visualizzare l'editor di espressioni.

      ![Screenshot che mostra l'azione "Inizializza variabile" con il cursore all'interno della proprietà "Valore", che apre l'elenco di contenuto dinamico.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      L'elenco di contenuto dinamico mostra gli output delle azioni precedenti disponibili per la selezione come input per le azioni successive nel flusso di lavoro. L'elenco di contenuto dinamico include un editor di espressioni che è possibile usare per selezionare le funzioni che eseguono operazioni nell'espressione. Questo editor di espressioni è disponibile solo nell'elenco di contento dinamico.

   1. Nell'editor di espressioni immettere questa espressione: `div(,60)`

      ![Screenshot che mostra l'editor di espressioni con l'espressione "div(,60)" immessa.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Posizionare il cursore all'interno dell'espressione tra la parentesi aperta ( **(** ) e la virgola ( **,** ), quindi selezionare **Contenuto dinamico**.

      ![Screenshot che mostra dove posizionare il cursore nell'espressione "div(,60)" con il "Contenuto dinamico" selezionato.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Nell'elenco di contenuto dinamico selezionare il valore della proprietà **Travel Duration Traffic** (Durata percorrenza con traffico).

      ![Screenshot che mostra il valore della proprietà "Travel Duration Traffic" selezionato.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Dopo la risoluzione del valore della proprietà nell'espressione, fare clic su **OK**.

      ![Schermata che mostra il pulsante "OK" selezionato.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      La proprietà **Valore** verrà ora visualizzata come illustrato di seguito:

      ![Screenshot che mostra la proprietà "Valore" con l'espressione risolta.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Salvare l'app per la logica.

Successivamente, aggiungere una condizione che controlla se il tempo di viaggio corrente è maggiore di un limite specifico.

## <a name="compare-the-travel-time-with-limit"></a>Confrontare il tempo di percorrenza con il limite

1. Nell'azione **Create variable to store travel time** (Crea variabile in cui archiviare il tempo di percorrenza) selezionare **Nuovo passaggio**.

1. In **Scegliere un'operazione** selezionare **Predefinita**. Nella casella di ricerca immettere `condition`. Nell'elenco delle azioni, scegliere l'azione **Condizione**.

   ![Screenshot che mostra l'azione "Condizione" selezionata](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Rinominare la condizione con questa descrizione: `If travel time exceeds limit`

1. Creare una condizione che controlla se il valore della proprietà **travelTime** supera il limite specificato, come descritto e illustrato di seguito:

   1. Sul lato sinistro della condizione fare clic all'interno della casella **Scegliere un valore**.

   1. Nell'elenco di contenuto dinamico che viene visualizzato selezionare la proprietà **travelTime** in **Variabili**.

      ![Screenshot che mostra la casella "Scegliere un valore" sul lato sinistro della condizione con l'elenco di contenuto dinamico aperto e la proprietà "travelTime" selezionata.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. Nella casella di confronto centrale selezionare l'operatore **è maggiore di**.

   1. Nella casella **Scegliere un valore** sul lato destro della condizione immettere questo limite: `15`

      Al termine, la condizione avrà un aspetto simile a questo esempio:

      ![Screenshot che mostra la condizione completata per confrontare il tempo di percorrenza con il limite specificato.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Salvare l'app per la logica.

Aggiungere quindi l'azione da eseguire quando il tempo di percorrenza supera il limite.

## <a name="send-email-when-limit-exceeded"></a>Inviare un messaggio di posta elettronica quando il limite viene superato

Aggiungere ora un'azione per inviare un messaggio di posta elettronica quando il tempo di percorrenza supera il limite. Questo messaggio di posta elettronica include il tempo di viaggio corrente e il tempo aggiuntivo necessario per percorrere l'itinerario specificato.

1. Nel ramo **True** della condizione selezionare **Aggiungi un'azione**.

1. In **Scegliere un'operazione** selezionare **Standard**. Nella casella di ricerca immettere `send email`. Dato che l'elenco restituisce molti risultati, per filtrarlo, selezionare prima il connettore di posta elettronica desiderato.

   Se ad esempio si ha un account di posta elettronica di Outlook, selezionare il connettore per questo tipo di account:

   * Per gli account aziendali o dell'istituto di istruzione di Azure, selezionare **Office 365 Outlook**.
   * Per gli account Microsoft personali, selezionare **Outlook.com**.

   Questo esempio continua con la selezione di Office 365 Outlook.

   ![Screenshot che mostra l'elenco "Scegliere un'operazione" con la categoria "Standard" e il connettore "Office 365 Outlook" selezionati.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

1. Quando vengono visualizzate le azioni del connettore, selezionare quella per l'invio di posta elettronica, ad esempio:

   ![Screenshot che mostra l'azione "Invia un messaggio di posta elettronica" selezionata.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Se non si ha già una connessione, accedere e autenticare l'accesso all'account di posta elettronica quando richiesto.

   App per la logica di Azure crea una connessione all'account di posta elettronica.

1. Rinominare l'azione con questa descrizione: `Send email with travel time`

1. Per la proprietà **A** immettere l'indirizzo di posta elettronica del destinatario. AI fini del test è possibile usare il proprio indirizzo di posta elettronica.

1. Per la proprietà **Oggetto** specificare l'oggetto del messaggio di posta elettronica e includere la variabile **travelTime** seguendo questa procedura:

   1. Immettere il testo `Current travel time (minutes):` con uno spazio finale. Mantenere il cursore nella casella **Oggetto** in modo che l'elenco di contenuto dinamico rimanga aperto.

   1. Nell'elenco di contenuto dinamico selezionare **Vedi altro** nell'intestazione **Variabili** per visualizzare la variabile **travelTime**.

      ![Screenshot che mostra l'elenco di contenuto dinamico con la sezione "Variabili" e l'opzione "Vedi altro" selezionate.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

      > [!NOTE]
      > L'elenco di contenuto dinamico non mostra automaticamente la variabile **travelTime** perché la proprietà **Oggetto** prevede un valore stringa, mentre **travelTime** è un valore intero.

      ![Screenshot che mostra l'elenco di contenuto dinamico con la variabile "travelTime" selezionata.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Per la proprietà **Corpo** specificare il contenuto del corpo del messaggio di posta elettronica seguendo questa procedura:

   1. Immettere il testo `Add extra travel time (minutes):` con uno spazio finale. Mantenere il cursore nella casella **Corpo** in modo che l'elenco di contenuto dinamico rimanga aperto.

   1. Nell'elenco di contenuto dinamico selezionare **Espressione** per visualizzare l'editor di espressioni.

      ![Screenshot che mostra l'elenco di contenuto dinamico con l'opzione "Espressione" selezionata.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. Nell'editor di espressioni immettere `sub(,15)` in modo da calcolare il numero di minuti che superano il limite: 

      ![Screenshot che mostra l'editor di espressioni con l'espressione "sub(,15)" immessa.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Posizionare il cursore all'interno dell'espressione tra la parentesi aperta ( **(** ) e la virgola ( **,** ), quindi selezionare **Contenuto dinamico**.

      ![Screenshot che mostra dove posizionare il cursore nell'espressione "sub(,15)" con l'opzione "Contenuto dinamico" selezionata.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. In **Variabili** selezionare **travelTime**.

      ![Screenshot che mostra l'elenco di contenuto dinamico con la variabile "travelTime" selezionata.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Dopo la risoluzione della proprietà nell'espressione, fare clic su **OK**.

      ![Screenshot che mostra l'elenco di contenuto dinamico con il pulsante "OK" selezionato.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      La proprietà **Corpo** verrà ora visualizzata come illustrato di seguito:

      ![Screenshot che mostra l'elenco di contenuto dinamico con l'espressione risolta nella proprietà "Corpo" dell'azione di posta elettronica.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Salvare l'app per la logica.

Quindi testare ed eseguire l'app per la logica, che ora sarà simile a questo esempio:

![Screenshot che mostra il flusso di lavoro completato dell'app per la logica di esempio](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Eseguire l'app per la logica

Per avviare manualmente l'app per la logica, selezionare **Esegui** sulla barra degli strumenti della finestra di progettazione.

* Se il tempo di percorrenza corrente rientra nel limite, l'app per la logica non esegue altre azioni e attende l'intervallo successivo per eseguire un nuovo controllo. 

* Se invece il tempo di percorrenza corrente supera il limite, si riceve un messaggio di posta elettronica con il tempo di percorrenza corrente e il numero di minuti oltre il limite. Ecco un esempio di messaggio di posta elettronica inviato dall'app per la logica:

  ![Screenshot che mostra un esempio di messaggio di posta elettronica che segnala il tempo di percorrenza corrente e il tempo aggiuntivo che supera il limite specificato.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

  > [!TIP]
  > Se non si riceve alcun messaggio di posta elettronica, controllare la cartella della posta indesiderata. Il filtro della posta indesiderata potrebbe reindirizzare questi tipi di messaggi di posta elettronica. In caso contrario, se non si è certi che l'app per la logica sia stata eseguita correttamente, vedere [Risolvere i problemi dell'app per la logica](../logic-apps/logic-apps-diagnosing-failures.md).

A questo punto, è stata creata ed eseguita un'app per la logica ricorrente basata su pianificazione. 

Per creare altre app per la logica che usano il trigger **Ricorrenza**, esaminare questi modelli, disponibili dopo aver creato un'app per la logica:

* Ricevere promemoria giornalieri.
* Eliminare i BLOB di Azure meno recenti.
* Aggiungere un messaggio a una coda di archiviazione di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

L'app per la logica continuerà a essere eseguita finché non viene disabilitata o eliminata. Quando l'app per la logica di esempio non è più necessaria, eliminare il gruppo di risorse che contiene l'app e le risorse correlate.

1. Nella casella di ricerca del portale di Azure immettere il nome del gruppo di risorse creato. Nei risultati selezionare il gruppo di risorse in **Gruppi di risorse**.

   Questo esempio ha creato il gruppo di risorse denominato `LA-TravelTime-RG`.

   ![Screenshot che mostra la casella di ricerca di Azure con la stringa "a-travel-time-rg" immessa e il risultato **LA-TravelTime-RG** selezionato.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-resource-group.png)

   > [!TIP]
   > Se la home page di Azure mostra il gruppo di risorse in **Risorse recenti**, è possibile selezionarlo qui.

1. Nel menu gruppo di risorse verificare che sia selezionata l'opzione **Panoramica**. Sulla barra degli strumenti del riquadro **Panoramica** selezionare **Elimina gruppo di risorse**.

   ![Screenshot che mostra il riquadro "Panoramica" del gruppo di risorse e l'opzione "Elimina gruppo di risorse" selezionata sulla barra degli strumenti del riquadro.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Nel riquadro di conferma visualizzato immettere il nome del gruppo di risorse, quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'app per la logica che controlla il traffico in base a una pianificazione specificata (la mattina di ogni giorno feriale) ed esegue un'azione (invio di un messaggio di posta elettronica) quando il tempo di percorrenza supera un limite specificato. Leggere ora come creare un'app per la logica che invia richieste di approvazione di una lista di distribuzione integrando servizi di Azure, servizi Microsoft e altre app SaaS (Software-as-a-Service).

> [!div class="nextstepaction"]
> [Gestire le richieste di sottoscrizione di una lista di distribuzione](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
