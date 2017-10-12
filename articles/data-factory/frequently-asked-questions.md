---
title: Domande frequenti su Azure Data Factory | Documentazione Microsoft
description: Domande frequenti su Azure Data Factory.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: shlo
ms.openlocfilehash: 95b088d2fdc331dc33e973172d32892693fcb648
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory - Domande frequenti
Questo articolo è valido per la versione 2 del servizio Azure Data Factory. Fornisce un elenco di domande frequenti (FAQ) e le relative risposte.  

## <a name="what-is-azure-data-factory"></a>Che cos'è Azure Data Factory? 
Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud che automatizza lo spostamento e la trasformazione dei dati. Analogamente a quanto avviene in uno stabilimento di produzione, in cui vengono usate attrezzature per trasformare le materie prime in prodotti finiti, Azure Data Factory orchestra i servizi esistenti che raccolgono i dati non elaborati e li trasforma in informazioni pronte per l'uso. Azure Data Factory consente di creare flussi di lavoro basati sui dati per spostare dati dagli archivi locali a quelli sul cloud e viceversa, nonché per elaborare o trasformare i dati usando servizi di calcolo come Azure HDInsight, Azure Data Lake Analytics e il runtime di esecuzione di SQL Server Integration Services (SSIS). Data Factory offre la possibilità di eseguire l'elaborazione dei dati in un servizio cloud basato su Azure oppure di utilizzare l'ambiente di calcolo self-hosted, ad esempio SSIS, SQL Server e Oracle. Dopo aver creato una pipeline che esegue l'azione necessaria, è possibile pianificarne l'esecuzione periodica (ogni ora, giorno, settimana e così via) oppure eseguire il trigger della pipeline all’occorrenza di un evento. Per altre informazioni, vedere l'[introduzione ad Azure Data Factory](introduction.md).

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

## <a name="what-is-integration-runtime"></a>Che cos'è il runtime di integrazione?
Il runtime di integrazione è l'infrastruttura di calcolo usata da Azure Data Factory per fornire le seguenti funzionalità di integrazione di dati in diversi ambienti di rete:

- **Spostamento dei dati**: spostare dati tra gli archivi dati nella rete pubblica e gli archivi dati nella rete privata (locale o VPN). Fornisce il supporto per i connettori predefiniti, la conversione dei formati, il mapping di colonne e il trasferimento di dati scalabile e ad alte prestazioni.
- **Attività di invio**: consente di inviare e monitorare le attività di trasformazione in esecuzione in diversi servizi di calcolo, come Azure HDInsight, Azure Machine Learning, il database SQL di Azure, SQL Server e altro ancora.
- **Esecuzione del pacchetto SSIS**: esegue in modo nativo i pacchetti SQL Server Integration Services (SSIS) in un ambiente di calcolo di Azure gestito.

Gli utenti possono distribuire una o più istanze del runtime di integrazione in base alla necessità per lo spostamento e la trasformazione di dati.  Il runtime di integrazione può essere eseguito in una rete pubblica di Azure o in una rete privata, ad esempio in locale, nella Rete virtuale di Azure o nel cloud privato virtuale (VPC) degli Amazon Web Services. 

