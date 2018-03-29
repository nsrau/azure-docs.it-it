---
title: Introduzione a Data Factory, un servizio di integrazione dei dati | Documentazione Microsoft
description: 'Informazioni su Azure Data Factory: è un servizio di integrazione dei dati cloud che consente di orchestrare e automatizzare lo spostamento e la trasformazione dei dati.'
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 6747dc6ef482fecba7e9fb4fae5748de377051eb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-azure-data-factory"></a>Introduzione a Data factory di Azure 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](data-factory-introduction.md)
> * [Versione 2 - Anteprima](../introduction.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 di Azure Data Factory, disponibile a livello generale. Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere l'[introduzione a Data Factory versione 2](../introduction.md).


## <a name="what-is-azure-data-factory"></a>Che cos'è Azure Data Factory?
Nel mondo dei Big Data, per sfruttare i dati esistenti a livello aziendale e arricchire i dati generati nel cloud, è possibile usare dati di riferimento di origini dati locali o di diverso tipo. 

Ad esempio, una società di giochi raccoglie i log generati dai giochi nel cloud. La società intende analizzare questi log per ottenere informazioni sui clienti, tra cui preferenze, dati demografici e comportamento di utilizzo. Vuole anche identificare opportunità di up-selling e cross-selling, sviluppare nuove funzionalità interessanti per promuovere la crescita del business e fornire un'esperienza migliore ai clienti. 

Per analizzare questi log, la società deve usare i dati di riferimento presenti in un archivio dati locale, ad esempio le informazioni sui clienti, sui giochi e sulle campagne di marketing. La società intende quindi inserire i dati dei log dall'archivio dati cloud e fare riferimento a questi dati dall'archivio locale. 

Infine vuole elaborare i dati usando Hadoop nel (Azure HDInsight) e pubblicare i risultati in un data warehouse cloud come Azure SQL Data Warehouse o un archivio dati locale, ad esempio SQL Server. La società intende eseguire questo flusso di lavoro una volta alla settimana. 

È quindi necessaria una piattaforma in cui poter creare un flusso di lavoro e inserire i dati da archivi dati sia locali che cloud. La società deve anche poter trasformare o elaborare i dati usando i servizi di calcolo esistenti, ad esempio Hadoop, e pubblicare i risultati in un archivio dati locale o cloud per consentire alle applicazioni BI di utilizzarli. 

![Panoramica di Data Factory](media/data-factory-introduction/what-is-azure-data-factory.png) 

Azure Data Factory è la piattaforma per questi tipi di scenari. È un *servizio di integrazione di dati basato sul cloud che consente di creare flussi di lavoro basati sui dati nel cloud che orchestrano e automatizzano lo spostamento e la trasformazione dei dati stessi*. Usando Azure Data Factory, è possibile eseguire queste attività: 

- Creare e pianificare flussi di lavoro basati sui dati (denominati pipeline) che possono inserire dati da archivi dati diversi.

- Elaborare o trasformare i dati usando servizi di calcolo, ad esempio Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning.

-  Pubblicare i dati di output in archivi dati, ad esempio Azure SQL Data Warehouse, per consentire alle applicazioni di business intelligence (BI) di utilizzarli.  

È più una piattaforma di estrazione e caricamento e di trasformazione e caricamento che una piattaforma di estrazione, trasformazione e caricamento tradizionale. Le trasformazioni elaborano i dati usando i servizi di calcolo invece di aggiungere colonne derivate, conteggiare il numero di righe, ordinare i dati e così via. 

In Azure Data Factory i dati attualmente utilizzati e prodotti dai flussi di lavoro sono *dati a suddivisione temporale*, ovvero dati orari, giornalieri, settimanali e così via. Una pipeline potrebbe ad esempio leggere i dati di input, elaborare i dati e generare dati di output una volta al giorno. È anche possibile eseguire un flusso di lavoro solo una volta.  
  

## <a name="how-does-it-work"></a>Come funziona? 
Le pipeline, ovvero i flussi di lavoro basati sui dati, in Azure Data Factory eseguono in genere i tre passaggi seguenti:

![Le tre fasi di Azure Data Factory](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>Connettersi e raccogliere
Le aziende hanno dati di tipi diversi in origini differenti. Il primo passaggio nella creazione di un sistema di produzione di informazioni è quello di connettersi a tutte le origini di dati necessarie e di elaborarli. Queste origini includono servizi SaaS, condivisioni file, FTP e servizi Web. Spostare quindi i dati in una posizione centralizzata in base alla necessità per l'elaborazione successiva.

Senza Data factory le grandi imprese devono creare componenti personalizzati per lo spostamento dei dati oppure scrivere servizi personalizzati per l'integrazione delle origini dati e delle elaborazioni. Integrare e gestire tali sistemi è costoso e complesso. Questi sistemi spesso non forniscono neppure il monitoraggio e gli avvisi di livello aziendale, oltre ai controlli che solo un servizio completamente gestito può offrire.

Con Data Factory, è possibile usare l'attività di copia in una pipeline di dati per spostare i dati da archivi dati di origine sia in locale che nel cloud a un archivio centralizzato nel cloud per analisi aggiuntive. 

È ad esempio possibile raccogliere i dati in Azure Data Lake Store e quindi trasformarli usando un servizio di calcolo Azure Data Lake Analytics oppure raccogliere i dati in un archivio BLOB di Azure e quindi trasformarli usando un cluster Hadoop Azure HDInsight.

### <a name="transform-and-enrich"></a>Trasformare e arricchire
Dopo che i dati sono presenti in un archivio dati centralizzato nel cloud, elaborarli o trasferirli tramite servizi di calcolo come Hadoop HDInsight, Spark, Data Lake Analytics o Machine Learning. L'obiettivo è generare in modo affidabile dati trasformati in una pianificazione gestibile e controllata in modo da alimentare gli ambienti di produzione con dati attendibili. 

### <a name="publish"></a>Pubblica 
Inviare i dati trasformati dal cloud alle origini locali, ad esempio SQL Server. In alternativa, mantenere i dati nelle origini di archiviazione cloud per l'uso da parte di strumenti BI e di analisi e di altre applicazioni.

## <a name="key-components"></a>Componenti chiave
Una sottoscrizione di Azure può includere una o più istanze di Azure Data Factory (o data factory). Azure Data Factory è costituito da quattro componenti chiave. Questi componenti forniscono la piattaforma nella quale è possibile comporre flussi di lavoro basati sui dati con passaggi per lo spostamento e la trasformazione dei dati stessi. 

### <a name="pipeline"></a>Pipeline
Una data factory può comprendere una o più pipeline. Una pipeline è un gruppo di attività, che insieme eseguono un'operazione. 

Una pipeline, ad esempio, può contenere un gruppo di attività che inserisce dati da un BLOB di Azure e quindi esegue una query Hive in un cluster HDInsight per partizionare i dati. La pipeline offre il vantaggio di poter gestire le attività come un set anziché singolarmente. È ad esempio possibile distribuire e pianificare la pipeline invece di pianificare attività separate. 

### <a name="activity"></a>Attività
Una pipeline può comprendere una o più attività. Le attività definiscono le azioni da eseguire sui dati. È ad esempio possibile usare un'attività di copia per copiare i dati da un archivio dati all'altro. Analogamente, è possibile usare un'attività Hive. Un'attività Hive esegue una query Hive su un cluster Azure HDInsight per trasformare o analizzare i dati. Data Factory supporta due tipi di attività: attività di spostamento dei dati e attività di trasformazione dei dati.

### <a name="data-movement-activities"></a>Attività di spostamento dei dati
L'attività di copia in Data Factory esegue la copia dei dati da un archivio dati di origine a un archivio dati sink. I dati da qualsiasi origine possono essere scritti in qualsiasi sink. Selezionare un archivio dati per informazioni su come copiare dati da e verso tale archivio. Data Factory supporta gli archivi dati seguenti:

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Per altre informazioni, vedere [Spostare dati con l'attività di copia](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Attività di trasformazione dei dati
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Per altre informazioni, vedere [Spostare dati con l'attività di copia](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Attività .NET personalizzate
Creare un'attività .NET personalizzata se è necessario spostare dati da o verso un archivio dati che non è supportato dall'attività di copia oppure se è necessario trasformare i dati usando la propria logica. Per informazioni dettagliate su come creare e usare un'attività personalizzata, vedere [Usare attività personalizzate in una pipeline di Azure Data Factory](data-factory-use-custom-activities.md).

### <a name="datasets"></a>Set di dati
Un'attività accetta zero o più set di dati come input e uno o più set di dati come output. I set di dati rappresentano le strutture dei dati negli archivi dati. Queste strutture puntano o fanno riferimento ai dati che si vuole usare nelle attività (ad esempio, input o output). 

Un set di dati BLOB di Azure, ad esempio, specifica il contenitore BLOB e la cartella nell'archivio BLOB di Azure da cui la pipeline dovrà leggere i dati. Il set di dati della tabella SQL di Azure specifica invece la tabella in cui l'attività scriverà i dati di output. 

### <a name="linked-services"></a>Servizi collegati
I servizi collegati sono molto simili a stringhe di connessione e definiscono le informazioni necessarie per la connessione di Data Factory a risorse esterne. In sintesi, un servizio collegato definisce la connessione all'origine dati, mentre un set di dati rappresenta la struttura dei dati. 

Ad esempio, un servizio collegato di Archiviazione di Azure specifica una stringa con cui connettersi all'account di archiviazione di Azure. Un set di dati BLOB di Azure specifica il contenitore e la cartella BLOB che contengono i dati.   

In Data factory i servizi collegati vengono usati per i due motivi seguenti:

* Per rappresentare un *archivio dati* che include, a titolo esemplificativo, un database di SQL Server locale, un database Oracle, una condivisione file o un account di archiviazione BLOB di Azure. Vedere la sezione [Attività di spostamento dei dati](#data-movement-activities) per un elenco di archivi dati supportati.

* Per rappresentare una *risorsa di calcolo* che può ospitare l'esecuzione di un'attività. Ad esempio, l'attività HDInsightHive viene eseguita in un cluster HDInsight Hadoop. Vedere la sezione [Attività di trasformazione dei dati](#data-transformation-activities) per un elenco di ambienti di calcolo supportati.

### <a name="relationship-between-data-factory-entities"></a>Relazioni tra le entità di Data Factory

![Diagramma: Data Factory, servizio di integrazione dei dati cloud - concetti chiave](./media/data-factory-introduction/data-integration-service-key-concepts.png)

## <a name="supported-regions"></a>Aree supportate
È attualmente possibile creare data factory nelle aree Stati Uniti occidentali, Stati Uniti orientali ed Europa settentrionale. Una data factory può accedere ad archivi dati e servizi di calcolo in altre aree di Azure per spostare i dati tra archivi dati o elaborare i dati usando i servizi di calcolo.

Azure Data Factory stesso non archivia alcun dato. Consente di creare flussi di lavoro basati sui dati per orchestrare lo spostamento di dati tra [archivi dati supportati](#data-movement-activities). Consente anche di elaborare i dati usando i [servizi di calcolo](#data-transformation-activities) in altre aree o in un ambiente locale. Consente anche di [monitorare e gestire i flussi di lavoro](data-factory-monitor-manage-pipelines.md) usando meccanismi a livello di codice e di interfaccia utente.

Data Factory è disponibile solo nelle aree Stati Uniti occidentali, Stati Uniti orientali ed Europa settentrionale. Il servizio su cui si basa lo spostamento dei dati in Data Factory è tuttavia disponibile [a livello globale](data-factory-data-movement-activities.md#global) in diverse aree. Se l'archivio dati è protetto da firewall, i dati verranno spostati da un [gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) installato nell'ambiente locale.

Si supponga ad esempio che gli ambienti di calcolo, come un cluster Azure HDInsight e Azure Machine Learning, si trovino nell'area Europa occidentale. È possibile creare e usare un'istanza di Azure Data Factory in Europa settentrionale. È quindi possibile usarla per pianificare processi negli ambienti di calcolo in Europa occidentale. Data Factory necessita di alcuni millisecondi per attivare il processo nell'ambiente di calcolo, ma il tempo per l'esecuzione del processo nell'ambiente di calcolo non cambia.

## <a name="get-started-with-creating-a-pipeline"></a>Introduzione alla creazione di una pipeline
È possibile usare uno di questi strumenti o le API per creare pipeline in Azure Data Factory: 

- Portale di Azure
- Visual Studio
- PowerShell
- API .NET
- API REST
- Modello di Azure Resource Manager

Per informazioni su come creare data factory con pipeline di dati, seguire le istruzioni dettagliate disponibili nelle esercitazioni seguenti:

| Esercitazione | DESCRIZIONE |
| --- | --- |
| [Spostare dati tra due archivi dati cloud](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Creare una data factory con una pipeline per spostare i dati dall'archivio BLOB a un database SQL. |
| [Trasformare i dati usando cluster Hadoop](data-factory-build-your-first-pipeline.md) |Compilare la prima istanza di Azure Data Factory con una pipeline di dati che elabora i dati eseguendo uno script Hive in un cluster Azure HDInsight (Hadoop). |
| [Spostare dati tra un archivio dati locale e un archivio dati cloud usando il gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) |Creare una data factory con una pipeline che sposta i dati da un database di SQL Server locale a un BLOB di Azure. Come parte della procedura dettagliata, viene installato e configurato il gateway di gestione dati nel computer. |
