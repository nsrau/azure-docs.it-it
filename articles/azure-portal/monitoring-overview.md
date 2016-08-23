<properties
	pageTitle="Panoramica sul monitoraggio in Microsoft Azure | Microsoft Azure"
	description="Panoramica generale sul monitoraggio e la diagnostica negli avvisi di Azure rispetto ad altri sistemi non Azure."
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
	ms.date="08/03/2016"
	ms.author="robb"/>  

# Panoramica sul monitoraggio in Microsoft Azure

Questo articolo fornisce una panoramica concettuale sulla risorsa per il monitoraggio in Azure e indicazioni ad altre informazioni su tipi di risorse specifici. Per informazioni generali sul monitoraggio dell'applicazione da un punto di vista diverso da Azure, vedere [Indicazioni di monitoraggio e diagnostica](../best-practices-monitoring.md).

Le applicazioni cloud sono complesse e hanno molte parti mobili. Il monitoraggio offre la possibilità di garantire il funzionamento e l'integrità dell'applicazione, verificando anche le metriche essenziali per prevenire potenziali problemi o risolvere quelli precedenti. È anche possibile usare i dati di monitoraggio per automatizzare alcune azioni che richiederebbero un intervento manuale e ottenere informazioni approfondite sull'applicazione, utili a migliorarne le prestazioni o la manutenibilità.

Il diagramma seguente mostra una visualizzazione logica del monitoraggio in Azure. L'implementazione sottostante è più complessa. La figura mostra il tipo di log che è possibile raccogliere e cosa fare con i dati.

![Modello logico per il monitoraggio e la diagnostica per risorse non di calcolo](./media/monitoring-overview/monitoring-azure-resources-non-compute.png)  

Figura 1: Modello logico per il monitoraggio e la diagnostica per risorse non di calcolo

<br/>  

![Modello logico per il monitoraggio e la diagnostica per risorse di calcolo](./media/monitoring-overview/monitoring-azure-resources-compute.png)  

Figura 2: Modello logico per il monitoraggio e la diagnostica per risorse di calcolo


## Monitoraggio delle origini
### Log di controllo (attività) dell'infrastruttura
È possibile ottenere i log di controllo, detti anche log attività o operativi, relativi alla risorsa così come visualizzati dall'infrastruttura di Azure. Questi log contengono informazioni quali gli orari di creazione ed eliminazione delle risorse.

### Macchina virtuale host
**Solo calcolo**

Attualmente non è possibile raccogliere metriche sulla macchina virtuale host, ma qui viene elencata per completezza.

Alcune risorse di calcolo come Servizi cloud, Macchine virtuali e Service Fabric hanno una macchina virtuale host dedicata con cui interagiscono. La macchina virtuale host è l'equivalente della macchina virtuale radice del modello di hypervisor Hyper-V.

Per altri servizi di Azure non c'è necessariamente una corrispondenza 1:1 tra la risorsa e una determinata macchina virtuale host.


### Risorsa: log di diagnostica e metriche
Le metriche che è possibile raccogliere variano in base al tipo di risorsa. Ad esempio, Macchine virtuali fornisce statistiche sull'I/O del disco e sulla percentuale CPU, mentre una coda del bus di servizio fornisce metriche come la dimensione della coda e la velocità effettiva dei messaggi.

Per le risorse di calcolo è possibile ottenere metriche relative al sistema operativo guest, nonché moduli di diagnostica come Diagnostica di Azure. Diagnostica di Azure consente la raccolta e il routing dei dati di diagnostica raccolti in altre posizioni, inclusa l'Archiviazione di Azure.

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
La visualizzazione in grafici dei dati di monitoraggio permette di identificare le tendenze molto più rapidamente della ricerca sui dati. Ecco alcuni modi in cui visualizzare i dati di monitoraggio:

- Uso del portale di Azure
- Routing dei dati ad Azure Application Insights
- Routing dei dati a Power BI
- Routing dei dati a uno strumento di visualizzazione di terze parti usando lo streaming live o facendo in modo che lo strumento legga da un archivio in Archiviazione di Azure

### Archiviazione
In genere i dati di monitoraggio vengono scritti in Archiviazione di Azure e conservati fino a quando non vengono eliminati.

