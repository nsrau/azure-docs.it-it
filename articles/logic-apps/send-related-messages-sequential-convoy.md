---
title: Inviare messaggi correlati in ordine usando una serie di istruzioni sequenziali
description: Inviare messaggi correlati in ordine usando il modello di serie di istruzioni sequenziali in app per la logica di Azure con il bus di servizio di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: apseth, divswa, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 8c00d2e4f622bcfad7b2468013336f0d936e318c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87048661"
---
# <a name="send-related-messages-in-order-by-using-a-sequential-convoy-in-azure-logic-apps-with-azure-service-bus"></a>Inviare messaggi correlati in ordine usando una serie di istruzioni sequenziali in app per la logica di Azure con il bus di servizio di Azure

Quando è necessario inviare messaggi correlati in un ordine specifico, è possibile seguire il modello di serie di istruzioni [ *sequenziali* ](/azure/architecture/patterns/sequential-convoy) quando si usa app per la [logica di Azure](../logic-apps/logic-apps-overview.md) usando il connettore del bus di [servizio di Azure](../connectors/connectors-create-api-servicebus.md). I messaggi correlati hanno una proprietà che definisce la relazione tra tali messaggi, ad esempio l'ID della [sessione](../service-bus-messaging/message-sessions.md) nel bus di servizio.

Si supponga, ad esempio, di disporre di 10 messaggi per una sessione denominata "sessione 1" e di 5 messaggi per una sessione denominata "sessione 2" che vengono tutti inviati alla stessa [coda del bus di servizio](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). È possibile creare un'app per la logica che elabora i messaggi dalla coda in modo che tutti i messaggi della "sessione 1" siano gestiti da un'unica esecuzione del trigger e che tutti i messaggi della "sessione 2" siano gestiti dall'esecuzione del trigger successiva.

![Modello di serie di istruzioni sequenziali generali](./media/send-related-messages-sequential-convoy/sequential-convoy-pattern-general.png)

Questo articolo illustra come creare un'app per la logica che implementa questo modello usando il modello di **recapito correlato nell'ordine usando le sessioni del bus di servizio** . Questo modello definisce un flusso di lavoro dell'app per la logica che inizia con il connettore del bus di servizio **quando un messaggio viene ricevuto in un trigger di coda (blocco di visualizzazione)** , che riceve i messaggi da una [coda del bus di servizio](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Ecco i passaggi di alto livello eseguiti da questa app per la logica:

* Inizializzare una sessione in base a un messaggio che il trigger legge dalla coda del bus di servizio.

* Legge ed elabora tutti i messaggi dalla stessa sessione nella coda durante l'esecuzione del flusso di lavoro corrente.

