---
title: "Creare flussi di lavoro automatizzati basati su utilità di pianificazione - App per la logica di Azure | Microsoft Docs"
description: "Questa esercitazione illustra come creare un flusso di lavoro automatizzato basato su utilità di pianificazione e ricorrente con App per la logica di Azure"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: 4360249367632a01e4357c527f595f8416c7cc38
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="check-traffic-with-a-scheduler-based-logic-app"></a>Controllare il traffico con un'app per la logica basata su utilità di pianificazione

App per la logica di Azure aiuta ad automatizzare i flussi di lavoro eseguiti in base a una pianificazione. Questa esercitazione illustra come creare un'[app per la logica](../logic-apps/logic-apps-overview.md) con un trigger che funziona come utilità di pianificazione, che viene eseguito al mattino di ogni giorno feriale e controlla il tempo di viaggio, incluso il traffico, tra due luoghi. Se il tempo supera un limite specifico, l'app per la logica invia un messaggio di posta elettronica in cui sono indicati il tempo di viaggio e il tempo aggiuntivo necessario per raggiungere la destinazione.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'app per la logica vuota. 
> * Aggiungere un trigger che funziona come utilità di pianificazione per l'app per la logica.
> * Aggiungere un'azione che ottiene il tempo di viaggio per un itinerario.
> * Aggiungere un'azione che crea una variabile, converte il tempo di viaggio da secondi a minuti e salva il risultato nella variabile.
> * Aggiungere una condizione che confronta il tempo di viaggio con un limite specificato.
> * Aggiungere un'azione che invia un messaggio di posta elettronica se il tempo di viaggio supera il limite.

Al termine, a livello generale l'app per la logica dovrebbe avere un flusso di lavoro simile al seguente:

