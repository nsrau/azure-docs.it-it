---
title: Monitorare e gestire i test nel portale di Azure Stack VaaS | Microsoft Docs
description: Monitorare e gestire i test nel portale di Azure Stack VaaS.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: c4a4b70e39731b3a78fd42078bfa1b7340343f9a
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334372"
---
# <a name="monitor-and-manage-tests-in-the-vaas-portal"></a>Monitorare e gestire i test nel portale di VaaS

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Dopo la pianificazione di test con la soluzione di Azure Stack, convalida come servizio (VaaS) inizierà la segnalazione dello stato di esecuzione di test. Queste informazioni sono disponibili nel portale di VaaS e azioni, ad esempio la ripianificazione e l'annullamento dei test.

## <a name="navigate-to-the-workflow-tests-summary-page"></a>Passare alla pagina di riepilogo del test del flusso di lavoro

1. Nel dashboard soluzioni, selezionare una soluzione esistente che ha almeno un flusso di lavoro.

    ![Riquadri del flusso di lavoro](media/tile_all-workflows.png)

1. Selezionare **Gestisci** sul riquadro del flusso di lavoro. Nella pagina successiva sono elencati i flussi di lavoro create per la soluzione selezionata.

1. Selezionare il nome del flusso di lavoro per aprire il riepilogo del test.

## <a name="change-workflow-parameters"></a>Modificare i parametri del flusso di lavoro