Ecco alcuni modi in cui usare questi dati:

- Fare in modo che i dati scritti vengano letti ed elaborati da altri strumenti all'interno o all'esterno di Azure.
- Scaricare i dati in locale per l'uso in un archivio locale o modificare i criteri di conservazione nel cloud per conservare i dati per lunghi periodi di tempo.
- Lasciare i dati in Archiviazione di Azure a tempo indeterminato. Si noti che il costo di Archiviazione di Azure dipende dalla quantità di dati conservati.

### Query
Per accedere ai dati nel sistema o in Archiviazione di Azure, è possibile usare l'API REST di Insights, i comandi dell'interfaccia della riga di comando, i cmdlet di PowerShell o .NET SDK. Ecco alcuni esempi:

-  Recuperare i dati per un'applicazione di monitoraggio personalizzata che si è scritta.
-  Creare query personalizzate e inviare i dati a un'applicazione di terze parti.

### Route
È possibile trasmettere i dati di monitoraggio ad altre posizioni in tempo reale. Tra gli esempi sono inclusi:

- Invio ad Application Insights per usare gli strumenti di visualizzazione al suo interno.
- Invio a Hub eventi per il routing a strumenti di terze parti per eseguire l'analisi in tempo reale.

### Automazione
È possibile usare i dati di monitoraggio per attivare eventi o interi processi. Ecco alcuni esempi:

- Usare i dati per la scalabilità automatica delle istanze di calcolo in base al carico dell'applicazione.
- Inviare messaggi di posta elettronica quando una metrica supera una soglia predeterminata.
- Chiamare un URL Web (webhook) per eseguire un'azione in un sistema all'esterno di Azure.
- Avviare un runbook in Automazione di Azure per eseguire varie attività.



## Metodi d'uso
In generale, è possibile modificare i dati rilevati, la relativa destinazione e il recupero dei dati usando uno dei metodi seguenti. Non tutti i metodi disponibili per tutte le azioni.

- [Portale di Azure](https://portal.azure.com)
- [PowerShell](insights-powershell-samples.md)
- [Interfaccia della riga di comando](insights-cli-samples.md)
- [API REST](https://msdn.microsoft.com/library/dn931943.aspx)
- [.NET SDK](https://msdn.microsoft.com/library/dn802153.aspx)

## Offerte di monitoraggio di Azure
Le offerte di Azure consentono di monitorare i servizi, dall'infrastruttura bare metal ai dati di telemetria dell'applicazione. La migliore strategia di monitoraggio riunisce tutte queste opzioni per fornire informazioni complete e dettagliate sull'integrità dei servizi.

- [Monitoraggio (di piattaforma) di Azure Insights](http://aka.ms/azmondocs): offre funzionalità di visualizzazione, query, routing, avviso, scalabilità automatica e automazione dei dati sia dall'infrastruttura di Azure (log di controllo) che da ogni singola risorsa di Azure (log di diagnostica). Questo articolo fa parte della documentazione di Azure Insights.
- **[Application Insights](https://azure.microsoft.com/documentation/services/application-insights/)**: consente il rilevamento avanzato e la diagnostica per problemi a livello di applicazione del servizio, ben integrati sui dati provenienti dal monitoraggio di Azure.
- **[Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/)**, parte di **[Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)**: offre una soluzione di gestione IT olistica per l'infrastruttura in locale o basata sul cloud, inclusa l'analisi e la ricerca dei log dalle risorse di Azure.


## Passaggi successivi
Altre informazioni su:

- [Azure Insights](http://aka.ms/azmondocs), disponibili tramite i collegamenti nel sommario a sinistra e nei video in questo collegamento.
- [Diagnostica di Azure](../azure-diagnostics.md), utili per la diagnosi di problemi nelle applicazioni di Servizi cloud, Macchine virtuali e Service Fabric.
- [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/), utili per la diagnosi di problemi nell'app Web del servizio app.
- [Risoluzione dei problemi di Archiviazione di Azure](../storage/storage-e2e-troubleshooting.md), utili per l'uso di BLOB di archiviazione, tabelle o code.
- [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) e [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)

<!---HONumber=AcomDC_0810_2016-->