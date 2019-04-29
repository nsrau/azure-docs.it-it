---
title: Risoluzione dei problemi e diagnostica degli errori - App per la logica di Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi e diagnosticare gli errori del flusso di lavoro nelle App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.topic: article
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.date: 10/15/2017
ms.openlocfilehash: 62a74364939fffb6e06f51f1c0cabb6cce8c10e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60999813"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Risolvere i problemi e diagnosticare gli errori del flusso di lavoro nelle App per la logica di Azure

L'app per la logica genera informazioni che possono consentire di eseguire diagnostica e debug dei problemi dell'app. È possibile eseguire la diagnostica di un'app per la logica esaminando ogni passaggio del flusso di lavoro tramite il portale di Azure. Oppure è possibile aggiungere alcuni passaggi a un flusso di lavoro per il debug al runtime.

## <a name="review-trigger-history"></a>Esaminare la cronologia di attivazione

Ogni app per la logica si avvia con un trigger. Se il trigger non viene attivato, controllare innanzitutto la cronologia del trigger. Questa cronologia elenca tutti i tentativi di trigger che l'app per la logica ha effettuato e i dettagli relativi agli input e output per ogni tentativo di trigger.

1. Per esaminare se il trigger si è attivato, nel menu dell'app per la logica scegliere **Panoramica**. In **Cronologia trigger** esaminare lo stato del trigger.

   > [!TIP]
   > Se il menu dell'app per la logica non è visualizzato, provare a tornare al dashboard di Azure e riaprire l'app per la logica.

   ![Esaminare la cronologia di attivazione](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Se non si trovano i dati previsti, provare a selezionare **Aggiorna** sulla barra degli strumenti.
   > * Se l'elenco mostra molti tentativi di trigger e non si trova la voce desiderata, provare a filtrare l'elenco.

   Di seguito sono riportati i possibili stati per un tentativo di trigger:

   | Stato | DESCRIZIONE | 
   | ------ | ----------- | 
   | **Completato** | Il trigger ha controllato l'endpoint e ha trovato dati disponibili. In genere compare anche lo stato "Attivato" accanto a questo stato. In caso contrario, la definizione del trigger potrebbe essere una condizione o comando `SplitOn` che non è soddisfatto. <p>Questo stato può essere associato a un trigger manuale, un trigger di ricorrenza o un trigger di poll. Un trigger può essere eseguito correttamente, ma l'esecuzione stessa potrebbe comunque non riuscire quando le azioni generano errori non gestiti. | 
   | **Ignorato** | Il trigger ha controllato l'endpoint ma non ha trovato dati. | 
   | **Non riuscito** | Si è verificato un errore. Per esaminare i messaggi di errore generati per un trigger non riuscito, selezionare il tentativo di trigger e scegliere **Output**. Ad esempio, si potrebbero trovare input che non sono validi. | 
   ||| 

   Potrebbero esserci più voci di trigger con la stessa data e ora, cosa che accade quando l'app per la logica trova più elementi. 
   Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica per eseguire il flusso di lavoro. Per impostazione predefinita, ogni istanza viene eseguita in parallelo, in modo che nessun flusso di lavoro debba attendere prima di avviare un'esecuzione.

   > [!TIP]
   > È possibile ricontrollare il trigger senza attendere la successiva ricorrenza. Sulla barra degli strumenti Panoramica scegliere **Esegui trigger** e selezionare il trigger, in modo da imporre un controllo. In alternativa selezionare **Esegui** sulla barra degli strumenti della finestra di progettazione delle app per la logica.

3. Per esaminare i dettagli di un tentativo di trigger, in **Cronologia trigger** selezionare il tentativo di trigger. 

   ![Selezionare un tentativo di trigger](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Esaminare gli input e qualsiasi output che il trigger ha generato. Gli output dei trigger mostrano i dati ricevuti dal trigger. Questi output consentono di determinare se tutte le proprietà sono state restituite come previsto.

   > [!NOTE]
   > Se si trovano contenuti poco chiari, potrebbe essere utile comprendere il modo in cui le App per la logica di Azure [gestiscono i diversi tipi di contenuto](../logic-apps/logic-apps-content-type.md).

   ![Output dei trigger](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Esaminare la cronologia di esecuzione

Ogni trigger attivato avvia un'esecuzione del flusso di lavoro. È possibile esaminare ciò che è accaduto durante quell'esecuzione includendo lo stato per ogni passaggio nel flusso di lavoro, nonché gli input e output per ogni passaggio.

1. Nel menu dell'app per la logica scegliere **Panoramica**. In **Cronologia esecuzioni** esaminare l'esecuzione del trigger attivato.

   > [!TIP]
   > Se il menu dell'app per la logica non è visualizzato, provare a tornare al dashboard di Azure e riaprire l'app per la logica.

   ![Esaminare la cronologia delle esecuzioni](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Se non si trovano i dati previsti, provare a selezionare **Aggiorna** sulla barra degli strumenti.
   > * Se l'elenco mostra molte esecuzioni e non si trova la voce desiderata, provare a filtrare l'elenco.

   Di seguito sono riportati i possibili stati per un'esecuzione:

   | Stato | DESCRIZIONE | 
   | ------ | ----------- | 
   | **Completato** | Tutte le azioni hanno avuto esito positivo. <p>Se si sono verificati errori in un'azione specifica, un'azione successiva nel flusso di lavoro ha gestito l'errore. | 
   | **Non riuscito** | Almeno un'azione non è riuscita e non è stata configurata alcuna azione successiva nel flusso di lavoro per la gestione dell'errore. | 
   | **Annullato** | Il flusso di lavoro era in esecuzione ma ha ricevuto una richiesta di annullamento. | 
   | **Running** | Il flusso di lavoro è attualmente in esecuzione, <p>Questo stato potrebbe verificarsi per flussi di lavoro limitati o come conseguenza del piano tariffario corrente. Per altre informazioni vedere i [limiti delle azioni sulla pagina dei prezzi](https://azure.microsoft.com/pricing/details/logic-apps/). Se si configura la [registrazione diagnostica](../logic-apps/logic-apps-monitor-your-logic-apps.md), è anche possibile ottenere informazioni su tutti gli eventi di limitazione che si verificano. | 
   ||| 

2. Esaminare i dettagli per ogni passaggio in un'esecuzione specifica. In **Cronologia esecuzioni** selezionare l'esecuzione che si vuole esaminare.

   ![Esaminare la cronologia delle esecuzioni](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   Che l'esecuzione sia riuscita o meno, la visualizzazione Dettagli esecuzione mostra ogni passaggio e indica se è o non è riuscito.

   ![Visualizzare i dettagli per un'esecuzione dell'app per la logica](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Per esaminare gli input, gli output e gli eventuali messaggi di errore per un passaggio specifico, scegliere quel passaggio, in modo che la forma si espanda e vengano mostrati i dettagli. Ad esempio: 

   ![Visualizzare i dettagli del passaggio](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Eseguire il debug al runtime

Per facilitare il debug, è possibile aggiungere passaggi di diagnostica a un flusso di lavoro, oltre a esaminare il trigger e la cronologia delle esecuzioni. È possibile ad esempio aggiungere passaggi che usano il servizio [Webhook Tester](https://webhook.site/), in modo da poter controllare le richieste HTTP e determinare dimensione, forma e formato esatti.

1. Visitare [Webhook Tester](https://webhook.site/) e copiare l'URL univoco creato

2. Nell'app per la logica aggiungere un'azione HTTP POST con qualsiasi contenuto del corpo da verificare, ad esempio un'espressione o l'output di un altro passaggio.

3. Incollare l'URL per Webhook Tester nell'azione HTTP POST.

4. Per esaminare in che modo viene formata una richiesta quando è generata dal motore di App per la logica di Azure, eseguire l'app per la logica e aggiornare Webhook Tester.

## <a name="next-steps"></a>Passaggi successivi

[Monitorare l'app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md)
