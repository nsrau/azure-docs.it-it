---
title: Panoramica di Monitoraggio di Azure | Documentazione Microsoft
description: "Monitoraggio di Azure raccoglie dati statistici da usare in avvisi, webhook, scalabilità automatica e automazione. L&quot;articolo elenca anche altre opzioni di monitoraggio di Microsoft."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 09b2847586eec50d8512ec5eabdc05a11d8b238a
ms.openlocfilehash: da4dc84501ca6b0c5a2d23e885196167be9c61ad


---

# <a name="overview-of-azure-monitor"></a>Panoramica di Monitoraggio di Azure
Questo articolo fornisce una panoramica concettuale del monitoraggio delle risorse di Azure con collegamenti a informazioni su tipi di risorse specifici.  Per informazioni generali sul monitoraggio dell'applicazione da un punto di vista diverso da Azure, vedere [Indicazioni di monitoraggio e diagnostica](../best-practices-monitoring.md).

Una procedura dettagliata video di Monitoraggio di Azure è disponibile alla pagina  
[Explore Microsoft Azure monitoring and diagnostics](https://channel9.msdn.com/Blogs/Azure-Monitoring/Get-Started-with-Azure-Monitor) (Esplorare le funzionalità Diagnostica e Monitoraggio di Microsoft Azure). Un altro video che illustra uno scenario in cui è possibile usare Monitoraggio di Azure è disponibile alla pagina [Explore Microsoft Azure monitoring and diagnostics](https://channel9.msdn.com/events/Ignite/2016/BRK2234) (Esplorare le funzionalità Diagnostica e Monitoraggio di Microsoft Azure).  

Le applicazioni cloud sono complesse e hanno molte parti mobili. Il monitoraggio offre la possibilità di garantire il funzionamento e l'integrità dell'applicazione. Consente anche di prevenire i problemi potenziali o di risolvere quelli precedenti. Inoltre, è possibile usare i dati di monitoraggio per ottenere informazioni approfondite sull'applicazione, utili per migliorarne le prestazioni o la manutenibilità oppure per automatizzare azioni che altrimenti richiederebbero un intervento manuale.

Il diagramma seguente mostra una visualizzazione concettuale del monitoraggio di Azure, incluso il tipo di log che è possibile raccogliere e cosa fare con i dati.   

![Modello logico per il monitoraggio e la diagnostica per risorse non di calcolo](./media/monitoring-overview/MonitoringAzureResources-non-compute_v3.png)

Figura 1: Modello logico per il monitoraggio e la diagnostica per le risorse non di calcolo

<br/>

![Modello logico per il monitoraggio e la diagnostica per risorse di calcolo](./media/monitoring-overview/MonitoringAzureResources-compute_v3.png)

Figura 2: Modello concettuale per il monitoraggio e la diagnostica per le risorse di calcolo

## <a name="monitoring-sources"></a>Monitoraggio delle origini
### <a name="activity-logs"></a>Log attività
Nei log attività, in precedenza chiamati log operativi o di controllo, è possibile cercare informazioni relative alla risorsa così come visualizzate dall'infrastruttura di Azure. I log contengono informazioni quali gli orari di creazione ed eliminazione delle risorse.  

### <a name="host-vm"></a>Macchina virtuale host
**Solo calcolo**

Alcune risorse di calcolo, come Servizi cloud, Macchine virtuali e Service Fabric, hanno una macchina virtuale host dedicata con cui interagiscono. La macchina virtuale host è l'equivalente della macchina virtuale radice del modello di hypervisor Hyper-V. In questo caso, è possibile raccogliere metriche sulla macchina virtuale host oltre che sul sistema operativo guest.  

Per altri servizi di Azure non esiste necessariamente una corrispondenza 1:1 tra la risorsa e una determinata macchina virtuale host e di conseguenza le metriche della macchina virtuale host non sono disponibili.

### <a name="resource---metrics-and-diagnostics-logs"></a>Risorsa: log di diagnostica e metriche
Le metriche che è possibile raccogliere variano in base al tipo di risorsa. Ad esempio, Macchine virtuali fornisce statistiche sull'I/O del disco e sulla percentuale CPU. Queste statistiche non esistono invece per una coda del bus di servizio, che invece fornisce metriche come le dimensioni della coda e la velocità effettiva dei messaggi.

Per le risorse di calcolo è possibile ottenere metriche relative al sistema operativo guest e moduli di diagnostica come Diagnostica di Azure. Diagnostica di Azure consente di raccogliere e instradare i dati di diagnostica raccolti in altre posizioni, ad esempio Archiviazione di Azure.

In [Metriche supportate](monitoring-supported-metrics.md) è disponibile un elenco delle metriche che è attualmente possibile raccogliere.

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Applicazione: log di diagnostica, log applicazioni e metriche
**Solo calcolo**

Nel modello di calcolo le applicazioni possono essere eseguite nel sistema operativo guest. Le applicazioni creano il proprio set di log e metriche.

Tra i tipi di metriche:

* Contatori delle prestazioni
* Log applicazioni
* Registri eventi di Windows
* Origine dell'evento .NET
* Log di IIS
* ETW basato su manifesto
* Dump di arresto anomalo del sistema
* Log degli errori dei clienti

## <a name="uses-for-monitoring-data"></a>Uso dei dati di monitoraggio
### <a name="visualize"></a>Visualizzazione
La visualizzazione in grafici dei dati di monitoraggio permette di identificare le tendenze molto più rapidamente della ricerca sui dati.  

Ecco alcuni metodi di visualizzazione:

* Usare il portale di Azure
* Instradare i dati ad Application Insights in Azure
* Instradare i dati a Microsoft Power BI
* Instradare i dati a uno strumento di visualizzazione di terze parti usando lo streaming live o facendo in modo che lo strumento esegua la lettura da un archivio in Archiviazione di Azure

### <a name="archive"></a>Archiviazione
In genere i dati di monitoraggio vengono scritti in Archiviazione di Azure e conservati fino a quando non vengono eliminati.

Ecco alcuni modi in cui usare questi dati:

* Fare in modo che i dati scritti vengano letti ed elaborati da altri strumenti all'interno o all'esterno di Azure.
* Scaricare i dati in locale per l'uso in un archivio locale o modificare i criteri di conservazione nel cloud per conservare i dati per lunghi periodi di tempo.  
* <a name="you-leave-the-data-in-azure-storage-indefinitely-though-you-have-to-pay-for-azure-storage-based-on-the-amount-of-data-you-keep"></a>Lasciare i dati in Archiviazione di Azure a tempo indeterminato, anche se il costo di Archiviazione di Azure dipende dalla quantità di dati conservati.
  -

### <a name="query"></a>Query
Per accedere ai dati nel sistema o in Archiviazione di Azure, è possibile usare l'API REST di Monitoraggio di Azure, l'interfaccia della riga di comando multipiattaforma, i cmdlet di PowerShell o .NET SDK.

Ecco alcuni esempi: 

* Recuperare i dati per un'applicazione di monitoraggio personalizzata che si è scritta.
* Creare query personalizzate e inviare i dati a un'applicazione di terze parti.

### <a name="route"></a>Route
È possibile trasmettere i dati di monitoraggio ad altre posizioni in tempo reale.

Tra gli esempi sono inclusi:

* Invio ad Application Insights per usare gli strumenti di visualizzazione al suo interno.
* Invio a Hub eventi per il routing a strumenti di terze parti per eseguire l'analisi in tempo reale.

### <a name="automate"></a>Automazione
È possibile usare i dati di monitoraggio per attivare eventi o interi processi. Ecco alcuni esempi:

* Usare i dati per la scalabilità automatica delle istanze di calcolo in base al carico dell'applicazione.
* Inviare messaggi di posta elettronica quando una metrica supera una soglia predeterminata.
* Chiamare un URL Web (webhook) per eseguire un'azione in un sistema all'esterno di Azure.
* Avviare un runbook in Automazione di Azure per eseguire varie attività.

## <a name="methods-of-use"></a>Metodi d'uso
In generale, è possibile modificare il rilevamento, il routing e il recupero dei dati usando uno dei metodi seguenti. Non tutti i metodi sono disponibili per tutte le azioni o tutti i tipi di dati.

* [Portale di Azure](https://portal.azure.com)
* [PowerShell](insights-powershell-samples.md)  
* [Interfaccia della riga di comando multipiattaforma](insights-cli-samples.md)
* [API REST](https://msdn.microsoft.com/library/dn931943.aspx)
* [.NET SDK](https://msdn.microsoft.com/library/dn802153.aspx)

## <a name="azures-monitoring-offerings"></a>Offerte di monitoraggio di Azure
Le offerte di Azure consentono di monitorare i servizi, dall'infrastruttura bare metal ai dati di telemetria dell'applicazione. La migliore strategia di monitoraggio riunisce tutte queste opzioni per fornire informazioni complete e dettagliate sull'integrità dei servizi.

* [Monitoraggio di Azure](http://aka.ms/azmondocs): offre funzionalità di visualizzazione, query, routing, avviso, ridimensionamento automatico e automazione dei dati sia dall'infrastruttura di Azure (log attività) che da ogni singola risorsa di Azure (log di diagnostica). Questo articolo fa parte della documentazione di Monitoraggio di Azure. Il nome Monitoraggio di Azure è stato rilasciato il 25 settembre nel corso dell'evento Ignite 2016.  In precedenza il nome era "Azure Insights".  
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) : consente il rilevamento avanzato e la diagnostica per i problemi a livello dell'applicazione del servizio, ben integrati nei dati provenienti dal monitoraggio di Azure. Si tratta della piattaforma di diagnostica predefinita per app Web del servizio app,  a cui è possibile indirizzare i dati da altri servizi.  
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), parte di [Operations Management Suite](https://www.microsoft.com/oms/): offre una soluzione di gestione IT olistica per l'infrastruttura sia locale che e basata sul cloud di terze parti (ad esempio Amazon Web Services), oltre alle risorse di Azure.  I dati di Monitoraggio di Azure possono essere indirizzati direttamente a Log Analytics, in modo da visualizzare le metriche e i log dell'intero ambiente in un'unica posizione.     

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su:

* [Monitoraggio di Azure in un video tratto dall'evento Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Introduzione a Monitoraggio di Azure](monitoring-get-started.md)
* [Diagnostica di Azure](../azure-diagnostics.md) , utile per la diagnosi di problemi nelle applicazioni di Servizi cloud, Macchine virtuali e Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) , utili per la diagnosi di problemi nell'app Web del servizio app.
* [Risoluzione dei problemi di Archiviazione di Azure](../storage/storage-e2e-troubleshooting.md) , utile per l'uso di BLOB, tabelle o code di archiviazione.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) e [Operations Management Suite](https://www.microsoft.com/oms/)



<!--HONumber=Feb17_HO2-->


