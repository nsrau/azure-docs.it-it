---
title: Tipi di runbook di Automazione di Azure
description: Descrive i diversi tipi di runbook che è possibile usare in Automazione di Azure e fornisce considerazioni di cui tenere conto per determinare il tipo da usare.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 10f9c829207dc17fa39711e273ae4fbfab3ecbcd
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010172"
---
# <a name="azure-automation-runbook-types"></a>Tipi di runbook di Automazione di Azure

Il servizio di automazione dei processi di Automazione di Azure supporta diversi tipi di runbook. I tipi sono brevemente definiti nella tabella seguente e descritti in modo più dettagliato nelle sezioni seguenti. Per altre informazioni sull'ambiente di automazione dei processi, vedere Esecuzione di Runbook in Automazione di Azure.To learn about the process automation environment, see [Runbook execution in Azure Automation.](automation-runbook-execution.md)

| Type | Descrizione |
|:--- |:--- |
| [Grafico](#graphical-runbooks)|Runbook grafico basato su Windows PowerShell e creato e modificato completamente nell'editor grafico nel portale di Azure.Graphical runbook based on Windows PowerShell and created and edited completely in the graphical editor in Azure portal. |
| [Grafico del flusso di lavoro di PowerShell](#graphical-runbooks)|Runbook grafico basato sul flusso di lavoro di Windows PowerShell e creato e modificato completamente nell'editor grafico nel portale di Azure.Graphical runbook based on Windows PowerShell Workflow and created and edited completely in the graphical editor in Azure portal. |
| [PowerShell](#powershell-runbooks) |Runbook di testo basato su script di Windows PowerShell. |
| [Flusso di lavoro PowerShell](#powershell-workflow-runbooks)|Runbook di testo basato sullo scripting del flusso di lavoro di Windows PowerShell.Text runbook based on Windows PowerShell Workflow scripting. |
| [Python](#python-runbooks) |Runbook di testo basato su script Python. |

## <a name="graphical-runbooks"></a>Runbook grafici

È possibile creare e modificare i runbook grafici e grafici del flusso di lavoro di PowerShell usando l'editor grafico nel portale di Azure.You can create and edit graphical and graphical PowerShell Workflow runbooks using the graphical editor in the Azure portal. Tuttavia, non è possibile creare o modificare questo tipo di runbook con un altro strumento.

Un runbook grafico ha le seguenti caratteristiche principali:

* Può essere esportato in un file nell'account Automation e quindi importato in un altro account Di automazione. 
* Genera codice PowerShell. 
* Può essere convertito in o da un runbook grafico del flusso di lavoro di PowerShell durante l'importazione. 

### <a name="advantages"></a>Vantaggi

* Utilizza il modello di creazione visivo di creazione di inserimenti e collegamenti.
* Si concentra sul modo in cui i dati passano attraverso il processo.
* Rappresenta visivamente i processi di gestione.
* Include altri runbook come runbook figlio per creare flussi di lavoro di alto livello.
* Incoraggia la programmazione modulare.

### <a name="limitations"></a>Limitazioni

* Non è possibile creare o modificare all'esterno del portale di Azure.Can't be created or edited outside of the Azure portal.
* Potrebbe essere necessaria un'attività di codice contenente codice PowerShell per eseguire logica complessa.
* Non può essere convertito in uno dei [formati di testo,](automation-runbook-types.md)né un runbook di testo può essere convertito in formato grafico. 
* Non consente di visualizzare o modificare direttamente il codice di PowerShell creato dal flusso di lavoro grafico. È possibile visualizzare il codice creato in qualsiasi attività di codice.
* Non viene eseguito in un runbook worker ibrido Linux. Vedere [Automatizzare le risorse nel data center o](automation-hybrid-runbook-worker.md)nel cloud utilizzando Hybrid Runbook Worker .

## <a name="powershell-runbooks"></a>Runbook di PowerShell

I runbook di PowerShell sono basati su Windows PowerShell. È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure.  È anche possibile usare un editor di testo offline e [importare il runbook](manage-runbooks.md) in Automazione di Azure.

### <a name="advantages"></a>Vantaggi

* Implementazione di tutta la logica complessa con codice di PowerShell senza le complessità aggiuntive del flusso di lavoro PowerShell.
* Avvio del runbook più rapido rispetto ai runbook del flusso di lavoro PowerShell poiché non è necessaria la compilazione prima dell'esecuzione.
* Può essere eseguito in Azure o in ruoli worker Linux e Windows Hybrid Runbook

### <a name="limitations"></a>Limitazioni

* Necessità di familiarità con la scrittura di script di PowerShell.
* Impossibilità di usare l'[elaborazione parallela](automation-powershell-workflow.md#parallel-processing) per eseguire più azioni in parallelo.
* Impossibilità di usare i [checkpoint](automation-powershell-workflow.md#checkpoints) per riprendere il runbook in caso di errore.
* Possibilità di includere i runbook del flusso di lavoro PowerShell e grafici come runbook figlio solo mediante il cmdlet Start-AzureAutomationRunbook che crea un nuovo processo.

### <a name="known-issues"></a>Problemi noti

Di seguito sono descritti i problemi noti correnti relativi ai runbook di PowerShell.

* I runbook di PowerShell non possono recuperare un [asset variabile](automation-variables.md) non crittografato con un valore null.
* I runbook di PowerShell non sono in grado di recuperare un [asset di tipo variabile](automation-variables.md) con *~* nel nome.
* Get-Process in un ciclo in un runbook di PowerShell può arrestarsi in modo anomalo dopo circa 80 iterazioni.
* Un runbook di PowerShell può avere esito negativo se tenta di scrivere una quantità elevata di dati nel flusso di output in una sola volta.   È possibile risolvere questo problema in genere restituendo solo le informazioni necessarie quando si usano oggetti di grandi dimensioni.  Anziché restituire *Get-Process*, ad esempio, è possibile restituire solo i campi obbligatori con *Get-Process | Select ProcessName, CPU*.

## <a name="powershell-workflow-runbooks"></a>Runbook del flusso di lavoro PowerShell

I runbook del flusso di lavoro PowerShell sono runbook di testo basati sul [flusso di lavoro Windows PowerShell](automation-powershell-workflow.md).  È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure.  È anche possibile usare un editor di testo offline e [importare il runbook](manage-runbooks.md) in Automazione di Azure.

### <a name="advantages"></a>Vantaggi

* Implementazione di tutta la logica complessa con codice del flusso di lavoro PowerShell.
* Uso dei [checkpoint](automation-powershell-workflow.md#checkpoints) per riprendere il runbook in caso di errore.
* Uso dell' [elaborazione parallela](automation-powershell-workflow.md#parallel-processing) per eseguire più operazioni in parallelo.
* Può includere altri runbook grafici e runbook di flusso di lavoro di PowerShell come runbook figli per creare flussi di lavoro di livello elevato.

### <a name="limitations"></a>Limitazioni

* Necessità che l'autore abbia familiarità con il flusso di lavoro PowerShell.
* Gestione nel runbook della complessità aggiuntiva del flusso di lavoro PowerShell, ad esempio gli [oggetti deserializzati](automation-powershell-workflow.md#code-changes).
* Tempi di avvio maggiori rispetto ai runbook di PowerShell perché è necessaria la compilazione prima dell'esecuzione.
* Possibilità di includere i runbook di PowerShell come runbook figlio solo mediante il cmdlet Start-AzureAutomationRunbook, che crea un nuovo processo.
* Impossibile eseguire in un runbook ibrido Linux.

## <a name="python-runbooks"></a>Runbook Python

I runbook Python vengono compilati in Python 2. È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure o con un qualsiasi editor di testo offline e [importare il runbook](manage-runbooks.md) in Automazione di Azure.

### <a name="advantages"></a>Vantaggi

* Usare le affidabili librerie di Python.
* Può essere eseguito in Azure o in entrambi i ruoli di lavoro Linux Hybrid Runbook.Can run in Azure or on both Linux Hybrid Runbook Workers. Windows Hybrid Runbook Workers sono supportati con [python2.7](https://www.python.org/downloads/release/latest/python2) installato.

### <a name="limitations"></a>Limitazioni

* Conoscenza delle nozioni sulla scrittura di script di Python.
* Al momento è supportato solo Python 2, di conseguenza le funzioni specifiche di Python 3 non verranno eseguite.
* Per utilizzare librerie di terze parti, è necessario [importare il pacchetto](python-packages.md) nell'account di automazione per poterlo utilizzare.

## <a name="considerations"></a>Considerazioni

Tenere conto delle considerazioni aggiuntive seguenti per determinare quale tipo usare per un runbook specifico.

* Non è possibile convertire i runbook dal tipo grafico al tipo testuale e viceversa.
* Esistono limitazioni di uso dei runbook di tipi diversi come runbook figlio. Per altre informazioni, vedere [Runbook figlio in Automazione di Azure](automation-child-runbooks.md).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla creazione grafica di runbook, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md)
* Per comprendere le differenze tra PowerShell e i flussi di lavoro di PowerShell per i runbook, vedere [Informazioni sul flusso di lavoro di Windows PowerShell](automation-powershell-workflow.md)
* Per ulteriori informazioni su come creare o importare un runbook, vedere [Creazione o importazione di un runbook](manage-runbooks.md)
* Per altre informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, vedere Documenti di [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
