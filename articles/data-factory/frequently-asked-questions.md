---
title: 'Azure Data Factory: Domande frequenti | Microsoft Docs'
description: Risposte alle domande frequenti su Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: d704c32ee7417c6460ad6cc880e451adddfa61de
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59048215"
---
# <a name="azure-data-factory-faq"></a>Domande frequenti su Azure Data Factory
Questo articolo risponde ad alcune domande frequenti su Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Che cos'è Azure Data Factory? 
Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud che automatizza lo spostamento e la trasformazione dei dati. Analogamente a quanto avviene in uno stabilimento di produzione, in cui vengono usate attrezzature per trasformare le materie prime in prodotti finiti, Azure Data Factory orchestra i servizi esistenti che raccolgono i dati non elaborati e li trasforma in informazioni pronte per l'uso. 

Usando Azure Data Factory, è possibile creare flussi di lavoro basati sui dati per spostare i dati tra archivi dati locali e cloud. È possibile elaborare e trasformare i dati usando servizi di calcolo come Azure HDInsight, Azure Data Lake Analitica e il runtime di integrazione di SQL Server Integration Services (SSIS). 

Con Data Factory, è possibile eseguire l'elaborazione dei dati in un servizio cloud basato su Azure oppure nell'ambiente di calcolo self-hosted, ad esempio SSIS, SQL Server o Oracle. Dopo aver creato una pipeline che esegue l'azione che è necessario, è possibile pianificarne l'esecuzione periodica (oraria, giornaliera o settimanale, ad esempio), intervallo di tempo di pianificazione o attivare la pipeline dall'occorrenza di un evento. Per altre informazioni, vedere l'[introduzione ad Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Flussi di controllo e scalabilità 
Per supportare i flussi di integrazione diversi e i modelli di data warehouse moderno, Data Factory consente dati flessibile pipeline di modellazione. Questo comporta il flusso di controllo completo paradigmi di programmazione, che includono l'esecuzione condizionale, creazione di un ramo nella pipeline di dati e la possibilità di passare in modo esplicito i parametri all'interno e tra i diversi flussi. Flusso di controllo include inoltre la trasformazione dei dati tramite l'invio di attività per motori di esecuzione esterna e funzionalità del flusso di dati, inclusi lo spostamento dei dati su larga scala, tramite l'attività di copia.

Data Factory consente di modellare liberamente qualsiasi stile di flusso che risulta necessario per l'integrazione dei dati e che può essere inviato su richiesta o ripetutamente in una pianificazione. Di seguito sono riportati alcuni dei flussi comuni abilitati:   

- Flussi di controllo:
    - Le attività possono essere concatenate in una sequenza all'interno di una pipeline.
    - È possibile creare rami attività all'interno di una pipeline.
    - Parametri
        - I parametri possono essere definiti a livello di pipeline e argomenti possono essere passati quando si richiama la pipeline su richiesta o da un trigger.
        - Le attività possono utilizzare gli argomenti passati alla pipeline.
    - Passaggio di stato personalizzato:
        - Output di attività, incluso lo stato, possono essere utilizzati da un'attività successiva nella pipeline.
    - I contenitori di ciclo:
        - L'attività foreach scorre una raccolta specificata di attività in un ciclo. 
- Flussi attivati da trigger:
    - Le pipeline possono essere attivate su richiesta o in base al tempo totale di esecuzione.
- Flussi delta:
    - Parametri possono essere utilizzati per definire il limite massimo per la copia delta durante lo spostamento delle tabelle delle dimensioni o di riferimento da un archivio relazionale, in locale o nel cloud, per caricare i dati nel lake. 

Per altre informazioni, vedere [Esercitazione: Flussi di controllo](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Dati trasformati su larga scala con le pipeline senza codice
I nuovi strumenti basati su browser forniscono funzionalità di creazione e distribuzione di pipeline senza codice offrendo un'esperienza moderna e interattiva basata sul Web.

Per gli sviluppatori di visualizzare i dati e data Engineer, l'interfaccia utente web di Data Factory è l'ambiente di progettazione senza codice che si utilizzerà per compilare le pipeline. È completamente integrato con Visual Studio Online Git e fornisce integrazione per integrazione continua/recapito Continuo e lo sviluppo iterativo con le opzioni di debug.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>SDK multipiattaforma avanzati per gli utenti esperti
Data Factory versione 2 offre un'ampia gamma di SDK che può essere utilizzato per creare, gestire e monitorare pipeline usando l'IDE preferito, tra cui:
* Python SDK
* Riga di comando PowerShell
* SDK per C#

Gli utenti possono anche usare le API REST documentata per interfacciarsi con Data Factory versione 2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Sviluppo iterativo e il debug con gli strumenti di visual
Azure Data Factory visual gli strumenti consentono lo sviluppo iterativo e il debug. È possibile creare le pipeline e avviare esecuzioni dei test usando il **Debug** funzionalità nel canvas della pipeline senza scrivere una singola riga di codice. È possibile visualizzare i risultati di esecuzioni dei test nel **Output** finestra del canvas della pipeline. Dopo l'esecuzione dei test ha esito positivo, è possibile aggiungere più attività alla pipeline e continuare il debug in modo iterativo. È anche possibile annullare le esecuzioni dei test dopo che sono in corso. 

Non è necessario per pubblicare le modifiche al servizio data factory prima di selezionare **Debug**. Ciò è utile negli scenari in cui si desidera assicurarsi che le nuove aggiunte o le modifiche verranno funzionino come previsto prima di aggiornare i flussi di lavoro di data factory negli ambienti di sviluppo, test o produzione. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Possibilità di distribuire pacchetti SSIS in Azure 
Per spostare i carichi di lavoro SSIS, è possibile creare un'istanza di Data Factory ed effettuare il provisioning di un runtime di integrazione SSIS di Azure. Un runtime di integrazione Azure-SSIS è un cluster completamente gestito di macchine virtuali di Azure (nodi) dedicate all'esecuzione di pacchetti SSIS nel cloud. Per istruzioni dettagliate, vedere l'esercitazione [Distribuire i pacchetti SSIS in Azure](tutorial-create-azure-ssis-runtime-portal.md). 
 
### <a name="sdks"></a>SDK
Se sei un utente avanzato e cercando un'interfaccia programmatica, Data Factory offre un'ampia gamma di SDK che è possibile usare per creare, gestire o monitorare pipeline usando l'IDE preferito. Sono supportati, tra gli altri, i linguaggi .NET, PowerShell, Python e REST.

### <a name="monitoring"></a>Monitoraggio
È possibile monitorare le istanze di Data Factory tramite PowerShell, SDK o gli strumenti di monitoraggio visivi nell'interfaccia utente del browser. È possibile monitorare e gestire flussi personalizzati su richiesta basato su trigger e basato su orologio in modo efficiente ed efficace. Annulla attività esistenti, vedere gli errori in modo immediato, il drill-down per ottenere messaggi di errore dettagliati, eseguire il debug di problemi, tutto da un'unica console senza cambiare contesto o passare alternativamente tra le schermate. 

### <a name="new-features-for-ssis-in-data-factory"></a>Nuove funzionalità di SSIS nella Data Factory
Poiché versione nel 2017 di anteprima pubblica iniziale, Data Factory ha aggiunto le funzionalità seguenti per SSIS:

-   Il supporto per tre ulteriori configurazioni le varianti del Database SQL di Azure per ospitare il database SSIS (SSISDB) di progetti/pacchetti:
-   Database SQL con gli endpoint di servizio di rete virtuale
-   Istanza gestita
-   Pool elastico
-   Supporto per una rete virtuale di Azure Resource Manager all'inizio di una rete virtuale classica a essere deprecata in futuro, che consente di inserire/join il runtime di integrazione Azure-SSIS a una rete virtuale configurata per il Database SQL con servizio di rete virtuale accesso ai dati di endpoint/istanza Gestita/da sito locale. Per altre informazioni, vedere anche [aggiungere un runtime di integrazione Azure-SSIS a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md).
-   Supporto per l'autenticazione di Azure Active Directory (Azure AD) e l'autenticazione di SQL per la connessione per il database SSISDB, consentendo l'autenticazione di Azure AD con l'identità gestita di Data Factory per le risorse di Azure
-   Supporto per portare la propria licenza di SQL Server in locale per ottenere risparmi sostanziali l'opzione per il vantaggio Azure Hybrid
-   Supporto per Enterprise Edition del runtime di integrazione Azure-SSIS che consente di utilizzare funzionalità avanzate o premium, un'interfaccia di installazione personalizzata per installare le estensioni/componenti aggiuntive e un ecosistema di partner. Per altre informazioni, vedere anche [Enterprise Edition, il programma di installazione personalizzato e 3rd estensibilità di terze parti per SSIS in Azure Data factory](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-   Integrazione più approfondita di SSIS nella Data Factory che consente di richiamare/trigger eccellente attività Esegui pacchetto SSIS nelle pipeline di Data Factory e pianificarli tramite SSMS. Per altre informazioni, vedere anche [Modernize ed estendere i flussi di lavoro ETL/ELT con le attività SSIS in Azure Data factory pipeline](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Che cos'è il runtime di integrazione?
Il runtime di integrazione è l'infrastruttura di calcolo che usa Azure Data Factory per fornire le seguenti funzionalità di integrazione dei dati in vari ambienti di rete:

- **Spostamento dei dati**: Per lo spostamento dei dati, il runtime di integrazione Sposta i dati tra gli archivi dati di origine e di destinazione, fornendo il supporto per connettori incorporati, conversione dei formati, mapping di colonne e ad alte prestazioni e il trasferimento dei dati scalabile.
- **Attività di invio**: Per la trasformazione, il runtime di integrazione offre possibilità di eseguire in modo nativo i pacchetti SSIS.
- **Esecuzione di pacchetti SSIS**: Il runtime di integrazione in modo nativo esegue i pacchetti SSIS in un ambiente di calcolo di Azure gestito. Il runtime di integrazione supporta anche l'invio e Monitoraggio attività di trasformazione in esecuzione su una vasta gamma di servizi di calcolo, come Azure HDInsight, Azure Machine Learning, Database SQL e SQL Server.

È possibile distribuire una o più istanze del runtime di integrazione come richiesto per spostare e trasformare i dati. Il runtime di integrazione è possibile eseguire in una rete pubblica di Azure o in una rete privata (locale, rete virtuale di Azure o il cloud privato virtuale di Amazon Web Services [VPC]). 

Per altre informazioni, vedere il [Runtime di integrazione in Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Qual è il limite al numero di runtime di integrazione?
Non sono previsti limiti rigidi per il numero di istanze di runtime di integrazione che è possibile avere in una data factory. È stato posto tuttavia un limite al numero di core di VM che il runtime di integrazione può usare per ogni sottoscrizione per l'esecuzione del pacchetto SSIS. Per altre informazioni, vedere [Limiti della data factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Quali sono i principali concetti su cui si basa Azure Data Factory?
Una sottoscrizione di Azure può includere una o più istanze di Azure Data Factory (o data factory). Azure Data Factory contiene quattro componenti chiave che interagiscono come piattaforma nella quale è possibile comporre flussi di lavoro basati sui dati con passaggi per lo spostamento e la trasformazione dei dati stessi.

### <a name="pipelines"></a>Pipeline
Una data factory può comprendere una o più pipeline. Una pipeline è un raggruppamento logico di attività per eseguire un'unità di lavoro, L'insieme delle attività di una pipeline esegue un'operazione. Una pipeline, ad esempio, può contenere un gruppo di attività che inseriscono dati da un BLOB di Azure e quindi eseguono una query Hive in un cluster HDInsight per partizionare i dati. Il vantaggio è che è possibile usare una pipeline per gestire le attività come set invece che singolarmente. È possibile concatenare le attività in una pipeline per usarle in modo sequenziale o indipendentemente in parallelo.

### <a name="activities"></a>attività
Le attività rappresentano un passaggio di elaborazione in una pipeline. Ad esempio, è possibile usare un'attività di copia per copiare dati da un archivio dati in un altro archivio dati. Allo stesso modo, è possibile usare un'attività Hive che esegue una query Hive su un cluster Azure HDInsight per trasformare o analizzare i dati. Data Factory supporta tre tipi di attività: attività di spostamento dei dati, attività di trasformazione dei dati e attività di controllo.

### <a name="datasets"></a>Set di dati
I set di dati rappresentano strutture dei dati all'interno degli archivi dati e fanno semplicemente riferimento ai dati da usare nelle attività come input o output. 

### <a name="linked-services"></a>Servizi collegati
I servizi collegati sono molto simili a stringhe di connessione e definiscono le informazioni necessarie per la connessione di Data Factory a risorse esterne. In sintesi di esso: Un servizio collegato definisce la connessione all'origine dati e un set di dati rappresenta la struttura dei dati. Ad esempio, un servizio collegato di Archiviazione di Azure specifica la stringa per la connessione all'account di archiviazione di Azure. E un set di dati blob di Azure specifica il contenitore blob e la cartella che contiene i dati.

In Data Factory i servizi collegati hanno due scopi:

- Rappresentare un *archivio dati* che include, a titolo esemplificativo, un'istanza di SQL Server locale, un'istanza di database Oracle, una condivisione file o un account di archiviazione BLOB di Azure. Per un elenco di archivi dati supportati, vedere [Attività di copia in Azure Data Factory](copy-activity-overview.md).
- Per rappresentare una *risorsa di calcolo* che può ospitare l'esecuzione di un'attività. Ad esempio, l'attività HDInsight Hive viene eseguita in un cluster HDInsight Hadoop. Per un elenco delle attività di trasformazione e degli ambienti di calcolo supportati, vedere [Trasformare i dati in Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Trigger
I trigger rappresentano unità di elaborazione che determinano quando viene avviata l'esecuzione di una pipeline. Esistono diversi tipi di trigger per i diversi tipi di eventi. 

### <a name="pipeline-runs"></a>Esecuzioni di pipeline
Un'esecuzione di pipeline è un'istanza dell'esecuzione di una pipeline. In genere si crea un'istanza di un'esecuzione di pipeline passando gli argomenti ai parametri definiti nella pipeline. È possibile passare gli argomenti manualmente o nella definizione di trigger.

### <a name="parameters"></a>Parametri
I parametri sono coppie chiave-valore in una configurazione di sola lettura. Si definiscono i parametri in una pipeline ed è possibile passare gli argomenti per i parametri definiti durante l'esecuzione da un contesto di esecuzione. Il contesto di esecuzione viene creato da un trigger o da una pipeline eseguita manualmente. Le attività all'interno della pipeline usano i valori dei parametri.

Un set di dati è un parametro fortemente tipizzato e un'entità che è possibile riutilizzare o di riferimento. Un'attività può referenziare set di dati e può usare le proprietà definite nella definizione del set di dati.

Anche un servizio collegato è un parametro fortemente tipizzato contenente le informazioni di connessione a un archivio dati o a un ambiente di calcolo. È anche un'entità che è possibile riutilizzare o di riferimento.

### <a name="control-flows"></a>Flussi di controllo
I flussi di controllo orchestrano le attività della pipeline che includono concatenamento di attività in una sequenza, diramazione, parametri definiti a livello di pipeline e argomenti passati quando si richiama la pipeline su richiesta o da un trigger. Flussi di controllo includono anche sullo stato personalizzate passaggio e ciclo contenitori (vale a dire gli iteratori foreach).


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
Per pianificare una pipeline, è possibile usare il trigger di pianificazione o quello relativo agli intervalli di tempo. Il trigger Usa una pianificazione basata sul tempo reale di calendario, che consente di pianificare le pipeline periodicamente o in modelli ricorrenti basati sul calendario, ad esempio, in (lunedì alle 18:00) e giovedì alle 21:00: 00. Per altre informazioni, vedere [Esecuzione e trigger della pipeline](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>È possibile passare parametri all'esecuzione di una pipeline?
Sì, i parametri sono un concetto di prima classe di livello superiore in Data Factory. È possibile definire i parametri a livello di pipeline e passare gli argomenti mentre si esegue la pipeline su richiesta o usando un trigger.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>È possibile definire i valori predefiniti per i parametri della pipeline? 
Sì. È possibile definire i valori predefiniti per i parametri nelle pipeline. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Un'attività in una pipeline può utilizzare gli argomenti passati a un'esecuzione di pipeline? 
Sì. Ogni attività all'interno della pipeline può utilizzare il valore del parametro passato alla pipeline ed eseguito con il costrutto `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Una proprietà di output di attività può essere utilizzata in un'altra attività? 
Sì. È possibile utilizzare un output di attività in un'attività successiva con il costrutto `@activity`.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Come gestire correttamente i valori null in un output di attività? 
È possibile utilizzare il costrutto `@coalesce` nelle espressioni per gestire correttamente i valori null. 

## <a name="mapping-data-flows"></a>Flussi di dati di mapping

### <a name="which-data-factory-version-do-i-use-to-create-data-flows"></a>Quale versione di Data Factory usano per creare flussi di dati
Usare la versione di Data Factory versione 2 per creare flussi di dati.
  
### <a name="i-was-a-previous-private-preview-customer-who-used-data-flows-and-i-used-the-data-factory-v2-preview-version-for-data-flows"></a>È clienti dell'anteprima privata di precedente che ha usato i flussi di dati e ho utilizzato la versione di anteprima di Data Factory versione 2 per i flussi di dati.
Questa versione è obsoleta. Usare Data Factory versione 2 per i flussi di dati.
  
### <a name="what-has-changed-from-private-preview-to-limited-public-preview-in-regard-to-data-flows"></a>Cosa è cambiato dalla versione di anteprima privata per l'anteprima pubblica limitata per quanto riguarda i flussi di dati?
Non è necessario portare i propri cluster Azure Databricks. Data Factory di gestire la creazione del cluster e chiusura. Set di dati BLOB e i set di dati di Azure Data Lake Storage Gen2 vengono suddivisi in set di dati Apache Parquet e testo delimitato. È comunque possibile usare Gen2 di archivio Data Lake e nell'archivio Blob per archiviare i file. Usare il servizio collegato appropriato per i motori di archiviazione.

### <a name="can-i-migrate-my-private-preview-factories-to-data-factory-v2"></a>È possibile migrare la factory di anteprima privata di Data Factory versione 2?

Sì. [Seguire le istruzioni](https://www.slideshare.net/kromerm/adf-mapping-data-flow-private-preview-migration).

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Ho bisogno di aiuto la logica del flusso di dati di risoluzione dei problemi. Quali sono le informazioni è necessario fornire per ottenere assistenza?

Quando Microsoft fornisce assistenza o risoluzione dei problemi con i flussi di dati, fornire il piano di codice di linguaggio specifico di dominio. A questo scopo, seguire questa procedura:

1. La progettazione del flusso di dati, selezionare **codice** nell'angolo superiore destro. Verrà visualizzato il codice JSON modificabile per il flusso di dati.
2. Visualizzazione codice, selezionare **pianificare** nell'angolo superiore destro. Questo interruttore passerà da JSON a sola lettura formattato DSL script piano.
3. Copiare e incollare lo script o salvarlo in un file di testo.

### <a name="how-do-i-access-data-by-using-the-other-80-dataset-types-in-data-factory"></a>Come posso accedere dati tramite altri 80 tipi di set di dati in Data Factory?

La funzionalità di Mapping del flusso di dati consente attualmente di Database SQL di Azure, Azure SQL Data Warehouse, delimitato da file di testo dall'archivio Blob di Azure o Azure Data Lake Storage Gen2 e i file Parquet dall'archiviazione Blob o Data Lake Storage Gen2 in modo nativo per l'origine e sink. 

Usare l'attività di copia per organizzare i dati da uno qualsiasi degli altri connettori e quindi eseguire un'attività flusso di dati e trasformare i dati dopo che è stato preconfigurato. Ad esempio, la pipeline copierà nell'archiviazione Blob e quindi un'attività flusso di dati utilizzerà un set di dati nell'origine per trasformare i dati.

## <a name="next-steps"></a>Passaggi successivi
Per istruzioni dettagliate per la creazione di una data factory, vedere le esercitazioni seguenti:

- [Guida introduttiva: Creare una data factory](quickstart-create-data-factory-dot-net.md)
- [Esercitazione: Copiare i dati nel cloud](tutorial-copy-data-dot-net.md)
