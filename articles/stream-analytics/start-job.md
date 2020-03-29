---
title: Come avviare un processo di Analisi di flusso di AzureHow to start an Azure Stream Analytics job
description: Questo articolo descrive come avviare un processo di Analisi di flusso dal portale di Azure, PowerShell e Visual Studio.This article describes how to start a Stream Analytics job from Azure portal, PowerShell, and Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: c393eb782c2ff16eb5b3e5967b39938dfe2f1534
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426473"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Come avviare un processo di Analisi di flusso di AzureHow to start an Azure Stream Analytics job

È possibile avviare il processo di Analisi di flusso di Azure usando il portale di Azure, Visual Studio e PowerShell.You can start your Azure Stream Analytics job using the Azure portal, Visual Studio, and PowerShell. Quando si avvia un processo, si seleziona un'ora per l'inizio della creazione dell'output da parte del processo. Portale di Azure, Visual Studio e PowerShell hanno metodi diversi per impostare l'ora di inizio. Tali metodi sono descritti di seguito.

## <a name="start-options"></a>Opzioni di avvio
Per avviare un lavoro sono disponibili le tre opzioni seguenti. Si noti che tutte le volte riportate di seguito sono quelle specificate in [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Se TIMESTAMP BY non è specificato, verrà utilizzato l'ora di arrivo.
* **Now**: Rende il punto di partenza del flusso dell'evento di output come quando viene avviato il processo. Se viene usato un operatore temporale (ad esempio intervallo di tempo, LAG o JOIN), Analisi di flusso di Azure esaminerà automaticamente i dati nell'origine di input. Ad esempio, se si avvia un processo "Ora" e se la query usa una finestra a cascata di 5 minuti, Analisi di flusso di Azure cercherà i dati di 5 minuti fa nell'input.
Il primo evento di output possibile avrebbe un timestamp uguale o maggiore dell'ora corrente e ASA garantisce che tutti gli eventi di input che possono contribuire logicamente all'output siano stati presi in considerazione. Ad esempio, non vengono generate aggregazioni con finestra parziale. È sempre il valore aggregato completo.

* **Personalizzato**: È possibile scegliere il punto di partenza dell'output. Analogamente all'opzione **Ora,** Analisi di flusso di Azure leggerà automaticamente i dati prima di questo orario se viene usato un operatore temporale 

* **All'ultimo arresto**. Questa opzione è disponibile quando il processo è stato avviato in precedenza, ma è stato arrestato manualmente o non è riuscito. Quando si sceglie questa opzione, Analisi di flusso di Azure userà l'ultimo tempo di output per riavviare il processo in modo che non vengano persi dati. Analogamente alle opzioni precedenti, Analisi di flusso di Azure leggerà automaticamente i dati prima di questo orario se viene usato un operatore temporale. Poiché più partizioni di input possono avere un tempo diverso, viene utilizzato l'ora di arresto meno recente di tutte le partizioni, di conseguenza alcuni duplicati possono essere visualizzati nell'output. Ulteriori informazioni sull'elaborazione esattamente una volta sono disponibili nella pagina [Garanzie di consegna degli eventi](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>Portale di Azure

Passare al processo nel portale di Azure e selezionare **Avvia** nella pagina di panoramica. Selezionare **un'ora di inizio output processo** e quindi selezionare **Avvia**.

Scegliere una delle opzioni per Ora di **inizio output processo.** Le opzioni sono *Now*, *Custom*e, se il processo è stato eseguito in precedenza, *All'ultimo arresto*. Per ulteriori informazioni su queste opzioni, vedere sopra.

## <a name="visual-studio"></a>Visual Studio

Nella vista processo, selezionare il pulsante freccia verde per avviare il processo. Impostare la modalità di **avvio dell'output del processo** e selezionare **Avvia**. Lo stato del processo verrà modificato in **In esecuzione**.

Sono disponibili tre opzioni per **job Output Start Mode**: *JobStartTime*, *CustomTime*e *LastOutputEventTime*. Se questa proprietà è assente, il valore predefinito è *JobStartTime*. Per ulteriori informazioni su queste opzioni, vedere sopra.


## <a name="powershell"></a>PowerShell

Utilizzare il cmdlet seguente per avviare il processo tramite PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Sono disponibili tre opzioni per **OutputStartMode**: *JobStartTime*, *CustomTime*e *LastOutputEventTime*. Se questa proprietà è assente, il valore predefinito è *JobStartTime*. Per ulteriori informazioni su queste opzioni, vedere sopra.

Per ulteriori informazioni `Start-AzStreamAnalyitcsJob` sul cmdlet, visualizzare il [riferimento Start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: creare un processo di Analisi di flusso di Azure tramite il portale di Azure](stream-analytics-quick-create-portal.md)
* [Guida introduttiva: Creare un processo di Analisi di flusso usando Azure PowerShellQuickstart: Create a Stream Analytics job using Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Guida introduttiva: Creare un processo di Analisi di flusso con gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-quick-create-vs.md)
