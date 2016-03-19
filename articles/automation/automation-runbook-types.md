<properties 
   pageTitle="Tipi di runbook di Automazione di Azure"
   description="Descrive i diversi tipi di runbook che è possibile usare in Automazione di Azure e fornisce considerazioni di cui tenere conto per determinare il tipo da usare."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />

# Tipi di runbook di Automazione di Azure

Automazione di Azure supporta tre tipi di runbook, descritti brevemente nella tabella seguente. Le sezioni seguenti forniscono altre informazioni su ogni tipo con alcune considerazioni sui casi in cui usarli.


| Tipo | Descrizione |
|:---|:---|
| [Grafico](#graphical-runbooks) | Basato su flusso di lavoro Windows PowerShell e creato e modificato completamente nell'editor grafico nel portale di Azure. | 
| [Flusso di lavoro PowerShell](#powershell-workflow-runbooks) | Runbook di testo basato su flusso di lavoro Windows PowerShell. |
| [PowerShell](#powershell-runbooks) | Runbook di testo basato su script di Windows PowerShell. |

## Runbook grafici

I [runbook grafici](automation-runbook-types.md#graphical-runbooks) vengono creati e modificati con l'editor grafico nel portale di Azure. È possibile esportarli in un file e quindi importarli in un altro account di automazione, ma non è possibile crearli o modificarli con un altro strumento. I runbook grafici generano codice di flusso di lavoro PowerShell, ma non è possibile visualizzare o modificare direttamente il codice. I runbook grafici non possono essere convertiti in uno dei [formati di testo](automation-runbook-types.md), né un runbook di testo può essere convertito in formato grafico.

### Vantaggi

- Creazione di runbook con una conoscenza minima del [flusso di lavoro PowerShell](automation-powershell-workflow.md).
- Rappresentazione grafica dei processi di gestione.
- Uso dei [checkpoint](automation-powershell-workflow.md#checkpoints) per riprendere il runbook in caso di errore.
- Uso dell'[elaborazione parallela](automation-powershell-workflow.md#parallel-processing) per eseguire più attività in parallelo.
- Può includere altri runbook grafici e runbook di flusso di lavoro PowerShell come runbook figli per la creazione di flussi di lavoro generali.


### Limitazioni

- Impossibilità di modificare il runbook all'esterno del portale di Azure.
- Possibile necessità di un [controllo script del flusso di lavoro](automation-powershell-workflow.md#activities) contenente codice del flusso di lavoro PowerShell per l'esecuzione di logica complessa.
- Impossibile visualizzare o modificare direttamente il codice del flusso di lavoro PowerShell creato dal flusso di lavoro con interfaccia grafica. Si noti che è possibile visualizzare il codice in qualsiasi attività di Script del flusso di lavoro.
- Tempi di avvio maggiori rispetto ai runbook di PowerShell perché è necessaria la compilazione prima dell'esecuzione.
- Possibilità di includere i runbook di PowerShell come runbook figlio solo mediante il cmdlet Start-AzureAutomationRunbook che crea un nuovo processo.


## Runbook del flusso di lavoro PowerShell

I runbook del flusso di lavoro PowerShell sono runbook di testo basati sul [flusso di lavoro Windows PowerShell](automation-powershell-workflow.md). È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure. È anche possibile usare un editor di testo offline e [importare il runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) in Automazione di Azure.

### Vantaggi

- Implementazione di tutta la logica complessa con codice del flusso di lavoro PowerShell.
- Uso dei [checkpoint](automation-powershell-workflow.md#checkpoints) per riprendere il runbook in caso di errore.
- Uso dell'[elaborazione parallela](automation-powershell-workflow.md#parallel-processing) per eseguire più operazioni in parallelo.
- Può includere altri runbook grafici e runbook di flusso di lavoro PowerShell come runbook figli per la creazione di flussi di lavoro generali.


### Limitazioni

- Necessità che l'autore abbia familiarità con il flusso di lavoro PowerShell.
- Gestione nel runbook della complessità aggiuntiva del flusso di lavoro PowerShell, ad esempio gli [oggetti deserializzati](automation-powershell-workflow.md#code-changes).
- Tempi di avvio maggiori rispetto ai runbook di PowerShell perché è necessaria la compilazione prima dell'esecuzione.
- Possibilità di includere i runbook di PowerShell come runbook figlio solo mediante il cmdlet Start-AzureAutomationRunbook che crea un nuovo processo.


## Runbook di PowerShell

I runbook di PowerShell sono basati su Windows PowerShell. È possibile modificare direttamente il codice del runbook usando l'editor di testo del portale di Azure. È anche possibile usare un editor di testo offline e [importare il runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) in Automazione di Azure.

### Vantaggi

- Implementazione di tutta la logica complessa con codice di PowerShell senza le complessità aggiuntive del flusso di lavoro PowerShell. 
- Avvio del runbook più rapido rispetto ai runbook grafici o del flusso di lavoro PowerShell poiché non è necessaria la compilazione prima dell'esecuzione.

### Limitazioni

- Necessità di familiarità con la scrittura di script di PowerShell.
- Impossibilità di usare l'[elaborazione parallela](automation-powershell-workflow.md#parallel-processing) per eseguire più operazioni in parallelo.
- Impossibilità di usare i [checkpoint](automation-powershell-workflow.md#checkpoints) per riprendere il runbook in caso di errore.
- Possibilità di includere i runbook del flusso di lavoro PowerShell e grafici come runbook figlio solo mediante il cmdlet Start-AzureAutomationRunbook che crea un nuovo processo.

### Problemi noti
Di seguito sono descritti i problemi noti correnti relativi ai runbook di PowerShell.

- I runbook di PowerShell non sono in grado di recuperare un [asset di tipo variabile](automation-variables.md) non crittografato con valore Null.
- I runbook di PowerShell non sono in grado di recuperare un [asset di tipo variabile](automation-variables.md) con *~* nel nome.
- Get-Process in un ciclo in un runbook di PowerShell può arrestarsi in modo anomalo dopo circa 80 iterazioni. 
- Un runbook di PowerShell può avere esito negativo se tenta di scrivere una quantità elevata di dati nel flusso di output in una sola volta. È possibile risolvere questo problema in genere restituendo solo le informazioni necessarie quando si usano oggetti di grandi dimensioni. Anziché restituire ad esempio *Get-Process*, è possibile restituire solo i campi con *Get-Process | Select ProcessName, CPU*.

## Considerazioni

È consigliabile tenere conto delle considerazioni aggiuntive seguenti per determinare quale tipo usare per un runbook specifico.

- Non è possibile convertire i runbook da un tipo a all'altro.
- Esistono limitazioni di uso dei runbook di tipi diversi come runbook figlio. Per altre informazioni, vedere [Runbook figlio in Automazione di Azure](automation-child-runbooks.md).



  
## Articoli correlati

- [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md)
- [Informazioni sul flusso di lavoro di Windows PowerShell](automation-powershell-workflow.md)
- [Creazione o importazione di un runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx)

<!---HONumber=AcomDC_0211_2016-->