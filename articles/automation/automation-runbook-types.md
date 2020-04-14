---
title: Tipi di runbook di Automazione di Azure
description: Descrive i diversi tipi di runbook che è possibile usare in Automazione di Azure e considerazioni per determinare il tipo da usare.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 4e8a5d2d168b8f60b7a32a8af358c6097003de60
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261296"
---
# <a name="azure-automation-runbook-types"></a>Tipi di runbook di Automazione di Azure

Il servizio di automazione dei processi di Automazione di Azure supporta diversi tipi di runbook, come definito nella tabella seguente. Per altre informazioni sull'ambiente di automazione dei processi, vedere Esecuzione di Runbook in Automazione di Azure.To learn about the process automation environment, see [Runbook execution in Azure Automation.](automation-runbook-execution.md)

| Type | Descrizione |
|:--- |:--- |
| [Grafico](#graphical-runbooks)|Runbook grafico basato su Windows PowerShell e creato e modificato completamente nell'editor grafico nel portale di Azure.Graphical runbook based on Windows PowerShell and created and edited completely in the graphical editor in Azure portal. |
| [Grafico del flusso di lavoro di PowerShell](#graphical-runbooks)|Runbook grafico basato sul flusso di lavoro di Windows PowerShell e creato e modificato completamente nell'editor grafico nel portale di Azure.Graphical runbook based on Windows PowerShell Workflow and created and edited completely in the graphical editor in Azure portal. |
| [PowerShell](#powershell-runbooks) |Runbook di testo basato su script di Windows PowerShell. |
| [Flusso di lavoro PowerShell](#powershell-workflow-runbooks)|Runbook di testo basato sullo scripting del flusso di lavoro di Windows PowerShell.Text runbook based on Windows PowerShell Workflow scripting. |
| [Python](#python-runbooks) |Runbook di testo basato su script Python. |

Prendere in considerazione le considerazioni seguenti quando si determina il tipo da utilizzare per un runbook specifico.

* Non è possibile convertire i runbook da grafici a tipo di testo o viceversa.
* Esistono limitazioni quando si usano runbook di tipi diversi come runbook figlio. Per altre informazioni, vedere [Runbook figlio in Automazione di Azure](automation-child-runbooks.md).

## <a name="graphical-runbooks"></a>Runbook grafici

È possibile creare e modificare i runbook grafici e grafici del flusso di lavoro di PowerShell usando l'editor grafico nel portale di Azure.You can create and edit graphical and graphical PowerShell Workflow runbooks using the graphical editor in the Azure portal. Tuttavia, non è possibile creare o modificare questo tipo di runbook con un altro strumento. Principali caratteristiche dei runbook grafici:

* Può essere esportato in file nell'account Automation e quindi importati in un altro account Di automazione. 
* Generare codice PowerShell.Generate PowerShell code. 
* Può essere convertito in o da runbook grafici del flusso di lavoro di PowerShell durante l'importazione. 

### <a name="advantages"></a>Vantaggi

* Usa il modello di creazione di inserimento-collegamento visivo.
* Concentrarsi sul modo in cui i dati attraversano il processo.
* Rappresentazione grafica dei processi di gestione.
* Includere altri runbook come runbook figlio per creare flussi di lavoro di alto livello.
* Incoraggiare la programmazione modulare.

### <a name="limitations"></a>Limitazioni

* Impossibile creare o modificare all'esterno del portale di Azure.Can't create or edit outside the Azure portal.
* Potrebbe essere necessaria un'attività di codice contenente codice PowerShell per eseguire logica complessa.
* Non è possibile convertire in uno dei formati di [testo,](automation-runbook-types.md)né è possibile convertire un runbook di testo in formato grafico. 
* Impossibile visualizzare o modificare direttamente il codice di PowerShell creato dal flusso di lavoro grafico. È possibile visualizzare il codice creato in qualsiasi attività di codice.
* Impossibile eseguire runbook in un runbook ibrido Linux.Can can runbooks on a Linux Hybrid Runbook Worker. Vedere [Automatizzare le risorse nel data center o](automation-hybrid-runbook-worker.md)nel cloud utilizzando Hybrid Runbook Worker .

## <a name="powershell-runbooks"></a>Runbook di PowerShell

I runbook di PowerShell sono basati su Windows PowerShell. È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure.  È anche possibile usare un editor di testo offline e [importare il runbook](manage-runbooks.md) in Automazione di Azure.

### <a name="advantages"></a>Vantaggi

* Implementazione di tutta la logica complessa con codice di PowerShell senza le complessità aggiuntive del flusso di lavoro PowerShell.
* Avviare più velocemente dei runbook del flusso di lavoro di PowerShell, poiché non è necessario compilarli prima dell'esecuzione.
* Eseguire in Azure e in Hybrid Runbook Worker per Windows e Linux.

### <a name="limitations"></a>Limitazioni

* È necessario avere familiarità con gli script di PowerShell.You must be familiar with PowerShell scripting.
* I Runbook non possono usare [l'elaborazione parallela](automation-powershell-workflow.md#parallel-processing) per eseguire più azioni in parallelo.
* I Runbook non possono usare [i checkpoint](automation-powershell-workflow.md#checkpoints) per riprendere il runbook in caso di errore.
* È possibile includere solo i runbook del flusso di lavoro di PowerShell e i runbook grafici come runbook figlio utilizzando il cmdlet [Start-AzAutomationRunbook,](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) che crea un nuovo processo.

### <a name="known-issues"></a>Problemi noti

Di seguito sono riportati i problemi noti correnti con i runbook di PowerShell:The following are current known issues with PowerShell runbooks:

* I runbook di PowerShell non possono recuperare un [asset variabile](automation-variables.md) non crittografato con un valore null.
* I runbook di PowerShell non sono in grado di recuperare un asset di tipo variabile con `*~*` nel nome.
* Un'operazione [Get-Process](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) in un ciclo in un runbook di PowerShell può bloccarsi dopo circa 80 iterazioni.
* Un runbook di PowerShell può avere esito negativo se tenta di scrivere una grande quantità di dati nel flusso di output contemporaneamente. In genere è possibile risolvere questo problema facendo in modo che l'output del runbook solo le informazioni necessarie per lavorare con oggetti di grandi dimensioni. Ad esempio, anziché utilizzare `Get-Process` senza limitazioni, è possibile fare in `Get-Process | Select ProcessName, CPU`modo che il cmdlet esempi i parametri obbligatori sia quello di .

## <a name="powershell-workflow-runbooks"></a>Runbook del flusso di lavoro PowerShell

I runbook del flusso di lavoro PowerShell sono runbook di testo basati sul [flusso di lavoro Windows PowerShell](automation-powershell-workflow.md). È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure. È anche possibile usare un editor di testo offline e [importare il runbook](manage-runbooks.md) in Automazione di Azure.

### <a name="advantages"></a>Vantaggi

* Implementazione di tutta la logica complessa con codice del flusso di lavoro PowerShell.
* Utilizzare [i checkpoint](automation-powershell-workflow.md#checkpoints) per riprendere l'operazione in caso di errore.
* Utilizzare [l'elaborazione parallela](automation-powershell-workflow.md#parallel-processing) per eseguire più azioni in parallelo.
* Possono includere altri runbook grafici e runbook del flusso di lavoro di PowerShell come runbook figlio per creare flussi di lavoro di alto livello.

### <a name="limitations"></a>Limitazioni

* È necessario avere familiarità con il flusso di lavoro di PowerShell.You must be familiar with PowerShell Workflow.
* I Runbook devono gestire la complessità aggiuntiva di PowerShell Workflow, ad esempio gli [oggetti deserializzati.](automation-powershell-workflow.md#code-changes)
* L'avvio dei runbook richiede più tempo rispetto ai runbook di PowerShell poiché devono essere compilati prima dell'esecuzione.
* È possibile includere i runbook di PowerShell `Start-AzAutomationRunbook` solo come runbook figlio utilizzando il cmdlet.
* I runbook non possono essere eseguiti in un runbook ibrido Linux.Runbooks can't run on a Linux Hybrid Runbook Worker.

## <a name="python-runbooks"></a>Runbook Python

I runbook Python vengono compilati in Python 2. È possibile modificare direttamente il codice del runbook usando l'editor di testo nel portale di Azure.You can directly edit the code of the runbook using the text editor in the Azure portal. È anche possibile usare un editor di testo offline e importare il runbook in Automazione di Azure.You can also use an offline text editor and [import the runbook](manage-runbooks.md) into Azure Automation.

### <a name="advantages"></a>Vantaggi

* Usa le robuste librerie Python.
* Può essere eseguito in Azure o in Linux Ibrido Runbook Worker. Windows Hybrid Runbook Workers sono supportati con [python2.7](https://www.python.org/downloads/release/latest/python2) installato.

### <a name="limitations"></a>Limitazioni

* È necessario avere familiarità con gli script Python.
* Solo Python 2 è supportato attualmente. Tutte le funzioni specifiche di Python 3 hanno esito negativo.
* Per utilizzare librerie di terze parti, è necessario [importare i pacchetti](python-packages.md) nell'account di automazione.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla creazione di runbook grafici, vedere [Creazione grafica in Automazione di Azure.To](automation-graphical-authoring-intro.md)learn more about graphical runbook authoring, see Graphical authoring in Azure Automation .
* Per comprendere le differenze tra i flussi di lavoro di PowerShell e PowerShell per i runbook, vedere [Learning Windows PowerShell Workflow](automation-powershell-workflow.md).
* Per altre informazioni su come creare o importare un runbook, vedere [Gestire i runbook in Automazione di Azure.For](manage-runbooks.md)more information on how to create or import a runbook, see Manage runbooks in Azure Automation.
* Per ulteriori informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, fare riferimento a Documenti di [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
