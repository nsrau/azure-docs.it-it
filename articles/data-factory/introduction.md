---
title: Introduzione ad Azure Data Factory | Microsoft Docs
description: Informazioni su Azure Data Factory, un servizio di integrazione dei dati cloud che consente di orchestrare e automatizzare lo spostamento e la trasformazione dei dati.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/29/2017
ms.author: shlo
ms.openlocfilehash: ef7055342a04057acfba9dad350f654aa4de6096
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-data-factory"></a>Introduzione a Data factory di Azure 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-introduction.md)
> * [Versione 2 - Anteprima](introduction.md)

Nel mondo dei Big Data, dati non elaborati e non organizzati vengono spesso archiviati nei sistemi di archiviazione relazionali, non relazionali e di altro tipo. Tuttavia i dati non elaborati non dispongono intrinsecamente di contesto o significato appropriato per generare informazioni significative agli analisti, ai data scientist e ai responsabili delle decisioni aziendali. È necessario un servizio che consenta di abilitare i processi di orchestrazione e operativi per ottimizzare questi enormi archivi di dati non elaborati in informazioni aziendali di utilità pratica. Azure Data Factory è un servizio cloud gestito ideato per questi complessi progetti ibridi di estrazione, trasformazione e caricamento (ETL), di estrazione, caricamento e trasformazione (ELT) e di integrazione dei dati.

Si prenda ad esempio una società di giochi che raccoglie petabyte di log relativi ai giochi generati dai giochi nel cloud. La società intende analizzare questi log per ottenere informazioni sui clienti, tra cui preferenze, dati demografici e comportamento di utilizzo, per identificare opportunità di upselling e cross-selling, sviluppare nuove funzionalità interessanti per favorire la crescita aziendale e offrire una migliore esperienza ai clienti.

Per analizzare questi log, la società deve usare dati di riferimento presenti in un archivio dati locale, ad esempio le informazioni sui clienti, sui giochi e sulle campagne di marketing. La società intende usare questi dati presenti nell'archivio dati locale, integrandoli con i dati di log aggiuntivi di cui dispone in un archivio dati cloud. Per estrarre informazioni dettagliate, desidera elaborare i dati aggiunti usando un cluster Spark nel cloud (HDInsight) e infine pubblicare i dati trasformati in un data warehouse cloud, ad esempio Azure SQL Data Warehouse, per creare facilmente un report. Questo flusso di lavoro deve essere automatizzato, monitorato e gestito in base a una pianificazione giornaliera ed eseguito quando i file vengono inseriti in un contenitore di archivi BLOB.

La piattaforma Azure Data Factory è in grado di gestire con successo scenari di dati di questo tipo. È un **servizio di integrazione di dati basato sul cloud che consente di creare flussi di lavoro basati sui dati nel cloud per orchestrare e automatizzare lo spostamento e la trasformazione dei dati stessi**. Usando Azure Data Factory è possibile creare e pianificare flussi di lavoro (denominati pipeline) basati sui dati che possono inserire dati da archivi diversi, elaborarli e trasformarli tramite servizi di calcolo come Hadoop di Azure HDInsight, Spark, Azure Data Lake Analytics e Azure Machine Learning e pubblicare l'output in archivi come Azure SQL Data Warehouse per l'uso da parte di applicazioni di business intelligence (BI). Infine, i dati non elaborati possono essere organizzati in archivi dati e data lake significativi per ottimizzare il processo decisionale aziendale usando Azure Data Factory.

![vista di primo livello di Data Factory](media/introduction/big-picture.png)

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale (GA), vedere [Introduzione a Data Factory versione 1](v1/data-factory-introduction.md).

## <a name="how-does-it-work"></a>Come funziona?
Le pipeline, ovvero i flussi di lavoro basati su dati, in Azure Data Factory eseguono in genere i quattro passaggi seguenti:

![Quattro passaggi del flusso di lavoro basato sui dati](media/introduction/four-steps-of-a-workflow.png)

