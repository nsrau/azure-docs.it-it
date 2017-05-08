---
title: Introduzione a Data Factory, un servizio di integrazione dei dati | Documentazione Microsoft
description: "Informazioni su Azure Data Factory: è un servizio di integrazione dei dati cloud che consente di orchestrare e automatizzare lo spostamento e la trasformazione dei dati."
keywords: "integrazione dei dati, integrazione dei dati cloud, che cos&quot;è azure data factory"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/21/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 260208e7c7a08110eb3c885ef86ec4c18ff42fc9
ms.openlocfilehash: 40552b5d3cea5b04826c08e7b4b1d046a9fcefba
ms.lasthandoff: 04/23/2017


---
# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>Introduzione al servizio Azure Data Factory, un servizio di integrazione dati nel cloud
## <a name="what-is-azure-data-factory"></a>Che cos'è Azure Data Factory?
Nel mondo dei Big Data, per sfruttare i dati esistenti a livello aziendale e arricchire i dati generati nel cloud usando dati di riferimento di origini dati locali o di diverso tipo è necessaria una piattaforma per l'aggregazione e l'elaborazione dei dati di una vasta gamma di origini. Azure Data Factory è un servizio di integrazione dei dati basato sul cloud che orchestra e automatizza lo **spostamento** e la **trasformazione** dei dati. È possibile creare soluzioni di integrazione dei dati per inserire dati di input da diversi archivi dati, trasformare/elaborare i dati e pubblicare dati di output in altri archivi. 

![Diagramma: Panoramica di Data Factory, un servizio di integrazione dei dati](./media/data-factory-introduction/what-is-azure-data-factory.png)

**Figure1.** Inserire i dati da svariate origini dati locali, prepararli, trasformarli e analizzarli e quindi pubblicare dati pronti per l'utilizzo.


## <a name="what-does-it-offer"></a>Vantaggi offerti 
Tradizionalmente, i progetti di integrazione dei dati sono incentrati sulla creazione di processi ETL (Extract-Transform-Load) che estraggono i dati da varie origini all'interno di un'organizzazione, li trasformano in modo che siano conformi allo schema di destinazione di un data warehouse aziendale e quindi li caricano in tale data warehouse come illustrato nell'immagine seguente. Il data warehouse aziendale è quindi accessibile come singola origine di dati reali per soluzioni di analisi di business intelligence.

![Processo ETL tradizionale](media/data-factory-introduction/traditional-etl.png)
**Processo ETL tradizionale**

L'attuale panorama dei dati nelle grandi imprese continua a crescere in misura esponenziale in termini di volume, varietà e complessità, come illustrato nell'immagine seguente. È più eterogeneo che mai, con dati di diversi formati e velocità che hanno origine in locale o nel cloud. L'elaborazione dei dati deve essere eseguita in diverse località geografiche e include una combinazione di software open source, soluzioni commerciali e servizi di elaborazione personalizzati costosi e difficili da integrare e gestire. La flessibilità necessaria per adattarsi al mutevole panorama attuale dei Big Data offre l'opportunità di unire il data warehouse aziendale tradizionale e le funzionalità richieste per un moderno sistema di produzione di informazioni. Azure Data Factory offre una piattaforma di composizione applicabile ai data warehouse aziendali tradizionali e al mutevole panorama dei dati per consentire alle grandi imprese di sfruttare tutti i dati disponibili per un processo decisionale guidato dai dati.

![Nuovo panorama dei Big Data](media/data-factory-introduction/new-big-data-landscape.png)
**Nuovo panorama dei Big Data**

Il servizio Azure Data Factory consente alle grandi imprese di sfruttare questa eterogeneità offrendo una piattaforma per **comporre servizi di elaborazione, archiviazione e spostamento dati in pipeline di produzione di informazioni** e gestire asset di dati attendibili.

Il servizio Azure Data Factory consente di:
- **Usare facilmente sistemi di elaborazione e archiviazione dati eterogenei**. 

    Le grandi imprese hanno svariati tipi di dati in diverse origini. Il primo passaggio per creare un sistema di produzione di informazioni consiste nel connettere tutte le origini di dati e di elaborazione necessarie, come servizi SaaS, condivisioni file, FTP e servizi Web, e nello spostare i dati in una posizione centralizzata in base alla necessità per l'elaborazione successiva.

    Senza Data factory le grandi imprese devono creare componenti personalizzati per lo spostamento dei dati oppure scrivere servizi personalizzati per l'integrazione delle origini dati e delle elaborazioni. È un procedimento costoso, i sistemi sono difficili da integrare e gestire e spesso non forniscono il monitoraggio e gli avvisi di livello aziendale, oltre ai controlli che solo un servizio completamente gestito è in grado di offrire.

    Con Data Factory, è possibile usare l'attività di copia in una pipeline di dati per spostare i dati da archivi dati di origine sia in locale che nel cloud a un archivio centralizzato nel cloud per analisi aggiuntive. È ad esempio possibile raccogliere i dati in un'istanza di Azure Data Lake Store e quindi trasformarli usando un servizio di calcolo Azure Data Lake Analytics oppure raccogliere i dati in un archivio BLOB di Azure e quindi trasformarli usando un cluster Hadoop Azure HDInsight.
