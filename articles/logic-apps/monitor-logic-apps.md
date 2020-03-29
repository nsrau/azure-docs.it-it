---
title: Monitorare lo stato, visualizzare la cronologia e impostare gli avvisi
description: Risolvere i problemi relativi alle app per la logica controllando lo stato dell'esecuzione, esaminando la cronologia dei trigger e abilitando gli avvisi nelle app per la logica di AzureTroubleshoot logic apps by checking run status, reviewing trigger history, and enabling alerts in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 495877f1c839de2cf3583a37180054c91bd9f139
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907773"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Monitorare lo stato di esecuzione, esaminare la cronologia dei trigger e configurare gli avvisi per le app per la logica di AzureMonitor run status, review trigger history, and set up alerts for Azure Logic Apps

Dopo aver [creato ed eseguito un'app per la logica,](../logic-apps/quickstart-create-first-logic-app-workflow.md)è possibile controllare lo stato di esecuzione dell'app per la logica, [l'esecuzione](#review-runs-history)della cronologia, la [cronologia dei trigger](#review-trigger-history)e le prestazioni. Per ottenere notifiche sugli errori o su altri possibili problemi, configurare gli [avvisi](#add-azure-alerts). È ad esempio possibile creare un avviso che rileva "quando più di cinque esecuzioni in un'ora hanno esito negativo".

Per il monitoraggio degli eventi in tempo reale e il debug più avanzato, configurare la registrazione diagnostica per l'app per la logica usando i log di Monitoraggio di [Azure.](../azure-monitor/overview.md) Questo servizio di Azure consente di monitorare gli ambienti cloud e locali in modo da poterne gestire più facilmente la disponibilità e le prestazioni. È quindi possibile trovare e visualizzare gli eventi, ad esempio gli eventi trigger, gli eventi di esecuzione e gli eventi azione. Archiviare queste informazioni nei log di Monitoraggio di [Azure,](../azure-monitor/platform/data-platform-logs.md)è possibile creare query di [log](../azure-monitor/log-query/log-query-overview.md) che consentono di trovare e analizzare tali informazioni. È anche possibile usare questi dati di diagnostica con altri servizi di Azure, ad esempio Archiviazione di Azure e Hub eventi di Azure.You can also use this diagnostic data with other Azure services, such as Azure Storage and Azure Event Hubs. Per altre informazioni, vedere [Monitorare le app per la logica tramite Monitoraggio di Azure.For](../logic-apps/monitor-logic-apps-log-analytics.md)more information, see Monitor logic apps by using Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>Esaminare la cronologia delle esecuzioni

Ogni volta che il trigger viene attivato per un elemento o un evento, il motore App per la logica crea ed esegue un'istanza del flusso di lavoro separata per ogni elemento o evento. Per impostazione predefinita, ogni istanza del flusso di lavoro viene eseguita in parallelo in modo che nessun flusso di lavoro debba attendere prima di avviare un'esecuzione. È possibile esaminare ciò che è accaduto durante l'esecuzione, incluso lo stato per ogni passaggio del flusso di lavoro più gli input e gli output per ogni passaggio.

1. Nel [portale di Azure](https://portal.azure.com)individuare e aprire l'app per la logica in Progettazione app per la logica.

   Per trovare l'app per la logica, `logic apps`nella casella di ricerca principale di Azure immettere , quindi selezionare **App per la logica**.

   ![Trovare e selezionare il servizio "App per la logica"](./media/monitor-logic-apps/find-your-logic-app.png)

   Il portale di Azure mostra tutte le app per la logica associate alle sottoscrizioni di Azure.The Azure portal shows all the logic apps that are associated with your Azure subscriptions. È possibile filtrare questo elenco in base al nome, alla sottoscrizione, al gruppo di risorse, alla posizione e così via.

   ![Visualizzare le app per la logica associate alle sottoscrizioniView logic apps associated with subscriptions](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Selezionare l'app per la logica e quindi **scegliere Panoramica**.

   Nel riquadro Panoramica, in **Cronologia esecuzioni**, vengono visualizzate tutte le esecuzioni passate, correnti ed eventuali esecuzioni in attesa per l'app per la logica. Se nell'elenco sono visualizzate molte esecuzioni e non è possibile trovare la voce desiderata, provare a filtrare l'elenco. Se non si trovano i dati previsti, provare a selezionare **Aggiorna** sulla barra degli strumenti.

   ![Panoramica, cronologia delle esecuzioni e altre informazioni sull'app per la logica](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   Ecco i possibili stati per l'esecuzione di un'app per la logica:Here are the possible statuses for a logic app run:

   | Stato | Descrizione |
   |--------|-------------|
   | **Operazione annullata** | Il flusso di lavoro era in esecuzione ma ha ricevuto una richiesta di annullamento |
   | **Operazione non riuscita** | Almeno un'azione non è riuscita e non sono state impostate azioni successive nel flusso di lavoro per gestire l'errore |
   | **In esecuzione** | Il flusso di lavoro è attualmente in esecuzione, <p>Questo stato può essere visualizzato anche per i flussi di lavoro limitati o a causa del piano tariffario corrente. Per altre informazioni vedere i [limiti delle azioni sulla pagina dei prezzi](https://azure.microsoft.com/pricing/details/logic-apps/). Se si imposta la [registrazione diagnostica,](../logic-apps/monitor-logic-apps.md)è possibile ottenere informazioni su tutti gli eventi di limitazione che si verificano. |
   | **Riuscito** | Tutte le azioni hanno avuto esito positivo. <p>**Nota:** se si sono verificati errori in un'azione specifica, un'azione successiva nel flusso di lavoro ha gestito tale errore. |
   | **Waiting** | Il flusso di lavoro non è stato avviato o è in pausa, ad esempio, a causa di un flusso di lavoro precedente ancora in esecuzione. |
   |||

1. Per esaminare i passaggi e altre informazioni per un'esecuzione specifica, in **Cronologia esecuzioni**selezionare tale esecuzione.

   ![Selezionare un'esecuzione specifica da esaminare](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   Il riquadro di **esecuzione dell'app per** la logica mostra ogni passaggio nell'esecuzione selezionata, lo stato di esecuzione di ogni passaggio e il tempo impiegato per ogni passaggio da eseguire, ad esempio:The Logic app run pane shows each step in the selected run, each step's run status, and the time taken for each step to run, for example:

   ![Ogni azione nell'esecuzione specifica](./media/monitor-logic-apps/logic-app-run-pane.png)

   Per visualizzare queste informazioni sotto forma di elenco, sulla barra degli strumenti **Esegui app per** la logica selezionare Dettagli **esecuzione**.

   ![Nella barra degli strumenti, selezionare "Esegui dettagli"](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   La visualizzazione Dettagli esecuzione mostra ogni passaggio, il relativo stato e altre informazioni.

   ![Esaminare i dettagli di ogni passaggio dell'esecuzioneReview details about each step in the run](./media/monitor-logic-apps/review-logic-app-run-details.png)

   Ad esempio, è possibile ottenere la proprietà **ID di correlazione** dell'esecuzione, che potrebbe essere necessaria quando si usa l'API [REST per le app per la logica](https://docs.microsoft.com/rest/api/logic).

1. Per ottenere ulteriori informazioni su un passaggio specifico, selezionare una delle due opzioni:To get more information about a specific step, select either option:

   * Nel riquadro **di esecuzione dell'app per** la logica selezionare il passaggio in modo che la forma si espanda. È ora possibile visualizzare informazioni quali input, output ed eventuali errori che si sono verificati in tale passaggio, ad esempio:You can now view such as inputs, outputs, and any errors that happened in that step, for example:

     ![Nel riquadro di esecuzione dell'app per la logica, visualizzare il passaggio non riuscito](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * Nel riquadro **dei dettagli dell'esecuzione dell'app** per la logica selezionare il passaggio desiderato.

     ![Nel riquadro dei dettagli di esecuzione, view failed step](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     È ora possibile visualizzare informazioni quali input e output per tale passaggio, ad esempio:You can now view such as inputs and outputs for that step, for example:

   > [!NOTE]
   > Tutti i dettagli ed eventi di runtime vengono crittografati nel servizio App per la logica. Vengono decrittografati solo quando un utente richiede di visualizzare i dati. È possibile [nascondere input e output nella cronologia di esecuzione](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) o controllare l'accesso degli utenti a queste informazioni usando il controllo degli accessi in base al ruolo di [Azure.](../role-based-access-control/overview.md)

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Esaminare la cronologia di attivazione

Ogni esecuzione dell'app per la logica inizia con un trigger. La cronologia dei trigger elenca tutti i tentativi di trigger effettuati dall'app per la logica e le informazioni sugli input e gli output per ogni tentativo di trigger.

1. Nel [portale di Azure](https://portal.azure.com)individuare e aprire l'app per la logica in Progettazione app per la logica.

   Per trovare l'app per la logica, `logic apps`nella casella di ricerca principale di Azure immettere , quindi selezionare **App per la logica**.

   ![Trovare e selezionare il servizio "App per la logica"](./media/monitor-logic-apps/find-your-logic-app.png)

   Il portale di Azure mostra tutte le app per la logica associate alle sottoscrizioni di Azure.The Azure portal shows all the logic apps that are associated with your Azure subscriptions. È possibile filtrare questo elenco in base al nome, alla sottoscrizione, al gruppo di risorse, alla posizione e così via.

   ![Visualizzare le app per la logica associate alle sottoscrizioniView logic apps associated with subscriptions](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Selezionare l'app per la logica e quindi **scegliere Panoramica**.

1. Nel menu dell'app per la logica selezionare **Panoramica**. Nella sezione **Riepilogo,** in **Valutazione**, selezionare **Visualizza cronologia trigger**.

   ![Visualizzare la cronologia dei trigger per l'app per la logica](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   Il riquadro cronologia trigger mostra tutti i tentativi di attivazione effettuati dall'app per la logica. Ogni volta che il trigger viene attivato per un elemento o un evento, il motore App per la logica crea un'istanza dell'app per la logica separata che esegue il flusso di lavoro. Per impostazione predefinita, ogni istanza viene eseguita in parallelo, in modo che nessun flusso di lavoro debba attendere prima di avviare un'esecuzione. Pertanto, se l'app per la logica viene attivata su più elementi contemporaneamente, viene visualizzata una voce trigger con la stessa data e ora per ogni elemento.

   ![Più tentativi di trigger per elementi diversiMultiple trigger attempts for different items](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Di seguito sono riportati i possibili stati per un tentativo di trigger:

   | Stato | Descrizione |
   |--------|-------------|
   | **Operazione non riuscita** | Si è verificato un errore. Per esaminare i messaggi di errore generati per un trigger non riuscito, selezionare il tentativo di trigger e scegliere **Output**. Ad esempio, si potrebbero trovare input che non sono validi. |
   | **Saltato** | Il trigger ha controllato l'endpoint ma non ha trovato dati. |
   | **Riuscito** | Il trigger ha controllato l'endpoint e ha trovato dati disponibili. In genere compare anche lo stato "Attivato" accanto a questo stato. In caso contrario, la definizione del trigger potrebbe essere una condizione o comando `SplitOn` che non è soddisfatto. <p>Questo stato può essere associato a un trigger manuale, un trigger di ricorrenza o un trigger di poll. Un trigger può essere eseguito correttamente, ma l'esecuzione stessa potrebbe comunque non riuscire quando le azioni generano errori non gestiti. |
   |||

   > [!TIP]
   > È possibile ricontrollare il trigger senza attendere la successiva ricorrenza. Nella barra degli strumenti Panoramica selezionare **Esegui trigger**e selezionare il trigger, che forza un controllo. In alternativa selezionare **Esegui** sulla barra degli strumenti della finestra di progettazione delle app per la logica.

1. Per visualizzare informazioni su un tentativo di trigger specifico, nel riquadro trigger selezionare l'evento trigger. Se nell'elenco sono riportati molti tentativi di attivazione e non è possibile trovare la voce desiderata, provare a filtrare l'elenco. Se non si trovano i dati previsti, provare a selezionare **Aggiorna** sulla barra degli strumenti.

   ![Visualizzare un tentativo di trigger specifico](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   È ora possibile esaminare le informazioni sull'evento trigger selezionato, ad esempio:You can now review information about the selected trigger event, for example:

   ![Visualizzare informazioni specifiche sul trigger](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>Impostare gli avvisi di monitoraggio

Per ottenere avvisi basati su metriche specifiche o soglie superate per l'app per la logica, impostare [avvisi in Monitoraggio di Azure.](../azure-monitor/platform/alerts-overview.md) Informazioni sulle [metriche in Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). Per configurare gli avvisi senza usare [Monitoraggio di Azure](../log-analytics/log-analytics-overview.md), eseguire la procedura seguente.

1. Nel menu dell'app per la logica, in **Monitoraggio,** selezionare**Nuova regola di avviso** **Avvisi.** > 

   ![Aggiungere un avviso per l'app per la logica](./media/monitor-logic-apps/add-new-alert-rule.png)

1. Nel riquadro **Crea regola,** in **Risorsa**, selezionare l'app per la logica, se non è già selezionata. In **Condizione**selezionare **Aggiungi** in modo da poter definire la condizione che attiva l'avviso.

   ![Aggiungere una condizione per la regola](./media/monitor-logic-apps/add-condition-for-rule.png)

1. Nel riquadro **Configura logica segnale** individuare e selezionare il segnale per il quale si desidera ricevere un avviso. È possibile utilizzare la casella di ricerca o per ordinare i segnali in ordine alfabetico, selezionare l'intestazione di colonna **Nome segnale.**

   Ad esempio, se si desidera inviare un avviso quando un trigger non riesce, attenersi alla seguente procedura:

   1. Nella colonna **Nome segnale** individuare e selezionare il segnale Trigger **non riusciti.**

      ![Selezionare il segnale per la creazione di avvisi](./media/monitor-logic-apps/find-and-select-signal.png)

   1. Nel riquadro informazioni che si apre per il segnale selezionato, in **Logica di avviso**, impostare la condizione, ad esempio:

   1. Per **Operatore**, selezionare **Maggiore di o uguale a**.

   1. Per **Tipo di aggregazione**, selezionare **Conteggio**.

   1. Per **Valore**soglia `1`, immettere .

   1. In **Anteprima condizione**verificare che la condizione sia corretta.

   1. In **Valutato in base**a , impostare l'intervallo e la frequenza di esecuzione della regola di avviso. Per **Granularità aggregazione (Periodo)** selezionare il periodo per il raggruppamento dei dati. Per **Frequenza di valutazione**, selezionare la frequenza con cui si desidera controllare la condizione.

   1. Quando si è pronti, selezionare **Fatto**.

   Ecco la condizione finita:

   ![Impostare la condizione per l'avviso](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   La pagina **Crea regola** ora mostra la condizione creata e il costo per l'esecuzione dell'avviso.

   ![Nuovo avviso nella pagina "Crea regola"](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Specificare un nome, una descrizione facoltativa e un livello di gravità per l'avviso. Lasciare attivata l'impostazione **Abilita regola alla creazione** oppure disattivarla finché non si è pronti per abilitare la regola.

1. Al termine, selezionare **Crea regola**di avviso .

> [!TIP]
> Per eseguire un'app per la logica da un avviso, è possibile includere il [trigger della richiesta](../connectors/connectors-native-reqres.md) nel flusso di lavoro, che consente di eseguire attività come le seguenti:
> 
> * [Pubblicare su Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Inviare un testo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Aggiungere un messaggio a una coda](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>Passaggi successivi

* [Monitor logic apps by using Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md)