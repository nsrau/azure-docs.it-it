---
title: Testare le App per la logica con dati fittizi - App per la logica di Azure
description: Configurare i risultati statici per il test di App per la logica con dati fittizi senza interessare gli ambienti di produzione
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: 45eeb20e5c572ddd98244b2e751322fcce1d4b76
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597204"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testare le App per la logica con dati fittizi configurando risultati statici

Durante il test di App per la logica, potrebbe non essere pronto a chiamare effettivamente o accedere alle App, servizi e sistemi per vari motivi. In genere in questi scenari, potrebbe essere necessario eseguire i percorsi di condizione diversa, forzati errori, forniscono i corpi delle risposte di messaggio specifico o anche ignorare alcuni passaggi. Configurando i risultati statici per un'azione nell'app per la logica, è possibile simulare i dati di output da tale azione. Offrendo risultati statici in un'azione non viene eseguita l'azione, ma restituisce invece i dati fittizi.

Ad esempio, se si configura posta elettronica azione di invio statici risultati per Outlook 365, il motore di App per la logica restituisce solo i dati fittizi è stato impostato come statici risultati, anziché chiamare Outlook e invia un messaggio di posta elettronica.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si desidera configurare risultati statici

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Configurare i risultati statici

1. Se già stato fatto, nelle [portale di Azure](https://portal.azure.com), aprire l'app per la logica in Progettazione App per la logica.

1. L'azione in cui si desidera configurare risultati statici, seguire questa procedura: 

   1. Nell'angolo superiore destro dell'azione, scegliere i puntini di sospensione (*...* ) e selezionare **risultato statico**, ad esempio:

      ![Selezionare "Risultato statico" > "Abilita risultato statico"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Scegli **Abilita risultato statico**. Per le proprietà necessarie (*), specificare i valori di output fittizio da restituire per la risposta dell'azione.

      Ad esempio, ecco le proprietà necessarie per l'azione HTTP:

      | Proprietà | Descrizione |
      |----------|-------------|
      | **Status** | Lo stato dell'azione da restituire |
      | **Codice di stato** | Il codice di stato specifici da restituire |
      | **Intestazioni** | Il contenuto dell'intestazione da restituire |
      |||

      ![Selezionare "Abilita risultato statico"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Per immettere i dati fittizi in formato JavaScript Object Notation (JSON), scegliere **passare alla modalità JSON** (![Scegli "Passa a modalità JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)).

   1. Per le proprietà facoltative, aprire il **selezionare i campi facoltativi** elencare e selezionare le proprietà che si vuole simulare.

      ![Selezionare le proprietà facoltative](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Quando sei pronto a salvare, scegliere **Done**.

   Nell'angolo superiore destro dell'azione, la barra del titolo Mostra ora un'icona becher test (![icona per ottenere risultati statici](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)), che indica che è stato abilitato risultati statici.

   ![Che Mostra icona abilitata statico](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Per trovare le esecuzioni precedenti che usano dati fittizi, vedere [trovare le esecuzioni che utilizzano risultati statici](#find-runs-mock-data) più avanti in questo argomento.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Riutilizzare gli output precedenti

Se l'app per la logica abbia una precedente esecuzione con output che come output fittizio, è possibile riutilizzare è possibile copiare e incollare gli output di esecuzione.

1. Se già stato fatto, nelle [portale di Azure](https://portal.azure.com), aprire l'app per la logica in Progettazione App per la logica.

1. Nel menu principale dell'app per la logica, selezionare **Panoramica**.

1. Nel **cronologia esecuzioni** sezione, selezionare l'app per la logica esecuzione che si desidera.

1. Nel flusso di lavoro dell'app per la logica, individuare ed espandere l'azione che ha l'output desiderato.

1. Scegliere il **Mostra output non elaborati** collegamento.

1. Copiare l'oggetto JavaScript Object Notation (JSON) completo o la sottosezione specifica da usare, ad esempio, la sezione di output o anche solo la sezione di intestazioni.

1. Seguire i passaggi per aprire il **risultato statico** casella per l'azione in [configurare risultati statici](#set-up-static-results).

1. Dopo il **risultato statico** viene visualizzata finestra, scegliere passaggio:

   * Per incollare un oggetto JSON completo, scegliere **passare alla modalità JSON** (![Scegli "Passa a modalità JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![Scegliere "Passa a modalità JSON" per l'oggetto completo](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Per incollare semplicemente una sezione JSON, accanto all'etichetta della sezione, scegliere **passare alla modalità JSON** per tale sezione, ad esempio:

     ![Scegliere "Passa a modalità JSON" per gli output](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. Nell'editor JSON, incollare il codice JSON copiato in precedenza.

   ![Modalità JSON](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Al termine, scegliere **Fine**. In alternativa, per tornare alla finestra di progettazione, scegliere **modalità Editor dei commutatore** (![scegliere "Cambia modalità Editor"](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)).

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Trovare le esecuzioni che utilizzano risultati statici

Cronologia delle esecuzioni dell'app per la logica identifica le esecuzioni in cui le azioni utilizzano risultati statici. Per trovare le esecuzioni, seguire questa procedura:

1. Nel menu principale dell'app per la logica, selezionare **Panoramica**. 

1. Nel riquadro di destra, sotto **cronologia esecuzioni**, trovare il **statico risultati** colonna. 

   Qualsiasi esecuzione che include le azioni con risultati è il **risultati statici** colonna impostata su **abilitato**, ad esempio:

   ![La cronologia di esecuzione - colonna statica dei risultati](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Per visualizzare le azioni che utilizzano risultati statici, selezionare l'esecuzione desiderato in cui il **risultati statici** colonna è impostata su **abilitato**.

   Le azioni che utilizzano risultati statici mostrano becher test (![icona per ottenere risultati statici](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)) icona, ad esempio:

   ![Eseguire le azioni che utilizzano risultati statici cronologia-](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Disabilitare i risultati statici

La disattivazione di risultati statici non genera eccezioni da subito i valori dall'ultima installazione. Pertanto, quando si attiva statici risultati la volta successiva, puoi continuare a usare i valori precedenti.

1. Trovare l'azione in cui si desidera disabilitare gli output statici. Nell'angolo superiore destro dell'azione, scegliere l'icona becher di test (![icona per ottenere risultati statici](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)).

   ![Disabilitare i risultati statici](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Scegli **disabilitare il risultato statico** > **eseguita**.

   ![Disabilitare i risultati statici](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Riferimenti

Per altre informazioni su questa impostazione nelle definizioni di flusso di lavoro sottostante, vedere [risultati statici - riferimento allo Schema per il linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md#static-results) e [runtimeConfiguration.staticResult - Runtime impostazioni di configurazione](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [App per la logica di Azure](../logic-apps/logic-apps-overview.md)