- **Trasformare i dati in informazioni attendibili**. 

    Quando i dati sono presenti in un archivio dati centralizzato nel cloud, si creano e si distribuiscono pipeline di dati per generare in modo affidabile dati trasformati in base a una pianificazione gestibile e controllata e inserire quindi dati attendibili negli ambienti di produzione. La trasformazione dei dati in Azure Data Factory viene eseguita con attività di trasformazione come Hive, Pig, MapReduce ed Esecuzione batch di Azure Machine Learning e attività C# personalizzate eseguite in cluster Hadoop Azure HDInsight, VM di Azure Machine Learning o pool di VM di Azure Batch.
- **Monitorare le pipeline di dati da un'unica posizione**.

    A causa dell'eterogeneità dei dati, è importante avere una visualizzazione affidabile e completa dei servizi di archiviazione, elaborazione e spostamento dei dati. Data Factory consente di valutare rapidamente l'integrità delle pipeline di dati end-to-end, individuare i problemi ed eseguire le azioni correttive eventualmente necessarie. È possibile tenere traccia visivamente della derivazione dei dati e delle relazioni tra i dati in qualsiasi origine, nonché visualizzare in un singolo dashboard di monitoraggio un riepilogo cronologico completo dell'esecuzione dei processi, dell'integrità del sistema e delle dipendenze.

## <a name="how-does-it-work"></a>Come funziona? 
Un'istanza di Azure Data Factory include tre fasi di produzione di informazioni:

![Tre fasi di produzione di informazioni](media/data-factory-introduction/three-information-production-stages.png)

- **Connessione e raccolta**: in questa fase, i dati di varie origini vengono raccolti in un'unica posizione.
- **Trasformazione e arricchimento**: in questa fase, i dati raccolti vengono elaborati o trasformati.
- **Pubblicazione**: in questa fase, i dati vengono pubblicati in modo che possano essere utilizzati in strumenti di business intelligence, strumenti di analisi e altre applicazioni.

## <a name="key-components"></a>Componenti chiave
Una sottoscrizione di Azure può includere una o più istanze di Azure Data Factory (o data factory). Un'istanza di Azure Data Factory è costituita da quattro componenti chiave che insieme forniscono la piattaforma sulla base della quale è possibile comporre orchestrazioni di spostamento e trasformazione dei dati sia semplici che complesse per il proprio flusso di dati.

### <a name="activity"></a>Attività
Le attività definiscono le azioni da eseguire sui dati. Ad esempio, è possibile usare un'attività Copia per copiare i dati da un archivio dati all'altro. Allo stesso modo, è possibile usare un'attività Hive che esegue una query Hive su un cluster Azure HDInsight per trasformare o analizzare i dati. Data Factory supporta due tipi di attività: attività di spostamento dei dati e attività di trasformazione dei dati.

Ogni attività può avere zero o più set di dati di input e generare uno o più set di dati di output. 


### <a name="data-movement-activities"></a>Attività di spostamento dei dati
L'attività di copia in Data Factory esegue la copia dei dati da un archivio dati di origine a un archivio dati sink. Data Factory supporta gli archivi dati seguenti. I dati da qualsiasi origine possono essere scritti in qualsiasi sink. Fare clic su un archivio dati per informazioni su come copiare dati da e verso tale archivio.

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Per altre informazioni, vedere l'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Attività di trasformazione dei dati
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

Per altre informazioni, vedere l'articolo [Attività di trasformazione dei dati](data-factory-data-transformation-activities.md).

Per spostare dati da e verso un archivio dati che non è supportato dall'attività di copia o per trasformare i dati usando la propria logica, creare un' **attività .NET personalizzata**. Per i dettagli sulla creazione e l'uso di un'attività personalizzata, vedere l'articolo [Usare attività personalizzate in una pipeline di Azure Data Factory](data-factory-use-custom-activities.md).

### <a name="pipeline"></a>Pipeline
Una pipeline è un gruppo di attività, che insieme eseguono un'operazione. Una pipeline, ad esempio, può contenere un gruppo di attività che inserisce dati da un BLOB di Azure e quindi esegue una query Hive in un cluster HDInsight per partizionare i dati di log. La pipeline offre il vantaggio di poter gestire le attività come un set anziché singolarmente. È ad esempio possibile distribuire e pianificare la pipeline anziché distribuire e pianificare le attività separatamente.

