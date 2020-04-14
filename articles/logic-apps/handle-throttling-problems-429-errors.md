---
title: Gestire i problemi di limitazione delle richieste o gli errori '429 - Troppe richieste'Handle throttling problems, or '429 - Too many requests' errors
description: Come risolvere i problemi di limitazione delle richieste o 'HTTP 429 troppe richieste' errori, in App per la logica di AzureHow to work around throttling problems, or 'HTTP 429 Too many requests' errors, in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: fbfd52065bc0522668488492de2181f252f86a4e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272679"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Gestire i problemi di limitazione delle richieste (429 - errori "Troppe richieste") nelle app per la logica di AzureHandle throttling problems (429 - "Too many requests' errors) in Azure Logic Apps

Nelle [app per](../logic-apps/logic-apps-overview.md)la logica di Azure l'app per la logica restituisce un errore ["HTTP 429 Too many requests"](https://developer.mozilla.org/docs/Web/HTTP/Status/429) quando si verifica la limitazione delle richieste, che si verifica quando il numero di richieste supera la velocità con cui la destinazione può gestire in un determinato periodo di tempo. La limitazione delle richieste può creare problemi quali l'elaborazione ritardata dei dati, la riduzione della velocità delle prestazioni e gli errori, ad esempio il superamento dei criteri di ripetizione dei tentativi specificati.

