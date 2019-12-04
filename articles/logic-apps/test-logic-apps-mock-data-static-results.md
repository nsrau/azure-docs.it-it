---
title: Testare le app per la logica con dati fittizi
description: Configurare risultati statici per il test di app per la logica con dati fittizi senza influire sugli ambienti di produzione
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: b71aae91f4a065b70537a300aa0bd7016edfd4b4
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790271"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testare app per la logica con dati fittizi impostando risultati statici

Quando si esegue il test delle app per la logica, è possibile che non si sia pronti a chiamare o ad accedere a app, servizi e sistemi per diversi motivi. In genere, in questi scenari potrebbe essere necessario eseguire diversi percorsi di condizione, forzare gli errori, fornire corpi di risposta ai messaggi specifici oppure provare a ignorare alcuni passaggi. Impostando i risultati statici per un'azione nell'app per la logica, è possibile simulare i dati di output da tale azione. L'abilitazione dei risultati statici in un'azione non comporta l'esecuzione dell'azione, ma restituisce invece i dati fittizi.

Ad esempio, se si configurano i risultati statici per l'azione Invia messaggi di Outlook 365, il motore delle app per la logica restituisce solo i dati fittizi specificati come risultati statici, anziché chiamare Outlook e inviare un messaggio di posta elettronica.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* App per la logica in cui si vogliono configurare i risultati statici

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Configurare risultati statici

1. Se non è già stato fatto, nella [portale di Azure](https://portal.azure.com)aprire l'app per la logica nella finestra di progettazione delle app per la logica.

1. Nell'azione in cui si desidera configurare i risultati statici, attenersi alla seguente procedura: 

   1. Nell'angolo superiore destro dell'azione scegliere il pulsante con i puntini di sospensione ( *...* ) e selezionare **risultato statico**, ad esempio:

      ![Selezionare "risultato statico" > "Abilita risultato statico"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Scegliere **Abilita risultato statico**. Per le proprietà obbligatorie (*) specificare i valori di output fittizi che si desidera restituire per la risposta dell'azione.

      Ad esempio, di seguito sono riportate le proprietà obbligatorie per l'azione HTTP:

      | Proprietà | Description |
      |----------|-------------|
      | **Status** | Stato dell'azione da restituire. |
      | **Codice di stato** | Codice di stato specifico da restituire. |
      | **Intestazioni** | Contenuto dell'intestazione da restituire. |
      |||

      ![Selezionare "Abilita risultato statico"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Per immettere i dati fittizi nel formato JavaScript Object Notation (JSON), scegliere **passa alla modalità JSON** (![Scegli "passa alla modalità json"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)).

   1. Per le proprietà facoltative, aprire l'elenco **Seleziona campi facoltativi** e selezionare le proprietà che si desidera simulare.

      ![Selezionare le proprietà facoltative](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Quando si è pronti per il salvataggio, scegliere **fine**.

   Nell'angolo superiore destro dell'azione, la barra del titolo Mostra ora un'icona del becher di test (![icona per i risultati statici](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)), che indica che sono stati abilitati i risultati statici.

   ![Icona che mostra i risultati statici abilitati](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Per trovare le esecuzioni precedenti che usano dati fittizi, vedere [trovare esecuzioni che usano risultati statici](#find-runs-mock-data) più avanti in questo argomento.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Riutilizza output precedenti

Se l'app per la logica ha un'esecuzione precedente con output che è possibile riutilizzare come output fittizi, è possibile copiare e incollare gli output da tale esecuzione.

1. Se non è già stato fatto, nella [portale di Azure](https://portal.azure.com)aprire l'app per la logica nella finestra di progettazione delle app per la logica.

1. Scegliere **Panoramica**dal menu principale dell'app per la logica.

1. Nella sezione **Cronologia esecuzioni** selezionare l'esecuzione dell'app per la logica desiderata.

1. Nel flusso di lavoro dell'app per la logica trovare ed espandere l'azione con gli output desiderati.

1. Scegliere il collegamento **Mostra output non elaborati** .

1. Copiare l'oggetto completo JavaScript Object Notation (JSON) o la sottosezione specifica che si vuole usare, ad esempio la sezione Outputs o anche solo la sezione Headers.

1. Seguire i passaggi per aprire la casella **risultato statico** per l'azione in [configurare i risultati statici](#set-up-static-results).

1. Quando si apre la casella **risultato statico** , scegliere uno dei due passaggi:

   * Per incollare un oggetto JSON completo, scegliere **passa alla modalità JSON** (![scegliere "passa alla modalità json"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![Scegliere "passa alla modalità JSON" per l'oggetto completo](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Per incollare solo una sezione JSON, accanto all'etichetta di tale sezione scegliere **passa alla modalità JSON** per tale sezione, ad esempio:

     ![Scegliere "passa alla modalità JSON" per gli output](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. Nell'editor JSON incollare il codice JSON copiato in precedenza.

   ![Modalità JSON](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Al termine, scegliere **Fine**. In alternativa, per tornare alla finestra di progettazione, scegliere **Cambia modalità editor** (![scegliere "cambia modalità editor"](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)).

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Trova esecuzioni che utilizzano risultati statici

La cronologia delle esecuzioni dell'app per la logica identifica le esecuzioni in cui le azioni usano risultati statici. Per trovare queste esecuzioni, attenersi alla procedura seguente:

1. Scegliere **Panoramica**dal menu principale dell'app per la logica. 

1. Nel riquadro di destra, in **Cronologia esecuzioni**, trovare la colonna **risultati statici** . 

   Qualsiasi esecuzione che includa azioni con risultati ha la colonna **risultati statici** impostata su **abilitato**, ad esempio:

   ![Cronologia di esecuzione-colonna risultati statici](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Per visualizzare le azioni che utilizzano risultati statici, selezionare l'esecuzione desiderata in cui la colonna **risultati statici** è impostata su **abilitata**.

   Le azioni che usano risultati statici mostrano l'icona del becher di test (icona![per i risultati statici](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)), ad esempio:

   ![Cronologia di esecuzione-azioni che usano risultati statici](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Disabilitare i risultati statici

La disattivazione dei risultati statici non elimina i valori dall'ultima installazione. Quindi, quando si attivano i risultati statici la volta successiva, è possibile continuare a usare i valori precedenti.

1. Individuare l'azione in cui si desidera disabilitare gli output statici. Nell'angolo superiore destro dell'azione scegliere l'icona del becher di test (![icona per i risultati statici](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)).

   ![Disabilitare i risultati statici](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Scegliere **Disabilita risultato statico** > **completato**.

   ![Disabilitare i risultati statici](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Riferimento

Per altre informazioni su questa impostazione nelle definizioni del flusso di lavoro sottostanti, vedere [risultati statici-riferimento allo schema per il linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md#static-results) e [impostazioni di configurazione di runtimeConfiguration. staticResult-Runtime](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [app](../logic-apps/logic-apps-overview.md) per la logica di Azure