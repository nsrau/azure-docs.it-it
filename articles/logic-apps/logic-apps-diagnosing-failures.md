---
title: Risolvere i problemi e diagnosticare gli errori del flusso di lavoro
description: Informazioni su come risolvere i problemi e diagnosticare problemi, errori ed errori nei flussi di lavoro in app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905037"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Risolvere i problemi e diagnosticare gli errori del flusso di lavoro nelle App per la logica di Azure

L'app per la logica genera informazioni che possono consentire di eseguire diagnostica e debug dei problemi dell'app. È possibile eseguire la diagnostica di un'app per la logica esaminando ogni passaggio del flusso di lavoro tramite il portale di Azure. In alternativa, è possibile aggiungere alcuni passaggi a un flusso di lavoro per il debug in fase di esecuzione.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Controllare la cronologia del trigger

Ogni esecuzione di app per la logica inizia con un tentativo di trigger, quindi se il trigger non viene attivato, attenersi alla procedura seguente:

1. Controllare lo stato del trigger controllando [la cronologia del trigger](../logic-apps/monitor-logic-apps.md#review-trigger-history). Per visualizzare ulteriori informazioni sul tentativo di attivazione, selezionare l'evento di attivazione, ad esempio:

   ![Visualizzare lo stato e la cronologia del trigger](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Controllare gli input del trigger per verificare che vengano visualizzati come previsto. In **input collegamento**selezionare il collegamento, che mostra il riquadro **input** .

   Gli input del trigger includono i dati previsti dal trigger e richiedono l'avvio del flusso di lavoro. Esaminando questi input è possibile determinare se gli input del trigger sono corretti e se la condizione è stata soddisfatta, in modo che il flusso di lavoro possa continuare.

   Ad esempio, la `feedUrl` proprietà contiene un valore di feed RSS errato:

   ![Esaminare gli input dei trigger per gli errori](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Controllare gli output dei trigger, se presenti, per verificare che vengano visualizzati come previsto. In **output collegamento**selezionare il collegamento, che mostra il riquadro **output** .

   Gli output dei trigger includono i dati che il trigger passa al passaggio successivo del flusso di lavoro. Esaminando questi output è possibile determinare se i valori corretti o previsti sono passati al passaggio successivo nel flusso di lavoro, ad esempio:

   ![Esaminare gli output dei trigger per gli errori](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Se si trovano contenuti non riconosciuti, vedere altre informazioni sui [diversi tipi di contenuto](../logic-apps/logic-apps-content-type.md) in app per la logica di Azure.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>Verifica cronologia esecuzioni

Ogni volta che il trigger viene attivato per un elemento o un evento, il motore delle app per la logica crea ed esegue un'istanza del flusso di lavoro separata per ogni elemento o evento. Se un'esecuzione ha esito negativo, attenersi alla procedura seguente per esaminare gli eventi verificatisi durante l'esecuzione, incluso lo stato di ogni passaggio nel flusso di lavoro più gli input e gli output per ogni passaggio.

1. Controllare lo stato di esecuzione del flusso di lavoro [controllando la cronologia delle esecuzioni](../logic-apps/monitor-logic-apps.md#review-runs-history). Per visualizzare ulteriori informazioni su un'esecuzione non riuscita, inclusi tutti i passaggi in eseguiti con lo stato, selezionare l'esecuzione non riuscita.

   ![Visualizza cronologia di esecuzione e seleziona esecuzione non riuscita](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. Dopo aver visualizzato tutti i passaggi dell'esecuzione, espandere il primo passaggio non riuscito.

   ![Espandi primo passaggio non riuscito](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Controllare gli input del passaggio non riuscito per verificare se sono visualizzati come previsto.

1. Esaminare i dettagli per ogni passaggio in un'esecuzione specifica. In **Cronologia esecuzioni** selezionare l'esecuzione che si vuole esaminare.

   ![Esaminare la cronologia delle esecuzioni](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Visualizzare i dettagli per un'esecuzione dell'app per la logica](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Per esaminare gli input, gli output e gli eventuali messaggi di errore per un passaggio specifico, scegliere quel passaggio, in modo che la forma si espanda e vengano mostrati i dettagli. Ad esempio:

   ![Visualizzare i dettagli del passaggio](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Eseguire il debug al runtime

Per semplificare il debug, è possibile aggiungere passaggi di diagnostica a un flusso di lavoro di un'app per la logica, oltre a esaminare il trigger e la cronologia delle esecuzioni. È possibile ad esempio aggiungere passaggi che usano il servizio [Webhook Tester](https://webhook.site/), in modo da poter controllare le richieste HTTP e determinare dimensione, forma e formato esatti.

1. Passare al sito del [tester webhook](https://webhook.site/) e copiare l'URL univoco generato.

1. Nell'app per la logica aggiungere un'azione HTTP POST e il contenuto del corpo che si vuole testare, ad esempio un'espressione o un altro output del passaggio.

1. Incollare l'URL da webhook tester nell'azione HTTP POST.

1. Per esaminare il modo in cui viene creata una richiesta quando viene generata dal motore di app per la logica, eseguire l'app per la logica e rivedere il sito del tester webhook per altri dettagli.

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare l'app per la logica](../logic-apps/monitor-logic-apps.md)
