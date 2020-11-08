---
title: Gestire i problemi di limitazione o gli errori ' 429-troppe richieste '
description: Come risolvere i problemi relativi alla limitazione delle richieste oppure a errori HTTP 429 troppi richieste in app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: ea153b1927a337be29c2eb69e2417cc250abf5e8
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366052"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Gestire i problemi di limitazione (429-"numero eccessivo di richieste") nelle app per la logica di Azure

In [app](../logic-apps/logic-apps-overview.md)per la logica di Azure, l'app per la logica restituisce un [errore "http 429 numero eccessivo di richieste"](https://developer.mozilla.org/docs/Web/HTTP/Status/429) quando si verifica la limitazione, che si verifica quando il numero di richieste supera la velocità di gestione della destinazione in un determinato periodo di tempo. La limitazione può creare problemi, ad esempio l'elaborazione ritardata dei dati, una riduzione della velocità delle prestazioni ed errori quali il superamento del criterio di ripetizione dei tentativi specificato.

![Limitazione delle richieste in SQL Server Connector](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Di seguito sono riportati alcuni tipi comuni di limitazione che possono verificarsi nell'app per la logica:

* [App per la logica](#logic-app-throttling)
* [Connettore](#connector-throttling)
* [Servizio o sistema di destinazione](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Limitazione app per la logica

Il servizio app per la logica di Azure ha [limiti di velocità effettiva](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Quindi, se l'app per la logica supera questi limiti, la risorsa dell'app per la logica viene limitata, non solo un'istanza specifica o l'esecuzione.

Per trovare gli eventi di limitazione a questo livello, controllare il riquadro **metriche** dell'app per la logica nella portale di Azure.

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica, in **monitoraggio** , selezionare **metriche**.

1. In **titolo grafico** selezionare **Aggiungi metrica** per aggiungere un'altra metrica alla esistente.

1. Nella prima barra metrica, dall'elenco **metrica** , selezionare **azione limitazione eventi**. Nell'elenco **metrica** della seconda barra metrica selezionare **attiva eventi** limitati.

Per gestire la limitazione a questo livello, sono disponibili le opzioni seguenti:

* Limitare il numero di istanze dell'app per la logica che possono essere eseguite contemporaneamente.

  Per impostazione predefinita, se la condizione del trigger dell'app per la logica viene soddisfatta più di una volta allo stesso tempo, più istanze del trigger per l'app per la logica vengono eseguite simultaneamente o *in parallelo*. Questo comportamento indica che ogni istanza del trigger viene attivata prima del completamento dell'esecuzione dell'istanza del flusso di lavoro precedente.

  Sebbene il numero predefinito di istanze del trigger che possono essere eseguite simultaneamente non sia [illimitato](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits), è possibile limitare questo numero attivando [l'impostazione di concorrenza del trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)e, se necessario, selezionare un limite diverso dal valore predefinito.

* Abilitare la modalità velocità effettiva elevata.

  Un'app per la logica ha un [limite predefinito per il numero di azioni che possono essere eseguite in un intervallo di 5 minuti in sequenza](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Per aumentare questo limite al numero massimo di azioni, attivare la [modalità di velocità effettiva elevata](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode) nell'app per la logica.

* Disabilitare il comportamento di debatch di matrici ("Split on") nei trigger.

  Se un trigger restituisce una matrice per le azioni del flusso di lavoro rimanenti da elaborare, l' [impostazione **Split on**](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) del trigger suddivide gli elementi della matrice e avvia un'istanza del flusso di lavoro per ogni elemento della matrice, attivando in modo efficace più esecuzioni simultanee fino alla [ **divisione al** limite](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Per controllare la limitazione delle richieste, disattivare il comportamento di **suddivisione** e fare in modo che l'app per la logica elabori l'intera matrice con una singola chiamata, anziché gestire un singolo elemento per chiamata.

* Effettuare il refactoring delle azioni in app per la logica più piccole.

  Come indicato in precedenza, un'app per la logica è limitata a un [numero predefinito di azioni che possono essere eseguite in un periodo di 5 minuti](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Sebbene sia possibile aumentare questo limite abilitando la [modalità di velocità effettiva elevata](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode), è anche possibile considerare se si vuole suddividere le azioni dell'app per la logica in app per la logica più piccole, in modo che il numero di azioni eseguite in ogni app per la logica rimanga inferiore al limite. In questo modo, si riduce il carico di una singola risorsa app per la logica e si distribuisce il carico tra più app per la logica. Questa soluzione funziona meglio per le azioni che gestiscono set di dati di grandi dimensioni o attivano numerose azioni simultanee, iterazioni del ciclo o azioni all'interno di ogni iterazione del ciclo che superano il limite di esecuzione delle azioni.

  Ad esempio, questa app per la logica esegue tutte le operazioni per recuperare le tabelle da un database di SQL Server e ottiene le righe da ogni tabella. Il ciclo **for each** scorre simultaneamente ogni tabella in modo che l'azione **Ottieni righe** restituisca le righe per ogni tabella. In base alle quantità di dati presenti in tali tabelle, queste azioni potrebbero superare il limite per le esecuzioni di azioni.

  ![Refactoring dell'app per la logica "before"](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  Dopo il refactoring, l'app per la logica è ora un'app per la logica padre e figlio. Il padre ottiene le tabelle da SQL Server e quindi chiama un'app per la logica figlio per ogni tabella per ottenere le righe:

  ![Creare un'app per la logica per un'azione](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  Di seguito è riportata l'app per la logica figlio chiamata dall'app per la logica padre per ottenere le righe per ogni tabella:

  ![Creare un'altra app per la logica per una seconda azione](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Limitazione del connettore

Ogni connettore ha limiti di limitazione, che è possibile trovare nella pagina di riferimento tecnico del connettore. Il [connettore del bus di servizio di Azure](/connectors/servicebus/) , ad esempio, ha un limite di limitazione che consente fino a 6.000 chiamate al minuto, mentre il connettore SQL Server presenta limiti di [limitazione che variano in base al tipo di operazione](/connectors/sql/).

Alcuni trigger e azioni, ad esempio HTTP, hanno un ["criterio di ripetizione"](../logic-apps/logic-apps-exception-handling.md#retry-policies) che è possibile personalizzare in base ai [limiti dei criteri di ripetizione](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) per implementare la gestione delle eccezioni. Questo criterio specifica se e con quale frequenza un trigger o un'azione Ritenta una richiesta quando la richiesta originale ha esito negativo o scade e restituisce una risposta 408, 429 o 5xx. Quindi, quando viene avviata la limitazione e viene restituito un errore 429, app per la logica segue i criteri di ripetizione dei tentativi, laddove supportati.

Per sapere se un trigger o un'azione supporta i criteri di ripetizione dei tentativi, controllare le impostazioni del trigger o dell'azione. Per visualizzare i tentativi di un trigger o un'azione, passare alla cronologia delle esecuzioni dell'app per la logica, selezionare l'esecuzione che si vuole rivedere ed espandere il trigger o l'azione per visualizzare i dettagli degli input, degli output e degli eventuali tentativi, ad esempio:

![Visualizzare la cronologia di esecuzione, i tentativi, gli input e gli output dell'azione](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

Sebbene la cronologia dei tentativi fornisca informazioni sugli errori, è possibile che si siano riscontrati problemi di differenziazione tra la limitazione del connettore e la [limitazione della destinazione](#destination-throttling). In questo caso, potrebbe essere necessario esaminare i dettagli della risposta o eseguire alcuni calcoli di intervallo di limitazione per identificare l'origine.

Per le app per la logica nel servizio app per la logica di Azure globale multi-tenant, la limitazione avviene a livello di *connessione* . Quindi, ad esempio, per le app per la logica che vengono eseguite in un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), la limitazione delle richieste continua a verificarsi per le connessioni non ISE perché vengono eseguite nel servizio app per la logica globale multi-tenant. Tuttavia, le connessioni ISE, create dai connettori ISE, non sono limitate perché vengono eseguite in ISE.

Per gestire la limitazione a questo livello, sono disponibili le opzioni seguenti:

* Configurare più connessioni per una singola azione in modo che l'app per la logica partizioni i dati per l'elaborazione.

  Per questa opzione, valutare se è possibile distribuire il carico di lavoro suddividendo le richieste di un'azione tra più connessioni alla stessa destinazione usando le stesse credenziali.

  Si supponga, ad esempio, che l'app per la logica ottenga tabelle da un database di SQL Server e quindi ottenga le righe da ogni tabella. In base al numero di righe da elaborare, è possibile usare più connessioni e più cicli **for each** per dividere il numero totale di righe in set più piccoli per l'elaborazione. Questo scenario usa due cicli **for each** per suddividere il numero totale di righe nella metà. Il primo ciclo **for each** usa un'espressione che ottiene la prima metà. L'altro ciclo **for each** usa un'espressione diversa che ottiene la seconda metà, ad esempio:<p>

    * Espressione 1: la `take()` funzione ottiene l'inizio di una raccolta. Per ulteriori informazioni, vedere la [ **`take()`** funzione](workflow-definition-language-functions-reference.md#take).

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * Espressione 2: la `skip()` funzione rimuove la parte anteriore di una raccolta e restituisce tutti gli altri elementi. Per ulteriori informazioni, vedere la [ **`skip()`** funzione](workflow-definition-language-functions-reference.md#skip).

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Ecco un esempio visivo che Mostra come è possibile usare queste espressioni:

    ![Creazione e utilizzo di più connessioni per una singola azione](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Configurare una connessione diversa per ogni azione.

  Per questa opzione, valutare se è possibile distribuire il carico di lavoro distribuendo le richieste di ogni azione tramite la propria connessione, anche quando le azioni si connettono allo stesso servizio o sistema e utilizzano le stesse credenziali.

  Si supponga, ad esempio, che l'app per la logica ottenga le tabelle da un database di SQL Server e ottenga ogni riga in ogni tabella. È possibile utilizzare connessioni separate, in modo che il recupero delle tabelle utilizzi una sola connessione, mentre il recupero di ogni riga utilizza un'altra connessione.

  ![Creare e utilizzare connessioni diverse per ogni azione](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* Modificare la concorrenza o il parallelismo in un [ciclo "for each"](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

  Per impostazione predefinita, le iterazioni del ciclo "for each" vengono eseguite allo stesso tempo fino al [limite di concorrenza](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Se si dispone di un connettore che sta per essere limitato all'interno di un ciclo "for each", è possibile ridurre il numero di iterazioni del ciclo eseguite in parallelo. Per altre informazioni, vedere gli argomenti seguenti:
  
  * [Cicli "for each"-modifica della concorrenza o esecuzione sequenziale](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [Schema del linguaggio di definizione del flusso di lavoro-per ogni ciclo](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [Schema del linguaggio di definizione del flusso di lavoro: modifica della concorrenza del ciclo "for each"](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [Schema del linguaggio di definizione del flusso di lavoro-esecuzione sequenziale dei cicli](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>Limitazione del sistema o del servizio di destinazione

Anche se un connettore ha limiti di limitazione, il servizio o il sistema di destinazione chiamato dal connettore potrebbe avere limiti di limitazione. Alcune API di Microsoft Exchange Server, ad esempio, hanno limiti di limitazione più restrittivi rispetto al connettore Office 365 Outlook.

Per impostazione predefinita, le istanze di un'app per la logica e qualsiasi ciclo o ramo all'interno di tali istanze vengono eseguite *in parallelo*. Questo comportamento indica che più istanze possono chiamare lo stesso endpoint nello stesso momento. Ogni istanza non è a conoscenza dell'esistenza dell'altra, quindi i tentativi di ritentare le azioni non riuscite possono creare [race condition](https://en.wikipedia.org/wiki/Race_condition) in cui più chiamate tentano di essere eseguite contemporaneamente, ma per avere esito positivo tali chiamate devono arrivare al servizio o al sistema di destinazione prima che si verifichi la limitazione delle richieste.

Si supponga, ad esempio, di disporre di una matrice con 100 di elementi. Usare un ciclo "for each" per scorrere la matrice e attivare il controllo della concorrenza del ciclo in modo che sia possibile limitare il numero di iterazioni parallele a 20 o il [limite predefinito corrente](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). All'interno del ciclo, un'azione inserisce un elemento dalla matrice in un database di SQL Server, che consente solo 15 chiamate al secondo. Questo scenario comporta un problema di limitazione delle richieste perché un backlog di tentativi si accumula e non viene mai eseguito.

Questa tabella descrive la sequenza temporale relativa a ciò che accade nel ciclo quando l'intervallo tra tentativi dell'azione è 1 secondo:

| Temporizzazione | Numero di azioni eseguite | Numero di azioni che non riescono | Numero di tentativi in attesa |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 secondi | 20 inserimenti | 5 ha esito negativo, a causa del limite SQL | 5 tentativi |
| T + 0,5 secondi | 15 inserimenti, a causa di 5 tentativi precedenti in attesa | Tutti i 15 hanno esito negativo, a causa del limite SQL precedente ancora attivo per altri 0,5 secondi | 20 tentativi <br>(precedente 5 + 15 nuovi) |
| T + 1 secondo | 20 inserimenti | 5 esito negativo e 20 tentativi precedenti, a causa del limite SQL | 25 tentativi (precedenti 20 + 5 nuovi)
|||||

Per gestire la limitazione a questo livello, sono disponibili le opzioni seguenti:

* Creare app per la logica in modo che ogni gestisca una singola operazione.

  * Continuando con lo scenario di esempio SQL Server in questa sezione, è possibile creare un'app per la logica che inserisce elementi di matrice in una coda, ad esempio una [coda del bus di servizio di Azure](../connectors/connectors-create-api-servicebus.md). Si crea quindi un'altra app per la logica che esegue solo l'operazione di inserimento per ogni elemento della coda. In questo modo, viene eseguita una sola istanza di app per la logica in un momento specifico, che completa l'operazione di inserimento e passa all'elemento successivo della coda oppure l'istanza ottiene 429 errori ma non tenta i tentativi non produttivi.

  * Creare un'app per la logica padre che chiama un'app per la logica figlio o nidificata per ogni azione. Se l'elemento padre deve chiamare app figlio diverse in base al risultato dell'elemento padre, è possibile usare un'azione della condizione o un'azione di commutazione che determina l'app figlio da chiamare. Questo modello può essere utile per ridurre il numero di chiamate o operazioni.

    Si supponga, ad esempio, di avere due app per la logica, ciascuna con un trigger di polling che controlla l'account di posta elettronica ogni minuto per un oggetto specifico, ad esempio "operazione riuscita" o "errore". Questa installazione restituisce 120 chiamate all'ora. Al contrario, se si crea una singola app per la logica padre che esegue il polling ogni minuto, ma chiama un'app per la logica figlio che viene eseguita in base al fatto che l'oggetto sia "operazione riuscita" o "errore", si taglia il numero di controlli di polling alla metà o 60 in questo caso.

* Configurare l'elaborazione batch.

  Se il servizio di destinazione supporta le operazioni batch, è possibile indirizzare la limitazione elaborando gli elementi in gruppi o batch, anziché singolarmente. Per implementare la soluzione di elaborazione batch, creare un'app per la logica "ricevitore batch" e un'app per la logica "mittente batch". Il mittente del batch raccoglie i messaggi o gli elementi fino a quando i criteri specificati non vengono soddisfatti, quindi invia tali messaggi o elementi in un singolo gruppo. Il ricevitore batch accetta tale gruppo ed elabora i messaggi o gli elementi. Per ulteriori informazioni, vedere [batch process messages in groups](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

* Usare le versioni webhook per trigger e azioni anziché per le versioni di polling.

  Perché? Un trigger di polling continua a controllare il servizio o il sistema di destinazione a intervalli specifici. Un intervallo molto frequente, ad esempio ogni secondo, può creare problemi di limitazione delle richieste. Tuttavia, un trigger o un'azione webhook, ad esempio [webhook http](../connectors/connectors-native-webhook.md), crea solo una singola chiamata al servizio o al sistema di destinazione, che si verifica al momento della sottoscrizione e richiede che la destinazione comunichi il trigger o l'azione solo quando si verifica un evento. In questo modo, il trigger o l'azione non deve controllare continuamente la destinazione.
  
  Quindi, se il servizio o il sistema di destinazione supporta i webhook o fornisce un connettore con una versione di Webhook, questa opzione è migliore rispetto all'uso della versione di polling. Per identificare i trigger e le azioni del webhook, verificare che abbiano il `ApiConnectionWebhook` tipo o che non sia necessario specificare una ricorrenza. Per altre informazioni, vedere [trigger APIConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e [azione APIConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sui [limiti e la configurazione delle app](../logic-apps/logic-apps-limits-and-config.md) per la logica
