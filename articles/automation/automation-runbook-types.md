---
title: Tipi di runbook di Automazione di Azure
description: Vengono descritti i diversi tipi di manuali operativi che è possibile usare in automazione di Azure e considerazioni per determinare il tipo da usare.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 1ac6347bd8e723f356da4803da54a6ea45a4a71a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535520"
---
# <a name="azure-automation-runbook-types"></a>Tipi di runbook di Automazione di Azure

Il servizio automazione del processo di automazione di Azure supporta diversi tipi di manuali operativi, come definito nella tabella seguente. Per informazioni sull'ambiente di automazione dei processi, vedere [esecuzione di Runbook in automazione di Azure](automation-runbook-execution.md).

| Type | Descrizione |
|:--- |:--- |
| [Grafico](#graphical-runbooks)|Runbook grafico basato su Windows PowerShell e creato e modificato completamente nell'editor grafico in portale di Azure. |
| [Grafico del flusso di lavoro di PowerShell](#graphical-runbooks)|Runbook grafico basato sul flusso di lavoro di Windows PowerShell e creato e modificato completamente nell'editor grafico in portale di Azure. |
| [PowerShell](#powershell-runbooks) |Runbook di testo basato sullo scripting di Windows PowerShell. |
| [Flusso di lavoro PowerShell](#powershell-workflow-runbooks)|Runbook di testo basato sullo scripting del flusso di lavoro di Windows PowerShell. |
| [Python](#python-runbooks) |Runbook di testo basato sullo scripting di Python. |

Prendere in considerazione le considerazioni seguenti per determinare il tipo da usare per un determinato Runbook.

* Non è possibile convertire manuali operativi da tipo grafico a testo o viceversa.
* Esistono alcune limitazioni quando si usa manuali operativi di tipi diversi come manuali operativi figlio. Per altre informazioni, vedere [Runbook figlio in Automazione di Azure](automation-child-runbooks.md).

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="graphical-runbooks"></a>Runbook grafici

È possibile creare e modificare runbook grafici e runbook del flusso di lavoro PowerShell grafico usando l'editor grafico nel portale di Azure. Tuttavia, non è possibile creare o modificare questo tipo di runbook con un altro strumento. Funzionalità principali dei manuali operativi grafici:

* Possono essere esportati in file nell'account di automazione e quindi importati in un altro account di automazione. 
* Genera codice PowerShell. 
* Può essere convertito in o da manuali operativi del flusso di lavoro PowerShell grafico durante l'importazione. 

### <a name="advantages"></a>Vantaggi

* Usare il modello di creazione di Visual Insert-link-configure.
* Concentrarsi sul modo in cui i dati passano attraverso il processo.
* Rappresentazione grafica dei processi di gestione.
* Includere altre manuali operativi come manuali operativi figlio per creare flussi di lavoro di alto livello.
* Incoraggiare la programmazione modulare.

### <a name="limitations"></a>Limitazioni

* Non è possibile creare o modificare all'esterno del portale di Azure.
* Potrebbe richiedere un'attività del codice contenente il codice di PowerShell per eseguire la logica complessa.
* Non è possibile eseguire la conversione in uno dei [formati di testo](automation-runbook-types.md), né convertire un Runbook di testo in formato grafico. 
* Non è possibile visualizzare o modificare direttamente il codice di PowerShell creato dal flusso di lavoro grafico. È possibile visualizzare il codice creato in qualsiasi attività di codice.
* Non è possibile eseguire manuali operativi in un ruolo di lavoro ibrido per Runbook Linux. Vedere [automatizzare le risorse nel Data Center o nel cloud tramite il ruolo di lavoro ibrido per Runbook](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>Runbook di PowerShell

I runbook di PowerShell sono basati su Windows PowerShell. È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure.  È anche possibile usare un editor di testo offline e [importare il runbook](manage-runbooks.md) in Automazione di Azure.

### <a name="advantages"></a>Vantaggi

* Implementazione di tutta la logica complessa con codice di PowerShell senza le complessità aggiuntive del flusso di lavoro PowerShell.
* Avviare più velocemente rispetto al manuali operativi del flusso di lavoro PowerShell, perché non è necessario compilarlo prima di eseguire.
* Eseguire in Azure e nei ruoli di lavoro ibridi per Runbook sia per Windows che per Linux.

### <a name="limitations"></a>Limitazioni

* È necessario avere familiarità con gli script di PowerShell.
* Manuali operativi non può usare l' [elaborazione parallela](automation-powershell-workflow.md#parallel-processing) per eseguire più azioni in parallelo.
* Manuali operativi non può usare i [Checkpoint](automation-powershell-workflow.md#checkpoints) per riprendere runbook se si verifica un errore.
* È possibile includere solo manuali operativi del flusso di lavoro PowerShell e manuali operativi grafici come manuali operativi figlio usando il cmdlet [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) , che consente di creare un nuovo processo.

### <a name="known-issues"></a>Problemi noti

Di seguito sono riportati i problemi noti correnti relativi a manuali operativi di PowerShell:

* PowerShell manuali operativi non è in grado di recuperare un [asset di variabile](automation-variables.md) non crittografato con un valore null.
* I runbook di PowerShell non sono in grado di recuperare un asset di tipo variabile con `*~*` nel nome.
* Un'operazione [Get-Process](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) in un ciclo in un Runbook di PowerShell può arrestarsi in modo anomalo dopo circa 80 iterazioni.
* Un Runbook di PowerShell può avere esito negativo se tenta di scrivere una grande quantità di dati nel flusso di output in una sola volta. In genere è possibile ovviare a questo problema facendo in modo che Runbook restituisca solo le informazioni necessarie per l'utilizzo di oggetti di grandi dimensioni. Ad esempio, invece di usare `Get-Process` senza limitazioni, è possibile fare in modo che il cmdlet restituisca solo i parametri `Get-Process | Select ProcessName, CPU`obbligatori come in.

## <a name="powershell-workflow-runbooks"></a>Runbook del flusso di lavoro PowerShell

Manuali operativi del flusso di lavoro PowerShell sono manuali operativi di testo basati sul [flusso di lavoro di Windows PowerShell](automation-powershell-workflow.md). È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure. È anche possibile usare un editor di testo offline e [importare il runbook](manage-runbooks.md) in Automazione di Azure.

### <a name="advantages"></a>Vantaggi

* Implementazione di tutta la logica complessa con codice del flusso di lavoro PowerShell.
* Usare i [Checkpoint](automation-powershell-workflow.md#checkpoints) per riprendere l'operazione se si verifica un errore.
* Usare l' [elaborazione parallela](automation-powershell-workflow.md#parallel-processing) per eseguire più azioni in parallelo.
* Può includere altri manuali operativi grafici e manuali operativi di flusso di lavoro PowerShell come manuali operativi figlio per creare flussi di lavoro di alto livello.

### <a name="limitations"></a>Limitazioni

* È necessario avere familiarità con il flusso di lavoro di PowerShell.
* Manuali operativi deve gestire la complessità aggiuntiva del flusso di lavoro PowerShell, ad esempio [gli oggetti deserializzati](automation-powershell-workflow.md#code-changes).
* L'avvio di manuali operativi è più lungo rispetto a PowerShell manuali operativi poiché devono essere compilati prima dell'esecuzione.
* È possibile includere solo manuali operativi di PowerShell come manuali operativi figlio utilizzando il `Start-AzAutomationRunbook` cmdlet.
* Manuali operativi non può essere eseguito in un ruolo di lavoro ibrido per Runbook Linux.

## <a name="python-runbooks"></a>Runbook Python

I runbook Python vengono compilati in Python 2. È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure. È anche possibile usare un editor di testo offline e [importare il Runbook](manage-runbooks.md) in automazione di Azure.

### <a name="advantages"></a>Vantaggi

* Usare le librerie Python affidabili.
* Può essere eseguito in Azure o in ruoli di lavoro ibridi per Runbook Linux. I ruoli di lavoro ibridi per Runbook Windows sono supportati con [Python 2.7](https://www.python.org/downloads/release/latest/python2) installato.

### <a name="limitations"></a>Limitazioni

* È necessario avere familiarità con gli script Python.
* Attualmente è supportato solo Python 2. Tutte le funzioni specifiche di Python 3 hanno esito negativo.
* Per usare librerie di terze parti, è necessario [importare i pacchetti](python-packages.md) nell'account di automazione.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla creazione di Runbook grafici, vedere [creazione grafica in automazione di Azure](automation-graphical-authoring-intro.md).
* Per comprendere le differenze tra PowerShell e i flussi di lavoro di PowerShell per manuali operativi, vedere [Learning Windows PowerShell Workflow](automation-powershell-workflow.md).
* Per altre informazioni su come creare o importare un Runbook, vedere [gestire manuali operativi in automazione di Azure](manage-runbooks.md).
* Per altre informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
