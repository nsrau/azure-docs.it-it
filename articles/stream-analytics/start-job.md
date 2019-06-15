---
title: Come avviare un processo Azure Stream Analitica
description: Questo articolo descrive come avviare un processo di Stream Analitica.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 9bc3e4132919e5fc5baadc78841e66efd3c34bcd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61362266"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Come avviare un processo Azure Stream Analitica

È possibile avviare il processo di Analitica Stream di Azure usando il portale di Azure, Visual Studio e PowerShell. Quando si avvia un processo, si seleziona un tempo per il processo avviare la creazione di output. Portale di Azure, Visual Studio e PowerShell dispongono di diversi metodi per impostare l'ora di inizio. Tali metodi sono descritti di seguito.

## <a name="start-options"></a>Opzioni di avvio
Le tre opzioni seguenti sono disponibili per avviare un processo. Si noti che tutte le volte indicate di seguito sono quelli specificati nel [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Se TIMESTAMP BY non viene specificato, ora di arrivo verrà utilizzato.
* **A questo punto**: Rende il punto di partenza dell'evento di output identico quando viene avviato il processo di flusso. Se viene usato un operatore temporale (ad esempio, finestra temporale, LAG o JOIN), Azure Stream Analitica automaticamente esaminerà nuovamente i dati nell'origine di input. Ad esempio, se si avvia un processo di "Adesso" e se la query Usa una finestra a cascata di 5 minuti, Azure Stream Analitica cercherà i dati di 5 minuti fa nell'input.
Il primo evento di output possibili sarebbe un timestamp uguale o maggiore rispetto all'ora corrente e ASA garantisce che tutti gli eventi di input che possono contribuire in modo logico per l'output dispone stati presi in considerazione. Ad esempio, non le aggregazioni parziali vengono generate. È sempre il valore aggregato completo.

* **Personalizzato**: È possibile scegliere il punto di partenza dell'output. In modo simile al **ora** opzione, Azure Stream Analitica leggerà automaticamente i dati prima dell'operazione se viene usato un operatore temporale 

* **Ultimo arresto**. Questa opzione è disponibile quando il processo è stato avviato in precedenza, ma è stato interrotto manualmente o non è riuscito. Quando si sceglie questa opzione per riavviare il processo in modo che venga perso alcun dato Analitica Stream di Azure userà ora dell'ultimo output. In modo analogo alle opzioni precedenti, Azure Stream Analitica automaticamente leggerà i dati prima di questa volta se viene usato un operatore temporale. Dato che più partizioni di input abbia tempo diverso, viene usata la prima ora di arresto di tutte le partizioni, di conseguenza si verifichino alcuni duplicati nell'output. Altre informazioni su esattamente-l'elaborazione di una volta sono disponibili nella pagina [garanzie di recapito di eventi](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>Portale di Azure

Passare al processo nel portale di Azure e seleziona **avviare** nella pagina di panoramica. Selezionare una **output del processo ora di inizio** e quindi selezionare **avviare**.

Scegliere una delle opzioni per la **output del processo ora di inizio**. Le opzioni disponibili sono *adesso*, *Custom*, e, se il processo è stato eseguito in precedenza, *ultimo arresto*. Per altre informazioni su queste opzioni, vedere sopra.

## <a name="visual-studio"></a>Visual Studio

Nella visualizzazione del processo, selezionare il pulsante freccia verde per avviare il processo. Impostare il **modalità di avvio Output processo** e selezionare **avviare**. Lo stato del processo cambierà da **in esecuzione**.

Sono disponibili tre opzioni per la **modalità di avvio Output processo**: *JobStartTime*, *CustomTime*, e *LastOutputEventTime*. Se questa proprietà è assente, il valore predefinito è *JobStartTime*. Per altre informazioni su queste opzioni, vedere sopra.


## <a name="powershell"></a>PowerShell

Usare il cmdlet seguente per avviare il processo tramite PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Sono disponibili tre opzioni per la **proprietà OutputStartMode**: *JobStartTime*, *CustomTime*, e *LastOutputEventTime*. Se questa proprietà è assente, il valore predefinito è *JobStartTime*. Per altre informazioni su queste opzioni, vedere sopra.

Per altre informazioni sul `Start-AzStreamAnalyitcsJob` cmdlet, visualizzare il [Start-AzStreamAnalyticsJob riferimento](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)
* [Avvio rapido: Creare un processo di Stream Analitica con Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Avvio rapido: Creare un processo di Analisi di flusso con gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-quick-create-vs.md)