Per altre informazioni, vedere il [Runtime di integrazione in Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Qual è il limite al numero di runtime di integrazione?
Non sono previsti limiti rigidi sul numero di istanze di runtime di integrazione che è possibile avere in una data factory. È stato posto tuttavia un limite al numero di core di macchina virtuale (VM) che il runtime di integrazione può utilizzare per ogni sottoscrizione per l'esecuzione del pacchetto SSIS. Per altre informazioni, vedere [Limiti della data factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="when-to-use-version-2-rather-than-version-1"></a>Quando utilizzare la versione 2, anziché la versione 1? 
Se si ha familiarità con Azure Data Factory, iniziare direttamente con la versione 2. Se si sta già usando la versione 1, ricompilare le data factory nella versione 2.

> [!WARNING]
> La versione 2 della data factory è in anteprima, non disponibile a livello generale (GA). Pertanto, non rientra nello stesso ambito di commitment del contratto di servizio (SLA) di Azure come la versione 1 della data factory, che è in fase GA. 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>Quali sono i concetti di livello superiore della versione 2?
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

## <a name="what-is-the-pricing-model-for-data-factory"></a>Qual è il modello di prezzi per Data Factory?
Per saperne di più sui prezzi di Azure Data Factory, vedere la pagina [Dettagli prezzi dle data factory](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="what-regions-support-azure-data-factory-version-2"></a>Quali regioni supportano la versione 2 di Azure Data Factory?
È attualmente possibile creare data factory di versione 2 nelle aree Stati Uniti orientali e Stati Uniti orientali 2. Tuttavia, una data factory può utilizzare un runtime di integrazione in un'altra area per spostare dati tra archivi dati, per le attività di invio nei confronti di servizi di calcolo o per l’invio di pacchetti SSIS.  Per altre informazioni, vedere [Posizioni della data factory](concepts-integration-runtime.md#integration-runtime-location).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Come è possibile rimanere aggiornati con le informazioni su Data Factory?
Per rimanere aggiornati con le informazioni su Azure Data Factory, utilizzare i seguenti siti:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Home page della documentazione](/azure/data-factory)
- [Home page prodotti](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Approfondimento tecnico 

### <a name="can-i-have-version-1-and-version-2-pipelines-run-side-by-side"></a>È possibile avere la pipeline di versione 1 e di versione 2 in esecuzione contemporaneamente?
No. Data factory di versione 2 o di versione 1 non possono disporre di entità (servizi collegati, set di dati, pipeline e così via) di altra versione.   

### <a name="do-i-still-need-to-define-datasets-in-version-2"></a>È necessario definire i set di dati nella versione 2?
Il set di dati non è più un'entità obbligatoria per la maggior parte delle attività. È necessario per le attività di copia, machine learning, ricerca, convalida e altre attività personalizzate che utilizzano lo schema e altre informazioni sui metadati del set di dati per la trasformazione. Il resto delle attività non richiede più set di dati.

### <a name="can-i-chain-two-activities-without-a-dataset-in-version-2"></a>È possibile concatenare due attività senza un set di dati nella versione 2?
Sì. È possibile concatenare le attività nella versione 2 senza necessità dei set di dati. La concatenazione delle attività avviene tramite la proprietà **dependsOn** nella definizione JSON della pipeline. 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>Tutte le attività di versione 1 sono supportate nella versione 2? 
Sì, sono supportate tutte le attività di versione 1

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>Come è possibile pianificare una pipeline di versione 2? 
È possibile utilizzare il trigger di pianificazione per pianificare una pipeline di versione 2. Questo utilizza una pianificazione tramite calendario reale e consente agli utenti di pianificare le pipeline periodicamente oppure utilizzando modelli ricorrenti basati sul calendario (ad esempio, ogni settimana di lunedì alle 6 PM e giovedì alle 9 PM). Per altre informazioni, vedere [Esecuzione e trigger della pipeline](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>È possibile passare parametri all’esecuzione del flusso di una pipeline nella versione 2?
Sì, i parametri sono un concetto di prima classe di livello superiore nella versione 2. È possibile definire i parametri a livello della pipeline e passare argomenti durante l'esecuzione della pipeline eseguita su richiesta o tramite un trigger.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>È possibile definire i valori predefiniti per i parametri della pipeline? 
Sì. È possibile definire i valori predefiniti per i parametri nelle pipeline. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-passed-to-a-pipeline-run"></a>Un'attività in una pipeline può utilizzare gli argomenti passati a un'esecuzione del flusso di una pipeline? 
Sì. Ogni attività all'interno della pipeline può utilizzare il valore del parametro passato all’esecuzione del flusso della pipeline con il costrutto `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Una proprietà di output di attività può essere utilizzata in un'altra attività? 
Sì. È possibile utilizzare un output di attività in un'attività successiva con il costrutto @activity.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Come gestire correttamente i valori null in un output di attività? 
È possibile utilizzare il costrutto `@coalesce` nelle espressioni per gestire correttamente i valori null. 

### <a name="can-i-use-retries-timeouts-at-the-activity-level-in-version-2"></a>È possibile utilizzare nuovi tentativi e timeout a livello di attività nella versione 2?
Sì. È possibile configurare i tentativi e i timeout a livello di attività per controllare l'esecuzione di attività nella versione 2, come nella versione 1. 

## <a name="next-steps"></a>Passaggi successivi
Per istruzioni dettagliate per la creazione di una data factory della versione 2, vedere le esercitazioni seguenti:

- [Guida introduttiva: creare una data factory](quickstart-create-data-factory-dot-net.md)
- [Esercitazione: copiare i dati nel cloud](tutorial-copy-data-dot-net.md)

