---
title: 'Azure Data Factory: domande frequenti | Microsoft Docs'
description: Risposte alle domande frequenti su Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: ebe8745db06113d0508d86554bf031a4235c8e44
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045950"
---
# <a name="azure-data-factory-faq"></a>Domande frequenti su Azure Data Factory
Questo articolo risponde ad alcune domande frequenti su Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Che cos'è Azure Data Factory? 
Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud che automatizza lo spostamento e la trasformazione dei dati. Analogamente a quanto avviene in uno stabilimento di produzione, in cui vengono usate attrezzature per trasformare le materie prime in prodotti finiti, Azure Data Factory orchestra i servizi esistenti che raccolgono i dati non elaborati e li trasforma in informazioni pronte per l'uso. 

Usando Azure Data Factory, è possibile creare flussi di lavoro basati sui dati per spostare i dati tra archivi dati locali e cloud. È anche possibile elaborare e trasformare i dati usando servizi di calcolo come Azure HDInsight, Azure Data Lake Analytics e il runtime di integrazione di SQL Server Integration Services (SSIS). 

Con Data Factory, è possibile eseguire l'elaborazione dei dati in un servizio cloud basato su Azure oppure nell'ambiente di calcolo self-hosted, ad esempio SSIS, SQL Server o Oracle. Dopo aver creato una pipeline che esegue l'azione necessaria, è possibile pianificarne l'esecuzione periodica (ad esempio, ogni ora, giorno o settimana), l'esecuzione in base a intervalli di tempo oppure l'attivazione al verificarsi di un evento. Per altre informazioni, vedere l'[introduzione ad Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Flussi di controllo e scalabilità 
Per supportare i diversi flussi e schemi di integrazione nel data warehouse moderno, Data Factory consente la modellazione flessibile di pipeline di dati che comprende paradigmi di programmazione completi dei flussi di controllo inclusa l'esecuzione condizionale, la creazione di rami nelle pipeline di dati e il passaggio esplicito di parametri all'interno di questi flussi e tra un flusso e l'altro. Il flusso di controllo comprende anche la trasformazione dei dati attraverso l'invio di attività a motori di esecuzione esterni e attraverso funzionalità di gestione dei flussi di dati, incluso lo spostamento dati su larga scala, tramite l'attività di copia.

Data Factory consente di modellare liberamente qualsiasi stile di flusso che risulta necessario per l'integrazione dei dati e che può essere inviato su richiesta o ripetutamente in una pianificazione. Di seguito sono riportati alcuni dei flussi comuni abilitati:   

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

### <a name="transform-your-data-at-scale-with-code-free-pipelines"></a>Trasformare i dati su larga scala con pipeline senza codice
I nuovi strumenti basati su browser forniscono funzionalità di creazione e distribuzione di pipeline senza codice offrendo un'esperienza moderna e interattiva basata sul Web.

Per gli sviluppatori di dati visivi e i data engineer, l'interfaccia utente Web di Azure Data Factory è l'ambiente di progettazione senza codice da usare per compilare le pipeline. Completamente integrata con Visual Studio Online Git, l'interfaccia fornisce l'integrazione per CI/CD e lo sviluppo iterativo con opzioni di debug.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>SDK avanzati multipiattaforma per utenti esperti
Per gli utenti esperti alla ricerca di un'interfaccia programmatica, ADF V2 fornisce un ampio set di SDK che possono essere usati per creare, gestire e monitorare pipeline tramite l'IDE preferito:
1.  Python SDK
2.  Interfaccia della riga di comando PowerShell
3.  SDK per C#. Per interfacciarsi con ADF V2, gli utenti possono anche utilizzare le API REST nella documentazione.

### <a name="iterative-development-and-debugging-using-visual-tools"></a>Sviluppo e debug iterativi tramite strumenti visivi
Gli strumenti visivi di Azure Data Factory (ADF) consentono di eseguire attività di sviluppo e debug iterative. È possibile creare pipeline ed eseguire test usando la funzionalità Debug nel pannello Canvas della pipeline senza dover scrivere una sola riga di codice. È possibile visualizzare i risultati dell'esecuzione dei test nella finestra Output del pannello Canvas della pipeline. Al termine dell'esecuzione dei test, è possibile aggiungere alla pipeline altre attività e continuare il debug in modo iterativo. È anche possibile annullare l'esecuzione dei test in corso. Non è necessario pubblicare le modifiche apportate al servizio Data Factory prima di fare clic su Debug. Questo accorgimento è particolarmente utile negli scenari in cui si vuole verificare che le aggiunte o le modifiche apportate funzionino come previsto prima di aggiornare i flussi di lavoro di Data Factory negli ambienti di sviluppo, test e produzione. 

### <a name="deploy-ssis-packages-to-azure"></a>Distribuire i pacchetti SSIS in Azure 
Per spostare i carichi di lavoro SSIS, è possibile creare un'istanza di Data Factory ed effettuare il provisioning di un runtime di integrazione SSIS di Azure. Il runtime di integrazione SSIS di Azure è un cluster completamente gestito di macchine virtuali (nodi) di Azure dedicate all'esecuzione di pacchetti SSIS nel cloud. Per istruzioni dettagliate, vedere l'esercitazione [Distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). 
 
