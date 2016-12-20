---
title: Ciclo di vita del processo di analisi scientifica dei dati per i team | Documentazione Microsoft
description: Definizione dei componenti principali del ciclo di vita di analisi scientifica dei dati per i team.
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: bradsev;hangzh;gokuma
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 725f6d4a209ada154eb9aa90a8226dcaa0859990


---
# <a name="team-data-science-process-lifecycle"></a>Ciclo di vita del processo di analisi scientifica dei dati per i team
Il processo di analisi scientifica dei dati per i team (TDSP) fornisce un ciclo di vita consigliato da usare per strutturare lo sviluppo dei progetti di analisi scientifica dei dati. Il ciclo di vita descrive tutti i passaggi generalmente seguiti dai progetti in fase di esecuzione. Se si usa un altro ciclo di vita per l'analisi scientifica dei dati, ad esempio [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) o un processo personalizzato dell'organizzazione, è comunque possibile usare il TDSP basato sulle attività nel contesto di tali cicli di vita di sviluppo. 

Questo ciclo di vita è stato messo a punto per i progetti di analisi scientifica dei dati destinati a far parte di applicazioni intelligenti. Queste applicazioni distribuiscono modelli di Machine Learning o intelligenza artificiale per l'analisi predittiva. I progetti esplorativi di analisi scientifica dei dati e i progetti di analisi on/off o ad hoc possono trarre vantaggio da questo processo, ma in questi casi potrebbero non essere necessarie le procedure descritte.    

Di seguito si riporta una rappresentazione visiva del **Ciclo di vita del processo di analisi scientifica dei dati di Team**. 

![Ciclo di vita TDSP](./media/data-science-process-overview/tdsp-lifecycle.png) 

Il ciclo di vita TDSP è composto da cinque fasi principali che vengono eseguite in modo iterativo. Di seguito ne sono elencati alcuni:

* **Informazioni commerciali**
* **Acquisizione e comprensione dei dati**
* **Modellazione**
* **Distribuzione**
* **Accettazione del cliente**

Per ogni fase, specificare le informazioni seguenti:

* **Obiettivi**: gli obiettivi specifici elencati.
* **Procedura**: le attività specifiche descritte e le istruzioni fornite per il completamento delle attività.
* **Elementi**: i risultati finali e il supporto durante la produzione degli stessi.

## <a name="1-business-understanding"></a>1. Informazioni commerciali
### <a name="goals"></a>Obiettivi
* Vengono specificate le **variabili principali** da usare come **target dei modelli** e le relative metriche vengono usate per determinare la riuscita del progetto.
* Vengono identificate le **origini dati** pertinenti a cui/da cui l'azienda accede e/o da altre origini, quando necessario.

### <a name="how-to-do-it"></a>Procedura
Questa fase comprende due attività principali: 

* **Definizione degli obiettivi**: interagire con il cliente e altre parti interessate per comprendere e identificare i problemi aziendali. Formulare domande che definiscano gli obiettivi aziendali e a cui possono essere destinate le tecniche di analisi scientifica dei dati.
* **Identificare le origini dati**: trovare i dati rilevanti che consentono di rispondere alle domande di definizione degli obiettivi del progetto.

#### <a name="11-define-objectives"></a>1.1 Definire gli obiettivi
1. Un obiettivo centrale di questo passaggio consiste nell'identificare le **variabili aziendali** principali che l'analisi deve prevedere. Queste variabili vengono definite come **target dei modelli** e le metriche associate vengono usate per stabilire la riuscita del progetto. Tra questi target, le previsioni di vendita o la probabilità di frode di un ordine.
2. Definire gli **obiettivi del progetto** ponendo e creando domande "precise" e rilevanti, specifiche e non ambigue. L'analisi scientifica dei dati è il processo di uso di nomi e numeri per rispondere a queste domande. "*Quando si sceglie la domanda, si supponga che si sta per raggiungere un oracolo in grado di identificare qualsiasi cosa nell'universo, purché la risposta sia un numero o un nome*". Per altre istruzioni, vedere la sezione **Ask a Sharp Question** (Porre una domanda acuto) del blog [How to do Data Science](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) (Come eseguire l'analisi scientifica dei dati).   L'analisi scientifica dei dati o il Machine Learning vengono in genere usati per rispondere a cinque tipi di domande:
   * In che quantità o in che numero? (regressione)
   * Quale categoria? (classificazione)
   * Quale gruppo? (clustering)
   * È strano? (rilevamento anomalie)
   * Quale opzione scegliere? (suggerimento)