### <a name="connect-and-collect"></a>Connettersi e raccogliere

Le aziende dispongono di dati di vari tipi archiviati in origini disomogenee in locale e nel cloud; dati strutturati, non strutturati e semistrutturati vengono tutti ricevuti a velocità e a intervalli diversi. Il primo passaggio per creare un sistema di produzione di informazioni consiste nel connettersi a tutte le origini dati e di elaborazione necessarie, come servizi software-as-a-service (SaaS), database, condivisioni file, servizi Web FTP, e nello spostare i dati in una posizione centralizzata in base alle necessità per l'elaborazione successiva.

Senza Data factory le grandi imprese devono creare componenti personalizzati per lo spostamento dei dati oppure scrivere servizi personalizzati per l'integrazione delle origini dati e delle elaborazioni. È un procedimento costoso, i sistemi sono difficili da integrare e gestire e spesso non forniscono il monitoraggio e gli avvisi di livello aziendale, oltre ai controlli che solo un servizio completamente gestito può offrire.

Con Data Factory è possibile usare l'[attività di copia](copy-activity-overview.md) in una pipeline di dati per spostare i dati dagli archivi dati di origine sia in locale che nel cloud a un archivio dati centralizzato nel cloud per analisi aggiuntive. È ad esempio possibile raccogliere i dati in un'istanza di Azure Data Lake Store e quindi trasformarli usando un servizio di calcolo Azure Data Lake Analytics oppure raccogliere i dati in un archivio BLOB di Azure e quindi trasformarli usando un cluster Hadoop Azure HDInsight.

### <a name="transform-and-enrich"></a>Trasformare e arricchire
Quando i dati sono presenti in un archivio dati centralizzato nel cloud, elaborare o trasformare i dati raccolti tramite servizi di calcolo come Hadoop HDInsight, Spark, Data Lake Analytics e Machine Learning. L'obiettivo è generare in modo affidabile dati trasformati in una pianificazione gestibile e controllata in modo da alimentare gli ambienti di produzione con dati attendibili.

### <a name="publish"></a>Pubblica
Ora che i dati non elaborati sono stati ottimizzati in un formato utilizzabile e pronto per il business, caricare i dati in Azure Data Warehouse, nel database SQL di Azure, in Azure CosmosDB o in qualsiasi motore di analisi a cui gli utenti business possono fare riferimento dai propri strumenti di Business Intelligence.

### <a name="monitor"></a>Monitorare
Dopo avere compilato e distribuito la pipeline di integrazione di dati, fornendo il valore business dai dati ottimizzati, è possibile monitorare le attività e le pipeline pianificate per determinare le frequenze di operazioni riuscite e non riuscite. Azure Data Factory include supporto predefinito per il monitoraggio delle pipeline tramite Monitoraggio di Azure, API, PowerShell, Microsoft Operations Management Suite e i pannelli di integrità nel portale di Azure.

## <a name="whats-different-in-version-2"></a>Differenze nella versione 2
Azure Data Factory versione 2 si basa sul servizio di trasformazione e spostamento di dati di Azure Data Factory originale, aggiungendo un set più ampio di scenari di integrazione dati cloud-first. Azure Data Factory versione 2 offre le funzionalità seguenti:

- Flusso di controllo e scalabilità
- Distribuzione ed esecuzione di pacchetti SSIS in Azure

Dopo il rilascio della versione 1 è stato constatato che i clienti hanno la necessità di progettare complessi scenari di integrazione di dati ibridi che richiedono sia lo spostamento dei dati sia l'elaborazione nel cloud, in locale e nelle macchine virtuali cloud. Questi requisiti hanno introdotto la necessità di trasferire ed elaborare i dati all'interno di ambienti di rete virtuale protetti e di scalare orizzontalmente con la potenza di elaborazione su richiesta.

