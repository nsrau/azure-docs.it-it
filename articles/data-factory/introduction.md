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
ms.openlocfilehash: 7961f998b59991b47e1c22d826bf0a50fcd09099
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="introduction-to-azure-data-factory"></a>Introduzione a Data factory di Azure 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-introduction.md)
> * [Versione 2 - Anteprima](introduction.md)

Nel mondo dei Big Data, dati non elaborati e non organizzati vengono spesso archiviati in sistemi di archiviazione relazionali, non relazionali e di altro tipo. I dati non elaborati non hanno tuttavia intrinsecamente un contesto o significato appropriato per generare informazioni utili per gli analisti, i data scientist e i responsabili delle decisioni aziendali. 

I Big Data necessitano di un servizio che possa orchestrare e rendere operativi i processi per ottimizzare questi enormi archivi di dati non elaborati trasformandoli in informazioni aziendali di utilità pratica. Azure Data Factory è un servizio cloud gestito ideato per questi complessi progetti ibridi di estrazione, trasformazione e caricamento (ETL), di estrazione, caricamento e trasformazione (ELT) e di integrazione dei dati.

Si prenda ad esempio una società di giochi che raccoglie petabyte di log generati dai giochi nel cloud. La società intende analizzare questi log per ottenere informazioni sui clienti, tra cui preferenze, dati demografici e comportamento di utilizzo. Vuole anche identificare opportunità di up-selling e cross-selling, sviluppare nuove funzionalità interessanti per promuovere la crescita del business e fornire un'esperienza migliore ai clienti.

Per analizzare questi log, la società deve usare dati di riferimento presenti in un archivio dati locale, ad esempio le informazioni sui clienti, sui giochi e sulle campagne di marketing. La società intende usare questi dati presenti nell'archivio dati locale, integrandoli con i dati di log aggiuntivi disponibili in un archivio dati cloud. 

Per estrarre informazioni dettagliate, intende elaborare i dati uniti usando un cluster Spark nel cloud (Azure HDInsight) e infine pubblicare i dati trasformati in un data warehouse cloud, ad esempio Azure SQL Data Warehouse, per creare facilmente un report. La società vuole automatizzare il flusso di lavoro e monitorarlo e gestirlo in una pianificazione giornaliera. Intende anche eseguirlo quando vengono aggiunti file a un contenitore dell'archivio BLOB.

La piattaforma Azure Data Factory è in grado di gestire con successo scenari di dati di questo tipo. È un *servizio di integrazione di dati basato sul cloud che consente di creare flussi di lavoro basati sui dati nel cloud per orchestrare e automatizzare lo spostamento e la trasformazione dei dati stessi*. Con Azure Data Factory è possibile creare e pianificare flussi di lavoro basati sui dati (denominati "pipeline") che possono acquisire dati da archivi dati diversi. Azure Data Factory può elaborare e trasformare i dati usando servizi di calcolo, ad esempio Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning. 

È anche possibile pubblicare i dati di output in archivi dati, ad esempio Azure SQL Data Warehouse, per consentire alle applicazioni di business intelligence (BI) di utilizzarli. Infine, i dati non elaborati possono essere organizzati in archivi dati e data lake significativi per ottimizzare il processo decisionale aziendale usando Azure Data Factory.

![Vista generale di Data Factory](media/introduction/big-picture.png)

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale (GA), vedere [Introduzione a Data Factory versione 1](v1/data-factory-introduction.md).

## <a name="how-does-it-work"></a>Come funziona?
Le pipeline, ovvero i flussi di lavoro basati su dati, in Azure Data Factory eseguono in genere i quattro passaggi seguenti:

![Quattro passaggi del flusso di lavoro basato sui dati](media/introduction/four-steps-of-a-workflow.png)

### <a name="connect-and-collect"></a>Connettersi e raccogliere

Le aziende hanno dati di vari tipi archiviati in origini disomogenee in locale e nel cloud; dati strutturati, non strutturati e semistrutturati vengono tutti ricevuti a velocità e a intervalli diversi. 

Il primo passaggio per creare un sistema di produzione di informazioni consiste nel connettere tutte le origini di dati e di elaborazione necessarie, come servizi SaaS, database, condivisioni file, FTP e servizi Web. Il secondo passaggio prevede di spostare i dati in una posizione centralizzata in base alla necessità per l'elaborazione successiva.

Senza Data factory le grandi imprese devono creare componenti personalizzati per lo spostamento dei dati oppure scrivere servizi personalizzati per l'integrazione delle origini dati e delle elaborazioni. Integrare e gestire tali sistemi è costoso e complesso. Questi sistemi spesso non forniscono neppure il monitoraggio e gli avvisi di livello aziendale, oltre ai controlli che solo un servizio completamente gestito può offrire.

