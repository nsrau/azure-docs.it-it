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
ms.date: 09/22/2016
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: 0175f2877a59db214a38bfe27eca74748518a498


---
# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>Introduzione al servizio Azure Data Factory, un servizio di integrazione dati nel cloud
## <a name="what-is-azure-data-factory"></a>Che cos'è Azure Data Factory?
Data factory è un servizio di integrazione dei dati basato sul cloud che permette di automatizzare lo **spostamento** e la **trasformazione** dei dati. È possibile creare soluzioni di integrazione dei dati usando il servizio Data Factory che può inserire dati da diversi archivi dati, trasformare/elaborare i dati e pubblicare i dati risultanti negli archivi dati.

Il servizio Data Factory consente di creare pipeline di dati che spostano e trasformano i dati e quindi di eseguire le pipeline in base a una pianificazione specificata (ogni ora, ogni giorno, ogni settimana e così via). Offre anche viste avanzate per visualizzare la derivazione e le dipendenze tra le pipeline di dati e monitorare tutte le pipeline di dati da una singola visualizzazione unificata per individuare facilmente i problemi e configurare avvisi di monitoraggio.

![Diagramma: Panoramica di Data Factory, servizio di integrazione dei dati](./media/data-factory-introduction/what-is-azure-data-factory.png)
**Figura 1.**  Inserire i dati da svariate origini dati locali, prepararli, trasformarli e analizzarli e quindi pubblicare dati pronti per l'utilizzo.

## <a name="pipelines-and-activities"></a>Pipeline e attività
In una soluzione Data Factory si creano una o più **pipeline**di dati. Una pipeline è un raggruppamento logico di attività. Vengono usate per raggruppare in un'unità attività che insieme eseguono un'operazione.

**attività** definiscono le azioni da eseguire sui dati. Ad esempio, è possibile usare un'attività Copia per copiare i dati da un archivio dati all'altro. Allo stesso modo, è possibile usare un'attività Hive che esegue una query Hive su un cluster Azure HDInsight per trasformare o analizzare i dati. Data Factory supporta due tipi di attività: attività di spostamento dei dati e attività di trasformazione dei dati.