### <a name="sdks"></a>SDK
Per gli utenti esperti alla ricerca di un'interfaccia programmatica, ADF fornisce un ampio set di SDK che possono essere usati per creare, gestire o monitorare pipeline tramite l'IDE preferito. Sono supportati, tra gli altri, i linguaggi .NET, PowerShell, Python e REST.

### <a name="monitoring"></a>Monitoraggio
È possibile monitorare le istanze di Data Factory tramite PowerShell, SDK o gli strumenti di monitoraggio visivi nell'interfaccia utente del browser. I flussi personalizzati su richiesta, basati su trigger e su clock possono essere monitorati e gestiti in modo efficiente ed efficace. È possibile annullare le attività esistenti, visualizzare una sintesi degli errori, eseguire il drill-down per accedere ai messaggi di errore dettagliati ed eseguire il debug degli errori da un unico riquadro senza dover cambiare contesto o spostarsi tra le schermate. 

### <a name="new-features-for-ssis-in-adf"></a>Nuove funzionalità per SSIS in ADF
Dopo la versione di anteprima pubblica iniziale del 2017, Data Factory è stato integrato con le funzionalità seguenti per SSIS:

-   Supporto per altre tre configurazioni o varianti del database SQL di Azure per ospitare il catalogo SSIS di progetti/pacchetti (SSISDB):
-   Database SQL di Azure con endpoint del servizio di rete virtuale
-   Istanza gestita
-   Pool elastico
-   Supporto per la rete virtuale di Azure Resource Manager sopra la rete virtuale classica che sarà deprecata in futuro. In questo modo, è possibile inserire o aggiungere il runtime di integrazione SSIS di Azure in una rete virtuale configurata per il database SQL di Azure con endpoint del servizio di rete virtuale, istanza gestita e accesso ai dati locali. Per informazioni, vedere: https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network. 
-   Supporto per l'autenticazione Azure Active Directory (AAD) sopra l'autenticazione SQL per la connessione a SSISDB. In questo modo, è possibile usare l'autenticazione AAD con l'identità del servizio gestita di ADF.
-   Supporto per portare la licenza di SQL Server locale in modo da ottenere un notevole risparmio sui costi grazie all'opzione Vantaggio Azure Hybrid (AHB).
-   Supporto per l'edizione Enterprise del runtime di integrazione SSIS di Azure che consente di usare funzionalità avanzate o di livello Premium, configurazione personalizzata per installare estensioni e componenti aggiuntivi ed ecosistema di terze parti. Per informazioni, vedere: https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/. 
-   Maggiore integrazione di SSIS in ADF per attivare o richiamare le principali attività di esecuzione del pacchetto SSIS nelle pipeline ADF e pianificarle tramite SSMS. Per informazioni, vedere: https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/. 


## <a name="what-is-integration-runtime"></a>Che cos'è il runtime di integrazione?
Il runtime di integrazione è l'infrastruttura di calcolo usata da Azure Data Factory per fornire le funzionalità di integrazione di dati seguenti in diversi ambienti di rete:

- **Spostamento dati**: per lo spostamento dati, il runtime di integrazione sposta i dati tra gli archivi dati di origine e di destinazione, fornendo al tempo stesso il supporto per connettori predefiniti, conversione di formati, mapping di colonne e trasferimenti di dati efficienti e ridimensionabili.
- **Attività di invio**: per la trasformazione, il runtime di integrazione fornisce le funzionalità per l'esecuzione nativa di pacchetti SSIS.
- **Esecuzione del pacchetto SSIS**: esegue in modo nativo i pacchetti SSIS in un ambiente di calcolo di Azure gestito. Il runtime di integrazione supporta anche l'invio e il monitoraggio delle attività di trasformazione in esecuzione in diversi servizi di calcolo, ad esempio Azure HDInsight, Azure Machine Learning, database SQL di Azure, SQL Server e altro ancora.

Gli utenti possono distribuire una o più istanze del runtime di integrazione in base alla necessità per lo spostamento e la trasformazione di dati. Il runtime di integrazione può essere eseguito in una rete pubblica di Azure o in una rete privata (locale, Rete virtuale di Azure o cloud privato virtuale di Amazon Web Services). 

