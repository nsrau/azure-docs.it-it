---
title: Diagnosi degli errori - App per la logica di Azure | Documentazione Microsoft
description: Approcci comuni per la comprensione degli errori delle app per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 25f4520bae3089ce9f11efcf6acd176255043e70
ms.contentlocale: it-it
ms.lasthandoff: 03/10/2017


---
# <a name="diagnose-logic-app-failures"></a>Eseguire una diagnosi degli errori delle app per la logica
Se si verificano problemi o errori con le app per la logica, con alcuni accorgimenti sarà possibile capire meglio l'origine degli errori.  

## <a name="azure-portal-tools"></a>Strumenti del portale di Azure
Il portale di Azure offre molti strumenti per la diagnosi delle app per la logica a ogni passaggio.

### <a name="trigger-history"></a>Cronologia di attivazione

Ogni app per la logica ha almeno un trigger. Se si nota che le app non vengono attivate, controllare prima la cronologia di attivazione per altre informazioni. La cronologia di attivazione è disponibile nel pannello principale delle app per la logica.

![Accedere alla cronologia dei trigger][1]

Elenca tutti i tentativi di attivazione eseguiti dall'app per la logica. Facendo clic su ogni tentativo di attivazione si esegue il drill-down dei dettagli, nello specifico sugli eventuali input o output generati dal tentativo di attivazione. Se si individuano trigger non riusciti, selezionare il tentativo di trigger e scegliere il collegamento **Output** per rivedere tutti i messaggi di errore generati, ad esempio quelli relativi a credenziali FTP non valide.

Possono essere visualizzati i seguenti stati:

* **Operazione ignorata**. È stato eseguito il poll dell'endpoint per verificare la presenza di dati e la risposta ricevuta ha indicato che non ci sono dati disponibili.
* **Operazione completata**. La risposta ricevuta dal trigger ha indicato che non sono disponibili dati. Questo può essere il risultato di un trigger manuale, un trigger di ricorrenza o un trigger di poll. È possibile che sia associato in genere allo stato **Attivato**, ma questo stato non viene visualizzato se in visualizzazione Codice è presente un valore SplitOn o una condizione non soddisfatta.
* **Operazione non riuscita**. È stato generato un errore.

#### <a name="start-a-trigger-manually"></a>Avviare manualmente un trigger

Se si vuole che l'app per la logica verifichi immediatamente la presenza di un trigger disponibile, senza attendere la ricorrenza successiva, è possibile fare clic sul pulsante **Seleziona trigger** nel pannello principale per forzare un controllo. Ad esempio, facendo clic su questo collegamento con un trigger di Dropbox, il flusso di lavoro esegue immediatamente il poll di Dropbox alla ricerca di nuovi file.

### <a name="run-history"></a>Cronologia di esecuzione

Ogni trigger attivato comporta un'esecuzione. Le informazioni sull'esecuzione sono accessibili dal pannello principale, che contiene numerose informazioni potenzialmente utili per capire cosa si è verificato durante il flusso di lavoro.

![Accedere alla cronologia di attivazione][2]

Un'esecuzione può visualizzare uno dei seguenti stati:

* **Operazione completata**. Tutte le azioni hanno avuto esito positivo. Se è stato generato un errore, questo è stato gestito da un'azione che si è verificata in un secondo momento nel flusso di lavoro. Vale a dire, l'errore è stato gestito da un'azione che è stata impostata per l'esecuzione dopo un'azione non riuscita.
* **Operazione non riuscita**. Almeno un'azione presenta un errore non gestito da un'azione successiva nel flusso di lavoro.
* **Operazione annullata**. Il flusso di lavoro era in esecuzione ma ha ricevuto una richiesta di annullamento.
* **In esecuzione**. Il flusso di lavoro è attualmente in esecuzione, ad esempio per flussi di lavoro limitati o a causa del piano tariffario corrente. Per i dettagli, vedere la [pagina relativa ai limiti di azione sui prezzi](https://azure.microsoft.com/pricing/details/app-service/plans/). Inoltre, la configurazione della diagnostica (i grafici sotto la cronologia di esecuzione) possono fornire informazioni sugli eventuali eventi di limitazione in atto.

Quando si analizza una cronologia di esecuzione, è possibile visualizzare ulteriori dettagli.  

#### <a name="trigger-outputs"></a>Output dei trigger

Gli output dei trigger mostrano i dati ricevuti dal trigger. Questi output consentono di determinare se tutte le proprietà sono state restituite come previsto.

> [!NOTE]
> Se si notano contenuti poco chiari, potrebbe essere utile comprendere il modo in cui le App per la logica di Azure [gestiscono i diversi tipi di contenuto](../logic-apps/logic-apps-content-type.md).
> 

![Esempi di output di trigger][3]

#### <a name="action-inputs-and-outputs"></a>Input e output delle azioni

È possibile visualizzare informazioni dettagliate sugli input e sugli output ricevuti da un'azione, in modo da semplificare la comprensione delle dimensioni e delle forme degli output, oltre a cercare eventuali messaggi di errore generati.

![Input e output delle azioni][4]

## <a name="debug-workflow-runtime"></a>Debug del runtime del flusso di lavoro

Oltre al monitoraggio di input, output e trigger di un'esecuzione, potrebbe essere utile aggiungere alcuni passaggi a un flusso di lavoro per semplificare il debug. 
[RequestBin](http://requestb.in) è uno strumento utile che è possibile aggiungere come passaggio in un flusso di lavoro. Utilizzando RequestBin, è possibile configurare un controllo per le richieste HTTP, in modo da stabilire esattamente le dimensioni, la forma e il formato di una richiesta HTTP. È possibile creare un nuovo RequestBin e incollare l'URL in un'azione HTTP POST dell'app per la logica, con qualsiasi contenuto del corpo da verificare, ad esempio un'espressione o l'output di un altro passaggio. Dopo l'esecuzione dell'app per la logica, è possibile aggiornare RequestBin per verificare in che modo è stata formata la richiesta durante la generazione dal motore di App per la logica.

<!-- image references -->
[1]: ./media/logic-apps-diagnosing-failures/triggerhistory.png
[2]: ./media/logic-apps-diagnosing-failures/runhistory.png
[3]: ./media/logic-apps-diagnosing-failures/triggeroutputslink.png
[4]: ./media/logic-apps-diagnosing-failures/actionoutputs.png