## <a name="data-movement-activities"></a>Attività di spostamento dei dati
[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Per altre informazioni dettagliate, vedere l'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) .

## <a name="data-transformation-activities"></a>Attività di trasformazione dei dati
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

Per altre informazioni dettagliate, vedere l'articolo [Attività di trasformazione dei dati](data-factory-data-transformation-activities.md) .

Per spostare dati da e verso un archivio dati che non è supportato dall'attività di copia o per trasformare i dati usando la propria logica, creare un' **attività .NET personalizzata**. Per i dettagli sulla creazione e l'uso di un'attività personalizzata, vedere l'articolo [Usare attività personalizzate in una pipeline di Azure Data Factory](data-factory-use-custom-activities.md).

## <a name="linked-services"></a>Servizi collegati
I servizi collegati definiscono le informazioni necessarie perché il servizio Data Factory si connetta a risorse esterne (ad esempio: Archiviazione di Azure, SQL Server locale, Azure HDInsight). In Data factory i servizi collegati vengono usati per i due scopi seguenti:

* Per rappresentare un **archivio dati** , inclusi, a titolo esemplificativo, un'istanza di SQL Server locale, un database Oracle, una condivisione file o un account di archiviazione BLOB di Azure. Vedere la sezione [Attività di spostamento dei dati](data-factory-data-movement-activities.md) per un elenco di archivi dati supportati.
* Per rappresentare una **risorsa di calcolo** che può ospitare l'esecuzione di un'attività. Ad esempio, l'attività HDInsightHive viene eseguita in un cluster HDInsight Hadoop. Vedere la sezione [Attività di spostamento dei dati](data-factory-data-transformation-activities.md) per un elenco di ambienti di calcolo supportati.

## <a name="datasets"></a>DATASETS
I servizi collegati collegano archivi dati a un'istanza di Azure Data Factory. I set di dati rappresentano strutture di dati negli archivi dati. Ad esempio, un servizio collegato di Archiviazione di Azure offre informazioni di connessione perché il servizio Data Factory si connetta a un account di archiviazione di Azure. Un set di dati BLOB di Azure specifica il contenitore BLOB e la cartella nell'archivio BLOB di Azure da cui la pipeline deve leggere i dati. Allo stesso modo, un servizio collegato di Azure SQL offre informazioni di connessione per un database SQL di Azure e un set di dati di Azure SQL specifica la tabella contenente i dati.   

## <a name="relationship-between-data-factory-entities"></a>Relazioni tra le entità di Data Factory
Le entità principali di Data factory interagiscono tra loro per definire i dati di input e di output, gli eventi di elaborazione e la pianificazione e le risorse necessarie per eseguire il flusso di dati desiderato.

![Diagramma: Data Factory, servizio di integrazione dei dati cloud - Concetti chiave](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**Figura 2.**  Relazioni tra set di dati, attività, pipeline e servizio collegato

Dopo aver acquisito familiarità con i quattro semplici concetti relativi a servizi collegati, set di dati, attività e pipeline si è pronti per iniziare. È possibile [creare la prima pipeline](data-factory-build-your-first-pipeline.md).

## <a name="supported-regions"></a>Aree supportate
È attualmente possibile creare data factory nelle aree **Stati Uniti occidentali**, **Stati Uniti orientali** ed **Europa settentrionale**. Una data factory può accedere ad archivi dati e servizi di calcolo in altre aree di Azure per spostare i dati tra archivi dati o elaborare i dati usando i servizi di calcolo.

Azure Data Factory stesso non archivia alcun dato. Consente di creare flussi basati sui dati per orchestrare lo spostamento di dati tra [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats) e l'elaborazione di dati usando i [servizi di calcolo](data-factory-compute-linked-services.md) in altre aree o in un ambiente locale. Consente anche di [monitorare e gestire i flussi di lavoro](data-factory-monitor-manage-pipelines.md) usando meccanismi a livello di codice e di interfaccia utente.

Anche se Azure Data Factory è disponibile solo nelle aree **Stati Uniti occidentali**, **Stati Uniti orientali** ed **Europa settentrionale**, il servizio che consente lo spostamento dei dati in Data Factory è disponibile [a livello globale](data-factory-data-movement-activities.md#global) in alcune aree. Nel caso in cui un archivio dati si trovi dietro un firewall, i dati verranno spostati da un [Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) installato nell'ambiente locale.

Si supponga ad esempio che gli ambienti di calcolo, come un cluster Azure HDInsight e Azure Machine Learning, siano in esecuzione nell'area Europa occidentale. È possibile creare e usare un'istanza di Azure Data Factory in Europa settentrionale e usarla per pianificare processi negli ambienti di calcolo in Europa occidentale. Data Factory necessita di pochi secondi per attivare il processo nell'ambiente di calcolo, ma il tempo per l'esecuzione del processo nell'ambiente di calcolo non cambia.

In futuro si prevede che Azure Data Factory sia disponibile in ogni area geografica supportata da Azure.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come creare data factory con pipeline di dati, attenersi alle istruzioni dettagliate disponibili nelle esercitazioni seguenti.

| Esercitazione | Descrizione |
| --- | --- |
| [Creare la prima data factory (panoramica)](data-factory-build-your-first-pipeline.md) |Questa esercitazione mostra come compilare la prima istanza di Azure Data Factory con una pipeline di dati che **elabora i dati** eseguendo uno script Hive in un cluster Azure HDInsight (Hadoop). |
| [Copy data from Blob Storage to SQL Database using Data Factory (Copiare dati dall'archivio BLOB al database SQL usando Data Factory)](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |In questa esercitazione viene creata una data factory con una pipeline per **spostare i dati** dall'archivio BLOB al database SQL. |
| [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) |In questa esercitazione viene creata una data factory con una pipeline che **sposta i dati** da un database di SQL Server **locale** a un BLOB di Azure. Come parte della procedura dettagliata, viene installato e configurato il gateway di gestione dati nel computer. |



<!--HONumber=Nov16_HO2-->


