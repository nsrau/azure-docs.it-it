---
title: Tipi di runbook di Automazione di Azure | Documentazione Microsoft
description: "Descrive i diversi tipi di runbook che è possibile usare in Automazione di Azure e fornisce considerazioni di cui tenere conto per determinare il tipo da usare. "
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 9265c975-4281-4819-a84f-d86641277f36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/01/2017
ms.author: bwren
ms.openlocfilehash: e4a8ab0e68d6614fea1b44f0115a1c633f145277
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="azure-automation-runbook-types"></a>Tipi di runbook di Automazione di Azure
Automazione di Azure supporta diversi tipi di runbook, descritti brevemente nella tabella seguente.  Le sezioni seguenti forniscono altre informazioni su ogni tipo con alcune considerazioni sui casi in cui usarli.

| type | DESCRIZIONE |
|:--- |:--- |
| [Grafico](#graphical-runbooks) |Basato su Windows PowerShell e creato e modificato completamente nell'editor grafico nel portale di Azure. |
| [Grafico del flusso di lavoro di PowerShell](#graphical-runbooks) |Basato su flusso di lavoro Windows PowerShell e creato e modificato completamente nell'editor grafico nel portale di Azure. |
| [PowerShell](#powershell-runbooks) |Runbook di testo basato su script di Windows PowerShell. |
| [Flusso di lavoro PowerShell](#powershell-workflow-runbooks) |Runbook di testo basato su flusso di lavoro Windows PowerShell. |
| [Python](#python-runbooks) |Runbook di testo basato su Python. |

## <a name="graphical-runbooks"></a>Runbook grafici
[Grafico](automation-runbook-types.md#graphical-runbooks) and Grafico PowerShell Workflow runbooks are created and edited with the graphical editor in the Azure portal.  È possibile esportarli in un file e quindi importarli in un altro account di automazione, ma non è possibile crearli o modificarli con un altro strumento.  I runbook grafici generano codice di PowerShell, ma non è possibile visualizzare o modificare direttamente il codice. I runbook grafici non possono essere convertiti in uno dei [formati di testo](automation-runbook-types.md), né un runbook di testo può essere convertito in formato grafico. I runbook grafici possono essere convertiti in runbook grafici del flusso di lavoro di PowerShell durante l'importazione e viceversa.

### <a name="advantages"></a>Vantaggi
* Modello di creazione con inserimento, collegamento e configurazione visivi  
* Possibilità di analizzare il flusso dei dati attraverso il processo  
* Rappresentazione visiva dei processi di gestione  
* Inclusione di altri runbook come runbook figlio per creare flussi di lavoro di livello elevato  
* Agevolazione della programmazione modulare  


### <a name="limitations"></a>Limitazioni
* Impossibilità di modificare il runbook all'esterno del portale di Azure.
* Potrebbe richiedere un'attività di codice contenente il codice di PowerShell per eseguire una logica complessa.
* Impossibilità di visualizzare o modificare direttamente il codice di PowerShell creato dal flusso di lavoro grafico. Si noti che è possibile visualizzare il codice creato in qualsiasi attività di codice.

## <a name="powershell-runbooks"></a>Runbook di PowerShell
I runbook di PowerShell sono basati su Windows PowerShell.  È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure.  È anche possibile usare un editor di testo offline e [importare il runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) in Automazione di Azure.

### <a name="advantages"></a>Vantaggi
* Implementazione di tutta la logica complessa con codice di PowerShell senza le complessità aggiuntive del flusso di lavoro PowerShell. 
* Avvio del runbook più rapido rispetto ai runbook del flusso di lavoro PowerShell poiché non è necessaria la compilazione prima dell'esecuzione.

### <a name="limitations"></a>Limitazioni
* Necessità di familiarità con la scrittura di script di PowerShell.
* Impossibilità di usare l' [elaborazione parallela](automation-powershell-workflow.md#parallel-processing) per eseguire più operazioni in parallelo.
* Impossibilità di usare i [checkpoint](automation-powershell-workflow.md#checkpoints) per riprendere il runbook in caso di errore.
* Possibilità di includere i runbook del flusso di lavoro PowerShell e grafici  come runbook figlio solo mediante il cmdlet Start-AzureAutomationRunbook che crea un nuovo processo.

### <a name="known-issues"></a>Problemi noti
Di seguito sono descritti i problemi noti correnti relativi ai runbook di PowerShell.

* I runbook di PowerShell non sono in grado di recuperare un [asset di tipo variabile](automation-variables.md) non crittografato con valore Null.
* I runbook di PowerShell non sono in grado di recuperare un [asset di tipo variabile](automation-variables.md) con *~* nel nome.
* Get-Process in un ciclo in un runbook di PowerShell può arrestarsi in modo anomalo dopo circa 80 iterazioni. 
* Un runbook di PowerShell può avere esito negativo se tenta di scrivere una quantità elevata di dati nel flusso di output in una sola volta.   È possibile risolvere questo problema in genere restituendo solo le informazioni necessarie quando si usano oggetti di grandi dimensioni.  Anziché restituire *Get-Process*, ad esempio, è possibile restituire solo i campi obbligatori con *Get-Process | Select ProcessName, CPU*.

## <a name="powershell-workflow-runbooks"></a>Runbook del flusso di lavoro PowerShell
I runbook del flusso di lavoro PowerShell sono runbook di testo basati sul [flusso di lavoro Windows PowerShell](automation-powershell-workflow.md).  È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure.  È anche possibile usare un editor di testo offline e [importare il runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) in Automazione di Azure.

### <a name="advantages"></a>Vantaggi
* Implementazione di tutta la logica complessa con codice del flusso di lavoro PowerShell.
* Uso dei [checkpoint](automation-powershell-workflow.md#checkpoints) per riprendere il runbook in caso di errore.
* Uso dell' [elaborazione parallela](automation-powershell-workflow.md#parallel-processing) per eseguire più operazioni in parallelo.
* Può includere altri runbook grafici e runbook di flusso di lavoro di PowerShell come runbook figli per creare flussi di lavoro di livello elevato.

### <a name="limitations"></a>Limitazioni
* Necessità che l'autore abbia familiarità con il flusso di lavoro PowerShell.
* Gestione nel runbook della complessità aggiuntiva del flusso di lavoro PowerShell, ad esempio gli [oggetti deserializzati](automation-powershell-workflow.md#code-changes).
* Tempi di avvio maggiori rispetto ai runbook di PowerShell perché è necessaria la compilazione prima dell'esecuzione.
* Possibilità di includere i runbook di PowerShell come runbook figlio solo mediante il cmdlet Start-AzureAutomationRunbook che crea un nuovo processo.

## <a name="python-runbooks"></a>Runbook Python
I runbook Python vengono compilati in Python 2.  È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure oppure usare un qualsiasi editor di testo offline e [importare il runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) in Automazione di Azure.

### <a name="advantages"></a>Vantaggi
* Uso dell'affidabile libreria standard di Python.

### <a name="limitations"></a>Limitazioni
* Conoscenza delle nozioni sulla scrittura di script di Python.
* Al momento è supportato solo Python 2, di conseguenza le funzioni specifiche di Python 3 non verranno eseguite.

### <a name="known-issues"></a>Problemi noti
Di seguito sono descritti i problemi noti correnti relativi ai runbook Python.

* Per potere usare librerie di terze parti, il runbook deve essere eseguito in un [ruolo di lavoro ibrido per runbook Windows](https://docs.microsoft.com/azure/automation/automation-windows-hrw-install) o in un [ruolo di lavoro ibrido per runbook Linux](https://docs.microsoft.com/azure/automation/automation-linux-hrw-install) con le librerie già installate nel computer prima dell'avvio del runbook.

## <a name="considerations"></a>Considerazioni
È consigliabile tenere conto delle considerazioni aggiuntive seguenti per determinare quale tipo usare per un runbook specifico.

* Non è possibile convertire i runbook dal tipo grafico al tipo testuale e viceversa.
* Esistono limitazioni di uso dei runbook di tipi diversi come runbook figlio.  Per altre informazioni, vedere [Runbook figlio in Automazione di Azure](automation-child-runbooks.md) .

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla creazione grafica di runbook, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md)
* Per comprendere le differenze tra PowerShell e i flussi di lavoro di PowerShell per i runbook, vedere [Informazioni sul flusso di lavoro di Windows PowerShell](automation-powershell-workflow.md)
* Per ulteriori informazioni su come creare o importare un runbook, vedere [Creazione o importazione di un runbook](automation-creating-importing-runbook.md)

