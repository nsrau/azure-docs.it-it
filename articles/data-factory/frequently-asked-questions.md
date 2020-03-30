---
title: 'Azure Data Factory: domande frequentiAzure Data Factory: Frequently asked questions '
description: Risposte alle domande frequenti su Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 34972e70039fef17161bdef66f64278cbabf908f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130809"
---
# <a name="azure-data-factory-faq"></a>Domande frequenti su Azure Data Factory
Questo articolo risponde ad alcune domande frequenti su Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Che cos'è Azure Data Factory? 
Data Factory è un servizio ETL completamente gestito, basato sul cloud e basato sull'integrazione dei dati che automatizza lo spostamento e la trasformazione dei dati. Analogamente a quanto avviene in uno stabilimento di produzione, in cui vengono usate attrezzature per trasformare le materie prime in prodotti finiti, Azure Data Factory orchestra i servizi esistenti che raccolgono i dati non elaborati e li trasforma in informazioni pronte per l'uso. 

Usando Azure Data Factory, è possibile creare flussi di lavoro basati sui dati per spostare i dati tra archivi dati locali e cloud. È inoltre possibile elaborare e trasformare i dati con i flussi di dati. ADF supporta anche motori di calcolo esterni per trasformazioni codificate a mano usando servizi di calcolo come Azure HDInsight, Azure Databricks e il runtime di integrazione di SQL Server Integration Services (SSIS). 

Con Data Factory, è possibile eseguire l'elaborazione dei dati in un servizio cloud basato su Azure oppure nell'ambiente di calcolo self-hosted, ad esempio SSIS, SQL Server o Oracle. Dopo aver creato una pipeline che esegue l'azione necessaria, è possibile pianificarne l'esecuzione periodica (ogni ora, giornaliera o settimanale), ad esempio la pianificazione dell'intervallo di tempo o l'attivazione della pipeline da un'occorrenza dell'evento. Per altre informazioni, vedere l'[introduzione ad Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Flussi di controllo e scalabilità 
Per supportare i diversi flussi e modelli di integrazione nel data warehouse moderno, Data Factory consente una modellazione flessibile della pipeline di dati. Ciò comporta paradigmi di programmazione del flusso di controllo completo, che includono l'esecuzione condizionale, la diramazione nelle pipeline di dati e la possibilità di passare in modo esplicito i parametri all'interno e tra questi flussi. Il flusso di controllo include anche la trasformazione dei dati tramite l'invio di attività a motori di esecuzione esterni e le funzionalità del flusso di dati, incluso lo spostamento dei dati su larga scala, tramite l'attività di copia.

Data Factory consente di modellare liberamente qualsiasi stile di flusso che risulta necessario per l'integrazione dei dati e che può essere inviato su richiesta o ripetutamente in una pianificazione. Di seguito sono riportati alcuni dei flussi comuni abilitati:   

- Flussi di controllo:
    - Le attività possono essere concatenate in una sequenza all'interno di una pipeline.
    - Le attività possono essere ramificate all'interno di una pipeline.
    - Parametri
        - I parametri possono essere definiti a livello di pipeline e gli argomenti possono essere passati mentre si richiama la pipeline su richiesta o da un trigger.
        - Le attività possono utilizzare gli argomenti passati alla pipeline.
    - Passaggio dello stato personalizzato:Custom state passing:
        - Gli output dell'attività, incluso lo stato, possono essere utilizzati da un'attività successiva nella pipeline.
    - Contenitori a ciclo:
        - L'attività foreach eseguirà l'iterazione su una raccolta specificata di attività in un ciclo. 
- Flussi attivati da trigger:
    - Le pipeline possono essere attivate su richiesta o in base al tempo totale di esecuzione.
- Flussi delta:
    - I parametri possono essere utilizzati per definire il contrassegno di livello elevato per la copia delta durante lo spostamento di tabelle di dimensioni o di riferimento da un archivio relazionale, locale o nel cloud, per caricare i dati nel lago. 