3. Definire il **team del progetto** specificando i ruoli e le responsabilità dei membri. Sviluppare un piano di alto livello da ripetere man mano che la quantità di informazioni acquisite aumenta.  
4. **Definire le metrica di riuscita**. Ad esempio: ottenere l'accuratezza della stima della varianza del cliente di X% entro la fine di questo progetto della durata di 3 mesi, in modo da poter offrire promozioni per ridurre la varianza. Le metriche devono essere **SMART**: 
   * **S**pecific: specifiche 
   * **M**easurable: misurabili
   * **A**chievable: conseguibili 
   * **R**elevant: rilevanti 
   * **T**ime-bound: con associazione temporale 

#### <a name="12-identify-data-sources"></a>1.2 Identificare le origini dei dati
Identificare le origini dati che contengono esempi noti di risposte a domande precise. Cercare i dati seguenti:

* Dati **pertinenti** alla domanda. Sono presenti misure del target e caratteristiche correlate al target?
* Dati che non rappresentano una **misurazione accurata** del target del modello e delle funzionalità di interesse.

Non è ad esempio insolito scoprire che i sistemi esistenti devono raccogliere e registrare tipi di dati aggiuntivi per risolvere il problema e raggiungere gli obiettivi del progetto. In questo caso, si desidera cercare origini dati esterne o aggiornare i sistemi per raccogliere dati più recenti.

### <a name="artifacts"></a>Elementi
Ecco i risultati finali di questa fase:

* [**Documento di dichiarazione di intenti**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): nella definizione della struttura del progetto TDSP viene fornito un modello standard. Si tratta di un documento aggiornato nel corso del progetto quando vengono effettuate nuove scoperte e quando i requisiti dell'azienda cambiano. È importante eseguire l'iterazione di questo documento, aggiungendo dettagli durante l'avanzamento del processo di scoperta. Coinvolgere il cliente e le altre parti interessate nelle modifiche e comunicare chiaramente l'importanza di tali modifiche.  
* [**Origini dati**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources): parte del report sui dati reperibile nella struttura del progetto TDSP. Descrive le origini dei dati non elaborati. Nelle fasi successive vengono inseriti dettagli aggiuntivi, ad esempio gli script per spostare i dati nell'ambiente di analisi.  
* [**Dati**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/DataDictionaries): questo documento fornisce le descrizioni e lo schema (tipi di dati, informazioni sulle regole di convalida, se disponibili) per i dati usati in risposta alla domanda. Se disponibili, sono compresi anche il diagramma o le descrizioni delle relazioni dell'entità.

## <a name="2-data-acquisition-and-understanding"></a>2. Acquisizione e comprensione dei dati
### <a name="goals"></a>Obiettivi
* Un set di dati pulito e di alta qualità, le cui relazioni con le variabili target vengono comprese e che si trova nell'ambiente di analisi, pronto per la modellazione.
* È stata sviluppata un'architettura della soluzione della pipeline di dati per aggiornare e valutare regolarmente i dati.

### <a name="how-to-do-it"></a>Procedura
Questa fase comprende tre attività principali:

* **Inserire i dati** nell'ambiente di analisi target.
* **Esplorare i dati** per determinare se la qualità dei dati è sufficiente per rispondere alla domanda. 
* **Configurare una pipeline di dati** per calcolare dati nuovi o regolarmente aggiornati.