Con Data Factory è possibile usare l'[attività di copia](copy-activity-overview.md) in una pipeline di dati per spostare i dati dagli archivi dati di origine sia in locale che nel cloud a un archivio dati centralizzato nel cloud per analisi aggiuntive. È ad esempio possibile raccogliere i dati in Azure Data Lake Store e quindi trasformarli usando un servizio di calcolo Azure Data Lake Analytics oppure raccogliere i dati in un archivio BLOB di Azure e quindi trasformarli usando un cluster Hadoop Azure HDInsight.

### <a name="transform-and-enrich"></a>Trasformare e arricchire
Quando i dati raccolti sono presenti in un archivio dati centralizzato nel cloud, elaborarli o trasformarli tramite servizi di calcolo come Hadoop HDInsight, Spark, Data Lake Analytics e Machine Learning. L'obiettivo è generare in modo affidabile dati trasformati in una pianificazione gestibile e controllata in modo da alimentare gli ambienti di produzione con dati attendibili.

### <a name="publish"></a>Pubblica
Dopo che i dati non elaborati sono stati ottimizzati in un formato utilizzabile e pronto per il business, caricare i dati in Azure Data Warehouse, nel database SQL di Azure, in Azure CosmosDB o in qualsiasi motore di analisi a cui gli utenti business possono fare riferimento dai propri strumenti di Business Intelligence.

### <a name="monitor"></a>Monitorare
Dopo avere compilato e distribuito la pipeline di integrazione di dati, fornendo il valore business dai dati ottimizzati, monitorare le attività e le pipeline pianificate per determinare il tasso di operazioni riuscite e non riuscite. Azure Data Factory include supporto predefinito per il monitoraggio delle pipeline tramite Monitoraggio di Azure, API, PowerShell, Microsoft Operations Management Suite e i pannelli di integrità nel portale di Azure.

## <a name="top-level-concepts"></a>Concetti principali
Una sottoscrizione di Azure può includere una o più istanze di Azure Data Factory (o data factory). Azure Data Factory è costituito da quattro componenti chiave. Questi componenti forniscono la piattaforma nella quale è possibile comporre flussi di lavoro basati sui dati con passaggi per lo spostamento e la trasformazione dei dati stessi.

### <a name="pipeline"></a>Pipeline
Una data factory può comprendere una o più pipeline. Una pipeline è un raggruppamento logico di attività che esegue un'unità di lavoro. che insieme eseguono un'operazione. Una pipeline, ad esempio, può contenere un gruppo di attività che inserisce dati da un BLOB di Azure e quindi esegue una query Hive in un cluster HDInsight per partizionare i dati. 

La pipeline offre il vantaggio di poter gestire le attività come un set anziché singolarmente. Le attività in una pipeline possono essere concatenate per funzionare in modo sequenziale o indipendentemente in parallelo.

### <a name="activity"></a>Attività
Le attività rappresentano un passaggio di elaborazione in una pipeline. È ad esempio possibile usare un'attività di copia per copiare i dati da un archivio dati all'altro. Allo stesso modo è possibile usare un'attività Hive che esegue una query Hive in un cluster Azure HDInsight per trasformare o analizzare i dati. Data Factory supporta tre tipi di attività: attività di spostamento dei dati, attività di trasformazione dei dati e attività di controllo.

### <a name="datasets"></a>Set di dati
I set di dati rappresentano strutture dei dati all'interno degli archivi dati e fanno semplicemente riferimento ai dati da usare nelle attività come input o output. 

### <a name="linked-services"></a>Servizi collegati
I servizi collegati sono molto simili a stringhe di connessione e definiscono le informazioni necessarie per la connessione di Data Factory a risorse esterne. In sintesi, un servizio collegato definisce la connessione all'origine dati, mentre un set di dati rappresenta la struttura dei dati. Ad esempio, un servizio collegato di Archiviazione di Azure specifica una stringa per la connessione all'account di archiviazione di Azure. Un set di dati BLOB di Azure specifica il contenitore BLOB e la cartella che contiene i dati.

In Data factory i servizi collegati vengono usati per i due scopi seguenti:

- Per rappresentare un **archivio dati** che include, a titolo esemplificativo, un database di SQL Server locale, un database Oracle, una condivisione file o un account di archiviazione BLOB di Azure. Per un elenco degli archivi dati supportati, vedere l'articolo sull'[attività di copia](copy-activity-overview.md).

