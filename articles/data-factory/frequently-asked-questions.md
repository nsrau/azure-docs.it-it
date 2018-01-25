---
title: 'Azure Data Factory: domande frequenti | Microsoft Docs'
description: Risposte alle domande frequenti su Azure Data Factory.
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
ms.date: 01/15/2018
ms.author: shlo
ms.openlocfilehash: cf98bb7fab4942955287e8e211e98b9da59472f6
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="azure-data-factory-faq"></a>Domande frequenti su Azure Data Factory
Questo articolo è valido per la versione 2 del servizio Azure Data Factory. Contiene le risposte alle domande frequenti su Data Factory.  

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere le [Domande frequenti su Data Factory versione 1](v1/data-factory-faq.md).

## <a name="what-is-azure-data-factory"></a>Che cos'è Azure Data Factory? 
Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud che automatizza lo spostamento e la trasformazione dei dati. Analogamente a quanto avviene in uno stabilimento di produzione, in cui vengono usate attrezzature per trasformare le materie prime in prodotti finiti, Azure Data Factory orchestra i servizi esistenti che raccolgono i dati non elaborati e li trasforma in informazioni pronte per l'uso. 

Usando Azure Data Factory, è possibile creare flussi di lavoro basati sui dati per spostare i dati tra archivi dati locali e cloud. È anche possibile elaborare e trasformare i dati usando servizi di calcolo come Azure HDInsight, Azure Data Lake Analytics e il runtime di integrazione di SQL Server Integration Services (SSIS). 

Con Data Factory, è possibile eseguire l'elaborazione dei dati in un servizio cloud basato su Azure oppure nell'ambiente di calcolo self-hosted, ad esempio SSIS, SQL Server o Oracle. Dopo aver creato una pipeline che esegue l'azione necessaria, è possibile pianificarne l'esecuzione periodica (ad esempio, ogni ora, giorno o settimana) oppure attivare la pipeline dall'occorrenza di un evento. Per altre informazioni, vedere l'[introduzione ad Azure Data Factory](introduction.md).

## <a name="whats-different-in-version-2"></a>Differenze nella versione 2
Azure Data Factory versione 2 si basa sul servizio di trasformazione e spostamento di dati di Azure Data Factory originale, aggiungendo un set più ampio di scenari di integrazione dati cloud-first. Azure Data Factory versione 2 offre le funzionalità seguenti:

- Flussi di controllo e scalabilità
- Distribuzione ed esecuzione di pacchetti SSIS in Azure

Dopo il rilascio della versione 1 è stato constatato che i clienti hanno la necessità di progettare complessi scenari di integrazione di dati ibridi che richiedono sia lo spostamento dei dati che l'elaborazione nel cloud, in locale e nelle macchine virtuali (VM) cloud. Questi requisiti introducono la necessità di trasferire ed elaborare i dati all'interno di ambienti di rete virtuale protetti e di aumentare il numero di istanze con la potenza di elaborazione su richiesta.

Mano a mano che le pipeline di dati diventano una parte essenziale della strategia di analisi business, è stato osservato che queste attività richiedono una pianificazione flessibile per supportare carichi di dati incrementali ed esecuzioni attivate da eventi. La crescente complessità fa sì che anche il servizio debba sempre più supportare i paradigmi comuni del flusso di lavoro che includono diramazione, cicli ed elaborazione condizionale.

Con la versione 2 è anche possibile eseguire la migrazione dei pacchetti SSIS esistenti nel cloud. Questa azione trasferisce in modalità lift-and-shift SSIS come servizio di Azure gestito in Data Factory, che utilizza una nuova funzionalità di runtime di integrazione. Avviando un runtime di integrazione SSIS nella versione 2, è possibile eseguire, gestire, monitorare e compilare pacchetti SSIS nel cloud.

### <a name="control-flows-and-scale"></a>Flussi di controllo e scalabilità 
Per supportare l'integrazione di flussi e criteri di integrazione diversi nel data warehouse moderno, Data Factory ha abilitato un nuovo modello flessibile di pipeline di dati che non è più associato ai dati delle serie temporali. Con questa versione è possibile modellare le istruzioni condizionali, creare un ramo nel flusso di controllo di una pipeline di dati e passare in modo esplicito i parametri all'interno dei flussi e tra i diversi flussi.

È ora possibile modellare qualsiasi stile di flusso necessario che è per l'integrazione di dati e che può essere inviato su richiesta o ripetutamente in una pianificazione. Alcuni flussi comuni, in precedenza non possibili, sono ora abilitati:   

