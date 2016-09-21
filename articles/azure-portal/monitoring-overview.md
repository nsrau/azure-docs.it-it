<properties
	pageTitle="Panoramica sul monitoraggio in Microsoft Azure | Microsoft Azure"
	description="Panoramica generale del monitoraggio e della diagnostica in Microsoft Azure, inclusi avvisi, webhook, ridimensionamento automatico e altro."
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="robb"/>

# Panoramica sul monitoraggio in Microsoft Azure

Questo articolo fornisce una panoramica concettuale delle monitoraggio delle risorse di Azure e collegamenti ad altre informazioni su tipi di risorse specifici. Per informazioni generali sul monitoraggio dell'applicazione da un punto di vista diverso da Azure, vedere [Indicazioni di monitoraggio e diagnostica](../best-practices-monitoring.md).

Le applicazioni cloud sono complesse e hanno molte parti mobili. Il monitoraggio offre la possibilità di garantire il funzionamento e l'integrità dell'applicazione. Consente anche di prevenire i problemi potenziali o di risolvere quelli precedenti. Inoltre, è possibile usare i dati di monitoraggio per ottenere informazioni approfondite sull'applicazione, che possono essere utili per migliorarne le prestazioni o la manutenibilità oppure per automatizzare alcune azioni che altrimenti richiederebbero un intervento manuale.

Il diagramma seguente mostra una visualizzazione concettuale del monitoraggio di Azure, incluso il tipo di log che è possibile raccogliere e cosa fare con i dati.

![Modello logico per il monitoraggio e la diagnostica per risorse non di calcolo](./media/monitoring-overview/monitoring-azure-resources-non-compute.png)

Figura 1: Modello logico per il monitoraggio e la diagnostica per le risorse non di calcolo

<br/>

![Modello logico per il monitoraggio e la diagnostica per risorse di calcolo](./media/monitoring-overview/monitoring-azure-resources-compute.png)

Figura 2: Modello concettuale per il monitoraggio e la diagnostica per le risorse di calcolo


## Monitoraggio delle origini
### Log di attività dell'infrastruttura
Nei log di controllo, detti anche log attività o log operativi, è possibile cercare informazioni relativi alla risorsa così come sono visualizzati dall'infrastruttura di Azure. I log contengono informazioni quali gli orari di creazione ed eliminazione delle risorse.

### Macchina virtuale host
**Solo calcolo**

Attualmente non è possibile raccogliere metriche sulla macchina virtuale host, ma qui viene elencata per completezza.

Alcune risorse di calcolo, come Servizi cloud, Macchine virtuali e Service Fabric, hanno una macchina virtuale host dedicata con cui interagiscono. La macchina virtuale host è l'equivalente della macchina virtuale radice del modello di hypervisor Hyper-V.

Per altri servizi di Azure non c'è necessariamente una corrispondenza 1:1 tra la risorsa e una determinata macchina virtuale host.


### Risorsa: log di diagnostica e metriche
Le metriche che è possibile raccogliere variano in base al tipo di risorsa. Ad esempio, Macchine virtuali fornisce statistiche sull'I/O del disco e sulla percentuale CPU. Queste statistiche non esistono invece per una coda del bus di servizio, che invece fornisce metriche come le dimensioni della coda e la velocità effettiva dei messaggi.

Per le risorse di calcolo è possibile ottenere metriche relative al sistema operativo guest e moduli di diagnostica come Diagnostica di Azure. Diagnostica di Azure consente di raccogliere e instradare i dati di diagnostica raccolti in altre posizioni, ad esempio Archiviazione di Azure.

### Applicazione: log di diagnostica, log applicazioni e metriche
**Solo calcolo**

Nel modello di calcolo le applicazioni possono essere eseguite nel sistema operativo guest. Le applicazioni creano il proprio set di log e metriche.

Tra i tipi di metriche:

- Contatori delle prestazioni
- Log applicazioni
- Registri eventi di Windows
- Origine dell'evento .NET
- Log di IIS
- ETW basato su manifesto
- Dump di arresto anomalo del sistema
- Log degli errori dei clienti


## Uso dei dati di monitoraggio

### Visualizzazione
La visualizzazione in grafici dei dati di monitoraggio permette di identificare le tendenze molto più rapidamente della ricerca sui dati.

Ecco alcuni metodi di visualizzazione:

