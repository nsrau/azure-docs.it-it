---
title: Creare flussi di lavoro automatizzati basati su pianificazione
description: 'Esercitazione: Creare un flusso di lavoro automatizzato basato su pianificazione e ricorrente con App per la logica di Azure'
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: f9203f77d5b398f53fcb7c9fceb70604b364a4e0
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790291"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>Esercitazione: Creare un flusso di lavoro automatizzato basato su pianificazione e ricorrente con App per la logica di Azure

Questa esercitazione illustra come compilare un'[app per la logica](../logic-apps/logic-apps-overview.md) e automatizzare un flusso di lavoro ricorrente che viene eseguito in base a una pianificazione. Nello specifico, l'app per la logica di esempio viene eseguita la mattina di ogni giorno della settimana e controlla il tempo di percorrenza, incluso il traffico, da un luogo a un altro. Se il tempo supera un limite specifico, l'app per la logica invia un messaggio di posta elettronica in cui sono indicati il tempo di viaggio e il tempo aggiuntivo necessario per raggiungere la destinazione.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'app per la logica vuota.
> * Aggiungere un trigger di ricorrenza che specifica la pianificazione per l'app per la logica.
> * Aggiungere un'azione di Bing Maps che ottiene il tempo di percorrenza di un itinerario.
> * Aggiungere un'azione che crea una variabile, converte il tempo di percorrenza da secondi a minuti e archivia il risultato nella variabile.
> * Aggiungere una condizione che confronta il tempo di viaggio con un limite specificato.
> * Aggiungere un'azione che invia un messaggio di posta elettronica se il tempo di percorrenza supera il limite.

Al termine, a livello generale l'app per la logica dovrebbe avere un flusso di lavoro simile al seguente:

![Panoramica generale del flusso di lavoro dell'app per la logica](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, [iscriversi per creare un account di Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

* Un account di posta elettronica di un provider supportato da App per la logica, ad esempio un account Office 365 Outlook, Outlook.com o Gmail. Per altri provider, [vedere qui l'elenco dei connettori](https://docs.microsoft.com/connectors/). In questa guida di avvio rapido viene usato un account di Office 365 Outlook. Se si usa un account di posta elettronica diverso, la procedura generale resta invariata, ma l'interfaccia utente potrebbe essere leggermente diversa.

* Per ottenere il tempo di viaggio per un itinerario, è necessaria una chiave di accesso per l'API di Bing Maps. Per ottenere questa chiave, seguire i passaggi per [ottenere una chiave di Bing Maps](https://docs.microsoft.com/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

## <a name="create-your-logic-app"></a>Creare l'app per la logica

1. Dal menu principale di Azure selezionare **Crea una risorsa** > **Integrazione** > **App per la logica**.

   ![Creare la risorsa app per la logica](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. In **Crea app per la logica** specificare le informazioni sull'app per la logica come mostrato e descritto di seguito. Al termine, selezionare **Crea**.

   ![Specificare le informazioni sull'app per la logica](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Proprietà | Valore | DESCRIZIONE |
   |----------|-------|-------------|
   | **Nome** | LA-TravelTime | Il nome dell'app per la logica, che può contenere solo lettere, numeri, trattini (`-`), caratteri di sottolineatura (`_`), parentesi (`(`, `)`) e punti (`.`). In questo esempio viene usato "LA-TravelTime". |
   | **Sottoscrizione** | <*nome-sottoscrizione-Azure*> | Il nome della sottoscrizione di Azure |
   | **Gruppo di risorse** | LA-TravelTime-RG | Il nome del [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) usato per organizzare le risorse correlate. In questo esempio viene usato "LA-TravelTime-RG". |
   | **Posizione** | Stati Uniti occidentali | L'area in cui archiviare le informazioni sull'app per la logica. Questo esempio usa "Stati Uniti occidentali". |
   | **Log Analytics** | Off | Lasciare l'impostazione **No** per la registrazione diagnostica. |
   ||||

1. Dopo che Azure ha distribuito l'app, sulla barra degli strumenti di Azure selezionare **Notifiche** > **Vai alla risorsa** per l'app per la logica distribuita.

   ![Passare alla nuova risorsa app per la logica](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   In alternativa, è possibile trovare e selezionare l'app per la logica digitando il nome nella casella di ricerca.

   Viene visualizzata la finestra Progettazione app per la logica, che mostra una pagina con un video introduttivo, oltre ai trigger e ai modelli di app per la logica più usati. In **Modelli** selezionare **App per la logica vuota**.

   ![Selezionare il modello di app per la logica vuota](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Aggiungere quindi il trigger [Ricorrenza](../logic-apps/logic-apps-overview.md#logic-app-concepts), che viene attivato in base a una pianificazione specifica. Ogni app per la logica deve essere avviata con un trigger, che viene attivato quando si verifica un determinato evento o quando nuovi dati soddisfano una condizione specifica. Per altre informazioni, vedere [Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md) (Creare la prima app per la logica).

## <a name="add-the-recurrence-trigger"></a>Aggiungere il trigger Ricorrenza

1. Nella casella di ricerca della finestra Progettazione app per la logica immettere "ricorrenza" come filtro. Nell'elenco **Trigger** selezionare il trigger **Ricorrenza**.

   ![Aggiungere il trigger "Ricorrenza"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Nella forma **Ricorrenza** selezionare il pulsante con i **puntini di sospensione** ( **...** ) e quindi selezionare **Rinomina**. Rinominare il trigger con questa descrizione: `Check travel time every weekday morning`

   ![Rinominare la descrizione del trigger di ricorrenza](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Modificare le proprietà seguenti all'interno del trigger.

   ![Modificare l'intervallo e la frequenza del trigger di ricorrenza](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Proprietà | Obbligatoria | Value | DESCRIZIONE |
   |----------|----------|-------|-------------|
   | **Interval** | Sì | 1 | Numero di intervalli di attesa tra i controlli |
   | **Frequenza** | Sì | Settimana | Unità di tempo da usare per la ricorrenza |
   |||||

1. In **Intervallo** e **Frequenza** aprire l'elenco **Aggiungi nuovo parametro** e selezionare queste proprietà da aggiungere al trigger.

   * **In questi giorni**
   * **A queste ore**
   * **A questi minuti**

   ![Aggiungere le proprietà per il trigger di ricorrenza](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Impostare ora i valori per le proprietà aggiuntive, come illustrato e descritto qui.

   ![Specificare i dettagli relativi a pianificazione e ricorrenza](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Proprietà | Valore | DESCRIZIONE |
   |----------|-------|-------------|
   | **In questi giorni** | Lunedì, Martedì, Mercoledì, Giovedì, Venerdì | Disponibile solo quando l'opzione **Frequenza** è impostata su "Settimana" |
   | **A queste ore** | 7, 8, 9 | Disponibile solo quando l'opzione **Frequenza** è impostata su "Settimana" o "Giorno". Selezionare le ore del giorno per la ricorrenza. Questo esempio viene eseguito alle 7:00, alle 8:00 e alle 9:00. |
   | **A questi minuti** | 0, 15, 30, 45 | Disponibile solo quando l'opzione **Frequenza** è impostata su "Settimana" o "Giorno". Selezionare i minuti del giorno per la ricorrenza. Questo esempio viene eseguito ogni 15 minuti a partire dall'ora esatta. |
   ||||

   Il trigger viene attivato ogni giorno feriale, ogni 15 minuti, a partire dalle 7:00 fino alle 9:45. La casella **Anteprima** mostra la pianificazione della ricorrenza. Per altre informazioni, vedere [Pianificare attività e flussi di lavoro](../connectors/connectors-native-recurrence.md) e [Trigger e azioni dei flussi di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Per nascondere i dettagli del trigger per il momento, fare clic sulla barra del titolo della forma.

   ![Comprimere la forma per nascondere i dettagli](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

L'app per la logica è ora attiva, ma non esegue alcuna operazione oltre a rispettare la ricorrenza. Aggiungere quindi un'azione per la risposta all'attivazione del trigger.

## <a name="get-the-travel-time-for-a-route"></a>Ottenere il tempo di viaggio per un itinerario

Ora che si ha un trigger, aggiungere un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) che ottiene il tempo di viaggio tra due luoghi. App per la logica fornisce un connettore per l'API di Bing Maps che consente di ottenere facilmente queste informazioni. Prima di iniziare questa attività, assicurarsi di disporre di una chiave API di Bing Maps, come descritto nei prerequisiti di questa esercitazione.

1. In Progettazione app per la logica, sotto il trigger, selezionare **Nuovo passaggio**.

1. In **Scegliere un'azione** selezionare **Standard**. Nella casella di ricerca immettere "bing maps" come filtro e selezionare l'azione **Get route** (Ottieni itinerario).

   ![Selezionare l'azione Get route (Ottieni itinerario)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Se non si ha una connessione esistente a Bing Maps, viene chiesto di crearne una. Specificare i dettagli di connessione seguenti e scegliere **Crea**.

   ![Creare una connessione all'API di Bing Maps](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Proprietà | Obbligatoria | Value | DESCRIZIONE |
   |----------|----------|-------|-------------|
   | **Connection Name** (Nome connessione) | Sì | BingMapsConnection | Specificare un nome per la connessione. In questo esempio viene usato "BingMapsConnection". |
   | **Chiave API** | Sì | <*chiave-Bing-Maps*> | Immettere la chiave di Bing Maps ricevuta in precedenza. Se non si ha una chiave di Bing Maps, leggere le informazioni su [come ottenere una chiave](https://msdn.microsoft.com/library/ff428642.aspx). |
   |||||

1. Rinominare l'azione con questa descrizione: `Get route and travel time with traffic`

1. All'interno dell'azione aprire l'elenco **Aggiungi nuovo parametro** e selezionare queste proprietà da aggiungere all'azione.

   * **Optimize** (Ottimizza)
   * **Unità distanza**
   * **Travel mode** (Modalità di viaggio)

   ![Aggiungere le proprietà all'azione "Get route"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Impostare ora i valori per le proprietà dell'azione, come illustrato e descritto qui.

   ![Specificare i dettagli per l'azione "Get route"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Proprietà | Obbligatoria | Value | DESCRIZIONE |
   |----------|----------|-------|-------------|
   | **Punto di tragitto 1** | Sì | <*luogo-di-partenza*> | Origine dell'itinerario |
   | **Punto di tragitto 2** | Sì | <*luogo-di-arrivo*> | Destinazione dell'itinerario |
   | **Optimize** (Ottimizza) | No | timeWithTraffic | Parametro per ottimizzare l'itinerario, ad esempio distanza, tempo di viaggio con il traffico corrente e così via. Selezionare il parametro: "timeWithTraffic". |
   | **Unità distanza** | No | <*preferenza*> | Unità di distanza per l'itinerario. Questo esempio usa le miglia come unità. |
   | **Travel mode** (Modalità di viaggio) | No | Driving (Guida) | Modalità di viaggio lungo l'itinerario. Selezionare la modalità "Driving" (Guida). |
   ||||

   Per altre informazioni su questi parametri, vedere [Calculate a route](https://docs.microsoft.com/bingmaps/rest-services/routes/calculate-a-route) (Calcolare un itinerario).

1. Salvare l'app per la logica.

Creare quindi una variabile per poter convertire e archiviare il tempo di viaggio corrente sotto forma di minuti, invece che di secondi. In questo modo, è possibile evitare di ripetere la conversione e usare il valore più facilmente nei passaggi successivi. 

## <a name="create-a-variable-to-store-travel-time"></a>Creare una variabile in cui archiviare il tempo di percorrenza

A volte può essere necessario eseguire operazioni sui dati nel flusso di lavoro e quindi usare i risultati in operazioni successive. Per salvare i risultati in modo che sia possibile riutilizzarli o farvi riferimento facilmente, è possibile creare variabili dove archiviare i risultati dopo l'elaborazione. È possibile creare le variabili solo al livello principale nell'app per la logica.

Per impostazione predefinita, l'azione **Get route** (Ottieni itinerario) precedente restituisce il tempo di percorrenza corrente con il traffico in secondi dalla proprietà **Travel Duration Traffic** (Durata viaggio con traffico). Convertendo e archiviando questo valore sotto forma di minuti, sarà possibile riutilizzarlo più facilmente in un secondo momento senza convertirlo di nuovo.

1. Nell'azione **Get route** (Ottieni itinerario) selezionare **Nuovo passaggio**.

1. In **Scegliere un'azione** selezionare **Predefinita**. Nella casella di ricerca immettere "variabili" e selezionare l'azione **Inizializza variabile**.

   ![Selezionare l'azione "Inizializza variabile"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Rinominare l'azione con questa descrizione: `Create variable to store travel time`

1. Specificare i dettagli per la variabile come descritto di seguito:

   | Proprietà | Obbligatoria | Value | DESCRIZIONE |
   |----------|----------|-------|-------------|
   | **Nome** | Sì | travelTime | Nome della variabile In questo esempio viene usato "travelTime". |
   | **Tipo** | Sì | Integer | Tipo di dati della variabile |
   | **Valore** | No| Espressione che converte il tempo di viaggio corrente da secondi a minuti (vedere i passaggi descritti dopo la tabella). | Valore iniziale della variabile |
   ||||

   1. Per creare l'espressione per la proprietà **Valore**, fare clic all'interno della casella in modo da visualizzare l'elenco di contenuto dinamico. Se necessario, estendere la finestra del browser fino a visualizzare l'elenco. Nell'elenco di contenuto dinamico selezionare **Espressione**.

      ![Specificare le informazioni per l'azione "Inizializza variabile"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Quando si fa clic all'interno di alcune caselle di modifica, viene visualizzato l'elenco di contenuto dinamico. Questo elenco mostra le proprietà di azioni precedenti che è possibile usare come input nel flusso di lavoro. L'elenco di contenuto dinamico ha un editor di espressioni in cui è possibile selezionare le funzioni per l'esecuzione delle operazioni. Questo editor di espressioni viene visualizzato solo nell'elenco di contenuto dinamico.

   1. Nell'editor di espressioni immettere questa espressione: `div(,60)`

      ![Immettere l'espressione: "div(,60)"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Posizionare il cursore all'interno dell'espressione tra la parentesi aperta ( **(** ) e la virgola ( **,** ). 
   Selezionare **Contenuto dinamico**.

      ![Posizionare il cursore e selezionare "Contenuto dinamico"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Nell'elenco di contenuto dinamico selezionare **Travel Duration Traffic** (Durata viaggio con traffico).

      ![Selezionare la proprietà "Travel Duration Traffic" (Durata viaggio con traffico)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Dopo la risoluzione del valore della proprietà nell'espressione, fare clic su **OK**.

      ![Per completare la compilazione dell'espressione, scegliere "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      La proprietà **Valore** verrà ora visualizzata come illustrato di seguito:

      ![Proprietà "Valore" visualizzata con l'espressione risolta](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Salvare l'app per la logica.

Successivamente, aggiungere una condizione che controlla se il tempo di viaggio corrente è maggiore di un limite specifico.

## <a name="compare-the-travel-time-with-limit"></a>Confrontare il tempo di percorrenza con il limite

1. Nell'azione precedente selezionare **Nuovo passaggio**.

1. In **Scegliere un'azione** selezionare **Predefinita**. Nella casella di ricerca digitare "condizione" come filtro. Nell'elenco di azioni selezionare **Condizione**.

   ![Selezionare l'azione "Condizione"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Rinominare la condizione con questa descrizione: `If travel time exceeds limit`

1. Creare una condizione che controlla se il valore della proprietà **travelTime** supera il limite specificato, come descritto e illustrato di seguito:

   1. A sinistra della condizione fare clic all'interno della casella **Scegliere un valore**.

   1. Nell'elenco di contenuto dinamico che viene visualizzato selezionare la proprietà **TravelTime** in **Variabili**.

      ![Compilare il lato sinistro della condizione](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. Nella casella di confronto centrale selezionare l'operatore **è maggiore di**.

   1. Nella casella **Scegliere un valore** sul lato destro della condizione immettere questo limite: `15`

      Al termine, la condizione avrà un aspetto simile a questo esempio:

      ![Condizione completata per controllare il tempo di percorrenza](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Salvare l'app per la logica.

Aggiungere quindi l'azione da eseguire quando il tempo di percorrenza supera il limite.

## <a name="send-email-when-limit-exceeded"></a>Inviare un messaggio di posta elettronica quando il limite viene superato

Aggiungere quindi un'azione per inviare un messaggio di posta elettronica quando il tempo di viaggio supera il limite. Questo messaggio di posta elettronica include il tempo di viaggio corrente e il tempo aggiuntivo necessario per percorrere l'itinerario specificato.

1. Nel ramo **È true** della condizione selezionare **Aggiungi un'azione**.

1. In **Scegliere un'azione** selezionare **Standard**. Nella casella di ricerca immettere "invia un messaggio di posta elettronica". Dato che l'elenco restituisce molti risultati, selezionare prima il connettore di posta elettronica desiderato, ad esempio:

   ![Selezionare il connettore di posta elettronica da usare](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Per gli account aziendali o dell'istituto di istruzione di Azure, selezionare **Office 365 Outlook**.
   * Per gli account Microsoft personali, selezionare **Outlook.com**.

1. Quando vengono visualizzate le azioni del connettore, selezionare l'azione di invio di un messaggio di posta elettronica che si vuole usare, ad esempio:

   ![Selezionare l'azione di invio di un messaggio di posta elettronica](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Se non si ha già una connessione, viene chiesto di accedere al proprio account di posta elettronica.

   App per la logica crea una connessione all'account di posta elettronica.

1. Rinominare l'azione con questa descrizione: `Send email with travel time`

1. Nella casella **A** immettere l'indirizzo di posta elettronica del destinatario. A scopo di test, usare il proprio indirizzo di posta elettronica.

1. Nella casella **Oggetto** specificare l'oggetto del messaggio di posta elettronica e includere la variabile **travelTime**.

   1. Immettere il testo `Current travel time (minutes):` con uno spazio finale. 

   1. In **Variabili** nell'elenco di contenuto dinamico selezionare **Vedi altro**.

      ![Trovare la variabile "travelTime"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. Quando **travelTime** viene visualizzato in **Variabili**, selezionare **travelTime**.

      ![Immettere il testo dell'oggetto e l'espressione che restituisce il tempo di viaggio](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Nella casella **Corpo** specificare il contenuto del corpo del messaggio di posta elettronica.

   1. Immettere il testo `Add extra travel time (minutes):` con uno spazio finale.

   1. Nell'elenco di contenuto dinamico selezionare **Espressione**.

      ![Creare l'espressione per il corpo del messaggio di posta elettronica](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. Nell'editor di espressioni immettere questa espressione per poter calcolare il numero di minuti che superano il limite: ```sub(,15)```

      ![Immettere l'espressione per calcolare i minuti aggiuntivi per il tempo di viaggio](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Posizionare il cursore all'interno dell'espressione tra la parentesi aperta ( **(** ) e la virgola ( **,** ). Selezionare **Contenuto dinamico**.

      ![Continuare a creare l'espressione per calcolare i minuti aggiuntivi per il tempo di viaggio](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. In **Variabili** selezionare **travelTime**.

      ![Selezionare la proprietà "travelTime" da usare nell'espressione](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Dopo la risoluzione della proprietà nell'espressione, fare clic su **OK**.

      ![Al termine della risoluzione della proprietà "Corpo", scegliere "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      La proprietà **Corpo** verrà ora visualizzata come illustrato di seguito:

      ![Proprietà "Corpo" risolta nell'espressione](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Salvare l'app per la logica.

Testare quindi l'app per la logica, che sarà simile a questo esempio:

![Esempio completato del flusso di lavoro dell'app per la logica](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Eseguire l'app per la logica

Per avviare manualmente l'app per la logica, sulla barra degli strumenti della finestra di progettazione selezionare **Esegui**.

* Se il tempo di percorrenza corrente rientra nel limite, l'app per la logica non esegue altre azioni e attende l'intervallo successivo per eseguire un nuovo controllo. 

* Se invece il tempo di percorrenza corrente supera il limite, si riceve un messaggio di posta elettronica con il tempo di percorrenza corrente e il numero di minuti oltre il limite. Ecco un esempio di messaggio di posta elettronica inviato dall'app per la logica:

![Messaggio di posta elettronica di esempio inviato che mostra il tempo di percorrenza](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

Se non si riceve alcun messaggio di posta elettronica, controllare la cartella della posta indesiderata. Il filtro della posta indesiderata potrebbe reindirizzare questi tipi di messaggi di posta elettronica. In caso contrario, se non si è certi che l'app per la logica sia stata eseguita correttamente, vedere [Risolvere i problemi dell'app per la logica](../logic-apps/logic-apps-diagnosing-failures.md).

A questo punto, è stata creata ed eseguita un'app per la logica ricorrente basata su pianificazione. 

Per creare altre app per la logica che usano il trigger **Ricorrenza**, esaminare questi modelli, disponibili dopo aver creato un'app per la logica:

* Ricevere promemoria giornalieri.
* Eliminare i BLOB di Azure meno recenti.
* Aggiungere un messaggio a una coda di archiviazione di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando l'app per la logica di esempio non è più necessaria, eliminare il gruppo di risorse che contiene l'app e le risorse correlate. 

1. Nel menu principale di Azure passare a **Gruppi di risorse** e selezionare il gruppo di risorse per l'app per la logica.

1. Nel menu Gruppo di risorse selezionare **Panoramica** > **Elimina gruppo di risorse**. 

   !["Panoramica" > "Elimina gruppo di risorse"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Immettere il nome del gruppo di risorse per conferma e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'app per la logica che controlla il traffico in base a una pianificazione specificata (al mattino di ogni giorno feriale) ed esegue un'azione (invio di un messaggio di posta elettronica) quando il tempo di viaggio supera un limite specificato. Leggere ora come creare un'app per la logica che invia richieste di approvazione di una lista di distribuzione integrando servizi di Azure, servizi Microsoft e altre app SaaS.

> [!div class="nextstepaction"]
> [Gestire le richieste di sottoscrizione di una lista di distribuzione](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
