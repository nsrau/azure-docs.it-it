---
title: Come avviare un processo Azure Stream Analitica
description: Questo articolo descrive come avviare un processo di Stream Analitica.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: fb1d724907c09e2eb77930f5a235336ca8cd3a25
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886848"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Come avviare un processo Azure Stream Analitica

È possibile avviare il processo di Analitica Stream di Azure usando il portale di Azure, Visual Studio e PowerShell. Quando si avvia un processo, si seleziona un tempo per il processo avviare la creazione di output. Portale di Azure, Visual Studio e PowerShell dispongono di diversi metodi per impostare l'ora di inizio. Tali metodi sono descritti di seguito.

## <a name="azure-portal"></a>Portale di Azure

Passare al processo nel portale di Azure e seleziona **avviare** nella pagina di panoramica. Selezionare una **output del processo ora di inizio** e quindi selezionare **avviare**.

Sono disponibili tre opzioni per la **ora di inizio output del processo**: *A questo punto*, *Custom*, e *ultimo arresto*. Selezionando *ora* avvia il processo in quel momento. Selezionando *personalizzato* consente di impostare un'ora personalizzata nel passato o futuro per l'inizio del processo. Per riprendere un processo arrestato senza perdere i dati, scegliere *ultimo arresto*.

## <a name="visual-studio"></a>Visual Studio

Nella visualizzazione del processo, selezionare il pulsante freccia verde per avviare il processo. Impostare il **modalità di avvio Output processo** e selezionare **avviare**. Lo stato del processo cambierà da **in esecuzione**.

Sono disponibili tre opzioni per la **modalità di avvio Output processo**: *JobStartTime*, *CustomTime*, e *LastOutputEventTime*. Se questa proprietà è assente, il valore predefinito è *JobStartTime*.

*JobStartTime* rende il punto di partenza dell'evento di output identico quando viene avviato il processo di flusso.

*CustomTime* inizia l'output a un'ora personalizzata specificato nel *OutputStartTime* parametro.

*LastOutputEventTime* rende il punto di partenza del flusso di eventi di output ora lo stesso come ultimo evento di output.

## <a name="powershell"></a>PowerShell

Usare il cmdlet seguente per avviare il processo tramite PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Sono disponibili tre opzioni per la **proprietà OutputStartMode**: *JobStartTime*, *CustomTime*, e *LastOutputEventTime*. Se questa proprietà è assente, il valore predefinito è *JobStartTime*.

*JobStartTime* rende il punto di partenza dell'evento di output identico quando viene avviato il processo di flusso.

*CustomTime* inizia l'output a un'ora personalizzata specificato nel *OutputStartTime* parametro.

*LastOutputEventTime* rende il punto di partenza del flusso di eventi di output ora lo stesso come ultimo evento di output.

Per altre informazioni sul `Start-AzStreamAnalyitcsJob` cmdlet, visualizzare il [Start-AzStreamAnalyticsJob riferimento](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)
* [Guida introduttiva: Creare un processo di Stream Analitica con Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Guida introduttiva: Creare un processo di Analisi di flusso con gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-quick-create-vs.md)