#### <a name="21-ingest-the-data"></a>2.1 Inserire i dati
Impostare il processo per spostare i dati da posizioni di origine a posizioni destinazione in cui devono essere eseguite operazioni di analisi come il training e le stime. Per informazioni tecniche e per le opzioni su come eseguire questa operazione con diversi servizi di dati di Azure, vedere [Caricare i dati in ambienti di archiviazione per l'analisi](machine-learning-data-science-ingest-data.md). 

#### <a name="22-explore-the-data"></a>2.2 Esplorare i dati
Prima di eseguire il training dei modelli, è necessario sviluppare una buona comprensione dei dati. I set di dati reali sono spesso fastidiosi, mancano di valori, sono numerosi o presentano altre discrepanze. La visualizzazione e il riepilogo dei dati consente di controllare la qualità dei dati e fornire le informazioni necessarie per elaborare i dati prima che siano pronti per la modellazione. Per istruzioni sulla pilizia dei dati, vedere [Attività per preparare i dati per operazioni avanzate con Machine Learning](machine-learning-data-science-prepare-data.md). Spesso, questo processo è iterativo. 

TDSP fornisce un'utilità automatica denominata [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) per visualizzare i dati e preparare report di riepilogo sui dati. È consigliabile iniziare con IDEAR per esplorare i dati e sviluppare una comprensione iniziale sui dati in modo interattivo, senza codice, e quindi scrivere il codice personalizzato per la visualizzazione e l'esplorazione dei dati. 

Quando si è soddisfatti della qualità dei dati puliti, il passaggio successivo consiste nel comprendere meglio i modelli presenti nei dati, che consentono di scegliere e sviluppare un modello predittivo appropriato per il target. Verificare la qualità della connessione dei dati al target e scoprire se sono presenti dati sufficienti per procedere con i passaggi successivi di modellazione. Anche questo processo è iterativo. Potrebbe essere necessario trovare nuove origini dati con dati più accurati o più pertinenti per aumentare il set di dati identificato nella fase precedente.  

#### <a name="23-set-up-a-data-pipeline"></a>2.3 Impostare una pipeline di dati
Oltre all'inserimento iniziale e alla pulizia dei dati, è necessario in genere impostare un processo per valutare nuovi dati o aggiornare regolarmente i dati, come parte di un processo costante di apprendimento. Per ottenere questo risultato, è possibile configurare una pipeline di dati o un flusso di lavoro. Ecco un [esempio](machine-learning-data-science-move-sql-azure-adf.md) su come configurare una pipeline con [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). In questa fase viene sviluppata un'architettura della soluzione della pipeline di dati. La pipeline viene sviluppata parallelamente alle seguenti fasi del progetto di analisi scientifica dei dati. La pipeline potrebbe essere basata su batch, su un flusso, in tempo reale o ibrida, a seconda delle esigenze aziendali e delle restrizioni dei sistemi esistenti in cui la soluzione è integrata. 

### <a name="artifacts"></a>Elementi
Ecco i risultati finali di questa fase:

* [**Report sulla qualità dei dati**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): il report contiene riepiloghi dei dati, relazioni tra ogni attributo e il target, valutazione della variabile e così via. Lo strumento [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) viene fornito come parte di TDSP e può generare rapidamente questo report su qualsiasi set di dati tabulare, ad esempio un file CSV o una tabella relazionale. 
* **Architettura della soluzione**: può trattarsi di un diagramma o della descrizione della pipeline di dati usata per eseguire valutazioni o stime sui nuovi dati dopo aver creato un modello. Include anche la pipeline per ripetere il training del modello basato su nuovi dati. Il documento viene archiviato in questa [directory](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) quando si usa il modello della struttura di directory di TDSP.
* **Decisione di checkpoint**: prima di iniziare la progettazione completa delle funzionalità e la compilazione del modello, è possibile riesaminare il progetto per determinare se è conveniente continuare la progettazione. È possibile, ad esempio, che tutto sia predisposto per procedere, che si debbano raccogliere altri dati o abbandonare il progetto, se non esistono dati per rispondere alla domanda.

## <a name="3-modeling"></a>3. Modellazione
### <a name="goals"></a>Obiettivi
* Funzionalità ottimali dei dati per il modello di Machine Learning.
* Un modello ML informativo che consente di stimare la destinazione in modo più accurato.
* Un modello ML adatto per la produzione.

### <a name="how-to-do-it"></a>Procedura
Questa fase comprende tre attività principali:

* **Progettazione delle funzioni**: creare le funzionalità dei dati dai dati non elaborati per facilitare il training del modello.
* **Training del modello**: trovare il modello che risponde alla domanda in modo più accurato rispetto alle metriche di riuscita.
* Stabilire se il modello è **adatto alla produzione**.

#### <a name="31-feature-engineering"></a>3.1 Progettazione delle funzioni
La progettazione di funzionalità prevede l'inclusione, l'aggregazione e la trasformazione delle variabili non elaborate per creare funzionalità usate nell'analisi. Se si desidera comprendere i risultati di un modello, è necessario comprendere in che modo le funzioni sono correlate tra loro e come gli algoritmi di Machine Learning vengono usati da tali funzionalità. Questo passaggio richiede una combinazione creativa di competenze a livello di dominio e di informazioni approfondite ottenute dal passaggio di esplorazione dei dati. Si tratta di un compromesso di ricerca e inclusione di variabili informative evitando troppe variabili non correlate. Le variabili informative migliorano il risultato, mentre le variabili senza relazione introducono rumore inutile nel modello. È anche necessario generare le seguenti funzionalità per i nuovi dati ottenuti durante la valutazione. La generazione di queste funzionalità, quindi, può dipendere esclusivamente dai dati disponibili al momento della valutazione. Per indicazioni tecniche nella progettazione delle funzionalità quando si usano diverse tecnologie di dati di Azure, vedere [Progettazione di funzionalità in Cortana Analytics Process](machine-learning-data-science-create-features.md). 

#### <a name="32-model-training"></a>3.2 Training del modello
A seconda del tipo di domanda a cui si sta tentando di rispondere, sono disponibili numerosi algoritmi di modellazione. Per istruzioni sulla scelta dell'algoritmo, vedere [Come scegliere gli algoritmi di Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md). Sebbene questo articolo è inteso per Azure Machine Learning, le linee guida fornite sono utili per altri framework di ML. 

Il processo di training del modello include i passaggi seguenti: 

* Suddividere i dati di input in modo casuale per la modellazione in un set di dati di training e un set di dati di test.
* Compilare i modelli tramite il set di dati di training.
* Valutare (set di dati di training e set di dati di test) di una serie di algoritmi di Machine Learning concorrenti insieme a diversi parametri di regolazione associati (noto come sweep parametrico), pensati per rispondere alle domande di interesse con i dati correnti.
* Determinare la soluzione "migliore" per rispondere alla domanda confrontando la metrica di riuscita tra metodi alternativi.

> [!NOTE]
> **Evitare perdite**: la perdita di dati può essere causata dall'inclusione di dati dall'esterno del set di dati di training che consente a un modello o a un algoritmo di Machine Learning di eseguire stime estremamente valide. La perdita è il motivo comune che infastidisce gli esperti di dati quando ottengono risultati predittivi che appaiono troppo validi per essere reali. Queste dipendenze possono essere difficili da rilevare. Per evitare questo problema, spesso è necessaria l'iterazione della compilazione di un set di dati di analisi, della creazione di un modello e della valutazione dell'accuratezza. 
> 
> 

Con TDSP è disponibile uno [strumento di creazione di report e modellazione automatizzati](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling), da eseguire in diversi algoritmi e sweep parametrici per produrre un modello di base. Lo strumento produce anche un report di base sulla modellazione che riepiloga le prestazioni di ogni combinazione di modello e parametro, inclusa l'importanza della variabile. Anche questo processo è iterativo poiché potrebbe risultare in una nuova progettazione di funzionalità. 

### <a name="artifacts"></a>Elementi
Gli elementi generati in questa fase includono:

* [**Set di funzionalità**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): le funzionalità sviluppate per la modellazione sono descritte nella sezione Set di funzionalità del report di definizione dei dati. Il report contiene i puntatori al codice per generare le funzionalità e una descrizione sul metodo di generazione della funzionalità.
* [**Report di modellazione**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): per ogni modello provato, viene generato un report standard che segue un modello TDSP specificato.
* **Decisione di Checkpoint**: valutare se le prestazioni del modello sono sufficientemente adatte per la distribuzione in un sistema di produzione. Alcune domande chiave da porsi:
  * Il modello risponde alla domanda con sufficiente certezza rispetto ai dati del test? 
  * È necessario provare soluzioni alternative, come raccogliere dati aggiuntivi, eseguire nuova progettazione di funzionalità o provare con altri algoritmi?

## <a name="4-deployment"></a>4. Distribuzione
### <a name="goal"></a>Obiettivo
* I modelli e le pipeline vengono distribuiti in un ambiente di produzione o di simil-produzione per l'accettazione da parte di un utente finale. 

### <a name="how-to-do-it"></a>Procedura
Attività principali descritte in questa fase:

* **Rendere operativo il modello**: distribuire il modello e la pipeline in un ambiente di produzione o di simil-produzione per l'uso da parte dell'applicazione.

#### <a name="41-operationalize-a-model"></a>4.1 Rendere operativo il modello
Quando si ottiene un set di modelli con prestazioni ottimali, è possibile renderli operativi per l'uso da parte di altre applicazioni. In base ai requisiti aziendali, le previsioni vengono eseguite in tempo reale o in batch. Per essere resi operativi, i modelli devono essere esposti con un'interfaccia API aperta che può essere facilmente usata da diverse applicazioni, ad esempio un sito Web online, fogli di calcolo, dashboard applicazioni line-of-business e back-end. Per esempi su come rendere operativi i modelli con un servizio Web di Azure Machine Learning, vedere [Pubblicare un servizio Web di Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md). È anche consigliabile creare dati di telemetria e monitorare il modello di produzione e la pipeline di dati distribuiti per consentire allo stato del sistema di segnalare eventuali problemi e risolverli.  

### <a name="artifacts"></a>Elementi
* Dashboard di stato sull'integrità del sistema e metriche principali.
* Report di modellazione finale con informazioni dettagliate sulla distribuzione.
* Documento finale sull'architettura della soluzione.

## <a name="5-customer-acceptance"></a>5. Accettazione del cliente
### <a name="goal"></a>Obiettivo
* **Finalizzare i risultati del progetto**: verificare che la pipeline, il modello e la relativa distribuzione nell'ambiente di produzione soddisfino gli obiettivi del cliente.

### <a name="how-to-do-it"></a>Procedura
Questa fase comprende tre attività principali:

* **Convalida del sistema**: verificare che il modello e la pipeline distribuiti soddisfino le esigenze del cliente.
* **Consegna del progetto**: all'entità che eseguirà il sistema nell'ambiente di produzione.

Il cliente verifica che il sistema soddisfi le proprie esigenze aziendali e che risponda alle domande con un'accuratezza accettabile per la distribuzione nell'ambiente di produzione e l'uso da parte delle applicazioni client. Tutta la documentazione viene finalizzata ed esaminata. La consegna del progetto all'entità responsabile delle operazioni è completa. Potrebbe trattarsi, ad esempio, del team IT, del team di analisi scientifica dei dati o di un agente del cliente responsabile dell'esecuzione del sistema nell'ambiente di produzione. 

### <a name="artifacts"></a>Elementi
L'elemento principale prodotto in questa fase finale è il [**Report finale del progetto**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md). Si tratta del report tecnico del progetto, contenente tutti i dettagli del progetto utili per apprendere e usare il sistema. TDSP fornisce un [modello](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) da usare così com'è o da personalizzare secondo le esigenze specifiche del cliente. 

## <a name="summary"></a>Riepilogo
Il [ciclo di vita di analisi scientifica dei dati per i team](http://aka.ms/datascienceprocess) viene modellato come sequenza di passaggi iterati che offrono indicazioni sulle attività necessarie per usare modelli predittivi. Questi modelli possono essere distribuiti in un ambiente di produzione e usati per compilare applicazioni intelligenti. L'obiettivo di questo ciclo di vita del processo consiste nel procedere con il progetto di analisi scientifica di dati verso un punto finale di evidente coinvolgimento. Sebbene sia vero che l'analisi scientifica dei dati è un esercizio di ricerca e scoperta, essere in grado di comunicarlo chiaramente al team e ai clienti che usano un set ben definito di elementi che si servono di modelli standard consente di evitare malintesi e aumenta le probabilità di esito positivo di un progetto complesso di analisi scientifica dei dati.

## <a name="next-steps"></a>Passaggi successivi
Sono anche disponibili esercitazioni dettagliate complete che illustrano tutti i passaggi del processo per **scenari specifici** . Sono elencate, con collegamenti e brevi descrizioni, nell'argomento [Procedure dettagliate del Processo di analisi scientifica dei dati per i team](data-science-process-walkthroughs.md).




<!--HONumber=Nov16_HO3-->