Mano a mano che le pipeline di dati diventano una parte essenziale della strategia di analisi business, è stato osservato che queste attività di dati critiche richiedono una pianificazione flessibile per supportare carichi di dati incrementali ed esecuzioni attivate da eventi. Infine, la crescente complessità di queste orchestrazioni implica anche l'aumento del requisito per il servizio di supportare i paradigmi comuni del flusso di lavoro, inclusi la creazione di rami, i cicli e l'elaborazione condizionale.

Con la versione 2 è possibile anche migrare i pacchetti SQL Server Integration Services (SSIS) esistenti nel cloud per trasferire in modalità lift-and-shift SSIS come servizio Azure gestito all'interno del file di definizione dell'applicazione (ADF) usando una nuova funzionalità di "runtime di integrazione". Avviando un runtime di integrazione SSIS nella versione 2, è possibile eseguire, gestire, monitorare e compilare pacchetti SSIS nel cloud.

### <a name="control-flow-and-scale"></a>Flusso di controllo e scalabilità 
Per supportare l'integrazione di flussi e criteri di integrazione diversi nel data warehouse moderno, Data Factory ha abilitato un nuovo modello di pipeline di dati che non è più associato ai dati delle serie temporali. Con questa versione è possibile modellare le istruzioni condizionali e la creazione di rami nel flusso di controllo di una pipeline di dati e passare in modo esplicito i parametri all'interno dei flussi e tra i diversi flussi.

Ora è possibile modellare qualsiasi stile di flusso necessario per l'integrazione di dati e definire l'invio su richiesta o ripetutamente in una pianificazione basata sul tempo. Alcuni flussi comuni, in precedenza non possibili, sono ora abilitati:   

- Flusso di controllo:
    - Il concatenamento di attività in una sequenza all'interno di una pipeline
    - La creazione di rami di attività all'interno di una pipeline
    - parameters
        - Parametri che possono essere definiti a livello di pipeline e argomenti passati durante la chiamata della pipeline su richiesta o da un trigger
        - Le attività possono usare gli argomenti passati alla pipeline
    - Passaggio di stato personalizzato
        - Gli output delle attività che includono lo stato possono essere usati da un'attività successiva nella pipeline
    - Contenitori di ciclo
        - For-each 
- Flussi attivati da trigger
    - Le pipeline possono essere attivate su richiesta o con una pianificazione basata sul tempo reale
- Flussi delta
    - Usare i parametri e definire il limite massimo per la copia delta durante lo spostamento delle tabelle delle dimensioni o di riferimento da un archivio relazionale in locale o nel cloud per caricare i dati nel lake 

Per altre informazioni, vedere [esercitazione: flusso di controllo](tutorial-control-flow.md).

### <a name="deploy-ssis-packages-to-azure"></a>Distribuire i pacchetti SSIS in Azure 
Se si vuole spostare i carichi di lavoro SSIS, è possibile creare una data factory versione 2 ed effettuare il provisioning di un runtime di integrazione Azure-SSIS. Il runtime di integrazione Azure-SSIS è un cluster completamente gestito di macchine virtuali (nodi) di Azure dedicato all'esecuzione di pacchetti SSIS nel cloud. Per istruzioni dettagliate, vedere l'esercitazione: [Distribuire i pacchetti SSIS in Azure](tutorial-deploy-ssis-packages-azure.md). 
 

### <a name="sdks"></a>SDK
Se si è un utente Advanced e si cerca un'interfaccia programmatica, la versione 2 fornisce un ampio set di SDK, che può essere usata per creare, gestire, monitorare pipeline usando l'IDE preferito.

- .NET SDK - .NET SDK è aggiornato per la versione 2. 
- PowerShell - I cmdlet PowerShell sono aggiornati per la versione 2. I cmdlet per la versione 2 hanno **DataFactoryV2** nel proprio nome. Ad esempio: Get-AzureRmDataFactoryV2. 
- Python SDK - questo SDK è una novità nella versione 2.
- API REST - L'API REST è aggiornato per la versione 2.  

