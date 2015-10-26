<properties
   pageTitle="Migrazione da Orchestrator ad Automazione di Azure | Microsoft Azure"
   description="Descrive come eseguire la migrazione di runbook e Integration Pack da System Center Orchestrator in Automazione di Azure."
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
   ms.date="09/17/2015"
   ms.author="bwren" />


# Migrazione da Orchestrator ad Automazione di Azure

I runbook in [System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) si basano su attività di Integration Pack scritti specificatamente per Orchestrator, mentre i runbook di Automazione di Azure si basano sui flussi di lavoro di Windows PowerShell. I[Runbook grafici](automation-runbook-types#graphical-runbooks)di Automazione di Azure hanno un aspetto simile ai runbook di Orchestrator con attività che rappresentano i cmdlet di PowerShell, i runbook figlio e gli asset.

[System Center Orchestrator Migration Toolkit](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) include strumenti che facilitano la conversione dei runbook da Orchestrator ad Automazione di Azure. Oltre a convertire i runbook stessi, è necessario convertire gli Integration Pack con le relative attività usate in moduli di integrazione con cmdlet di Windows PowerShell.

Di seguito è illustrato il processo di base per la conversione di runbook di Orchestrator in Automazione di Azure. Ognuno di questi passaggi viene descritto nei dettagli nelle sezioni seguenti.

1.  Scaricare [System Center Orchestrator Migration Toolkit](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all), che contiene gli strumenti e i moduli descritti in questo articolo.
2.  Installare il [modulo delle attività standard](#standard-activities-module) in Automazione di Azure. Questo modulo include versioni convertite di attività standard di Orchestrator che possono essere usate dai runbook convertiti.
2.  Installare [System Center Orchestrator Integration Modules](#system-center-orchestrator-integration-modules) in Automazione di Azure per gli Integration Pack usati dai runbook.
3.  Convertire gli Integration Pack personalizzati e di terze parti mediante [Integration Pack Converter](#integration-pack-converter) ed eseguire l'installazione in Automazione di Azure.
4.  Ricreare manualmente gli asset globali di Orchestrator in Automazione di Azure poiché non esiste un metodo automatizzato per eseguire questa migrazione.
5.  Convertire i runbook di Orchestrator usando [Runbook Converter](#runbook-converter-coming-soon) (disponibile prossimamente) ed effettuare l'installazione in Automazione di Azure.
6.  Configurare un computer [Hybrid Runbook Worker](#hybrid-runbook-worker) nel data center locale per eseguire i runbook convertiti.

## Service Management Automation

[Service Management Automation](http://technet.microsoft.com/library/dn469260.aspx) (SMA) archivia ed esegue i runbook nel data center locale come Orchestrator e usa gli stessi moduli di integrazione di Automazione di Azure. Quando sarà disponibile, [Runbook Converter](#runbook-converter-coming-soon) convertirà i runbook di Orchestrator in runbook grafici, che non sono supportati in SMA. È comunque possibile installare il [modulo delle attività standard](#standard-activities-module) e [System Center Orchestrator Integration Modules](#system-center-orchestrator-integration-modules) in SMA, ma in tal caso è necessario [riscrivere i runbook](http://technet.microsoft.com/library/dn469262.aspx) manualmente.

## Hybrid Runbook Worker

I runbook in Orchestrator sono archiviati in un server di database e vengono eseguiti in server runbook, tutti contenuti nel data center locale. I runbook in Automazione di Azure sono archiviati nel cloud Azure e possono essere eseguiti nel data center locale tramite un computer [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Questo è il modo in cui verranno eseguiti in genere i runbook convertiti da Orchestrator poiché sono designati per l'esecuzione in server locali.

## Integration Pack Converter

Integration Pack Converter converte gli Integration Pack creati mediante Orchestrator Integration Toolkit (OIT) in moduli di integrazione basati su Windows PowerShell che possono essere importati in Automazione di Azure o in Service Management Automation.

Quando si esegue Integration Pack Converter, viene visualizzata una procedura guidata che consente di selezionare un file Integration Pack (con estensione oip). La procedura guidata elenca quindi le attività incluse nell'Integration Pack e consente di selezionare quella da sottoporre a migrazione. Al termine della procedura guidata, viene creato un modulo che include un cmdlet corrispondente per ognuna delle attività dell'Integration Pack originale.


### Parametri

Tutte le proprietà di un'attività dell'Integration Pack vengono convertite in parametri del cmdlet corrispondente nel modulo di integrazione. I cmdlet di Windows PowerShell dispongono di un set di [parametri comuni](http://technet.microsoft.com/library/hh847884.aspx) che possono essere usati con tutti i cmdlet. Con il parametro -Verbose ad esempio un cmdlet restituisce informazioni dettagliate sul proprio funzionamento. Nessun cmdlet può usare un parametro con lo stesso nome di un parametro comune. Se un'attività dispone di una proprietà con lo stesso nome del parametro comune, la procedura guidata chiederà di specificare un altro nome per il parametro.

### Attività di monitoraggio

I runbook di monitoraggio in Orchestrator iniziano con un'[attività di monitoraggio](http://technet.microsoft.com/library/hh403827.aspx) e vengono eseguiti in modo continuativo in attesa di essere richiamati da un evento specifico. Automazione di Azure non supporta i runbook di monitoraggio, pertanto le attività di monitoraggio nell'Integration Pack non verranno convertite. Nel modulo di integrazione verrà creato invece un cmdlet segnaposto per l'attività di monitoraggio. Questo cmdlet è privo di funzionalità, ma consente l'installazione dei runbook convertiti che lo usano. Questo runbook non potrà essere eseguito in Automazione di Azure, ma verrà comunque installato in modo che l'utente possa modificarlo.

### Integration Pack che non possono essere convertiti

Gli Integration Pack non creati con OIT, inclusi alcuni creati da Microsoft, non possono essere convertiti con questo strumento. Gli Integration Pack forniti da Microsoft sono stati convertiti in moduli di integrazione, in modo da poter essere installati in Automazione di Azure o in Service Management Automation.


## Modulo delle attività standard

Orchestrator contiene un insieme di [attività standard](http://technet.microsoft.com/library/hh403832.aspx) non incluse in un Integration Pack, ma usate da molti runbook. Il modulo delle attività standard è un modulo di integrazione che include un cmdlet equivalente per ognuna di queste attività. È necessario installare questo modulo di integrazione in Automazione di Azure prima di importare eventuali runbook convertiti che usano un'attività standard.

Oltre a supportare i runbook convertiti, i cmdlet del modulo delle attività standard possono essere usati da chiunque abbia familiarità con Orchestrator per creare nuovi runbook in Automazione di Azure. Benché le funzionalità di tutte le attività standard possano essere eseguite con i cmdlet, è possibile che funzionino in modo diverso. I cmdlet nel modulo delle attività standard convertite funzioneranno come le attività corrispondenti e useranno gli stessi parametri. Questo può rivelarsi utile per l'autore del runbook di Orchestrator esistente nella transizione ai runbook di Automazione di Azure.

## Moduli di integrazione di System Center Orchestrator
Microsoft mette a disposizione [Integration Pack](http://technet.microsoft.com/library/hh295851.aspx) per la creazione di runbook per l'automatizzazione di componenti di System Center e altri prodotti. Quando si scaricano alcuni di questi Integration Pack dalla [TechNet](http://www.microsoft.com/download/details.aspx?id=39622), attualmente non è possibile convertirli con Integration Pack Converter a causa di problemi noti che verranno risolti con la versione RC di System Center Orchestrator Migration Toolkit. [System Center Orchestrator Integration Modules](http://www.microsoft.com/download/details.aspx?id=47324&WT.mc_id=rss_alldownloads_all) include versioni convertite di questi Integration Pack che possono essere importate in Automazione di Azure e in Service Management Automation prima di questa versione.

## Runbook Converter (disponibile prossimamente)

Questo strumento convertirà i runbook di Orchestrator in [runbook grafici](automation-runbook-types.md#graph-runbooks) che possono essere importati in Automazione di Azure. Altri dettagli su questo strumento verranno forniti in questo sito quando saranno disponibili.

## Articoli correlati

- [System Center 2012 - Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
- [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
- [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)
- [Attività Standard di Orchestrator](http://technet.microsoft.com/library/hh403832.aspx)
 

<!---HONumber=Oct15_HO3-->