Per esaminare il file JSON del modello, vedere [GitHub: service-bus-sessions.json](https://github.com/Azure/logicapps/blob/master/templates/service-bus-sessions.json).

Per altre informazioni, vedere [modello di serie di istruzioni sequenziali-modelli di progettazione cloud di architettura di Azure](/azure/architecture/patterns/sequential-convoy).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Uno spazio dei nomi del bus di servizio e una [coda del bus di servizio](../service-bus-messaging/service-bus-queues-topics-subscriptions.md), ovvero un'entità di messaggistica che verrà usata nell'app per la logica. Questi elementi e l'app per la logica devono usare la stessa sottoscrizione di Azure. Assicurarsi di selezionare **Abilita sessioni** quando si crea la coda. Se non si hanno questi elementi, vedere [come creare lo spazio dei nomi e una coda del bus di servizio](../service-bus-messaging/service-bus-create-namespace-portal.md).

  [!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

* Informazioni di base su come creare app per la logica. Se non si ha familiarità con app per la logica di Azure, provare la Guida introduttiva, [creare il primo flusso di lavoro automatico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-access-to-service-bus-namespace"></a>Controllare l'accesso allo spazio dei nomi del bus di servizio

Se non si è certi che l'app per la logica disponga delle autorizzazioni per accedere allo spazio dei nomi del bus di servizio, confermare tali autorizzazioni.

1. Accedere al [portale di Azure](https://portal.azure.com). Trovare e selezionare lo *spazio dei nomi*del bus di servizio.

1. Nel menu spazio dei nomi, in **Impostazioni**, selezionare **criteri di accesso condiviso**. In **attestazioni**verificare di disporre delle autorizzazioni di **gestione** per lo spazio dei nomi.

   ![Gestire le autorizzazioni per lo spazio dei nomi del bus di servizio](./media/send-related-messages-sequential-convoy/check-service-bus-permissions.png)

1. Ottenere ora la stringa di connessione per lo spazio dei nomi del bus di servizio. È possibile usare questa stringa in un secondo momento quando si crea una connessione allo spazio dei nomi dall'app per la logica.

   1. Nel riquadro **criteri di accesso condiviso** , in **criteri**, selezionare **RootManageSharedAccessKey**.
   
   1. Accanto alla stringa di connessione primaria selezionare il pulsante copia. Salvare la stringa di connessione per usarla successivamente.

      ![Copiare la stringa di connessione dello spazio dei nomi del bus di servizio](./media/send-related-messages-sequential-convoy/copy-service-bus-connection-string.png)

   > [!TIP]
   > Per verificare se la stringa di connessione è associata allo spazio dei nomi del bus di servizio o a un'entità di messaggistica, ad esempio una coda, cercare il parametro `EntityPath`  nella stringa di connessione. Se questo parametro è presente, la stringa di connessione è destinata a un'entità specifica e non è la stringa corretta da usare con l'app per la logica.

## <a name="create-logic-app"></a>Creare l'app per la logica

In questa sezione si crea un'app per la logica usando il modello di **recapito correlato nell'ordine usando le sessioni del bus di servizio** , che include il trigger e le azioni per l'implementazione di questo modello di flusso di lavoro. Si crea anche una connessione allo spazio dei nomi del bus di servizio e si specifica il nome per la coda del bus di servizio che si vuole usare.

1. Nel [portale di Azure](https://portal.azure.com) creare un'app per la logica vuota. Dal Home page di Azure selezionare **Crea una risorsa**  >  **integrazione**  >  **app**per la logica.

1. Quando viene visualizzata la raccolta di modelli, scorrere oltre le sezioni video e trigger comuni. Nella sezione **modelli** selezionare il modello, **recapito correlato nell'ordine usando le sessioni del bus di servizio**.

   ![Selezionare il modello "recapito correlato nell'ordine con sessioni del bus di servizio"](./media/send-related-messages-sequential-convoy/select-correlated-in-order-delivery-template.png)

1. Quando viene visualizzata la casella di conferma, selezionare **utilizza questo modello**.

1. Nella finestra di progettazione dell'app per la logica, nella forma del **bus di servizio** , selezionare **continue (continua**) e quindi selezionare il segno più ( **+** ) visualizzato nella forma.

   ![Selezionare "continua" per connettersi al bus di servizio di Azure](./media/send-related-messages-sequential-convoy/connect-to-service-bus.png)

1. Creare ora una connessione del bus di servizio scegliendo una delle opzioni seguenti:

   * Per usare la stringa di connessione copiata in precedenza dallo spazio dei nomi del bus di servizio, seguire questa procedura:

     1. Selezionare **immettere manualmente le informazioni di connessione**.

     1. Per **nome connessione**specificare un nome per la connessione. Per **stringa di connessione**, incollare la stringa di connessione dello spazio dei nomi e selezionare **Crea**, ad esempio:

        ![Immettere il nome della connessione e la stringa di connessione del bus di servizio](./media/send-related-messages-sequential-convoy/provide-service-bus-connection-string.png)

        > [!TIP]
        > Se non si dispone di questa stringa di connessione, informazioni su come [trovare e copiare la stringa di connessione dello spazio dei nomi del bus di servizio](#permissions-connection-string).

   * Per selezionare uno spazio dei nomi del bus di servizio dalla sottoscrizione di Azure corrente, seguire questa procedura:

     1. Per **nome connessione**specificare un nome per la connessione. Per **spazio dei nomi del bus di servizio**, selezionare lo spazio dei nomi del bus di servizio, ad esempio:

        ![Immettere il nome della connessione e selezionare spazio dei nomi del bus di servizio](./media/send-related-messages-sequential-convoy/create-service-bus-connection.png)

     1. Quando viene visualizzato il riquadro successivo, selezionare i criteri del bus di servizio e selezionare **Crea**.

        ![Selezionare criteri del bus di servizio e quindi "Crea"](./media/send-related-messages-sequential-convoy/create-service-bus-connection-2.png)

1. Al termine, selezionare continue ( **continua**).

   La finestra di progettazione dell'app per la logica Mostra ora il modello **di recapito correlato nell'ordine usando le sessioni del bus di servizio** , che contiene un flusso di lavoro già popolato con un trigger e azioni, inclusi due ambiti che implementano la gestione degli errori che seguono il `Try-Catch` modello.

A questo punto è possibile ottenere altre informazioni sul trigger e le azioni nel modello oppure passare a [specificare i valori per il modello di app per la logica](#complete-template).

<a name="template-summary"></a>

## <a name="template-summary"></a>Riepilogo modelli

Di seguito è riportato il flusso di lavoro di livello superiore nel **recapito correlato nell'ordine usando il modello di sessioni del bus di servizio** quando i dettagli sono compressi:

![Flusso di lavoro di primo livello del modello](./media/send-related-messages-sequential-convoy/template-top-level-flow.png)

| Nome | Descrizione |
|------|-------------|
| **`When a message is received in a queue (peek-lock)`** | In base alla ricorrenza specificata, questo trigger del bus di servizio controlla la coda del bus di servizio specificata per tutti i messaggi. Se un messaggio è presente nella coda, viene attivato il trigger, che crea ed esegue un'istanza del flusso di lavoro. <p><p>Il termine *blocco di visualizzazione* indica che il trigger invia una richiesta per recuperare un messaggio dalla coda. Se è presente un messaggio, il trigger recupera e blocca il messaggio in modo che non si verifichi nessun'altra elaborazione sul messaggio fino alla scadenza del periodo di blocco. Per informazioni dettagliate, [inizializzare la sessione](#initialize-session). |
| **`Init isDone`** | Questa [azione di **inizializzazione variabile** ](../logic-apps/logic-apps-create-variables-store-values.md#initialize-variable) crea una variabile booleana impostata su `false` e indica quando sono soddisfatte le condizioni seguenti: <p><p>-Nessun altro messaggio nella sessione è disponibile per la lettura. <br>-Non è più necessario rinnovare il blocco della sessione in modo che l'istanza del flusso di lavoro corrente possa essere completata. <p><p>Per informazioni dettagliate, vedere [inizializzare la sessione](#initialize-session). |
| **`Try`** | Questa [azione dell' **ambito** ](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) contiene le azioni che vengono eseguite per elaborare un messaggio. Se si verifica un problema nell' `Try` ambito, l'azione di `Catch` **ambito** successiva gestisce il problema. Per ulteriori informazioni, vedere [ambito "Try"](#try-scope). |
| **`Catch`**| Questa [azione dell' **ambito** ](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) contiene le azioni che vengono eseguite se si verifica un problema nell' `Try` ambito precedente. Per ulteriori informazioni, vedere [ambito "catch"](#catch-scope). |
|||

<a name="try-scope"></a>

### <a name="try-scope"></a>Ambito "Try"

Di seguito è riportato il flusso di primo livello nell' `Try` [azione ambito](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) quando i dettagli sono compressi:

![Flusso di lavoro azione ambito "prova"](./media/send-related-messages-sequential-convoy/try-scope-action.png)

| Nome | Descrizione |
|------|-------------|
| **`Send initial message to topic`** | È possibile sostituire questa azione con qualsiasi azione che si desidera gestire il primo messaggio della sessione nella coda. L'ID sessione specifica la sessione. <p><p>Per questo modello, un'azione del bus di servizio invia il primo messaggio a un argomento del bus di servizio. Per informazioni dettagliate, vedere [gestire il messaggio iniziale](#handle-initial-message). |
| (ramo parallelo) | Questa [azione ramo parallelo](../logic-apps/logic-apps-control-flow-branches.md) crea due percorsi: <p><p>-Branch #1: continua l'elaborazione del messaggio. Per ulteriori informazioni, vedere [Branch #1: completare il messaggio iniziale nella coda](#complete-initial-message). <p><p>-Branch #2: abbandona il messaggio se si verifica un errore e rilascia il rilascio per il ritiro da un'altra esecuzione del trigger. Per altre informazioni, vedere [Branch #2: abbandonare il messaggio iniziale dalla coda](#abandon-initial-message). <p><p>Entrambi i percorsi si uniscono più avanti nella **sessione di chiusura in una coda ed è stata eseguita un'** azione, descritta nella riga successiva. |
| **`Close a session in a queue and succeed`** | Questa azione del bus di servizio unisce i rami descritti in precedenza e chiude la sessione nella coda quando si verifica uno degli eventi seguenti: <p><p>: Il flusso di lavoro completa l'elaborazione dei messaggi disponibili nella coda. <br>: Il flusso di lavoro abbandona il messaggio iniziale perché si è verificato un errore. <p><p>Per informazioni dettagliate, vedere [chiudere una sessione in una coda e avere esito positivo](#close-session-succeed). |
|||

<a name="complete-initial-message"></a>

#### <a name="branch-1-complete-initial-message-in-queue"></a>Branch #1: completare il messaggio iniziale nella coda

| Nome | Descrizione |
|------|-------------|
| `Complete initial message in queue` | Questa azione del bus di servizio contrassegna un messaggio recuperato correttamente come completo e rimuove il messaggio dalla coda per evitare la rielaborazione. Per informazioni dettagliate, vedere [gestire il messaggio iniziale](#handle-initial-message). |
| `While there are more messages for the session in the queue` | Il [ciclo **until** ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) continua a ricevere i messaggi mentre i messaggi esistono o fino a quando non viene passata un'ora. Per ulteriori informazioni sulle azioni in questo ciclo, vedere [mentre nella coda sono presenti più messaggi per la sessione](#while-more-messages-for-session). |
| **`Set isDone = true`** | Quando non sono più presenti messaggi, questa [azione **set variable** ](../logic-apps/logic-apps-create-variables-store-values.md#set-variable) imposta `isDone` su `true` . |
| **`Renew session lock until cancelled`** | Questo [ciclo **until** ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) garantisce che il blocco della sessione venga mantenuto da questa app per la logica mentre i messaggi esistono o fino a un'ora. Per ulteriori informazioni sulle azioni in questo ciclo, vedere [rinnovo del blocco della sessione fino a quando non viene annullato](#renew-session-while-messages-exist). |
|||

<a name="abandon-initial-message"></a>

#### <a name="branch-2-abandon-initial-message-from-the-queue"></a>Branch #2: abbandona il messaggio iniziale dalla coda

Se l'azione che gestisce il primo messaggio non riesce, l'azione del bus di servizio, **abbandona il messaggio iniziale dalla coda**, rilascia il messaggio per un'altra esecuzione dell'istanza del flusso di lavoro per prelevare ed elaborare. Per informazioni dettagliate, vedere [gestire il messaggio iniziale](#handle-initial-message).

<a name="catch-scope"></a>

### <a name="catch-scope"></a>Ambito "catch"

Se le azioni nell' `Try` ambito hanno esito negativo, l'app per la logica deve comunque chiudere la sessione. L' `Catch` [azione ambito](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) viene eseguita quando l' `Try` azione dell'ambito restituisce lo stato,, `Failed` `Skipped` o `TimedOut` . L'ambito restituisce un messaggio di errore che include l'ID di sessione in cui si è verificato il problema e termina l'app per la logica.

Di seguito è riportato il flusso di primo livello nell' `Catch` azione ambito quando i dettagli sono compressi:

![Flusso di lavoro azione ambito "catch"](./media/send-related-messages-sequential-convoy/catch-scope-action.png)

| Nome | Descrizione |
|------|-------------|
| **`Close a session in a queue and fail`** | Questa azione del bus di servizio chiude la sessione nella coda, in modo che il blocco della sessione non resti aperto. Per informazioni dettagliate, vedere [chiudere una sessione in una coda e avere esito negativo](#close-session-fail). |
| **`Find failure msg from 'Try' block`** | Questa [azione di **matrice di filtri** ](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) crea una matrice dagli input e dagli output di tutte le azioni all'interno dell' `Try` ambito in base ai criteri specificati. In questo caso, questa azione restituisce gli output delle azioni che hanno generato `Failed` lo stato. Per informazioni dettagliate, vedere [trovare un messaggio di errore dal blocco ' Try '](#find-failure-message). |
| **`Select error details`** | Questa [azione **Select** ](../logic-apps/logic-apps-perform-data-operations.md#select-action) crea una matrice che contiene oggetti JSON in base ai criteri specificati. Questi oggetti JSON vengono compilati in base ai valori della matrice creata dall'azione precedente, `Find failure msg from 'Try' block` . In questo caso, questa azione restituisce una matrice che contiene un oggetto JSON creato con i dettagli dell'errore restituiti dall'azione precedente. Per informazioni dettagliate, vedere [selezionare i dettagli dell'errore](#select-error-details). |
| **`Terminate`** | Questa [azione **termina** ](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) arresta l'esecuzione del flusso di lavoro, Annulla tutte le azioni in corso, ignora tutte le azioni rimanenti e restituisce lo stato specificato, l'ID sessione e il risultato dell'errore dall' `Select error details` azione. Per informazioni dettagliate, vedere [terminare l'app](#terminate-logic-app)per la logica. |
|||

<a name="complete-template"></a>

## <a name="complete-the-template"></a>Completare il modello

Per specificare i valori per il trigger e le azioni del modello di **recapito correlato nell'ordine usando le sessioni del bus di servizio** , seguire questa procedura. È necessario fornire tutti i valori necessari, contrassegnati da un asterisco ( **\*** ), prima di poter salvare l'app per la logica.

<a name="initialize-session"></a>

### <a name="initialize-the-session"></a>Inizializzare la sessione

* Per il trigger **quando un messaggio viene ricevuto in una coda (blocco di visualizzazione)** , fornire queste informazioni in modo che il modello possa inizializzare una sessione usando la proprietà **ID sessione** , ad esempio:

  ![Dettagli del trigger del bus di servizio per "quando un messaggio viene ricevuto in una coda (blocco di visualizzazione)"](./media/send-related-messages-sequential-convoy/service-bus-check-message-peek-lock-trigger.png)

  > [!NOTE]
  > Inizialmente, l'intervallo di polling è impostato su tre minuti, in modo che l'app per la logica non venga eseguita più frequentemente di quanto previsto e provochi costi di fatturazione imprevisti. Idealmente, impostare l'intervallo e la frequenza su 30 secondi in modo che l'app per la logica venga attivata immediatamente quando arriva un messaggio.

  | Proprietà | Obbligatorio per questo scenario | Valore | Descrizione |
  |----------|----------------------------|-------|-------------|
  | **Nome coda** | Sì | <*nome coda*> | Nome della coda del bus di servizio creata in precedenza. Questo esempio USA "Fabrikam-Service-Bus-Queue". |
  | **Tipo di coda** | Sì | **Principale** | Coda del bus di servizio principale |
  | **ID sessione** | Sì | **Successivo disponibile** | Questa opzione consente di ottenere una sessione per ogni esecuzione del trigger, in base all'ID di sessione del messaggio nella coda del bus di servizio. La sessione è bloccata anche in modo che nessun'altra app per la logica o un altro client possa elaborare messaggi correlati a questa sessione. Le azioni successive del flusso di lavoro elaborano tutti i messaggi associati a tale sessione, come descritto più avanti in questo articolo. <p><p>Di seguito sono riportate altre informazioni sulle altre opzioni di **ID sessione** : <p>- **None**: l'opzione predefinita, che non genera sessioni e non può essere usata per implementare il modello di serie di istruzioni sequenziali. <p>- **Immettere un valore personalizzato**: usare questa opzione quando si conosce l'ID di sessione che si vuole usare ed è sempre necessario eseguire il trigger per tale ID di sessione. <p>**Nota**: il connettore del bus di servizio può salvare un numero limitato di sessioni univoche alla volta dal bus di servizio di Azure alla cache del connettore. Se il numero di sessioni supera questo limite, le sessioni precedenti vengono rimosse dalla cache. Per altre informazioni, vedere [scambiare messaggi nel cloud con app per la logica di Azure e il bus di servizio di Azure](../connectors/connectors-create-api-servicebus.md#connector-reference). |
  | **Interval** | Sì | <*numero di intervalli*> | Il numero di unità di tempo tra ricorrenze prima di verificare la presenza di un messaggio. |
  | **Frequenza** | Sì | **Secondo**, **Minuto**, **Ora**, **Giorno**, **Settimana** o **Mese** | Unità di tempo per la ricorrenza da utilizzare durante la verifica di un messaggio. <p>**Suggerimento**: per aggiungere un **fuso orario** o un' **ora di inizio**, selezionare queste proprietà dall'elenco **Aggiungi nuovo parametro** . |
  |||||

  Per altre informazioni sui trigger, vedere [bus di servizio-quando un messaggio viene ricevuto in una coda (blocco di visualizzazione)](/connectors/servicebus/#when-a-message-is-received-in-a-queue-(peek-lock)). Il trigger restituisce un [ServiceBusMessage](/connectors/servicebus/#servicebusmessage).

Dopo l'inizializzazione della sessione, il flusso di lavoro usa l'azione **Inizializza variabile** per creare una variabile booleana inizialmente impostata su `false` e indica quando le condizioni seguenti sono vere: 

* Nessun altro messaggio nella sessione è disponibile per la lettura.

* Non è più necessario rinnovare il blocco della sessione in modo che l'istanza del flusso di lavoro corrente possa essere completata.

![Dettagli dell'azione "Inizializza variabile" per "init Undone"](./media/send-related-messages-sequential-convoy/init-is-done-variable.png)

Successivamente, nel blocco **try** , il flusso di lavoro esegue azioni sul primo messaggio letto.

<a name="handle-initial-message"></a>

### <a name="handle-the-initial-message"></a>Gestire il messaggio iniziale

La prima azione è un'azione del bus di servizio segnaposto, **Invia messaggio iniziale all'argomento**, che è possibile sostituire con qualsiasi altra azione che si vuole gestire il primo messaggio dalla sessione nella coda. L'ID sessione specifica la sessione da cui ha avuto origine il messaggio.

L'azione del bus di servizio segnaposto invia il primo messaggio a un argomento del bus di servizio specificato dalla proprietà **ID sessione** . In questo modo, tutti i messaggi associati a una sessione specifica passano allo stesso argomento. Tutte le proprietà dell' **ID di sessione** per le azioni successive in questo modello utilizzano lo stesso valore di ID di sessione.

![Dettagli azione del bus di servizio per "Invia messaggio iniziale all'argomento"](./media/send-related-messages-sequential-convoy/send-initial-message-to-topic-action.png)

1. Nell'azione del bus di servizio, **completare il messaggio iniziale in coda**, specificare il nome della coda del bus di servizio e tutti gli altri valori di proprietà predefiniti nell'azione.

   ![Dettagli azione del bus di servizio per "completa messaggio iniziale nella coda"](./media/send-related-messages-sequential-convoy/complete-initial-message-queue.png)

1. Nell'azione del bus di servizio, **abbandonare il messaggio iniziale dalla coda**, fornire il nome per la coda del bus di servizio e mantenendo tutti gli altri valori di proprietà predefiniti nell'azione.

   ![Dettagli dell'azione del bus di servizio per "abbandonare il messaggio iniziale dalla coda"](./media/send-related-messages-sequential-convoy/abandon-initial-message-from-queue.png)

Successivamente, verranno fornite le informazioni necessarie per le azioni che seguono il **messaggio iniziale completo nell'azione di Accodamento** . Si inizierà con le azioni in **mentre sono presenti più messaggi per la sessione nel ciclo di coda** .

<a name="while-more-messages-for-session"></a>

### <a name="while-there-are-more-messages-for-the-session-in-the-queue"></a>Sono presenti più messaggi per la sessione nella coda

Il [ciclo **until** ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) esegue queste azioni mentre i messaggi sono presenti nella coda o fino a un'ora. Per modificare il limite di tempo del ciclo, modificare il valore della proprietà **timeout** del ciclo.

* Consente di ottenere messaggi aggiuntivi dalla coda mentre sono presenti messaggi.

* Verificare il numero di messaggi rimanenti. Se i messaggi sono ancora presenti, continuare l'elaborazione dei messaggi. Se non esistono altri messaggi, il flusso di lavoro imposta la `isDone` variabile su `true` e chiude il ciclo.

![Fino a quando non vengono elaborati i messaggi in coda](./media/send-related-messages-sequential-convoy/while-more-messages-for-session-in-queue.png)

1. Nell'azione del bus di servizio, **ottenere messaggi aggiuntivi dalla sessione**, specificare il nome della coda del bus di servizio. In caso contrario, conserva tutti gli altri valori di proprietà predefiniti nell'azione.

   > [!NOTE]
   > Per impostazione predefinita, il numero massimo di messaggi è impostato su `175` , ma questo limite è influenzato dalle dimensioni del messaggio e dalla proprietà della dimensione massima dei messaggi nel bus di servizio. Per ulteriori informazioni, vedere [dimensioni dei messaggi per una coda](../service-bus-messaging/service-bus-quotas.md).

   ![Azione del bus di servizio: "ottenere messaggi aggiuntivi dalla sessione"](./media/send-related-messages-sequential-convoy/get-additional-messages-from-session.png)

   Il flusso di lavoro viene quindi suddiviso in questi rami paralleli:

   * Se si verifica un errore o un errore durante il controllo della presenza di messaggi aggiuntivi, impostare la `isDone` variabile su `true` .

   * Il **processo elabora messaggi se si verifica una** condizione che controlla se il numero di messaggi rimanenti è pari a zero. Se sono presenti FALSE e più messaggi, continuare l'elaborazione. Se true e non esistono altri messaggi, il flusso di lavoro imposta la `isDone` variabile su `true` .

   ![Condizione-elabora i messaggi se presenti](./media/send-related-messages-sequential-convoy/process-messages-if-any.png)

   Nella sezione **If false** , un ciclo **For Each** elabora ogni messaggio in ordine FIFO (First-in, First-out). Nelle **Impostazioni**del ciclo, l'impostazione del **controllo della concorrenza** è impostata su `1` , pertanto viene elaborato un solo messaggio alla volta.

   !["For each" loop-elabora ogni messaggio uno alla volta](./media/send-related-messages-sequential-convoy/for-each-additional-message.png)

1. Per le azioni del bus di servizio, **completare il messaggio in una coda** e **abbandonare il messaggio in una coda**, fornire il nome per la coda del bus di servizio.

   ![Azioni del bus di servizio: "completare il messaggio in una coda" e "abbandonare il messaggio in una coda"](./media/send-related-messages-sequential-convoy/abandon-or-complete-message-in-queue.png)

   Al termine del **periodo di tempo in cui sono presenti più messaggi per la sessione nella coda** , il flusso di lavoro imposta la `isDone` variabile su `true` .

A questo punto, verranno fornite le informazioni necessarie per le azioni nel ciclo di **rinnovo del blocco della sessione fino a quando non viene annullato** .

<a name="renew-session-while-messages-exist"></a>

### <a name="renew-session-lock-until-cancelled"></a>Rinnovo blocco sessione fino all'annullamento

Questo [ciclo **until** ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) garantisce che il blocco della sessione venga mantenuto da questa app per la logica mentre i messaggi sono presenti nella coda o fino a quando non viene superata un'ora eseguendo queste azioni. Per modificare il limite di tempo del ciclo, modificare il valore della proprietà **timeout** del ciclo.

* Ritardo per 25 secondi o per un periodo di tempo inferiore alla durata del timeout del blocco per la coda in fase di elaborazione. La durata minima del blocco è di 30 secondi, quindi il valore predefinito è sufficiente. Tuttavia, è possibile ottimizzare il numero di volte in cui il ciclo viene eseguito regolando in modo appropriato.

* Controllare se la `isDone` variabile è impostata su `true` .

  * Se `isDone` non è impostato su `true` , il flusso di lavoro sta ancora elaborando i messaggi, quindi il flusso di lavoro rinnova il blocco sulla sessione nella coda e controlla nuovamente la condizione del ciclo.

    È necessario specificare il nome per la coda del bus di servizio nell'azione del bus di servizio, [**rinnovare il blocco sulla sessione in una coda**](#renew-lock-on-session).

  * Se `isDone` è impostato su `true` , il flusso di lavoro non rinnova il blocco sulla sessione nella coda e chiude il ciclo.

  ![Fino al ciclo-"rinnovo blocco sessione fino a annullamento"](./media/send-related-messages-sequential-convoy/renew-lock-until-session-cancelled.png)

<a name="renew-lock-on-session"></a>

#### <a name="renew-lock-on-the-session-in-a-queue"></a>Rinnovare il blocco sulla sessione in una coda

Questa azione del bus di servizio rinnova il blocco sulla sessione nella coda mentre il flusso di lavoro sta ancora elaborando i messaggi.

* Nell'azione del bus di servizio, **rinnovare il blocco sulla sessione in una coda**, specificare il nome della coda del bus di servizio.

  ![Azione del bus di servizio: "rinnovo blocco per sessione nella coda"](./media/send-related-messages-sequential-convoy/renew-lock-on-session-in-queue.png)

Successivamente, verranno fornite le informazioni necessarie per l'azione del bus di servizio, la **chiusura di una sessione in una coda e l'esito positivo**.

<a name="close-session-succeed"></a>

### <a name="close-a-session-in-a-queue-and-succeed"></a>Chiude una sessione in una coda e ha esito positivo

Questa azione del bus di servizio chiude la sessione nella coda dopo che il flusso di lavoro ha completato l'elaborazione di tutti i messaggi disponibili nella coda o se il flusso di lavoro abbandona il messaggio iniziale.

* Nell'azione del bus di servizio **chiudere una sessione in una coda e**ottenere il nome della coda del bus di servizio.

  ![Azione del bus di servizio: "chiudere una sessione in una coda e avere esito positivo"](./media/send-related-messages-sequential-convoy/close-session-in-queue-succeed.png)

Le sezioni seguenti descrivono le azioni nella `Catch` sezione, che gestiscono gli errori e le eccezioni che si verificano nel flusso di lavoro.

<a name="close-session-fail"></a>

### <a name="close-a-session-in-a-queue-and-fail"></a>Chiude una sessione in una coda e non riesce

Questa azione del bus di servizio viene sempre eseguita come prima azione nell' `Catch` ambito e chiude la sessione nella coda.

* Nell'azione del bus di servizio **chiudere una sessione in una coda e generare un errore e**specificare il nome della coda del bus di servizio.

  ![Azione del bus di servizio: "chiudere una sessione in una coda e non riuscire"](./media/send-related-messages-sequential-convoy/close-session-in-queue-fail.png)

Successivamente, il flusso di lavoro crea una matrice con gli input e gli output di tutte le azioni nell'ambito, in `Try` modo che l'app per la logica possa accedere alle informazioni sull'errore o sul problema che si è verificato.

<a name="find-failure-message"></a>

### <a name="find-failure-msg-from-try-block"></a>Trova il messaggio di errore dal blocco ' Try '

Questa [azione di **matrice di filtri** ](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) crea una matrice con gli input e gli output di tutte le azioni all'interno dell' `Try` ambito in base ai criteri specificati tramite la [ `result()` funzione](../logic-apps/workflow-definition-language-functions-reference.md#result). In questo caso, questa azione restituisce gli output delle azioni con `Failed` stato usando la [ `equals()` funzione](../logic-apps/workflow-definition-language-functions-reference.md#equals) e la [ `item()` funzione](../logic-apps/workflow-definition-language-functions-reference.md#item).

![Azione filtra matrice-"trova il messaggio di errore dal blocco ' Try '"](./media/send-related-messages-sequential-convoy/find-failure-message.png)

Di seguito è illustrata la definizione JSON per questa azione:

```json
"Find_failure_msg_from_'Try'_block": {
   "inputs": {
      "from": "@Result('Try')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "Close_the_session_in_the_queue_and_fail": [
         "Succeeded"
      ]
   },
   "type": "Query"
},
```

Successivamente, il flusso di lavoro crea una matrice con un oggetto JSON contenente le informazioni sull'errore nella matrice restituita dall' `Find failure msg from 'Try' block` azione.

<a name="select-error-details"></a>

### <a name="select-error-details"></a>Seleziona dettagli errore

Questa [azione **Select** ](../logic-apps/logic-apps-perform-data-operations.md#select-action) crea una matrice che contiene oggetti JSON basati sulla matrice di input di output dell'azione precedente, `Find failure msg from 'Try' block` . In particolare, questa azione restituisce una matrice con solo le proprietà specificate per ogni oggetto nella matrice. In questo caso, la matrice contiene il nome dell'azione e le proprietà dei risultati degli errori.

![Selezionare l'azione-"seleziona Dettagli errore"](./media/send-related-messages-sequential-convoy/select-error-details.png)

Di seguito è illustrata la definizione JSON per questa azione:

```json
"Select_error_details": {
   "inputs": {
      "from": "@body('Find_failure_msg_from_''Try''_block')[0]['outputs']",
      "select": {
         "action": "@item()['name']",
         "errorResult": "@item()"
      }
   },
   "runAfter": {
      "Find_failure_msg_from_'Try'_block": [
         "Succeeded"
      ]
   },
   "type": "Select"
},
```

Il flusso di lavoro interrompe quindi l'esecuzione dell'app per la logica e restituisce lo stato di esecuzione insieme ad altre informazioni sull'errore o sul problema che si è verificato.

<a name="terminate-logic-app"></a>

### <a name="terminate-logic-app-run"></a>Termina esecuzione App per la logica

Questa [azione **termina** ](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) arresta l'esecuzione dell'app per la logica e restituisce lo `Failed` stato per l'esecuzione dell'app per la logica con l'ID sessione e il risultato dell'errore dell' `Select error details` azione.

![Termina l'azione per arrestare l'esecuzione dell'app per la logica](./media/send-related-messages-sequential-convoy/terminate-logic-app-run.png)

Di seguito è illustrata la definizione JSON per questa azione:

```json
"Terminate": {
   "description": "This Failure Termination only runs if the Close Session upon Failure action runs - otherwise the LA will be terminated as Success",
   "inputs": {
      "runError": {
         "code": "",
         "message": "There was an error processing messages for Session ID @{triggerBody()?['SessionId']}. The following error(s) occurred: @{body('Select_error_details')['errorResult']}"
         },
         "runStatus": "Failed"
      },
      "runAfter": {
         "Select_error_details": [
            "Succeeded"
         ]
      },
      "type": "Terminate"
   }
},
```

## <a name="save-and-run-logic-app"></a>Salvare ed eseguire l'app per la logica

Dopo aver completato il modello, è ora possibile salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

Per testare l'app per la logica, inviare messaggi alla coda del bus di servizio. 

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sui [trigger e sulle azioni del connettore del bus di servizio](/connectors/servicebus/)
