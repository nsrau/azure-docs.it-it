---
title: Come avviare un processo di analisi di flusso di Azure
description: Questo articolo descrive come avviare un processo di analisi di flusso da portale di Azure, PowerShell e Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 1e4cb63accf7e89ac02451e9c25b9902a8a10812
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173279"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Come avviare un processo di analisi di flusso di Azure

È possibile avviare il processo di analisi di flusso di Azure usando il portale di Azure, Visual Studio e PowerShell. Quando si avvia un processo, si seleziona un'ora in cui il processo inizierà a creare l'output. In portale di Azure, Visual Studio e PowerShell sono disponibili metodi diversi per l'impostazione dell'ora di inizio. Questi metodi sono descritti di seguito.

## <a name="start-options"></a>Opzioni di avvio
Per avviare un processo, sono disponibili le tre opzioni seguenti. Si noti che tutti gli orari indicati di seguito sono quelli specificati in [timestamp da](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Se TIMESTAMP BY non è specificato, verrà utilizzata l'ora di arrivo.
* **Ora**: Rende il punto iniziale del flusso di eventi di output uguale a quello in cui viene avviato il processo. Se viene usato un operatore temporale (ad esempio, intervallo di tempo, LAG o JOIN), analisi di flusso di Azure riguarderà automaticamente i dati nell'origine di input. Ad esempio, se si avvia un processo "Now" e se la query usa una finestra a cascata di 5 minuti, analisi di flusso di Azure cercherà i dati di 5 minuti fa nell'input.
Il primo evento di output potrebbe avere un timestamp uguale o maggiore dell'ora corrente e ASA garantisce che tutti gli eventi di input che possono contribuire logicamente all'output siano stati considerati. Ad esempio, non vengono generate aggregazioni a finestra parziali. Si tratta sempre del valore aggregato completo.

* **Personalizzato**: È possibile scegliere il punto iniziale dell'output. Analogamente all'opzione **Now** , analisi di flusso di Azure leggerà automaticamente i dati prima di questa ora se viene usato un operatore temporale 

* Data dell' **ultimo arresto**. Questa opzione è disponibile quando il processo è stato avviato in precedenza, ma è stato interrotto manualmente o non riuscito. Quando si sceglie questa opzione, analisi di flusso di Azure userà l'ora dell'ultimo output per riavviare il processo, in modo che i dati non vengano persi. Analogamente alle opzioni precedenti, analisi di flusso di Azure leggerà automaticamente i dati prima di questa ora se viene usato un operatore temporale. Poiché diverse partizioni di input possono avere un tempo diverso, viene usata la prima ora di arresto di tutte le partizioni, di conseguenza alcuni duplicati possono essere visualizzati nell'output. Altre informazioni sull'elaborazione di exactly-once sono disponibili nelle [garanzie](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)di recapito degli eventi della pagina.


## <a name="azure-portal"></a>Portale di Azure

Passare al processo nel portale di Azure e selezionare **Avvia** nella pagina panoramica. Selezionare l' **ora di inizio dell'output** di un processo e quindi fare clic su **Avvia**.

Scegliere una delle opzioni per l' **ora di inizio dell'output del processo**. Le opzioni sono *ora*, *personalizzate*e, se il processo è stato eseguito in precedenza, al *momento dell'ultimo arresto*. Per ulteriori informazioni su queste opzioni, vedere sopra.

## <a name="visual-studio"></a>Visual Studio

In visualizzazione processo selezionare il pulsante freccia verde per avviare il processo. Impostare la **modalità di avvio dell'output del processo** e selezionare **Avvia**. Lo stato del processo cambierà in **in esecuzione**.

Sono disponibili tre opzioni per la **modalità di avvio dell'output del processo**: *JobStartTime*, *CustomTime*e *LastOutputEventTime*. Se questa proprietà è assente, il valore predefinito è *JobStartTime*. Per ulteriori informazioni su queste opzioni, vedere sopra.


## <a name="powershell"></a>PowerShell

Usare il cmdlet seguente per avviare il processo usando PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Sono disponibili tre opzioni per **Proprietà outputstartmode**: *JobStartTime*, *CustomTime*e *LastOutputEventTime*. Se questa proprietà è assente, il valore predefinito è *JobStartTime*. Per ulteriori informazioni su queste opzioni, vedere sopra.

Per ulteriori informazioni sul `Start-AzStreamAnalyitcsJob` cmdlet, visualizzare il [riferimento Start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)
* [Avvio rapido: Creare un processo di analisi di flusso usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Avvio rapido: Creare un processo di Analisi di flusso con gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-quick-create-vs.md)
