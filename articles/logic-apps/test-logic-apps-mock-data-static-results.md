---
title: Testare le app per la logica con dati fittizi
description: Impostare risultati statici per il test di app per la logica con dati fittizi senza influire sugli ambienti di produzioneSet up static results for testing logic apps with mock data without affecting production environments
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: b71aae91f4a065b70537a300aa0bd7016edfd4b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790271"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testare le app per la logica con dati fittizi impostando risultati staticiTest logic apps with mock data by setting up static results

Durante il test delle app per la logica, potresti non essere pronto a chiamare o accedere effettivamente ad app, servizi e sistemi per vari motivi. In genere, in questi scenari, potrebbe essere necessario eseguire percorsi di condizione diversi, forzare errori, fornire corpi di risposta del messaggio specifici o anche provare a saltare alcuni passaggi. Impostando i risultati statici per un'azione nell'app per la logica, è possibile simulare i dati di output da tale azione. L'abilitazione di risultati statici in un'azione non esegue l'azione, ma restituisce i dati fittizi.

Ad esempio, se si impostano risultati statici per l'azione invia posta di Outlook 365, il motore App per la logica restituisce solo i dati fittizi specificati come risultati statici, anziché chiamare Outlook e inviare un messaggio di posta elettronica.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>.

* Informazioni di base sulla [creazione di app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si desidera impostare i risultati statici

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Impostare risultati statici

1. Se non è già stato fatto, nel portale di [Azure](https://portal.azure.com)aprire l'app per la logica in Progettazione app per la logica.

1. Nell'azione in cui si desidera impostare i risultati statici, attenersi alla seguente procedura: 

   1. Nell'angolo superiore destro dell'azione scegliere il pulsante con i puntini di sospensione (*...*) e selezionare **Risultato statico**, ad esempio:

      ![Selezionare "Risultato statico" > "Abilita risultato statico"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Scegliere **Abilita risultato statico**. Per le proprietà obbligatorie, specificare i valori di output fittizi e desiderati per la risposta dell'azione.

      Ad esempio, di seguito sono riportate le proprietà necessarie per l'azione HTTP:For example, here are the required properties for the HTTP action:

      | Proprietà | Descrizione |
      |----------|-------------|
      | **Stato** | Lo stato dell'azione da restituire |
      | **Codice di stato** | Il codice di stato specifico da restituire |
      | **Intestazioni** | Contenuto dell'intestazione da restituire |
      |||

      ![Selezionare "Abilita risultato statico"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Per immettere i dati fittizi in formato JSON (JavaScript Object![Notation), scegliere](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)Passa alla **modalità JSON** (Scegliere "Passa alla modalità JSON").

   1. Per le proprietà facoltative, aprire l'elenco **Seleziona campi facoltativi** e selezionare le proprietà che si desidera simulare.

      ![Selezionare le proprietà facoltative](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Quando si è pronti per il salvataggio, scegliere **Fatto**.

   Nell'angolo superiore destro dell'azione, la barra del titolo ora![mostra un'icona](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)di becher di test ( Icona per i risultati statici ), che indica che sono stati abilitati i risultati statici.

   ![Icona che mostra i risultati statici abilitati](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Per trovare esecuzioni precedenti che usano dati fittizi, vedere [Trovare esecuzioni che usano risultati statici](#find-runs-mock-data) più avanti in questo argomento.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Riutilizzare le uscite precedenti

Se l'app per la logica prevede un'esecuzione precedente con output, è possibile riutilizzarli come output fittizi, è possibile copiare e incollare gli output dell'esecuzione.

1. Se non è già stato fatto, nel portale di [Azure](https://portal.azure.com)aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu principale dell'app per la logica selezionare **Panoramica**.

1. Nella sezione **Cronologia esecuzioni** selezionare l'esecuzione dell'app per la logica desiderata.

1. Nel flusso di lavoro dell'app per la logica individuare ed espandere l'azione con gli output desiderati.

1. Scegliere il collegamento **Mostra output non elaborati.**

1. Copiare l'oggetto JSON (JavaScript Object Notation) completo o la sottosezione specifica che si desidera utilizzare, ad esempio la sezione outputs o anche solo la sezione headers.

1. Seguire i passaggi per aprire la casella **Risultati statici** per l'azione in [Impostare i risultati statici](#set-up-static-results).

1. Dopo aver aperto la finestra **risultati Statico,** scegliere uno dei passaggi seguenti:After the Static result box opens, choose either step:

   * Per incollare un oggetto JSON completo,![scegliere Passa alla](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) **modalità JSON** (scegliere "Passa alla modalità JSON"):

     ![Scegliere "Passa alla modalità JSON" per l'oggetto completo](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Per incollare solo una sezione JSON, accanto all'etichetta della sezione scegliere **Passa alla modalità JSON** per tale sezione, ad esempio:

     ![Scegliere "Passa alla modalità JSON" per gli output](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. Nell'editor JSON incollare il codice JSON copiato in precedenza.

   ![Modalità JSON](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Al termine, scegliere **Fine**. In alternativa, per tornare alla finestra![di progettazione,](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)scegliere Cambia **modalità editor** (scegliere "Cambia modalità editor".

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Trovare esecuzioni che utilizzano risultati staticiFind runs that use static results

La cronologia delle esecuzioni dell'app per la logica identifica le esecuzioni in cui le azioni usano risultati statici. Per trovare queste esecuzioni, attenersi alla seguente procedura:

1. Nel menu principale dell'app per la logica selezionare **Panoramica**. 

1. Nel riquadro destro, in **Cronologia esecuzioni**, individuare la colonna **Risultati statici.** 

   Per qualsiasi esecuzione che include azioni con risultati la colonna **Risultati statici** è impostata su **Abilitato**, ad esempio:

   ![Cronologia delle esecuzione - colonna dei risultati staticiRun history - static results column](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Per visualizzare le azioni che utilizzano risultati statici, selezionare l'esecuzione desiderata in cui la colonna **Risultati statici** è **impostata**su Abilitato .

   Le azioni che utilizzano risultati statici mostrano l'icona test beaker (![Icona per i risultati](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)statici), ad esempio:

   ![Cronologia delle operazioni: azioni che utilizzano risultati staticiRun history - actions that use static results](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Disabilitare i risultati statici

La disattivazione dei risultati statici non comporta la spegnimento dei valori dell'ultima configurazione. Così, quando si attivano i risultati statici la prossima volta, è possibile continuare a utilizzare i valori precedenti.

1. Individuare l'azione in cui si desidera disabilitare gli output statici. Nell'angolo superiore destro dell'azione scegliere l'icona del![becher](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)di test ( Icona per i risultati statici ).

   ![Disabilitare i risultati statici](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Scegliere **Disattiva risultato** > statico**completato**.

   ![Disabilitare i risultati statici](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Riferimento

Per altre informazioni su questa impostazione nelle definizioni del flusso di lavoro sottostanti, vedere [Risultati statici - Riferimento allo schema per Il](../logic-apps/logic-apps-workflow-definition-language.md#static-results) linguaggio di definizione del flusso di lavoro e runtimeConfiguration.staticResult - Impostazioni di configurazione del runtimeFor more information about this setting in your underlying workflow definitions, see Static results - Schema reference for Workflow Definition Language and [runtimeConfiguration.staticResult - Runtime configuration settings](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su App per la logica di [AzureLearn](../logic-apps/logic-apps-overview.md) more about Azure Logic Apps