Ogni tipo di flusso di lavoro consente di modificare la [parametri di Test](azure-stack-vaas-parameters.md#test-parameters) specificato durante la creazione del flusso di lavoro.

1. Nella pagina di riepilogo del test, selezionare la **modifica** pulsante.

1. Fornire nuovi valori in base alla [parametri comuni del flusso di lavoro per la convalida dello Stack di Azure come servizio](azure-stack-vaas-parameters.md).

1. Selezionare **Submit** per salvare i valori.

> [!NOTE]
> Nel **superamento Test** flusso di lavoro, è necessario completare la selezione di test e passare alla pagina di revisione prima di poter salvare i nuovi valori di parametro.

### <a name="add-tests-test-pass-only"></a>Aggiungere i test (solo superamento Test)

In **superamento Test** flussi di lavoro, entrambe le **aggiungere test** e **modifica** pulsanti consentono di pianificare i test di nuovo nel flusso di lavoro.

> [!TIP]
> Selezionare **aggiungere i test** se solo possibile pianificare nuovi test e non è necessario modificare i parametri per un **superamento Test** flusso di lavoro.

## <a name="managing-test-instances"></a>La gestione di istanze di test

Per le esecuzioni non ufficiali (ad esempio, il **superamento Test** flusso di lavoro), la pagina di riepilogo del test sono elencati i test pianificati per la soluzione di Azure Stack.

Per le esecuzioni ufficiali (ad esempio, il **convalida** i flussi di lavoro), la pagina di riepilogo del test sono elencati i test necessari per completare la convalida della soluzione Azure Stack. Test di convalida vengono pianificati da questa pagina.

Ogni istanza di test pianificato Mostra le informazioni seguenti:

| Colonna | DESCRIZIONE |
| --- | --- |
| Nome test | Il nome e versione del test. |
| Categoria | Lo scopo del test. |
| Data di creazione | Ora in cui è stato pianificato il test. |
| Started | Ora in cui il test ha iniziato l'esecuzione. |
| Duration | Il periodo di tempo che è stato eseguito il test. |
| Status | Lo stato o risultato del test. Sono stati pre-esecuzione o in corso: `Pending`, `Running`. Sono stati terminali: `Cancelled`, `Failed`, `Aborted`, `Succeeded`. |
| Nome agente | Il nome dell'agente che ha eseguito il test. |
| Operazioni totali | Numero totale delle operazioni tentate durante il test. |
| Operazioni passate | Il numero di operazioni che è riuscita durante il test. |
|  Operazioni non riuscite | Il numero di operazioni riuscite durante il test. |

### <a name="actions"></a>Azioni

Ogni istanza di test sono elencati disponibili azioni da eseguire quando si fa clic sul menu di scelta rapida **[...]**  nella tabella delle istanze di test.

#### <a name="view-information-about-the-test-definition"></a>Visualizzare le informazioni sulla definizione di test

Selezionare **consente di visualizzare informazioni** dal menu di scelta rapida per visualizzare informazioni generali sulla definizione di test. Si è condiviso da ogni istanza di test con lo stesso nome e versione.

| Proprietà di test | DESCRIZIONE |
| -- | -- |
| Nome test | Il nome del test. |
| Versione di prova | La versione del test. |
| Editore | Il server di pubblicazione del test. |
| Categoria |  Lo scopo del test. |
| Servizi di destinazione | Azure Stack i servizi sottoposto a test. |
| DESCRIZIONE | La descrizione del test. |
| Durata stimata (minuti) | Il runtime previsto del test. |
| Collegamenti | Informazioni importanti sul test o i punti di contatto. |

#### <a name="view-test-instance-parameters"></a>Parametri di istanza di visualizzazione test

Selezionare **visualizzare i parametri** dal menu di scelta rapida per visualizzare i parametri forniti all'istanza di test in fase di pianificazione. Le stringhe sensibili come le password non vengono visualizzate. Questa azione è disponibile solo per i test che sono stati pianificati.

In questa finestra sono inclusi i metadati per tutte le istanze di test seguenti:

| Proprietà dell'istanza di test | DESCRIZIONE |
| -- | -- |
| Nome test | Il nome del test. |
| Versione di prova | La versione del test. |
| ID dell'istanza di test | Un GUID che identifica l'istanza specifica del test. |

#### <a name="view-test-instance-operations"></a>Visualizzare le operazioni di istanza di test

Selezionare **per visualizzare le operazioni** dal contesto del menu per visualizzare uno stato dettagliato delle operazioni eseguite durante il test. Questa azione è disponibile solo per i test che sono stati pianificati.

![Visualizzare le operazioni](media/manage-test_context-menu-operations.png)

#### <a name="download-logs-for-a-completed-test-instance"></a>Scaricare i log per un'istanza di test completato

Selezionare **scaricare i log** dal menu di scelta rapida per scaricare un `.zip` file di output dei log durante l'esecuzione di test. Questa azione è disponibile solo per i test che sono state completate, ad esempio, un test con un indicatore di stato del `Cancelled`, `Failed`, `Aborted`, o `Succeeded`.

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>Modificare la pianificazione di un'istanza di test o pianificare un test

Pianificazione di test dalla pagina di gestione dipende dal tipo di flusso di lavoro che in cui viene eseguito il test.

##### <a name="test-pass-workflow"></a>Flusso di lavoro di superamento test

Nel flusso di lavoro, Test Pass **ripianificazione** un'istanza di test riusa lo stesso set di parametri dell'istanza di test originale e *sostituisce* risultati originale, inclusi i log. È necessario immettere nuovamente le stringhe sensibili come le password quando è modificare la pianificazione.

1. Selezionare **ripianificare** dal menu di scelta rapida per aprire un prompt dei comandi per l'istanza di prova di ripianificazione.

1. Immettere eventuali parametri applicabile.

1. Selezionare **Submit** ripianificare l'istanza di prova e sostituire l'istanza esistente.

##### <a name="validation-workflows"></a>Flussi di lavoro di convalida

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>Annulla un'istanza di test

Un test pianificato può essere annullato se il suo stato sia `Pending` o `Running`.  

1. Selezionare **annullare** dal menu di scelta rapida per aprire un prompt dei comandi per annullare l'istanza di prova.

1. Selezionare **Submit** per annullare l'istanza di prova.

## <a name="next-steps"></a>Passaggi successivi

- [Risolvere i problemi di convalida come servizio](azure-stack-vaas-troubleshoot.md)