- Per rappresentare una **risorsa di calcolo** che può ospitare l'esecuzione di un'attività. Ad esempio, l'attività HDInsightHive viene eseguita in un cluster HDInsight Hadoop. Vedere l'articolo sulla [trasformazione di dati](transform-data.md) per un elenco di attività di trasformazione e degli ambienti di calcolo supportati.

### <a name="triggers"></a>Trigger
I trigger rappresentano l'unità di elaborazione che determina quando deve essere avviata l'esecuzione di una pipeline. Esistono diversi tipi di trigger per i diversi tipi di eventi. Per l'anteprima, Data Factory supporta il trigger dell'utilità di pianificazione basata sul tempo. 

### <a name="pipeline-runs"></a>Esecuzioni di pipeline
Un'esecuzione di pipeline è un'istanza dell'esecuzione della pipeline. Le istanze delle esecuzioni di pipeline vengono create in genere passando gli argomenti ai parametri definiti nelle pipeline. Gli argomenti possono essere passati manualmente o all'interno della definizione di trigger.

### <a name="parameters"></a>Parametri
I parametri sono coppie chiave-valore della configurazione di sola lettura.  I parametri sono definiti nella pipeline. Gli argomenti per i parametri definiti vengono passati durante l'esecuzione dal contesto di esecuzione creato da un trigger o da una pipeline eseguita manualmente. Le attività all'interno della pipeline usano i valori dei parametri.

Un set di dati è un parametro fortemente tipizzato e un'entità referenziabile/riutilizzabile. Un'attività può fare riferimento a set di dati e può usare le proprietà contenute nella definizione del set di dati.

Anche un servizio collegato è un parametro fortemente tipizzato contenente le informazioni di connessione a un archivio dati o a un ambiente di calcolo. È anche un'entità referenziabile/riutilizzabile.

### <a name="control-flow"></a>Flusso di controllo
Il flusso di controllo è un'orchestrazione delle attività della pipeline che include il concatenamento di attività in una sequenza, la creazione di rami, la definizione di parametri a livello di pipeline e il passaggio di argomenti durante la chiamata della pipeline su richiesta o da un trigger. Include anche il passaggio di stati personalizzati e i contenitori di ciclo, vale a dire gli iteratori For-Each.


Per altre informazioni sui concetti relativi a Data Factory, vedere gli articoli seguenti:

- [Set di dati e servizi collegati](concepts-datasets-linked-services.md)
- [Pipeline e attività](concepts-pipelines-activities.md)
- [Runtime di integrazione](concepts-integration-runtime.md)

## <a name="supported-regions"></a>Aree supportate

È attualmente possibile creare data factory nelle aree Stati Uniti orientali, Stati Uniti orientali 2 ed Europa occidentale. Una data factory può accedere ad archivi dati e servizi di calcolo in altre aree di Azure per spostare i dati tra archivi dati o elaborare i dati usando i servizi di calcolo.

Azure Data Factory stesso non archivia alcun dato. Consente di creare flussi di lavoro basati sui dati per orchestrare lo spostamento di dati tra archivi dati supportati e l'elaborazione di dati usando i servizi di calcolo in altre aree o in un ambiente locale. Consente anche di monitorare e gestire i flussi di lavoro usando meccanismi a livello di codice e di interfaccia utente.

Anche se Data Factory è disponibile solo nelle aree Stati Uniti orientali, Stati Uniti orientali 2 ed Europa occidentale, il servizio che consente lo spostamento dei dati in Data Factory è disponibile a livello globale in diverse aree. Se l'archivio dati è protetto da firewall, i dati verranno invece spostati da un runtime di integrazione self-hosted installato nell'ambiente locale.

Si supponga ad esempio che gli ambienti di calcolo, come un cluster Azure HDInsight e Azure Machine Learning, siano in esecuzione nell'area Europa occidentale. È possibile creare e usare un'istanza di Azure Data Factory in Stati Uniti orientali o Stati Uniti orientali 2 e usarla per pianificare processi negli ambienti di calcolo in Europa occidentale. Data Factory necessita di alcuni millisecondi per attivare il processo nell'ambiente di calcolo, ma il tempo per l'esecuzione del processo nell'ambiente di calcolo non cambia.

## <a name="compare-with-version-2"></a>Eseguire il confronto con la versione 2
Per un elenco delle differenze tra la versione 1 e la versione 2 del servizio Data Factory, vedere [Eseguire il confronto con la versione 1](compare-versions.md). 

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come creare una data factory seguendo le istruzioni dettagliate nei seguenti modelli di avvio rapido: [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md), [API REST ](quickstart-create-data-factory-rest-api.md) e nel portale di Azure. 