### <a name="datasets"></a>Set di dati
I set di dati rappresentano strutture dei dati all'interno degli archivi dati e fanno semplicemente riferimento ai dati da usare nelle attività come input o output. Un set di dati BLOB di Azure, ad esempio, specifica il contenitore BLOB e la cartella nell'archivio BLOB di Azure da cui la pipeline dovrà leggere i dati. 

### <a name="linked-services"></a>Servizi collegati
I servizi collegati sono molto simili a stringhe di connessione e definiscono le informazioni necessarie per la connessione di Data Factory a risorse esterne. In sintesi, il set di dati rappresenta la struttura dei dati e il servizio collegato definisce la connessione all'origine dati.  In Data factory i servizi collegati vengono usati per i due scopi seguenti:

* Per rappresentare un **archivio dati** , inclusi, a titolo esemplificativo, un'istanza di SQL Server locale, un database Oracle, una condivisione file o un account di archiviazione BLOB di Azure. Vedere la sezione [Attività di spostamento dei dati](#data-movement-activities) per un elenco di archivi dati supportati.
* Per rappresentare una **risorsa di calcolo** che può ospitare l'esecuzione di un'attività. Ad esempio, l'attività HDInsightHive viene eseguita in un cluster HDInsight Hadoop. Vedere la sezione [Attività di spostamento dei dati](#data-transformation-activities) per un elenco di ambienti di calcolo supportati.

### <a name="relationship-between-data-factory-entities"></a>Relazioni tra le entità di Data Factory
![Diagramma: Data Factory, servizio di integrazione dei dati cloud - Concetti chiave](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**Figura 2.** Relazioni tra set di dati, attività, pipeline e servizio collegato

## <a name="supported-regions"></a>Aree supportate
È attualmente possibile creare data factory nelle aree **Stati Uniti occidentali**, **Stati Uniti orientali** ed **Europa settentrionale**. Una data factory può accedere ad archivi dati e servizi di calcolo in altre aree di Azure per spostare i dati tra archivi dati o elaborare i dati usando i servizi di calcolo.

Azure Data Factory stesso non archivia alcun dato. Consente di creare flussi basati sui dati per orchestrare lo spostamento di dati tra [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats) e l'elaborazione di dati usando i [servizi di calcolo](data-factory-compute-linked-services.md) in altre aree o in un ambiente locale. Consente anche di [monitorare e gestire i flussi di lavoro](data-factory-monitor-manage-pipelines.md) usando meccanismi a livello di codice e di interfaccia utente.

Anche se Data Factory è disponibile solo nelle aree **Stati Uniti occidentali**, **Stati Uniti orientali** ed **Europa settentrionale**, il servizio che consente lo spostamento dei dati in Data Factory è disponibile [a livello globale](data-factory-data-movement-activities.md#global) in alcune aree. Nel caso in cui un archivio dati si trovi dietro un firewall, i dati verranno spostati da un [Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) installato nell'ambiente locale.

Si supponga ad esempio che gli ambienti di calcolo, come un cluster Azure HDInsight e Azure Machine Learning, siano in esecuzione nell'area Europa occidentale. È possibile creare e usare un'istanza di Azure Data Factory in Europa settentrionale e usarla per pianificare processi negli ambienti di calcolo in Europa occidentale. Data Factory necessita di pochi secondi per attivare il processo nell'ambiente di calcolo, ma il tempo per l'esecuzione del processo nell'ambiente di calcolo non cambia.

In futuro si prevede che Azure Data Factory sia disponibile in ogni area geografica supportata da Azure.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come creare data factory con pipeline di dati, attenersi alle istruzioni dettagliate disponibili nelle esercitazioni seguenti:

| Esercitazione | Descrizione |
| --- | --- |
| [Spostare dati tra due archivi dati cloud](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |In questa esercitazione viene creata una data factory con una pipeline per **spostare i dati** dall'archivio BLOB al database SQL. |
| [Trasformare i dati usando cluster Hadoop](data-factory-build-your-first-pipeline.md) |Questa esercitazione mostra come compilare la prima istanza di Azure Data Factory con una pipeline di dati che **elabora i dati** eseguendo uno script Hive in un cluster Azure HDInsight (Hadoop). |
| [Spostare dati tra un archivio dati locale e un archivio dati cloud usando il gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) |In questa esercitazione viene creata una data factory con una pipeline che **sposta i dati** da un database di SQL Server **locale** a un BLOB di Azure. Come parte della procedura dettagliata, viene installato e configurato il gateway di gestione dati nel computer. |