![Panoramica generale dell'app per la logica](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account di Azure gratuito</a> prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

* Un account di posta elettronica di un provider supportato da App per la logica, ad esempio un account Office 365 Outlook, Outlook.com o Gmail. Per altri provider, [vedere qui l'elenco dei connettori](https://docs.microsoft.com/connectors/). Questa guida introduttiva usa un account Outlook.com. Se si usa un account di posta elettronica diverso, la procedura generale resta invariata, ma l'interfaccia utente potrebbe essere leggermente diversa.

* Per ottenere il tempo di viaggio per un itinerario, è necessaria una chiave di accesso per l'API di Bing Maps. Per ottenere questa chiave, seguire i passaggi per <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">ottenere una chiave di Bing Maps</a>. 

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a> con le credenziali dell'account Azure.

## <a name="create-your-logic-app"></a>Creare l'app per la logica

1. Nel menu principale di Azure scegliere **Crea una risorsa** > **Integrazione aziendale** > **App per la logica**.

   ![Creare l'app per la logica](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app.png)

2. In **Crea app per la logica** specificare le informazioni sull'app per la logica come mostrato e descritto di seguito. Al termine, scegliere **Aggiungi al dashboard** > **Crea**.

   ![Specificare le informazioni sull'app per la logica](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Impostazione | Valore | DESCRIZIONE | 
   | ------- | ----- | ----------- | 
   | **Nome** | LA-TravelTime | Nome dell'app per la logica | 
   | **Sottoscrizione** | <*nome-sottoscrizione-Azure*> | Nome della sottoscrizione di Azure | 
   | **Gruppo di risorse** | LA-TravelTime-RG | Nome del [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) usato per organizzare le risorse correlate | 
   | **Posizione** | Stati Uniti orientali 2 | Area in cui archiviare le informazioni sull'app per la logica | 
   | **Log Analytics** | Off | Lasciare l'impostazione **No** per la registrazione diagnostica. | 
   |||| 

3. Dopo che Azure distribuisce l'app, si apre Progettazione app per la logica, che mostra un video introduttivo e alcuni modelli di app per la logica comuni. In **Modelli** scegliere **App per la logica vuota**.

   ![Scegliere il modello App per la logica vuota](./media/tutorial-build-scheduled-recurring-logic-app-workflow/choose-logic-app-template.png)

Aggiungere quindi il [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) della ricorrenza, che viene attivato in base a una pianificazione specifica. Ogni app per la logica deve essere avviata con un trigger, che viene attivato quando si verifica un determinato evento o quando nuovi dati soddisfano una condizione specifica. Per altre informazioni, vedere [Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md) (Creare la prima app per la logica).

## <a name="add-scheduler-trigger"></a>Aggiungere un trigger di pianificazione

1. Nella finestra di progettazione immettere "ricorrenza" nella casella di ricerca. Selezionare il trigger **Pianificazione - Ricorrenza**

   ![Individuare e aggiungere il trigger "Pianificazione - Ricorrenza"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

2. Nella forma **Ricorrenza** fare clic sul pulsante con i **puntini di sospensione** (**...**) e scegliere **Rinomina**. Rinominare il trigger con questa descrizione: ```Check travel time every weekday morning```

   ![Rinominare il trigger](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

3. Nel trigger scegliere **Mostra opzioni avanzate**.

4. Specificare i dettagli relativi a pianificazione e ricorrenza per il trigger, come illustrato e descritto:

   ![Specificare i dettagli relativi a pianificazione e ricorrenza](./media/tutorial-build-scheduled-recurring-logic-app-workflow/schedule-recurrence-trigger-settings.png)

   | Impostazione | Valore | DESCRIZIONE | 
   | ------- | ----- | ----------- | 
   | **Interval** | 1 | Numero di intervalli di attesa tra i controlli | 
   | **Frequenza** | Settimana | Unità di tempo da usare per la ricorrenza | 
   | **Fuso orario** | Nessuna | Si applica solo quando si specifica un'ora di inizio. Utile per specificare un fuso orario non locale. | 
   | **Ora di inizio** | Nessuna | Consente di ritardare la ricorrenza fino a una data e un'ora specifiche. Per altre informazioni, vedere [Pianificare attività e flussi di lavoro regolarmente in esecuzione con le app per la logica](../connectors/connectors-native-recurrence.md). | 
   | **In questi giorni** | Lunedì, Martedì, Mercoledì, Giovedì, Venerdì | Disponibile solo quando l'opzione **Frequenza** è impostata su "Settimana" | 
   | **A queste ore** | 7, 8, 9 | Disponibile solo quando l'opzione **Frequenza** è impostata su "Settimana" o "Giorno". Selezionare le ore del giorno per la ricorrenza. Questo esempio viene eseguito alle 7:00, alle 8:00 e alle 9:00. | 
   | **A questi minuti** | 0, 15, 30, 45 | Disponibile solo quando l'opzione **Frequenza** è impostata su "Settimana" o "Giorno". Selezionare i minuti del giorno per la ricorrenza. Questo esempio viene eseguito ogni 15 minuti a partire dall'ora esatta. | 
   ||||

   Il trigger viene attivato ogni giorno feriale, ogni 15 minuti, a partire dalle 7:00 fino alle 9:45. 
   La casella **Anteprima** mostra la pianificazione della ricorrenza. 
   Per altre informazioni, vedere [Pianificare attività e flussi di lavoro](../connectors/connectors-native-recurrence.md) e [Trigger e azioni dei flussi di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

5. Per nascondere i dettagli del trigger per il momento, fare clic sulla barra del titolo della forma.

   ![Comprimere la forma per nascondere i dettagli](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

6. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**. 

L'app per la logica è ora attiva, ma non esegue alcuna operazione oltre a rispettare la ricorrenza. Aggiungere quindi un'azione per la risposta all'attivazione del trigger.

## <a name="get-the-travel-time-for-a-route"></a>Ottenere il tempo di viaggio per un itinerario

Ora che si ha un trigger, aggiungere un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) che ottiene il tempo di viaggio tra due luoghi. App per la logica fornisce un connettore per l'API di Bing Maps che consente di ottenere facilmente queste informazioni. Prima di iniziare questa attività, assicurarsi di disporre di una chiave API di Bing Maps, come descritto nei prerequisiti di questa esercitazione.

1. In Progettazione app per la logica, sotto il trigger, scegliere **+ Nuovo passaggio** > **Aggiungi un'azione**.

2. Cercare "maps" e selezionare questa azione: **Bing Maps - Get route** (Bing Maps - Ottieni itinerario)

3. Se non si ha una connessione esistente a Bing Maps, viene chiesto di crearne una. Specificare i dettagli della connessione e scegliere **Crea**.

   ![Selezionare l'azione "Bing Maps - Get route" (Bing Maps - Ottieni itinerario)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Impostazione | Valore | DESCRIZIONE |
   | ------- | ----- | ----------- |
   | **Connection Name** (Nome connessione) | BingMapsConnection | Specificare un nome per la connessione. | 
   | **Chiave API** | <*chiave-Bing-Maps*> | Immettere la chiave di Bing Maps ricevuta in precedenza. Se non si ha una chiave di Bing Maps, leggere le informazioni su <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">come ottenere una chiave</a>. | 
   | | | |  

4. Rinominare l'azione con questa descrizione: ```Get route and travel time with traffic```

5. Specificare i dettagli per l'azione **Get route** (Ottieni itinerario) come illustrato e descritto, ad esempio:

   ![Specificare le informazioni per l'azione "Bing Maps - Get route" (Bing Maps - Ottieni itinerario)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Impostazione | Valore | DESCRIZIONE |
   | ------- | ----- | ----------- |
   | **Punto di tragitto 1** | <*luogo-di-partenza*> | Origine dell'itinerario | 
   | **Punto di tragitto 2** | <*luogo-di-arrivo*> | Destinazione dell'itinerario | 
   | **Avoid** (Evita) | Nessuna | Elementi da evitare lungo l'itinerario, ad esempio autostrade, strade a pedaggio e così via | 
   | **Optimize** (Ottimizza) | timeWithTraffic | Parametro per ottimizzare l'itinerario, ad esempio distanza, tempo di viaggio con il traffico corrente e così via. Selezionare il parametro: "timeWithTraffic" | 
   | **Unità distanza** | <*preferenza*> | Unità di distanza per l'itinerario. Questo articolo usa le miglia  | 
   | **Travel mode** (Modalità di viaggio) | Driving (Guida) | Modalità di viaggio lungo l'itinerario. Selezionare la modalità "Driving" (Guida) | 
   | **Transit Date-Time** (Data e ora transito) | Nessuna | Si applica solo alla modalità di transito | 
   | **Date-Time Type** (Tipo di data e ora) | Nessuna | Si applica solo alla modalità di transito | 
   |||| 

   Per altre informazioni su questi parametri, vedere [Calculate a route](https://msdn.microsoft.com/library/ff701717.aspx) (Calcolare un itinerario).

6. Salvare l'app per la logica.

Creare quindi una variabile per poter convertire e archiviare il tempo di viaggio corrente sotto forma di minuti, invece che di secondi. In questo modo, è possibile evitare di ripetere la conversione e usare il valore più facilmente nei passaggi successivi. 

## <a name="create-variable-to-store-travel-time"></a>Creare una variabile per archiviare il tempo di viaggio

A volte può essere necessario eseguire operazioni sui dati nel flusso di lavoro e usare i risultati in operazioni successive. Per salvare i risultati in modo che sia possibile riutilizzarli o farvi riferimento facilmente, è possibile creare variabili dove archiviare i risultati dopo l'elaborazione. È possibile creare le variabili solo al livello principale nell'app per la logica.

Per impostazione predefinita, l'azione **Get route** (Ottieni itinerario) precedente restituisce il tempo di viaggio corrente con il traffico in secondi tramite il campo **Travel Duration Traffic** (Durata viaggio con traffico). Convertendo e archiviando questo valore sotto forma di minuti, sarà possibile riutilizzarlo più facilmente in un secondo momento senza convertirlo di nuovo.

1. Sotto l'azione **Get route** (Ottieni itinerario) scegliere **+ Nuovo passaggio** > **Aggiungi un'azione**.

2. Cercare "variabili" e selezionare l'azione: **Variabili - Inizializza variabile**

   ![Selezionare l'azione "Variabili - Inizializza variabile"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

3. Rinominare l'azione con questa descrizione: ```Create variable to store travel time```

4. Specificare i dettagli per la variabile come descritto di seguito:

   | Impostazione | Valore | DESCRIZIONE | 
   | ------- | ----- | ----------- | 
   | **Nome** | travelTime | Nome della variabile | 
   | **Tipo** | Integer | Tipo di dati della variabile | 
   | **Valore** | Espressione che converte il tempo di viaggio corrente da secondi a minuti (vedere i passaggi descritti dopo la tabella). | Valore iniziale della variabile | 
   |||| 

   1. Per creare l'espressione per il campo **Valore** fare clic all'interno del campo in modo da visualizzare l'elenco di contenuto dinamico. 
   Se necessario, estendere la finestra del browser fino a visualizzare l'elenco. 
   Nell'elenco di contenuto dinamico scegliere **Espressione**. 

      ![Specificare le informazioni per l'azione "Variabili - Inizializza variabile"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Quando si fa clic all'interno di alcune caselle di modifica, viene visualizzato un elenco di contenuto dinamico o un elenco di parametri inline. Questo elenco mostra i parametri di azioni precedenti che è possibile usare come input nel flusso di lavoro. 
      L'elenco di contenuto dinamico ha un editor di espressioni in cui è possibile selezionare le funzioni per l'esecuzione delle operazioni. 
      Questo editor di espressioni viene visualizzato solo nell'elenco di contenuto dinamico.

      La larghezza del browser determina l'elenco visualizzato. 
      Se la finestra del browser è estesa, viene visualizzato l'elenco di contenuto dinamico. 
      Se la finestra del browser è ridotta, viene visualizzato un elenco di parametri inline sotto la casella di modifica che ha attualmente lo stato attivo.

   2. Nell'editor di espressioni immettere questa espressione: ```div(,60)```

      ![Immettere l'espressione: "div(,60)"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   3. Posizionare il cursore all'interno dell'espressione tra la parentesi aperta (**(**) e la virgola (**,**). 
   Scegliere **Contenuto dinamico**.

      ![Posizionare il cursore, scegliere "Contenuto dinamico"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   4. Nell'elenco di contenuto dinamico selezionare **Travel Duration Traffic** (Durata viaggio con traffico).

      ![Selezionare il campo "Travel Duration Traffic" (Durata viaggio con traffico)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   5. Dopo che il campo viene risolto all'interno dell'espressione, scegliere **OK**.

      ![Scegliere "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Il campo **Valore** verrà ora visualizzato come illustrato di seguito:

      ![Campo "Valore" con l'espressione risolta](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

5. Salvare l'app per la logica.

Successivamente, aggiungere una condizione che controlla se il tempo di viaggio corrente è maggiore di un limite specifico.

## <a name="compare-travel-time-with-limit"></a>Confrontare il tempo di viaggio con il limite

1. Sotto l'azione precedente scegliere **+ Nuovo passaggio** > **Aggiungi una condizione**. 

2. Rinominare la condizione con questa descrizione: ```If travel time exceeds limit```

3. Creare una condizione che controlla se **travelTime** supera il limite specificato, come descritto e illustrato di seguito:

   1. All'interno della condizione fare clic nella casella **Scegliere un valore**, a sinistra (visualizzazione estesa del browser) o in alto (visualizzazione ridotta del browser).

   2. Nell'elenco di contenuto dinamico o nell'elenco di parametri selezionare il campo **travelTime** in **Variabili**.

   3. Nella finestra di confronto selezionare l'operatore: **è maggiore di**

   4. Nella casella **Scegliere un valore** a destra (visualizzazione estesa) o in basso (visualizzazione ridotta) immettere questo limite:```15```

   Se, ad esempio, si usa la visualizzazione ridotta, ecco come creare la condizione:

   ![Creare la condizione in visualizzazione ridotta](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time-narrow.png)

4. Salvare l'app per la logica.

Successivamente, aggiungere l'azione da eseguire quando il tempo di viaggio supera il limite.

## <a name="send-email-when-limit-exceeded"></a>Inviare un messaggio di posta elettronica quando il limite viene superato

Aggiungere quindi un'azione per inviare un messaggio di posta elettronica quando il tempo di viaggio supera il limite. Questo messaggio di posta elettronica include il tempo di viaggio corrente e il tempo aggiuntivo necessario per percorrere l'itinerario specificato. 

1. Nel ramo **È true** della condizione scegliere **Aggiungi un'azione**.

2. Cercare "invia messaggio di posta elettronica" e selezionare il connettore di posta elettronica e l'azione di invio del messaggio di posta elettronica da usare.

   ![Individuare e selezionare l'azione "invia messaggio di posta elettronica"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Per gli account Microsoft personali, selezionare **Outlook.com**. 
   * Per gli account aziendali o dell'istituto di istruzione di Azure, selezionare **Office 365 Outlook**.

3. Se non si ha già una connessione, viene chiesto di accedere al proprio account di posta elettronica.

   App per la logica crea una connessione all'account di posta elettronica.

4. Rinominare l'azione con questa descrizione: ```Send email with travel time```

5. Nella casella **A** immettere l'indirizzo di posta elettronica del destinatario. A scopo di test, usare il proprio indirizzo di posta elettronica.

6. Nella casella **Oggetto** specificare l'oggetto del messaggio di posta elettronica e includere la variabile **travelTime**.

   1. Immettere il testo ```Current travel time (minutes): ``` con uno spazio finale. 
   
   2. Nell'elenco di contenuto dinamico o nell'elenco di parametri selezionare **travelTime** in **Variabili**. 
   
      Ad esempio, se il browser è in visualizzazione ridotta:

      ![Immettere il testo dell'oggetto e l'espressione che restituisce il tempo di viaggio](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-subject-settings.png)

7. Nella casella **Corpo** specificare il contenuto del corpo del messaggio di posta elettronica. 

   1. Immettere il testo ```Add extra travel time (minutes): ``` con uno spazio finale. 
   
   2. Se necessario, estendere la finestra del browser fino a visualizzare l'elenco di contenuto dinamico. 
   Nell'elenco di contenuto dinamico scegliere **Espressione**.

      ![Creare l'espressione per il corpo del messaggio di posta elettronica](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   3. Nell'editor di espressioni immettere questa espressione per poter calcolare il numero di minuti che superano il limite: ```sub(,15)```

      ![Immettere l'espressione per calcolare i minuti aggiuntivi per il tempo di viaggio](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   4. Posizionare il cursore all'interno dell'espressione tra la parentesi aperta (**(**) e la virgola (**,**). Scegliere **Contenuto dinamico**.

      ![Continuare a creare l'espressione per calcolare i minuti aggiuntivi per il tempo di viaggio](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   5. In **Variabili** selezionare **travelTime**.

      ![Selezionare il campo "travelTime" da usare nell'espressione](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   6. Dopo che il campo viene risolto all'interno dell'espressione, scegliere **OK**.

      ![Campo "Corpo" con l'espressione risolta](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      Il campo **Corpo** verrà ora visualizzato come illustrato di seguito:

      ![Campo "Corpo" con l'espressione risolta](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

8. Salvare l'app per la logica.

Testare quindi l'app per la logica, che sarà simile a questo esempio:

![App per la logica completata](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Eseguire l'app per la logica

Per avviare manualmente l'app per la logica, sulla barra degli strumenti della finestra di progettazione scegliere **Esegui**. Se il tempo di viaggio corrente rientra nel limite, l'app per la logica non esegue alcuna azione e attende l'intervallo successivo per eseguire un nuovo controllo.
Se invece il tempo di viaggio corrente supera il limite, si riceve un messaggio di posta elettronica con il tempo di viaggio corrente e il numero di minuti oltre il limite. Ecco un esempio di messaggio di posta elettronica inviato dall'app per la logica:

![Messaggio di posta elettronica inviato con il tempo di viaggio](./media/tutorial-build-scheduled-recurring-logic-app-workflow/email-notification.png)

Se non si riceve alcun messaggio di posta elettronica, controllare la cartella della posta indesiderata. Il filtro della posta indesiderata potrebbe reindirizzare questi tipi di messaggi di posta elettronica. In caso contrario, se non si è certi che l'app per la logica sia stata eseguita correttamente, vedere [Risolvere i problemi dell'app per la logica](../logic-apps/logic-apps-diagnosing-failures.md).

A questo punto, è stata creata ed eseguita un'app per la logica ricorrente basata su pianificazione. 

Per creare altre app per la logica che usano il trigger **Pianificazione - Ricorrenza**, esaminare questi modelli, disponibili dopo aver creato un'app per la logica:

* Ricevere promemoria giornalieri.
* Eliminare i BLOB di Azure meno recenti.
* Aggiungere un messaggio a una coda di archiviazione di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse contenente l'app per la logica e le risorse correlate. Nel menu principale di Azure passare a **Gruppi di risorse** e selezionare il gruppo di risorse per l'app per la logica. Scegliere **Elimina gruppo di risorse**. Immettere il nome del gruppo di risorse come conferma e scegliere **Elimina**.

!["Panoramica" > "Elimina gruppo di risorse"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'app per la logica che controlla il traffico in base a una pianificazione specificata (al mattino di ogni giorno feriale) ed esegue un'azione (invio di un messaggio di posta elettronica) quando il tempo di viaggio supera un limite specificato. Leggere ora come creare un'app per la logica che invia richieste di approvazione di una lista di distribuzione integrando servizi di Azure, servizi Microsoft e altre app SaaS.

> [!div class="nextstepaction"]
> [Gestire le richieste di sottoscrizione di una lista di distribuzione](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