- Usare il portale di Azure
- Instradare i dati ad Application Insights in Azure
- Instradare i dati a Microsoft Power BI
- Instradare i dati a uno strumento di visualizzazione di terze parti usando lo streaming live o facendo in modo che lo strumento legga da un archivio in Archiviazione di Azure

### Archiviazione
In genere i dati di monitoraggio vengono scritti in Archiviazione di Azure e conservati fino a quando non vengono eliminati.

Ecco alcuni modi in cui usare questi dati:

- Fare in modo che i dati scritti vengano letti ed elaborati da altri strumenti all'interno o all'esterno di Azure.
- Scaricare i dati in locale per l'uso in un archivio locale o modificare i criteri di conservazione nel cloud per conservare i dati per lunghi periodi di tempo.
- Lasciare i dati in Archiviazione di Azure a tempo indeterminato. Si noti che il costo di Archiviazione di Azure dipende dalla quantità di dati conservati.

### Query
Per accedere ai dati nel sistema o in Archiviazione di Azure, è possibile usare l'API REST Insights, i comandi dell'interfaccia della riga di comando, i cmdlet di PowerShell o .NET SDK.

Tra gli esempi sono inclusi:

-  Recuperare i dati per un'applicazione di monitoraggio personalizzata che si è scritta.
-  Creare query personalizzate e inviare i dati a un'applicazione di terze parti.

### Route
È possibile trasmettere i dati di monitoraggio ad altre posizioni in tempo reale.

Tra gli esempi sono inclusi:

- Invio ad Application Insights per usare gli strumenti di visualizzazione al suo interno.
- Invio a Hub eventi per il routing a strumenti di terze parti per eseguire l'analisi in tempo reale.

### Automazione
È possibile usare i dati di monitoraggio per attivare eventi o interi processi. Ecco alcuni esempi:

- Usare i dati per la scalabilità automatica delle istanze di calcolo in base al carico dell'applicazione.
- Inviare messaggi di posta elettronica quando una metrica supera una soglia predeterminata.
- Chiamare un URL Web (webhook) per eseguire un'azione in un sistema all'esterno di Azure.
- Avviare un runbook in Automazione di Azure per eseguire varie attività.

## Metodi d'uso
In generale, è possibile modificare il rilevamento, il routing e il recupero dei dati usando uno dei metodi seguenti. Non tutti i metodi sono disponibili per tutte le azioni o tutti i tipi di dati.

- [Portale di Azure](https://portal.azure.com)
- [PowerShell](insights-powershell-samples.md)
- [Interfaccia della riga di comando multipiattaforma](insights-cli-samples.md)
- [API REST](https://msdn.microsoft.com/library/dn931943.aspx)
- [.NET SDK](https://msdn.microsoft.com/library/dn802153.aspx)

## Offerte di monitoraggio di Azure
Le offerte di Azure consentono di monitorare i servizi, dall'infrastruttura bare metal ai dati di telemetria dell'applicazione. La migliore strategia di monitoraggio riunisce tutte queste opzioni per fornire informazioni complete e dettagliate sull'integrità dei servizi.

- [Monitoraggio di Azure Insights (piattaforma)](http://aka.ms/azmondocs): offre funzionalità di visualizzazione, query, routing, avviso, ridimensionamento automatico e automazione dei dati sia dall'infrastruttura di Azure (log di controllo) che da ogni singola risorsa di Azure (log di diagnostica). Questo articolo fa parte della documentazione di Azure Insights.
- [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/): consente il rilevamento avanzato e la diagnostica per i problemi a livello dell'applicazione del servizio, ben integrati nei dati provenienti dal monitoraggio di Azure.
- [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), parte di [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite): offre una soluzione di gestione IT olistica per l'infrastruttura locale e basata sul cloud, inclusa l'analisi e la ricerca dei log dalle risorse di Azure.


## Passaggi successivi
Altre informazioni su:

- [Azure Insights](http://aka.ms/azmondocs), disponibili tramite i collegamenti nel sommario a sinistra e nei video in questo collegamento.
- [Diagnostica di Azure](../azure-diagnostics.md), utile per la diagnosi di problemi nelle applicazioni di Servizi cloud, Macchine virtuali e Service Fabric.
- [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/), utili per la diagnosi di problemi nell'app Web del servizio app.
- [Risoluzione dei problemi di Archiviazione di Azure](../storage/storage-e2e-troubleshooting.md), utile per l'uso di BLOB, tabelle o code di archiviazione.
- [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) e [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)

<!---HONumber=AcomDC_0907_2016-->