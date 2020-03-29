---
title: Risolvere i problemi e diagnosticare gli errori del flusso di lavoro
description: Informazioni su come risolvere e diagnosticare problemi, errori ed errori nei flussi di lavoro in App per la logica di AzureLearn how to troubleshoot and diagnose problems, errors, and failures in your workflows in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905037"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Risolvere i problemi e diagnosticare gli errori del flusso di lavoro nelle App per la logica di Azure

L'app per la logica genera informazioni che possono consentire di eseguire diagnostica e debug dei problemi dell'app. È possibile eseguire la diagnostica di un'app per la logica esaminando ogni passaggio del flusso di lavoro tramite il portale di Azure. In alternativa, è possibile aggiungere alcuni passaggi a un flusso di lavoro per il debug di runtime.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Controllare la cronologia dei trigger

Ogni esecuzione dell'app per la logica inizia con un tentativo di trigger, quindi se il trigger non viene attivato, attenersi alla seguente procedura:

1. Controllare lo stato del trigger [controllando la cronologia del trigger.](../logic-apps/monitor-logic-apps.md#review-trigger-history) Per visualizzare ulteriori informazioni sul tentativo di trigger, selezionare l'evento trigger, ad esempio:To view more information about the trigger attempt, select that trigger event, for example:

   ![Visualizzare lo stato e la cronologia dei trigger](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Controllare gli input del trigger per verificare che vengano visualizzati come previsto. In **Collegamento Input**selezionare il collegamento che mostra il riquadro **Input.**

   Gli input del trigger includono i dati previsti dal trigger e necessari per avviare il flusso di lavoro. La revisione di questi input consente di determinare se gli input del trigger sono corretti e se la condizione è stata soddisfatta in modo che il flusso di lavoro possa continuare.

   Ad esempio, `feedUrl` la proprietà qui ha un valore di feed RSS non corretto:

   ![Esaminare gli input dei trigger per individuare eventuali erroriReview trigger inputs for errors](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Controllare gli eventuali output dei trigger per verificare che vengano visualizzati come previsto. In **Collegamento Output**selezionare il collegamento che mostra il riquadro **Output.**

   Gli output del trigger includono i dati che il trigger passa al passaggio successivo nel flusso di lavoro. La revisione di questi output consente di determinare se i valori corretti o previsti vengono passati al passaggio successivo del flusso di lavoro, ad esempio:Reviewing these outputs can help you determine whether the correct or expected values passed on the next step in your workflow, for example:

   ![Esaminare gli output dei trigger per individuare eventuali erroriReview trigger outputs for errors](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Se si trova contenuto non riconosciuto, vedere altre informazioni sui [diversi tipi di contenuto](../logic-apps/logic-apps-content-type.md) in App per la logica di Azure.If you find any content that you don't recognize, learn more about different content types in Azure Logic Apps.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>Controllare la cronologia delle esecuzioni

Ogni volta che il trigger viene attivato per un elemento o un evento, il motore App per la logica crea ed esegue un'istanza del flusso di lavoro separata per ogni elemento o evento. Se un'esecuzione non riesce, seguire questi passaggi per esaminare ciò che è accaduto durante l'esecuzione, incluso lo stato per ogni passaggio del flusso di lavoro più gli input e gli output per ogni passaggio.

1. Controllare lo stato di esecuzione del flusso di lavoro [controllando la cronologia delle esecuzioni.](../logic-apps/monitor-logic-apps.md#review-runs-history) Per visualizzare ulteriori informazioni su un'esecuzione non riuscita, inclusi tutti i passaggi nell'esecuzione nel relativo stato, selezionare l'esecuzione non riuscita.

   ![Visualizzare la cronologia delle run e selezionare l'esecuzione non riuscitaView run history and select failed run](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. Dopo aver visualizzato tutti i passaggi dell'esecuzione, espandere il primo passaggio non riuscito.

   ![Espandi primo passaggio non riuscito](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Controllare gli input del passaggio non riuscito per verificare se vengono visualizzati come previsto.

1. Esaminare i dettagli per ogni passaggio in un'esecuzione specifica. In **Cronologia esecuzioni** selezionare l'esecuzione che si vuole esaminare.

   ![Esaminare la cronologia delle esecuzioni](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Visualizzare i dettagli per un'esecuzione dell'app per la logica](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Per esaminare gli input, gli output e gli eventuali messaggi di errore per un passaggio specifico, scegliere quel passaggio, in modo che la forma si espanda e vengano mostrati i dettagli. Ad esempio:

   ![Visualizzare i dettagli del passaggio](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Eseguire il debug al runtime

Per facilitare il debug, è possibile aggiungere passaggi di diagnostica a un flusso di lavoro dell'app per la logica, oltre a esaminare il trigger e la cronologia delle esecuzioni. È possibile ad esempio aggiungere passaggi che usano il servizio [Webhook Tester](https://webhook.site/), in modo da poter controllare le richieste HTTP e determinare dimensione, forma e formato esatti.

1. Passare al sito [Webhook Tester](https://webhook.site/) e copiare l'URL univoco generato.

1. Nell'app per la logica aggiungere un'azione HTTP POST più il contenuto del corpo che si vuole testare, ad esempio un'espressione o un altro output del passaggio.

1. Incollare l'URL da Tester Webhook nell'azione HTTP POST.

1. Per esaminare il modo in cui viene formata una richiesta quando viene generata dal motore App per la logica, eseguire l'app per la logica e rivedere il sito Webhook Tester per ulteriori dettagli.

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare l'app per la logica](../logic-apps/monitor-logic-apps.md)