![Limitazione del connettore SQL Server](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Ecco alcuni tipi comuni di limitazione che potrebbero verificarsi nell'app per la logica:Here are some common types of throttling that your logic app might experience:

* [App per la logica](#logic-app-throttling)
* [Connettore](#connector-throttling)
* [Servizio o sistema di destinazione](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Limitazione delle app per la logica

Il servizio App per la logica di Azure ha i propri limiti di [velocità effettiva.](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) Pertanto, se l'app per la logica supera questi limiti, la risorsa dell'app per la logica viene limitata, non solo un'istanza o un'esecuzione specifica.

Per trovare gli eventi di limitazione a questo livello, controllare il riquadro **Metriche** dell'app per la logica nel portale di Azure.To find throttling events at this level, check your logic app's Metrics pane in the Azure portal.

1. Nel [portale di Azure](https://portal.azure.com)aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **Monitoraggio,** selezionare **Metriche**.

1. In **Titolo grafico**selezionare Aggiungi **metrica** per aggiungere un'altra metrica all'esistente.

1. Nella prima barra delle metriche, dall'elenco **METRIC,** selezionare **Azioni con estensione eventi**. Nella seconda barra delle metriche, dall'elenco **METRIC,** selezionare **Attiva eventi limitati**.

Per gestire la limitazione delle richieste a questo livello, sono disponibili le opzioni seguenti:To handle throttling at this level, you have these options:

* Limitare il numero di istanze dell'app per la logica che possono essere eseguite contemporaneamente.

  Per impostazione predefinita, se la condizione di trigger dell'app per la logica viene soddisfatta più di una volta contemporaneamente, più istanze di trigger per l'app per la logica vengono eseguite contemporaneamente o *in parallelo.* Questo comportamento significa che ogni istanza del trigger viene attivata prima del termine dell'esecuzione dell'istanza del flusso di lavoro precedente.

  Sebbene il numero predefinito di istanze di trigger che possono essere eseguite contemporaneamente sia [illimitato,](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)è possibile limitare questo numero [attivando l'impostazione](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)di concorrenza del trigger e, se necessario, selezionare un limite diverso dal valore predefinito.

* Abilitare la modalità di velocità effettiva elevata.

  Un'app per la logica ha un [limite predefinito per il numero di azioni che possono essere eseguite su un intervallo di rollino](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)di 5 minuti. Per aumentare questo limite al numero massimo di azioni, attivare la [modalità velocità effettiva elevata](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode) nell'app per la logica.

* Disabilitare il comportamento di debatching di matrice ("split on") nei trigger.

  Se un trigger restituisce una matrice per le azioni rimanenti del flusso di lavoro da elaborare, [l'impostazione **Split On** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) del trigger suddivide gli elementi della matrice e avvia un'istanza del flusso di lavoro per ogni elemento della matrice, attivando in modo efficace più esecuzioni simultanee fino al limite Split [ **On** ](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Per controllare la limitazione delle richieste, disattivare il comportamento **Split On** e fare in modo che l'app per la logica elabori l'intera matrice con una singola chiamata, anziché gestire un singolo elemento per chiamata.

* Refactoring delle azioni in app per la logica più piccole.

  Come accennato in precedenza, un'app per la logica è limitata a un numero predefinito di azioni che possono essere eseguite in un periodo di [5 minuti.](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) Sebbene sia possibile aumentare questo limite abilitando la [modalità a velocità effettiva elevata,](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)è anche possibile considerare se si desidera suddividere le azioni dell'app per la logica in app per la logica più piccole in modo che il numero di azioni eseguite in ogni app per la logica rimanga al di sotto del limite. In questo modo, si riduce il carico su una singola risorsa app per la logica e si distribuisce il carico tra più app per la logica. Questa soluzione funziona meglio per le azioni che gestiscono set di dati di grandi dimensioni o spin up così tante azioni in esecuzione contemporaneamente, iterazioni del ciclo o azioni all'interno di ogni iterazione del ciclo che superano il limite di esecuzione dell'azione.

  Ad esempio, questa app per la logica esegue tutte le operazioni per ottenere tabelle da un database di SQL ServerSQL Server e ottenere le righe da ogni tabella. Il **ciclo For each** scorre contemporaneamente ogni tabella in modo che l'azione Ottieni **righe** restituisca le righe per ogni tabella. In base alla quantità di dati in tali tabelle, queste azioni potrebbero superare il limite per le esecuzioni di azioni.

  ![Refactoring dell'app per la logica "prima"](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  Dopo il refactoring, l'app per la logica è ora un'app per la logica padre e figlio. L'elemento padre ottiene le tabelle da SQL ServerSQL Server e quindi chiama un'app per la logica figlio per ogni tabella per ottenere le righe:The parent gets the tables from SQL Serverand then calls a child logic app for each table to get the rows:

  ![Creare un'app per la logica per un'azioneCreate logic app for one action](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  Ecco l'app per la logica figlio chiamata dall'app per la logica padre per ottenere le righe per ogni tabella:Here's the child logic app that's called by the parent logic app to get the rows for each table:

  ![Creare un'altra app per la logica per una seconda azioneCreate another logic app for a second action](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Limitazione del connettore

Ogni connettore ha i propri limiti di limitazione, che è possibile trovare nella pagina di riferimento tecnico del connettore. Ad esempio, il [connettore del bus](https://docs.microsoft.com/connectors/servicebus/) di servizio di Azure ha un limite di limitazione che consente fino a 6.000 chiamate al minuto, mentre il connettore SQL Server ha limiti di [limitazione che variano in base al tipo di operazione.](https://docs.microsoft.com/connectors/sql/)

Alcuni trigger e azioni, ad esempio HTTP, hanno un ["criterio di ripetizione dei tentativi"](../logic-apps/logic-apps-exception-handling.md#retry-policies) che è possibile personalizzare in base ai limiti dei [criteri di ripetizione](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) dei tentativi per implementare la gestione delle eccezioni. Questo criterio specifica se e con quale frequenza un trigger o un'azione ritenta una richiesta quando la richiesta originale ha esito negativo o scade e restituisce una risposta 408, 429 o 5xx. Pertanto, quando viene avviata la limitazione delle richieste e restituisce un errore 429, App per la logica segue i criteri di ripetizione dei tentativi se supportati.

Per sapere se un trigger o un'azione supporta i criteri di ripetizione dei tentativi, controllare le impostazioni del trigger o dell'azione. Per visualizzare i tentativi di un trigger o di un'azione, passare alla cronologia delle esecuzioni dell'app per la logica, selezionare l'esecuzione che si vuole esaminare ed espandere il trigger o l'azione per visualizzare i dettagli su input, output ed eventuali tentativi, ad esempio:

![Visualizzare la cronologia di esecuzione, i tentativi, gli input e gli output dell'azione](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

Sebbene la cronologia dei tentativi fornisca informazioni sugli errori, è possibile che si verifichino problemi di differenziazione tra la limitazione delle richieste del connettore e la [limitazione delle destinazioni.](#destination-throttling) In questo caso, potrebbe essere necessario esaminare i dettagli della risposta o eseguire alcuni calcoli dell'intervallo di limitazione per identificare l'origine.

Per le app per la logica nel servizio app per la logica di Azure multi-tenant a livello globale, multi-tenant, la limitazione delle richieste avviene a livello di *connessione.* Così, ad esempio, per le app per la logica eseguite in un ambiente del servizio di [integrazione (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)la limitazione delle richieste viene comunque eseguita per le connessioni non ISE perché vengono eseguite nel servizio App per la logica globale multi-tenant. Tuttavia, le connessioni ISE, create dai connettori ISE, non vengono limitate perché vengono eseguite nell'ISE.

Per gestire la limitazione delle richieste a questo livello, sono disponibili le opzioni seguenti:To handle throttling at this level, you have these options:

* Configurare più connessioni per una singola azione in modo che l'app per la logica partizioni i dati per l'elaborazione.

  Per questa opzione, valutare se è possibile distribuire il carico di lavoro dividendo le richieste di un'azione tra più connessioni alla stessa destinazione usando le stesse credenziali.

  Si supponga, ad esempio, che l'app per la logica ottenga le tabelle da un database di SQL Server e quindi ottenga le righe da ogni tabella. In base al numero di righe che è necessario elaborare, è possibile utilizzare più connessioni e più cicli **For each** per dividere il numero totale di righe in set più piccoli per l'elaborazione. Questo scenario utilizza due **ciclo For each** per dividere il numero totale di righe a metà. Il primo **ciclo For each** usa un'espressione che ottiene la prima metà. L'altro **ciclo For each** usa un'espressione diversa che ottiene la seconda metà, ad esempio:The other For each loop uses a different expression that gets the second half, for example:<p>

    * Espressione 1: `take()` la funzione ottiene la parte anteriore di una raccolta. Per ulteriori informazioni, [ **`take()`** ](workflow-definition-language-functions-reference.md#take)vedere la funzione .

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * Espressione 2: `skip()` la funzione rimuove la parte anteriore di una raccolta e restituisce tutti gli altri elementi. Per ulteriori informazioni, [ **`skip()`** ](workflow-definition-language-functions-reference.md#skip)vedere la funzione .

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Ecco un esempio visivo che mostra come usare queste espressioni:Here's a visual example that shows how you can use these expressions:

    ![Creare e usare più connessioni per una singola azioneCreate and use multiple connections for a single action](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Impostare una connessione diversa per ogni azione.

  Per questa opzione, valutare se è possibile distribuire il carico di lavoro distesando le richieste di ogni azione tramite la propria connessione, anche quando le azioni si connettono allo stesso servizio o sistema e utilizzano le stesse credenziali.

  Si supponga, ad esempio, che l'app per la logica ottenga le tabelle da un database di SQL ServerSQL Server e ottenga ogni riga in ogni tabella. È possibile utilizzare connessioni separate in modo che le tabelle utilizzino una connessione, mentre il recupero di ogni riga utilizza un'altra connessione.

  ![Creare e utilizzare connessioni diverse per ogni azioneCreate and use a different connections for each action](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* Modificare la concorrenza o il parallelismo in un [ciclo "For each".](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)

  Per impostazione predefinita, le iterazioni del ciclo "For each" vengono eseguite contemporaneamente fino al limite di [concorrenza.](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) Se si dispone di un connettore che viene limitato all'interno di un ciclo "For each", è possibile ridurre il numero di iterazioni del ciclo eseguite in parallelo. Per altre informazioni, vedere gli argomenti seguenti:
  
  * [Cicli "For each" - modificano la concorrenza o vengono eseguiti in sequenza](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [Schema del linguaggio di definizione del flusso di lavoro - Per ogni ciclo](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [Schema del linguaggio di definizione del flusso di lavoro - Modificare la concorrenza del ciclo "For each"](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [Schema del linguaggio di definizione del flusso di lavoro - Eseguire cicli "For each" in sequenza](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>Limitazione del servizio di destinazione o del sistema

Anche se un connettore ha i propri limiti di limitazione, il servizio o il sistema di destinazione chiamato dal connettore potrebbe anche avere limiti di limitazione. Ad esempio, alcune API in Microsoft Exchange Server hanno limiti di limitazione più severi rispetto al connettore di Outlook di Office 365.

Per impostazione predefinita, le istanze di un'app per la logica e tutti i cicli o rami all'interno di tali istanze vengono eseguiti *in parallelo.* Questo comportamento significa che più istanze possono chiamare lo stesso endpoint contemporaneamente. Ogni istanza non conosce l'esistenza dell'altra, pertanto i tentativi di ritentare le azioni non riuscite possono creare [race condition](https://en.wikipedia.org/wiki/Race_condition) in cui più chiamate tentano di essere eseguite contemporaneamente, ma per avere esito positivo, tali chiamate devono arrivare al servizio o al sistema di destinazione prima che inizi a verificarsi la limitazione delle richieste.

Si supponga, ad esempio, di disporre di una matrice con 100 elementi. Utilizzare un ciclo "for each" per scorrere la matrice e attivare il controllo della concorrenza del ciclo in modo da limitare il numero di iterazioni parallele a 20 o il [limite predefinito corrente.](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits) All'interno di tale ciclo, un'azione inserisce un elemento dalla matrice in un database di SQL Server, che consente solo 15 chiamate al secondo. Questo scenario comporta un problema di limitazione perché un backlog di tentativi compilare e mai arrivare a eseguire.

Questa tabella descrive la sequenza temporale per ciò che accade nel ciclo quando l'intervallo tra tentativi dell'azione è 1 secondo:

| Temporizzazione | Numero di azioni eseguite | Numero di azioni non riuscite | Numero di tentativi in attesa |
|---------------|----------------------------|-----------------------------|---------------------------|
| T - 0 secondi | 20 inserti | 5 errore, a causa del limite SQL | 5 tentativi |
| T - 0,5 secondi | 15 inserti, a causa di precedenti 5 tentativi in attesa | Tutti i 15 non riescono, a causa del precedente limite SQL ancora in vigore per altri 0,5 secondi | 20 tentativi <br>(precedente 5 x 15 nuovo) |
| T - 1 secondo | 20 inserti | 5 esito negativo più i 20 tentativi precedenti, a causa del limite SQL | 25 tentativi (precedente 20 x 5 nuovi)
|||||

Per gestire la limitazione delle richieste a questo livello, sono disponibili le opzioni seguenti:To handle throttling at this level, you have these options:

* Creare app per la logica in modo che ognuna gestisca una singola operazione.

  * Continuando con lo scenario di SQL Server di esempio in questa sezione, è possibile creare un'app per la logica che inserisce gli elementi della matrice in una coda, ad esempio una coda del bus di servizio di Azure.Continue with the example SQL Server scenario in this section, you can create a logic app that puts array items into a queue, such as an [Azure Service Bus queue](../connectors/connectors-create-api-servicebus.md). Si crea quindi un'altra app per la logica che esegue solo l'operazione di inserimento per ogni elemento nella coda. In questo modo, viene eseguita una sola istanza dell'app per la logica in un momento specifico, che completa l'operazione di inserimento e passa all'elemento successivo nella coda oppure l'istanza riceve errori 429 ma non tenta tentativi non produttivi.

  * Creare un'app per la logica padre che chiama un'app per la logica figlio o annidata per ogni azione. Se l'elemento padre deve chiamare app figlio diverse in base al risultato dell'elemento padre, puoi usare un'azione di condizione o di interruttore che determina l'app figlio da chiamare. Questo modello consente di ridurre il numero di chiamate o operazioni.

    Si supponga, ad esempio, di disporre di due app per la logica, ognuna con un trigger di polling che controlla l'account di posta elettronica ogni minuto per un oggetto specifico, ad esempio "Esito positivo" o "Errore". Questa configurazione comporta 120 chiamate all'ora. Al contrario, se si crea una singola app per la logica padre che esegue il polling ogni minuto ma chiama un'app per la logica figlio che viene eseguita in base al fatto che il soggetto sia "Success" o "Failure", si riduce il numero di controlli di polling a metà o 60 in questo caso.

* Impostare l'elaborazione batch.

  Se il servizio di destinazione supporta le operazioni batch, è possibile risolvere la limitazione delle richieste elaborando gli elementi in gruppi o batch, anziché singolarmente. Per implementare la soluzione di elaborazione batch, creare un'app per la logica "ricevitore batch" e un'app per la logica "mittente batch". Il mittente del batch raccoglie messaggi o elementi fino a quando non vengono soddisfatti i criteri specificati e quindi invia tali messaggi o elementi in un singolo gruppo. Il ricevitore batch accetta tale gruppo ed elabora tali messaggi o elementi. Per ulteriori informazioni, consultate [Elaborazione batch dei messaggi in gruppi.](../logic-apps/logic-apps-batch-process-send-receive-messages.md)

* Utilizzare le versioni webhook per trigger e azioni, anziché le versioni di polling.

  Perché? Un trigger di polling continua a controllare il servizio o il sistema di destinazione a intervalli specifici. Un intervallo molto frequente, ad esempio ogni secondo, può creare problemi di limitazione. Tuttavia, un trigger o un'azione webhook, ad esempio [HTTP Webhook](../connectors/connectors-native-webhook.md), crea una sola chiamata al servizio o al sistema di destinazione, che si verifica in fase di sottoscrizione e richiede che la destinazione notifica il trigger o l'azione solo quando si verifica un evento. In questo modo, il trigger o l'azione non deve controllare continuamente la destinazione.
  
  Pertanto, se il servizio o il sistema di destinazione supporta i webhook o fornisce un connettore con una versione webhook, questa opzione è migliore rispetto all'utilizzo della versione di polling. Per identificare i trigger e le azioni `ApiConnectionWebhook` del webhook, verificare che dispongano del tipo o che non richiedano di specificare una ricorrenza. Per ulteriori informazioni, vedere [Trigger APIConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e [azione APIConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action).

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni sui [limiti e sulla configurazione delle app per la logica](../logic-apps/logic-apps-limits-and-config.md)