Gli SDK che sono stati aggiornati per la versione 2 non sono compatibili con i client della versione 1. 

### <a name="monitoring"></a>Monitoraggio
Attualmente, la versione 2 supporta il monitoraggio dle data factory usando solo SDK. Il portale non dispone ancora del supporto per il monitoraggio dle data factory di versione 2. 

## <a name="load-the-data-into-a-lake"></a>Caricare i dati in un lake
Data Factory include oltre 30 connettori che consentono di caricare i dati da ambienti ibridi ed eterogenei in Azure.  Vedere [Guida alle prestazioni e all'ottimizzazione](copy-activity-performance.md) per i risultati delle prestazioni più recenti ottenuti da test interni e da suggerimenti per l'ottimizzazione. Inoltre sono state abilitate recentemente la scalabilità e la disponibilità elevata per il runtime di integrazione self-hosted, che viene installato in un ambiente di rete privata per soddisfare i requisiti di clienti enterprise di livello 1 in termini di maggiore disponibilità e scalabilità.

## <a name="top-level-concepts-in-version-2"></a>Concetti di primo livello nella versione 2
Una sottoscrizione di Azure può includere una o più istanze di Azure Data Factory (o data factory). Azure Data Factory è costituito da quattro componenti chiave che insieme forniscono la piattaforma nella quale è possibile comporre flussi di lavoro basati sui dati con passaggi per lo spostamento e la trasformazione dei dati stessi.

### <a name="pipeline"></a>Pipeline
Una data factory può comprendere una o più pipeline. Una pipeline è un raggruppamento logico di attività per eseguire un'unità di lavoro. che insieme eseguono un'operazione. Una pipeline, ad esempio, può contenere un gruppo di attività che inserisce dati da un BLOB di Azure e quindi esegue una query Hive in un cluster HDInsight per partizionare i dati. La pipeline offre il vantaggio di poter gestire le attività come un set anziché singolarmente. Le attività in una pipeline possono essere concatenate per funzionare in modo sequenziale o indipendentemente in parallelo

### <a name="activity"></a>Attività
Le attività rappresentano un passaggio di elaborazione in una pipeline. Ad esempio, è possibile usare un'attività Copia per copiare i dati da un archivio dati all'altro. Allo stesso modo, è possibile usare un'attività Hive che esegue una query Hive su un cluster Azure HDInsight per trasformare o analizzare i dati. Il servizio Data Factory supporta tre tipi di attività: attività di spostamento dei dati, attività di trasformazione dei dati e attività di controllo

### <a name="datasets"></a>DATASETS
I set di dati rappresentano strutture dei dati all'interno degli archivi dati e fanno semplicemente riferimento ai dati da usare nelle attività come input o output. 

### <a name="linked-services"></a>Servizi collegati
I servizi collegati sono molto simili a stringhe di connessione e definiscono le informazioni necessarie per la connessione di Data Factory a risorse esterne. In sintesi, un servizio collegato definisce la connessione all'origine dati, mentre un set di dati rappresenta la struttura dei dati. Ad esempio, un servizio collegato di Archiviazione di Azure specifica la stringa per la connessione all'account di archiviazione di Azure. Un set di dati BLOB di Azure specifica il contenitore e la cartella BLOB che contengono i dati.

In Data factory i servizi collegati vengono usati per i due scopi seguenti:

- Per rappresentare un **archivio dati** , inclusi, a titolo esemplificativo, un'istanza di SQL Server locale, un database Oracle, una condivisione file o un account di archiviazione BLOB di Azure. Per un elenco degli archivi dati supportati, vedere l'articolo sull'[attività di copia](copy-activity-overview.md).
- Per rappresentare una **risorsa di calcolo** che può ospitare l'esecuzione di un'attività. Ad esempio, l'attività HDInsightHive viene eseguita in un cluster HDInsight Hadoop. Vedere l'articolo sulla [trasformazione di dati](transform-data.md) per un elenco di attività di trasformazione e degli ambienti di calcolo supportati.

### <a name="triggers"></a>Trigger
I trigger rappresentano l'unità di elaborazione che determina quando deve essere avviata l'esecuzione di una pipeline. Esistono diversi tipi di trigger per i diversi tipi di eventi; per l'anteprima è supportato il trigger dell'utilità di pianificazione basato sul tempo reale. 


### <a name="pipeline-runs"></a>Esecuzioni di pipeline
Un'esecuzione di pipeline è un'istanza dell'esecuzione della pipeline. Le istanze delle esecuzioni di pipeline vengono create in genere passando gli argomenti ai parametri definiti nelle pipeline. Gli argomenti possono essere passati manualmente o all'interno della definizione di trigger

### <a name="parameters"></a>parameters
I parametri sono coppie chiave-valore della configurazione di sola lettura.  I parametri vengono definiti nella pipeline e gli argomenti per i parametri definiti vengono passati durante l'esecuzione dal contesto di esecuzione creato da un trigger o da una pipeline eseguita manualmente. Le attività all'interno della pipeline usano i valori dei parametri.
Un set di dati è un parametro fortemente tipizzato e un'entità referenziabile/riutilizzabile. Un'attività può referenziare set di dati e può usare le proprietà definite nella definizione del set di dati

Inoltre, anche un servizio collegato è un parametro fortemente tipizzato contenente le informazioni di connessione a un archivio dati o a un ambiente di calcolo. È anche un'entità referenziabile/riutilizzabile.

### <a name="control-flow"></a>Flusso di controllo
Orchestrazione delle attività della pipeline che include il concatenamento di attività in una sequenza, la creazione di rami, i parametri definibili a livello di pipeline e gli argomenti passati durante la chiamata della pipeline su richiesta o da un trigger. Include anche il passaggio di stati personalizzati e i contenitori di ciclo, vale a dire gli iteratori For-Each.


Per altre informazioni sui concetti relativi a Data Factory, vedere gli articoli seguenti:

- [Set di dati e servizi collegati](concepts-datasets-linked-services.md)
- [Pipeline e attività](concepts-pipelines-activities.md)
- [Runtime di integrazione](concepts-integration-runtime.md)

## <a name="supported-regions"></a>Aree supportate:

È attualmente possibile creare data factory nelle aree Stati Uniti orientali e Stati Uniti orientali 2. Una data factory può accedere ad archivi dati e servizi di calcolo in altre aree di Azure per spostare i dati tra archivi dati o elaborare i dati usando i servizi di calcolo.

Azure Data Factory stesso non archivia alcun dato. Consente di creare flussi di lavoro basati sui dati per orchestrare lo spostamento di dati tra archivi dati supportati e l'elaborazione di dati usando i servizi di calcolo in altre aree o in un ambiente locale. Consente anche di monitorare e gestire i flussi di lavoro usando meccanismi a livello di codice e di interfaccia utente.

Anche se Data Factory è disponibile solo nelle aree Stati Uniti orientali e Stati Uniti orientali 2, il servizio che consente lo spostamento dei dati in Data Factory è disponibile a livello globale in diverse aree. Se l'archivio dati è protetto da firewall, i dati verranno spostati da un gateway di gestione dati installato nell'ambiente locale.

Si supponga ad esempio che gli ambienti di calcolo, come un cluster Azure HDInsight e Azure Machine Learning, siano in esecuzione nell'area Europa occidentale. È possibile creare e usare un'istanza di Azure Data Factory in Europa settentrionale e usarla per pianificare processi negli ambienti di calcolo in Europa occidentale. Data Factory necessita di pochi secondi per attivare il processo nell'ambiente di calcolo, ma il tempo per l'esecuzione del processo nell'ambiente di calcolo non cambia.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come creare una data factory seguendo le istruzioni dettagliate nei seguenti modelli di avvio rapido: [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md), [API REST ](quickstart-create-data-factory-rest-api.md) e nel portale di Azure. 