Per altre informazioni, vedere il [Runtime di integrazione in Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Qual è il limite al numero di runtime di integrazione?
Non sono previsti limiti rigidi per il numero di istanze di runtime di integrazione che è possibile avere in una data factory. È stato posto tuttavia un limite al numero di core di VM che il runtime di integrazione può usare per ogni sottoscrizione per l'esecuzione del pacchetto SSIS. Per altre informazioni, vedere [Limiti della data factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Quali sono i principali concetti su cui si basa Azure Data Factory?
Una sottoscrizione di Azure può includere una o più istanze di Azure Data Factory (o data factory). Azure Data Factory contiene quattro componenti chiave che interagiscono come piattaforma nella quale è possibile comporre flussi di lavoro basati sui dati con passaggi per lo spostamento e la trasformazione dei dati stessi.

### <a name="pipelines"></a>Pipeline
Una data factory può comprendere una o più pipeline. Una pipeline è un raggruppamento logico di attività per eseguire un'unità di lavoro, che insieme eseguono un'operazione. Una pipeline, ad esempio, può contenere un gruppo di attività che inseriscono dati da un BLOB di Azure e quindi eseguono una query Hive in un cluster HDInsight per partizionare i dati. Il vantaggio è che è possibile usare una pipeline per gestire le attività come set invece che singolarmente. È possibile concatenare le attività in una pipeline per usarle in modo sequenziale o indipendentemente in parallelo.

### <a name="activity"></a>Attività
Le attività rappresentano un passaggio di elaborazione in una pipeline. È ad esempio possibile usare un'attività di *copia* per copiare i dati da un archivio dati all'altro. Allo stesso modo, è possibile usare un'attività Hive che esegue una query Hive su un cluster Azure HDInsight per trasformare o analizzare i dati. Data Factory supporta tre tipi di attività: attività di spostamento dei dati, attività di trasformazione dei dati e attività di controllo.

### <a name="datasets"></a>Set di dati
I set di dati rappresentano strutture dei dati all'interno degli archivi dati e fanno semplicemente riferimento ai dati da usare nelle attività come input o output. 

### <a name="linked-services"></a>Servizi collegati
I servizi collegati sono molto simili a stringhe di connessione e definiscono le informazioni necessarie per la connessione di Data Factory a risorse esterne. In sintesi, un servizio collegato definisce la connessione all'origine dati, mentre un set di dati rappresenta la struttura dei dati. Ad esempio, un servizio collegato di Archiviazione di Azure specifica la stringa per la connessione all'account di archiviazione di Azure. Un set di dati BLOB di Azure specifica il contenitore e la cartella BLOB che contengono i dati.

In Data Factory i servizi collegati hanno due scopi:

- Rappresentare un *archivio dati* che include, a titolo esemplificativo, un'istanza di SQL Server locale, un'istanza di database Oracle, una condivisione file o un account di archiviazione BLOB di Azure. Per un elenco di archivi dati supportati, vedere [Attività di copia in Azure Data Factory](copy-activity-overview.md).
- Per rappresentare una *risorsa di calcolo* che può ospitare l'esecuzione di un'attività. Ad esempio, l'attività HDInsight Hive viene eseguita in un cluster HDInsight Hadoop. Per un elenco delle attività di trasformazione e degli ambienti di calcolo supportati, vedere [Trasformare i dati in Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Trigger
I trigger rappresentano unità di elaborazione che determinano quando viene avviata l'esecuzione di una pipeline. Esistono diversi tipi di trigger per i diversi tipi di eventi. 

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

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Come è possibile avere sempre a disposizione le informazioni più aggiornate su Data Factory?
Per le informazioni più aggiornate su Azure Data Factory, andare ai siti seguenti:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Home page della documentazione](/azure/data-factory)
- [Home page prodotti](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Approfondimento tecnico 

### <a name="how-can-i-schedule-a-pipeline"></a>Come è possibile pianificare una pipeline? 
Per pianificare una pipeline, è possibile usare il trigger di pianificazione o quello relativo agli intervalli di tempo. Il trigger usa una pianificazione tramite calendario reale ed è possibile usarlo per pianificare le pipeline periodicamente oppure usando modelli ricorrenti basati sul calendario (ad esempio, ogni settimana di lunedì alle 18 e di giovedì alle 21). Per altre informazioni, vedere [Esecuzione e trigger della pipeline](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>È possibile passare parametri all'esecuzione di una pipeline?
Sì, i parametri sono uno dei principali concetti su cui si basa ADF. È possibile definire i parametri a livello di pipeline e passare gli argomenti mentre si esegue la pipeline su richiesta o usando un trigger.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>È possibile definire i valori predefiniti per i parametri della pipeline? 
Sì. È possibile definire i valori predefiniti per i parametri nelle pipeline. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Un'attività in una pipeline può utilizzare gli argomenti passati a un'esecuzione di pipeline? 
Sì. Ogni attività all'interno della pipeline può utilizzare il valore del parametro passato alla pipeline ed eseguito con il costrutto `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Una proprietà di output di attività può essere utilizzata in un'altra attività? 
Sì. È possibile utilizzare un output di attività in un'attività successiva con il costrutto `@activity`.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Come gestire correttamente i valori null in un output di attività? 
È possibile utilizzare il costrutto `@coalesce` nelle espressioni per gestire correttamente i valori null. 

## <a name="next-steps"></a>Passaggi successivi
Per istruzioni dettagliate per la creazione di una data factory, vedere le esercitazioni seguenti:

- [Guida introduttiva: Creare una data factory](quickstart-create-data-factory-dot-net.md)
- [Esercitazione: Copiare i dati nel cloud](tutorial-copy-data-dot-net.md)