- Flussi di controllo:
    - Concatenare attività in una sequenza all'interno di una pipeline.
    - Creare un ramo delle attività all'interno di una pipeline.
    - Parametri
        - Definire i parametri a livello della pipeline e passare gli argomenti mentre si richiama la pipeline su richiesta o da un trigger.
        - Le attività possono utilizzare gli argomenti passati alla pipeline.
    - Passaggio di stato personalizzato
        - Gli output delle attività che includono lo stato possono essere usati da un'attività successiva della pipeline.
    - Contenitori di ciclo
        - For-each 
- Flussi attivati da trigger:
    - Le pipeline possono essere attivate su richiesta o in base al tempo totale di esecuzione.
- Flussi delta:
    - Usare i parametri e definire il limite massimo per la copia delta durante lo spostamento delle tabelle delle dimensioni o di riferimento da un archivio relazionale in locale o nel cloud per caricare i dati nel lake. 

Per altre informazioni, vedere [Esercitazione: Flussi di controllo](tutorial-control-flow.md).

### <a name="deploy-ssis-packages-to-azure"></a>Distribuire i pacchetti SSIS in Azure 
Se si vuole spostare i carichi di lavoro SSIS, è possibile creare un'istanza di Data Factory versione 2 ed effettuare il provisioning di un runtime di integrazione SSIS di Azure. Il runtime di integrazione SSIS di Azure è un cluster completamente gestito di macchine virtuali (nodi) di Azure dedicate all'esecuzione di pacchetti SSIS nel cloud. Per istruzioni dettagliate, vedere l'esercitazione [Distribuire i pacchetti SSIS in Azure](tutorial-deploy-ssis-packages-azure.md). 
 

### <a name="sdks"></a>SDK
Per gli utenti Advanced alla ricerca di un'interfaccia programmatica, la versione 2 fornisce un ampio set di SDK che è possibile usare per creare, gestire o monitorare pipeline usando l'ambiente di sviluppo integrato preferito.

- **.NET SDK**: NET SDK è aggiornato per la versione 2. 
- **PowerShell**: i cmdlet PowerShell sono aggiornati per la versione 2. I cmdlet per la versione 2 hanno *DataFactoryV2* nel proprio nome. Ad esempio, *Get-AzureRmDataFactoryV2*. 
- **Python SDK**: questo SDK è una novità nella versione 2.
- **API REST**: l'API REST è aggiornata per la versione 2.  

Gli SDK che sono stati aggiornati per la versione 2 non sono compatibili con i client della versione 1. 

### <a name="monitoring"></a>Monitoraggio
Attualmente, la versione 2 supporta il monitoraggio dle data factory usando solo SDK. Il portale non dispone ancora del supporto per il monitoraggio dle data factory di versione 2. 

## <a name="what-is-integration-runtime"></a>Che cos'è il runtime di integrazione?
Il runtime di integrazione è l'infrastruttura di calcolo usata da Azure Data Factory per fornire le funzionalità di integrazione di dati seguenti in diversi ambienti di rete:

- **Spostamento dei dati**: spostare dati tra gli archivi dati nella rete pubblica e gli archivi dati nella rete privata (locale o rete privata virtuale). Fornisce il supporto per i connettori predefiniti, la conversione dei formati, il mapping di colonne e il trasferimento di dati scalabile e ad alte prestazioni.
- **Attività di invio**: consente di inviare e monitorare le attività di trasformazione in esecuzione in diversi servizi di calcolo, come Azure HDInsight, Azure Machine Learning, il database SQL di Azure, SQL Server e altro ancora.
- **Esecuzione del pacchetto SSIS**: esegue in modo nativo i pacchetti SSIS in un ambiente di calcolo di Azure gestito.

Gli utenti possono distribuire una o più istanze del runtime di integrazione in base alla necessità per lo spostamento e la trasformazione di dati. Il runtime di integrazione può essere eseguito in una rete pubblica di Azure o in una rete privata (locale, Rete virtuale di Azure o cloud privato virtuale di Amazon Web Services). 