Per altre informazioni, vedere [Esercitazione: Flussi di controllo](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Dati trasformati su larga scala con pipeline senza codice
I nuovi strumenti basati su browser forniscono funzionalità di creazione e distribuzione di pipeline senza codice offrendo un'esperienza moderna e interattiva basata sul Web.

Per gli sviluppatori di dati visivi e gli ingegneri dei dati, l'interfaccia utente Web di Data Factory è l'ambiente di progettazione senza codice che verrà usato per creare pipeline. È completamente integrato con Visual Studio Online Git e fornisce l'integrazione per CI/CD e lo sviluppo iterativo con opzioni di debug.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>SDK multipiattaforma avanzati per utenti esperti
Data Factory V2 fornisce un set completo di SDK che possono essere usati per creare, gestire e monitorare le pipeline usando l'IDE preferito, tra cui:Data Factory V2 provides a rich set of SDKs that can be used to author, manage, and monitor pipelines by using your favorite IDE, including:
* Python SDK
* Interfaccia di comando di PowerShell
* SDK per C#

Gli utenti possono anche usare le API REST documentate per interfacciarsi con Data Factory V2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Sviluppo iterativo e debug tramite strumenti visivi
Gli strumenti visivi di Azure Data Factory consentono lo sviluppo iterativo e il debug. È possibile creare le pipeline ed eseguire test utilizzando la funzionalità **Debug** nell'area di disegno della pipeline senza scrivere una singola riga di codice. È possibile visualizzare i risultati delle esecuzioni dei test nella finestra **Output** dell'area di disegno della pipeline. Al termine dell'esecuzione dei test, è possibile aggiungere altre attività alla pipeline e continuare il debug in modo iterativo. È inoltre possibile annullare le esecuzioni dei test dopo che sono in corso. 

Non è necessario pubblicare le modifiche nel servizio Data Factory prima di selezionare **Debug**. Ciò è utile negli scenari in cui si desidera assicurarsi che le nuove aggiunte o modifiche funzionino come previsto prima di aggiornare i flussi di lavoro della data factory in ambienti di sviluppo, test o produzione. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Possibilità di distribuire pacchetti SSIS in AzureAbility to deploy SSIS packages to Azure 
Per spostare i carichi di lavoro SSIS, è possibile creare un'istanza di Data Factory ed effettuare il provisioning di un runtime di integrazione SSIS di Azure. Un runtime di integrazione Azure-SSIS è un cluster completamente gestito di macchine virtuali di Azure (nodi) dedicate all'esecuzione dei pacchetti SSIS nel cloud. Per istruzioni dettagliate, vedere l'esercitazione [Distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). 
 
### <a name="sdks"></a>SDK
Se si è un utente avanzato e si cerca un'interfaccia a livello di codice, Data Factory fornisce un set completo di SDK che è possibile usare per creare, gestire o monitorare le pipeline utilizzando l'IDE preferito. Sono supportati, tra gli altri, i linguaggi .NET, PowerShell, Python e REST.

### <a name="monitoring"></a>Monitoraggio
È possibile monitorare le istanze di Data Factory tramite PowerShell, SDK o gli strumenti di monitoraggio visivi nell'interfaccia utente del browser. È possibile monitorare e gestire i flussi personalizzati su richiesta, basati su trigger e basati sull'orologio in modo efficiente ed efficace. Annullare le attività esistenti, visualizzare gli errori a colpo d'occhio, eseguire il drill-down per ottenere messaggi di errore dettagliati ed eseguire il debug dei problemi, il tutto da un singolo riquadro di vetro senza cambiare contesto o spostarsi avanti e indietro tra le schermate. 

### <a name="new-features-for-ssis-in-data-factory"></a>Nuove funzionalità per SSIS in Data Factory
Dalla versione iniziale di anteprima pubblica nel 2017, Data Factory ha aggiunto le seguenti funzionalità per SSIS:

-    Supporto per altre tre configurazioni/varianti del database SQL di Azure per ospitare il database SSIS (SSISDB) di progetti/pacchetti:Support for three more configurations/variants of Azure SQL Database to host the SSIS database (SSISDB) of projects/packages:
-    Database SQL con endpoint del servizio di rete virtuale
-    Istanza gestita
-    Pool elastico
-    Supporto per una rete virtuale di Azure Resource Manager in cima a una rete virtuale classica da predecidere in futuro, che consente di inserire/aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale configurata per il database SQL con endpoint del servizio di rete virtuale/MI/accesso ai dati locali. Per altre informazioni, vedere Anche Aggiungere un runtime di [integrazione Azure-SSIS a una rete virtuale.](join-azure-ssis-integration-runtime-virtual-network.md)
-    Supporto per l'autenticazione di Azure Active Directory (Azure AD) e l'autenticazione SQL per la connessione a SSISDB, consentendo l'autenticazione di Azure AD con l'identità gestita di Data Factory per le risorse di AzureSupport for Azure Active Directory (Azure AD) authentication and SQL authentication to connect to the SSISDB, allowing Azure AD authentication with your Data Factory managed identity for Azure resources
-    Supporto per portare la propria licenza di SQL Server locale per ottenere notevoli risparmi sui costi dall'opzione Vantaggi di Azure Hybrid
-    Supporto per Enterprise Edition del runtime di integrazione Azure-SSIS che consente di usare funzionalità avanzate/premium, un'interfaccia di installazione personalizzata per installare componenti/estensioni aggiuntivi e un ecosistema di partner. Per ulteriori informazioni, vedere anche [Enterprise Edition, Custom Setup ed 3rd Party Extensibility for SSIS in ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-    Integrazione più approfondita di SSIS in Data Factory che consente di richiamare/attivare le attività Di primo livello Execute SSIS Package nelle pipeline di Data Factory e di pianificarle tramite SSMS. Per ulteriori informazioni, vedere anche [Modernizzare ed estendere i flussi di lavoro ETL/ELT con le attività SSIS nelle pipeline ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Che cos'è il runtime di integrazione?
Il runtime di integrazione è l'infrastruttura di calcolo usata da Azure Data Factory per fornire le funzionalità di integrazione dei dati seguenti in vari ambienti di rete:The integration runtime is the compute infrastructure that Azure Data Factory uses to provide the following data integration capabilities across various network environments:

- **Spostamento dei dati**: per lo spostamento dei dati, il runtime di integrazione sposta i dati tra gli archivi dati di origine e di destinazione, fornendo al contempo il supporto per i connettori incorporati, la conversione dei formati, il mapping delle colonne e il trasferimento dei dati con prestazioni e scalabili.
- **Attività di**invio: per la trasformazione, il runtime di integrazione fornisce la funzionalità per eseguire in modo nativo i pacchetti SSIS.
- **Esegui pacchetti SSIS:** il runtime di integrazione esegue in modo nativo i pacchetti SSIS in un ambiente di calcolo di Azure gestito. Il runtime di integrazione supporta anche l'invio e il monitoraggio delle attività di trasformazione in esecuzione in un'ampia gamma di servizi di calcolo, ad esempio Azure HDInsight, Azure Machine Learning, SQL Database e SQL Server.The integration runtime also supports dispatching and monitoring transformation activities running on a variety of compute services, such as Azure HDInsight, Azure Machine Learning, SQL Database, and SQL Server.

È possibile distribuire una o più istanze del runtime di integrazione in base alle esigenze per spostare e trasformare i dati. Il runtime di integrazione può essere eseguito in una rete pubblica di Azure o in una rete privata (locale, rete virtuale di Azure o cloud privato virtuale di Amazon Web Services [VPC]). 

Per altre informazioni, vedere [Runtime di integrazione in Azure Data Factory.For](concepts-integration-runtime.md)more information, see Integration runtime in Azure Data Factory.

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Qual è il limite al numero di runtime di integrazione?
Non sono previsti limiti rigidi per il numero di istanze di runtime di integrazione che è possibile avere in una data factory. È stato posto tuttavia un limite al numero di core di VM che il runtime di integrazione può usare per ogni sottoscrizione per l'esecuzione del pacchetto SSIS. Per altre informazioni, vedere [Limiti della data factory](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Quali sono i principali concetti su cui si basa Azure Data Factory?
Una sottoscrizione di Azure può includere una o più istanze di Azure Data Factory (o data factory). Azure Data Factory contiene quattro componenti chiave che interagiscono come piattaforma nella quale è possibile comporre flussi di lavoro basati sui dati con passaggi per lo spostamento e la trasformazione dei dati stessi.

### <a name="pipelines"></a>Pipeline
Una data factory può comprendere una o più pipeline. Una pipeline è un raggruppamento logico di attività per eseguire un'unità di lavoro, L'insieme delle attività di una pipeline esegue un'operazione. Una pipeline, ad esempio, può contenere un gruppo di attività che inseriscono dati da un BLOB di Azure e quindi eseguono una query Hive in un cluster HDInsight per partizionare i dati. Il vantaggio è che è possibile usare una pipeline per gestire le attività come set invece che singolarmente. È possibile concatenare le attività in una pipeline per usarle in modo sequenziale o indipendentemente in parallelo.

### <a name="data-flows"></a>Flussi di dati
I flussi di dati sono oggetti compilati visivamente in Data Factory che trasformano i dati su larga scala nei servizi Spark back-end. Non è necessario comprendere la programmazione o gli interni Spark. È sufficiente progettare l'intento di trasformazione dei dati utilizzando grafici (mapping) o fogli di calcolo (Wrangling).

### <a name="activities"></a>attività
Le attività rappresentano un passaggio di elaborazione in una pipeline. Ad esempio, è possibile usare un'attività Copia per copiare i dati da un archivio dati a un altro. Allo stesso modo, è possibile usare un'attività Hive che esegue una query Hive su un cluster Azure HDInsight per trasformare o analizzare i dati. Data Factory supporta tre tipi di attività: attività di spostamento dei dati, attività di trasformazione dei dati e attività di controllo.

### <a name="datasets"></a>Set di dati
I set di dati rappresentano strutture dei dati all'interno degli archivi dati e fanno semplicemente riferimento ai dati da usare nelle attività come input o output. 

### <a name="linked-services"></a>Servizi collegati
I servizi collegati sono molto simili a stringhe di connessione e definiscono le informazioni necessarie per la connessione di Data Factory a risorse esterne. Considerarlo in questo modo: un servizio collegato definisce la connessione all'origine dati e un set di dati rappresenta la struttura dei dati. Ad esempio, un servizio collegato di Archiviazione di Azure specifica la stringa per la connessione all'account di archiviazione di Azure. Un set di dati BLOB di Azure specifica ora il contenitore BLOB e la cartella che contiene i dati.

In Data Factory i servizi collegati hanno due scopi:

- Rappresentare un *archivio dati* che include, a titolo esemplificativo, un'istanza di SQL Server locale, un'istanza di database Oracle, una condivisione file o un account di archiviazione BLOB di Azure. Per un elenco di archivi dati supportati, vedere [Attività di copia in Azure Data Factory](copy-activity-overview.md).
- Per rappresentare una *risorsa di calcolo* che può ospitare l'esecuzione di un'attività. Ad esempio, l'attività HDInsight Hive viene eseguita in un cluster HDInsight Hadoop. Per un elenco delle attività di trasformazione e degli ambienti di calcolo supportati, vedere [Trasformare i dati in Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Trigger
I trigger rappresentano unità di elaborazione che determinano quando viene avviata l'esecuzione di una pipeline. Esistono diversi tipi di trigger per i diversi tipi di eventi. 

### <a name="pipeline-runs"></a>Esecuzioni di pipeline
Un'esecuzione di pipeline è un'istanza dell'esecuzione di una pipeline. In genere si crea un'istanza di un'esecuzione di pipeline passando gli argomenti ai parametri definiti nella pipeline. È possibile passare gli argomenti manualmente o nella definizione di trigger.

### <a name="parameters"></a>Parametri
I parametri sono coppie chiave-valore in una configurazione di sola lettura.Si definiscono i parametri in una pipeline ed è possibile passare gli argomenti per i parametri definiti durante l'esecuzione da un contesto di esecuzione. Il contesto di esecuzione viene creato da un trigger o da una pipeline eseguita manualmente. Le attività all'interno della pipeline usano i valori dei parametri.

Un set di dati è un parametro fortemente tipizzato e un'entità che è possibile riutilizzare o fare riferimento. Un'attività può fare riferimento ai set di dati e può usare le proprietà definite nella definizione del set di dati.

Anche un servizio collegato è un parametro fortemente tipizzato contenente le informazioni di connessione a un archivio dati o a un ambiente di calcolo. È anche un'entità che è possibile riutilizzare o fare riferimento.

### <a name="control-flows"></a>Flussi di controllo
I flussi di controllo orchestrano le attività della pipeline che includono concatenamento di attività in una sequenza, diramazione, parametri definiti a livello di pipeline e argomenti passati quando si richiama la pipeline su richiesta o da un trigger. I flussi di controllo includono anche contenitori di passaggio e ciclo di stato personalizzati, ovvero iteratori foreach.Control flows also include custom state passing and looping containers (that is, foreach iterators).


Per altre informazioni sui concetti relativi a Data Factory, vedere gli articoli seguenti:

- [Set di dati e servizi collegati](concepts-datasets-linked-services.md)
- [Pipeline e attività](concepts-pipelines-activities.md)
- [Runtime di integrazione](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Qual è il modello di prezzi per Data Factory?
Per informazioni dettagliate sui prezzi di Azure Data Factory, vedere [Dettagli prezzi di Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Come è possibile avere sempre a disposizione le informazioni più aggiornate su Data Factory?
Per le informazioni più aggiornate su Azure Data Factory, andare ai siti seguenti:

- [Blog (in lingua)](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Home page della documentazione](/azure/data-factory)
- [Home page prodotti](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Approfondimento tecnico 

### <a name="how-can-i-schedule-a-pipeline"></a>Come è possibile pianificare una pipeline? 
Per pianificare una pipeline, è possibile usare il trigger di pianificazione o quello relativo agli intervalli di tempo. Il trigger utilizza una pianificazione del calendario a muro, che può pianificare le pipeline periodicamente o in modelli ricorrenti basati sul calendario (ad esempio, il lunedì alle 18:00 e il giovedì alle 21:00). Per altre informazioni, vedere [Esecuzione e trigger della pipeline](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>È possibile passare parametri all'esecuzione di una pipeline?
Sì, i parametri sono un concetto di prima classe di primo livello in Data Factory.Yes, parameters are a first-class, top-level concept in Data Factory. È possibile definire i parametri a livello di pipeline e passare gli argomenti mentre si esegue la pipeline su richiesta o usando un trigger.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>È possibile definire i valori predefiniti per i parametri della pipeline? 
Sì. È possibile definire i valori predefiniti per i parametri nelle pipeline. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Un'attività in una pipeline può utilizzare gli argomenti passati a un'esecuzione di pipeline? 
Sì. Ogni attività all'interno della pipeline può utilizzare il valore del parametro passato alla pipeline ed eseguito con il costrutto `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Una proprietà di output di attività può essere utilizzata in un'altra attività? 
Sì. È possibile utilizzare un output di attività in un'attività successiva con il costrutto `@activity`.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Come gestire correttamente i valori null in un output di attività? 
È possibile utilizzare il costrutto `@coalesce` nelle espressioni per gestire correttamente i valori null. 

## <a name="mapping-data-flows"></a>Flussi di dati di mapping

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Ho bisogno di aiuto per risolvere i problemi relativi alla logica del flusso di dati. Quali informazioni devo fornire per ricevere assistenza?

Quando Microsoft fornisce assistenza o risoluzione dei problemi con i flussi di dati, fornire lo script del flusso di dati. Questo è lo script code-behind del grafico del flusso di dati. Nell'interfaccia utente di ADF aprire il flusso di dati, quindi fare clic sul pulsante "Script" nell'angolo superiore destro. Copiare e incollare lo script o salvarlo in un file di testo.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>Come si accede ai dati utilizzando gli altri 90 tipi di set di dati in Data Factory?

La funzionalità di mapping del flusso di dati consente attualmente il database SQL di Azure, il data warehouse SQL di Azure, i file di testo delimitati dall'archiviazione BLOB di Azure o dall'archiviazione di Azure Data Lake Gen2 e i file di parquet dall'archivio BLOB o da Data Lake Storage Gen2 in modo nativo per l'origine e il sink. 

Usare l'attività Copia per eseguire temporaneamente i dati da qualsiasi altro connettore e quindi eseguire un'attività Flusso di dati per trasformare i dati dopo che sono stati gestiti temporaneamente. Ad esempio, la pipeline verrà prima copiata nell'archivio BLOB e quindi un'attività Flusso di dati utilizzerà un set di dati nell'origine per trasformare i dati.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>Il runtime di integrazione self-hosted è disponibile per i flussi di dati?

Il metodo di gestione delle applicazioni self-hosted è un costrutto di pipeline ADF che è possibile usare con l'attività di copia per acquisire o spostare dati da e verso origini dati e sink in locale o basata su VM. Eseguire lo spostamento dei dati prima con una copia, quindi un flusso di dati per la trasformazione e quindi una copia successiva se è necessario spostare nuovamente i dati trasformati nell'archivio locale.

### <a name="does-the-data-flow-compute-engine-serve-multiple-tenants"></a>Il motore di calcolo del flusso di dati serve più tenant?
I cluster non vengono mai condivisi. Garantiamo l'isolamento per ogni processo eseguito nelle esecuzioni di produzione. In caso di scenario di debug una persona ottiene un cluster e tutti i debug verranno sottoposti a tale cluster avviati dall'utente.

## <a name="wrangling-data-flows"></a>Flussi di dati Wrangling

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>Quali sono le aree supportate per il flusso di dati di wrangling?

Il flusso di dati wrangling è attualmente supportato nelle data factory create nelle aree seguenti:Wrangling data flow is currently supported in data factoryies created in following regions:

* Australia orientale
* Canada centrale
* India centrale
* Stati Uniti orientali
* Stati Uniti orientali 2
* Giappone orientale
* Europa settentrionale
* Asia sud-orientale
* Stati Uniti centro-meridionali
* Regno Unito meridionale
* Stati Uniti centro-occidentali
* Europa occidentale
* Stati Uniti occidentali
* Stati Uniti occidentali 2

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>Quali sono le limitazioni e i vincoli relativi al flusso di dati wrangling?

I nomi dei set di dati possono contenere solo caratteri alfanumerici. Sono supportati i seguenti archivi dati:

* Set di dati DelimitedText in Archiviazione BLOB di Azure usando l'autenticazione della chiave dell'accountDelimitedText dataset in Azure Blob Storage using account key authentication
* Set di dati DelimitedText in Azure Data Lake Storage gen2 usando la chiave account o l'autenticazione dell'entità servizioDelimitedText dataset in Azure Data Lake Storage gen2 using account key or service principal authentication
* Set di dati DelimitedText in Azure Data Lake Storage gen1 usando l'autenticazione dell'entità servizioDelimitedText dataset in Azure Data Lake Storage gen1 using service principal authentication
* Database SQL di Azure e Data Warehouse usando l'autenticazione SQL. Vedere i tipi SQL supportati di seguito. Non è disponibile alcun supporto PolyBase o di gestione temporanea per il data warehouse.

Al momento, l'integrazione dell'insieme di chiavi del servizio collegato non è supportata nei flussi di dati wrangling.

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>Qual è la differenza tra il mapping e il wrangling dei flussi di dati?

Il mapping dei flussi di dati consente di trasformare i dati su larga scala senza alcuna codifica necessaria. È possibile progettare un processo di trasformazione dei dati nell'area di disegno del flusso di dati creando una serie di trasformazioni. Iniziare con un numero qualsiasi di trasformazioni di origine seguite da passaggi di trasformazione dei dati. Completare il flusso di dati con un lavandino per ottenere i risultati in una destinazione. Il mapping del flusso di dati è ideale per eseguire il mapping e la trasformazione dei dati con schemi noti e sconosciuti nei sink e nelle origini.

I flussi di dati wrangling consentono di eseguire la preparazione e l'esplorazione agile dei dati tramite l'editor di mashup di Power Query Online su larga scala tramite l'esecuzione di spark. Con l'aumento dei data lake a volte è sufficiente esplorare un set di dati o creare un set di dati nel lake. Non si esegue il mapping a una destinazione nota. I flussi di dati wrangling vengono utilizzati per scenari di analisi meno formali e basati su modelli.

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>Qual è la differenza tra Power Platform Dataflows e wrangling data flows?

I flussi di dati di Power Platform consentono agli utenti di importare e trasformare i dati da un'ampia gamma di origini dati in Common Data Service e Azure Data Lake per creare applicazioni PowerApps, report di Power BI o automazioni flusso. I flussi di dati di Power Platform usano le esperienze di preparazione dei dati di Power Query stabilite, in modo simile a Power BI ed Excel. I flussi di dati di Power Platform consentono inoltre un facile riutilizzo all'interno di un'organizzazione e gestiscono automaticamente l'orchestrazione (ad esempio, l'aggiornamento automatico dei flussi di dati che dipendono da un altro flusso di dati quando il primo viene aggiornato).

Azure Data Factory (ADF) è un servizio di integrazione dati gestito che consente ai tecnici di dati e all'integratore di dati per i cittadini di creare complessi flussi di lavoro ETL (Extract-Transform-Load) ibrido ed ELT (Extract-Load-Transform). Il flusso di dati in ADF consente agli utenti di un ambiente senza codice e senza server che semplifica la preparazione dei dati nel cloud e la scalabilità a qualsiasi dimensione dei dati senza che sia necessaria una gestione dell'infrastruttura. Usa la tecnologia di preparazione dei dati di Power Query (usata anche nei flussi di dati di Power Platform, Excel, Power BI) per preparare e modellare i dati. Progettato per gestire tutte le complessità e le sfide di scalabilità dell'integrazione dei Big Data, i flussi di dati consentono agli utenti di preparare rapidamente i dati su larga scala tramite l'esecuzione di spark. Gli utenti possono creare pipeline di dati resilienti in un ambiente visivo accessibile con l'interfaccia basata su browser e consentire ad ADF di gestire le complessità dell'esecuzione di Spark.Users can build resilient data pipelines in an accessible visual environment with our browser-based interface and let ADF handle the complexities of Spark execution. Creare pianificazioni per le pipeline e monitorare le esecuzioni del flusso di dati dal portale di monitoraggio ADF. Gestisci facilmente i contratti di servizio per la disponibilità dei dati con il monitoraggio e gli avvisi di disponibilità avanzata di ADF e sfrutta le funzionalità integrate di integrazione e distribuzione continue per salvare e gestire i flussi in un ambiente gestito. Stabilire avvisi e visualizzare i piani di esecuzione per verificare che la logica funzioni come pianificato durante l'ottimizzazione dei flussi di dati.

### <a name="supported-sql-types"></a>Tipi SQL supportati

Il flusso di dati wrangling supporta i tipi di dati seguenti in SQL. Verrà visualizzato un errore di convalida per l'utilizzo di un tipo di dati non supportato.

* short
* double
* real
* float
* char
* NCHAR
* varchar
* NVARCHAR
* integer
* INT
* bit
* boolean
* SMALLINT
* TINYINT
* bigint
* long
* text
* Data
* Datetime
* datetime2
* smalldatetime
*  timestamp
* UNIQUEIDENTIFIER
* Xml

Altri tipi di dati saranno supportati in futuro.

## <a name="next-steps"></a>Passaggi successivi
Per istruzioni dettagliate per la creazione di una data factory, vedere le esercitazioni seguenti:

- [Guida introduttiva: Creare una data factory](quickstart-create-data-factory-dot-net.md)
- [Esercitazione: Copiare i dati nel cloud](tutorial-copy-data-dot-net.md)