Per altre informazioni, vedere il [Runtime di integrazione in Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Qual è il limite al numero di runtime di integrazione?
Non sono previsti limiti rigidi per il numero di istanze di runtime di integrazione che è possibile avere in una data factory. È stato posto tuttavia un limite al numero di core di VM che il runtime di integrazione può usare per ogni sottoscrizione per l'esecuzione del pacchetto SSIS. Per altre informazioni, vedere [Limiti della data factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="when-should-i-use-version-2-rather-than-version-1"></a>Quando è consigliabile usare la versione 2 invece della versione 1? 
Se si ha familiarità con Azure Data Factory, iniziare direttamente con la versione 2. Se si sta già usando la versione 1, ricompilare le data factory nella versione 2.

> [!WARNING]
> La versione 2 di Data Factory è in anteprima e non è disponibile a livello generale. Pertanto, non rientra nello stesso ambito di commitment del contratto di servizio (SLA) di Azure come la versione 1 della data factory, che è in fase GA. 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>Quali sono i concetti di livello superiore della versione 2?
Una sottoscrizione di Azure può includere una o più istanze di Azure Data Factory (o data factory). Azure Data Factory contiene quattro componenti chiave che interagiscono come piattaforma nella quale è possibile comporre flussi di lavoro basati sui dati con passaggi per lo spostamento e la trasformazione dei dati stessi.

### <a name="pipelines"></a>Pipeline
Una data factory può comprendere una o più pipeline. Una pipeline è un raggruppamento logico di attività per eseguire un'unità di lavoro, che insieme eseguono un'operazione. Una pipeline, ad esempio, può contenere un gruppo di attività che inseriscono dati da un BLOB di Azure e quindi eseguono una query Hive in un cluster HDInsight per partizionare i dati. Il vantaggio è che è possibile usare una pipeline per gestire le attività come set invece che singolarmente. È possibile concatenare le attività in una pipeline per usarle in modo sequenziale o indipendentemente in parallelo.

### <a name="activity"></a>Attività
Le attività rappresentano un passaggio di elaborazione in una pipeline. È ad esempio possibile usare un'attività di *copia* per copiare i dati da un archivio dati all'altro. Allo stesso modo, è possibile usare un'attività Hive che esegue una query Hive su un cluster Azure HDInsight per trasformare o analizzare i dati. Data Factory supporta tre tipi di attività: attività di spostamento dei dati, attività di trasformazione dei dati e attività di controllo.

### <a name="data-sets"></a>Set di dati
I set di dati rappresentano strutture dei dati all'interno degli archivi dati e fanno semplicemente riferimento ai dati da usare nelle attività come input o output. 

### <a name="linked-services"></a>Servizi collegati
I servizi collegati sono molto simili a stringhe di connessione e definiscono le informazioni necessarie per la connessione di Data Factory a risorse esterne. In sintesi, un servizio collegato definisce la connessione all'origine dati, mentre un set di dati rappresenta la struttura dei dati. Ad esempio, un servizio collegato di Archiviazione di Azure specifica la stringa per la connessione all'account di archiviazione di Azure. Un set di dati BLOB di Azure specifica il contenitore e la cartella BLOB che contengono i dati.

In Data Factory i servizi collegati hanno due scopi:

- Rappresentare un *archivio dati* che include, a titolo esemplificativo, un'istanza di SQL Server locale, un'istanza di database Oracle, una condivisione file o un account di archiviazione BLOB di Azure. Per un elenco di archivi dati supportati, vedere [Attività di copia in Azure Data Factory](copy-activity-overview.md).
- Per rappresentare una *risorsa di calcolo* che può ospitare l'esecuzione di un'attività. Ad esempio, l'attività HDInsight Hive viene eseguita in un cluster HDInsight Hadoop. Per un elenco delle attività di trasformazione e degli ambienti di calcolo supportati, vedere [Trasformare i dati in Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Trigger
I trigger rappresentano unità di elaborazione che determinano quando viene avviata l'esecuzione di una pipeline. Esistono diversi tipi di trigger per i diversi tipi di eventi. Per l'anteprima è supportato un trigger dell'utilità di pianificazione del tempo totale di esecuzione. 


### <a name="pipeline-runs"></a>Esecuzioni di pipeline
Un'esecuzione di pipeline è un'istanza dell'esecuzione di una pipeline. In genere si crea un'istanza di un'esecuzione di pipeline passando gli argomenti ai parametri definiti nella pipeline. È possibile passare gli argomenti manualmente o nella definizione di trigger.

### <a name="parameters"></a>Parametri
I parametri sono coppie chiave-valore in una configurazione di sola lettura. Si definiscono i parametri in una pipeline ed è possibile passare gli argomenti per i parametri definiti durante l'esecuzione da un contesto di esecuzione. Il contesto di esecuzione viene creato da un trigger o da una pipeline eseguita manualmente. Le attività all'interno della pipeline usano i valori dei parametri.

Un set di dati è un parametro fortemente tipizzato e un'entità che è possibile riutilizzare o a cui fare riferimento. Un'attività può fare riferimento a set di dati e può usare le proprietà contenute nella definizione del set di dati.

Anche un servizio collegato è un parametro fortemente tipizzato contenente le informazioni di connessione a un archivio dati o a un ambiente di calcolo. È anche un'entità che è possibile riutilizzare o a cui fare riferimento.

### <a name="control-flows"></a>Flussi di controllo
I flussi di controllo orchestrano le attività della pipeline che includono concatenamento di attività in una sequenza, diramazione, parametri definiti a livello di pipeline e argomenti passati quando si richiama la pipeline su richiesta o da un trigger. I flussi di controllo includono anche il passaggio di stati personalizzati e i contenitori di ciclo (iteratori for-each).


Per altre informazioni sui concetti relativi a Data Factory, vedere gli articoli seguenti:

- [Set di dati e servizi collegati](concepts-datasets-linked-services.md)
- [Pipeline e attività](concepts-pipelines-activities.md)
- [Runtime di integrazione](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Qual è il modello di prezzi per Data Factory?
Per informazioni dettagliate sui prezzi di Azure Data Factory, vedere [Dettagli prezzi di Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="what-regions-support-azure-data-factory-version-2"></a>Quali regioni supportano la versione 2 di Azure Data Factory?
È attualmente possibile creare data factory versione 2 nelle aree Stati Uniti orientali, Stati Uniti orientali 2 ed Europa occidentale. Una data factory può tuttavia usare un runtime di integrazione in un'altra area per spostare dati tra archivi dati, inviare attività ai servizi di calcolo o inviare pacchetti SSIS. Per altre informazioni, vedere [Posizioni della data factory](concepts-integration-runtime.md#integration-runtime-location).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Come è possibile rimanere aggiornati con le informazioni su Data Factory?
Per le informazioni più aggiornate su Azure Data Factory, andare ai siti seguenti:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Home page della documentazione](/azure/data-factory)
- [Home page prodotti](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Approfondimento tecnico 

### <a name="can-version-1-and-version-2-pipelines-run-side-by-side"></a>Le pipeline versione 1 e versione 2 possono essere eseguite contemporaneamente?
di serie Le data factory versione 1 e versione 2 non possono contenere entità (ad esempio, servizi collegati, set di dati o pipeline) dell'altra versione.   

### <a name="do-i-still-need-to-define-data-sets-in-version-2"></a>È necessario definire i set di dati nella versione 2?
Un set di dati non è più un'entità obbligatoria per la maggior parte delle attività. È necessario per le attività di copia, apprendimento automatico, ricerca, convalida e attività personalizzate che usano lo schema e altre informazioni sui metadati del set di dati per la trasformazione. Le altre attività non richiedono più i set di dati.

### <a name="can-i-chain-two-activities-without-a-data-set-in-version-2"></a>È possibile concatenare due attività senza un set di dati nella versione 2?
Sì. È possibile concatenare le attività nella versione 2 senza set di dati. La concatenazione delle attività avviene tramite la proprietà **dependsOn** nella definizione JSON della pipeline. 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>Tutte le attività di versione 1 sono supportate nella versione 2? 
Sì, tutte le attività della versione 1 sono supportate nella versione 2.

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>Come è possibile pianificare una pipeline di versione 2? 
È possibile utilizzare il trigger di pianificazione per pianificare una pipeline di versione 2. Il trigger usa una pianificazione tramite calendario reale ed è possibile usarlo per pianificare le pipeline periodicamente oppure usando modelli ricorrenti basati sul calendario (ad esempio, ogni settimana di lunedì alle 18 e di giovedì alle 21). Per altre informazioni, vedere [Esecuzione e trigger della pipeline](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>È possibile passare parametri all’esecuzione del flusso di una pipeline nella versione 2?
Sì, i parametri sono un concetto di prima classe di livello superiore nella versione 2. È possibile definire i parametri a livello di pipeline e passare gli argomenti mentre si esegue la pipeline su richiesta o usando un trigger.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>È possibile definire i valori predefiniti per i parametri della pipeline? 
Sì. È possibile definire i valori predefiniti per i parametri nelle pipeline. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Un'attività in una pipeline può utilizzare gli argomenti passati a un'esecuzione di pipeline? 
Sì. Ogni attività all'interno della pipeline può utilizzare il valore del parametro passato alla pipeline ed eseguito con il costrutto `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Una proprietà di output di attività può essere utilizzata in un'altra attività? 
Sì. È possibile utilizzare un output di attività in un'attività successiva con il costrutto `@activity`.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Come gestire correttamente i valori null in un output di attività? 
È possibile utilizzare il costrutto `@coalesce` nelle espressioni per gestire correttamente i valori null. 

### <a name="can-i-use-retry-and-timeout-at-the-activity-level-in-version-2"></a>È possibile usare i tentativi e il timeout a livello di attività nella versione 2?
Sì. Per controllare l'esecuzione di attività nella versione 2 come nella versione 1, è possibile configurare i tentativi e il timeout a livello di attività. 

## <a name="next-steps"></a>Passaggi successivi
Per istruzioni dettagliate per la creazione di una data factory della versione 2, vedere le esercitazioni seguenti:

- [Guida introduttiva: Creare una data factory](quickstart-create-data-factory-dot-net.md)
- [Esercitazione: Copiare i dati nel cloud](tutorial-copy-data-dot-net.md)

