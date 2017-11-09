---
title: Esecuzione di progetti di data science - Azure | Microsoft Docs
description: "Informazioni su come un esperto di dati può eseguire un progetto di data science in modo tracciabile, collaborativo e con controllo della versione."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev;
ms.openlocfilehash: 8c318f87243d0c98b6a42bebcdffb433f9cc456e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="execution-of-data-science-projects"></a>Esecuzione di progetti di data science

Questo documento descrive come un esperto di dati può eseguire un progetto di data science in modo sistematico, collaborativo e con controllo della versione all'interno di un team di progetto tramite il [Team Data Science Process roles and tasks](overview.md) TDSP (Ruoli e attività del processo di data science per i team). Il TDSP è un framework sviluppato da Microsoft che fornisce una sequenza strutturata di attività per eseguire in modo efficiente soluzioni di analisi predittiva, basate sul cloud. Per una descrizione dei ruoli del personale e delle relative attività associate gestiti da un team di analisi scientifica dei dati, che vengono standardizzati con il processo, vedere [Team Data Science Process roles and tasks](roles-tasks.md) (Ruoli e attività del processo di analisi scientifica dei dati per i team). 

Questo argomento comprende istruzioni su come: 

1. eseguire la **pianificazione dello sprint** per gli elementi di lavoro interessati da un progetto.<br> Se non si ha familiarità con la pianificazione dello sprint, è possibile trovare i dettagli riportati di seguito e le informazioni generali [qui](https://en.wikipedia.org/wiki/Sprint_(software_development) "qui"). 
2. **aggiungere elementi di lavoro** agli sprint.
3. **collegare gli elementi di lavoro con le attività di codifica** rilevate da git.
4. eseguire la **revisione del codice**. 


>[AZURE.NOTE] Nel seguente set di istruzioni vengono illustrati i passaggi necessari per configurare un ambiente per il team TDSP usando Visual Studio Team Services (VSTS). Viene specificato come eseguire queste attività con VSTS dal momento che questa è la modalità di implementazione di TDSP in Microsoft. Gli elementi (3) e (4) nell'elenco precedente sono i vantaggi che si ottengono naturalmente se si sceglie di usare Visual Studio Team Services. Se per il gruppo viene usata un'altra piattaforma di hosting del codice, le attività che il responsabile del team deve completare di per sé non cambiano. Sarà tuttavia diverso il modo in cui queste attività vengono completate. Ad esempio, l'elemento nella sezione 6, **Link a work item with a git branch** (Collega un elemento di lavoro con un ramo git) potrebbe non essere semplice come è in Visual Studio Team Services.

La figura seguente illustra una pianificazione tipica dello sprint, la codifica e il flusso di lavoro di controllo del codice sorgente relativi all'implementazione di un progetto di data science:

![1](./media/project-execution/project-execution-1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminologia 

Nel framework di pianificazione dello sprint TDSP sono disponibili quattro tipi frequentemente usati di **elementi di lavoro**: **Funzionalità**, **Storia utente**, **Attività** e **Bug**. Ogni progetto team conserva un unico backlog per tutti gli elementi di lavoro. Non è presente nessun backlog a livello di repository git in un progetto team. Di seguito sono illustrate le relative definizioni:

- **Funzionalità**: una funzionalità corrisponde a un impegno di progetto. Diversi impegni con un client vengono considerati funzionalità diverse. Analogamente, è consigliabile considerare le diverse fasi di un progetto con un client come diverse funzionalità. Se si sceglie uno schema, ad esempio ***ClientName-EngagementName*** per denominare le funzionalità, allora è possibile riconoscere facilmente il contesto del progetto/impegno dai nomi stessi.
- **Storia**: le storie sono elementi di lavoro diversi che sono necessari per completare una funzionalità (progetto) end-to-end. Gli esempi di storie comprendono:
    - Recupero dei dati 
    - Esplorazione dei dati 
    - Generazione delle funzionalità
    - Compilazione dei modelli
    - Attività che rende operativi i modelli 
    - Ripetizione del training dei modelli
- **Attività**: le attività sono elementi di lavoro di codice o di documento assegnabili o altre attività che devono essere eseguite per completare una storia specifica. Ad esempio, le attività nella storia *Getting Data* (Acquisizione dei dati) potrebbero essere:
    -  Ottenere le credenziali di SQL Server 
    -  Caricamento dei dati in SQL Data Warehouse. 
- **Bug**: i bug fanno in genere riferimento alle correzioni necessarie per un codice o un documento esistenti che vengono eseguite durante il completamento di un'attività. È possibile inoltrare allo stato di una storia o di un'attività se il bug è causato rispettivamente da passaggi o attività mancanti. 

>[AZURE.NOTE] Si stanno usando concetti di funzionalità, storie, attività e bug dalla gestione del codice software (SCM) da usare nel data science. Potrebbero essere leggermente diverse dalle definizioni SCM convenzionali.

##  2. <a name='SprintPlanning-2'></a>Pianificazione dello sprint 

La pianificazione dello sprint è utile per la definizione della priorità e per l'allocazione e la pianificazione delle risorse. Molti data scientist sono impegnati su più progetti, ciascuno dei quali può richiedere mesi per essere completato. I progetti spesso proseguono a ritmi diversi. Nel server di Visual Studio Team Services è possibile creare, gestire e tenere traccia facilmente degli elementi di lavoro nel progetto team e condurre la pianificazione dello sprint per garantire che i progetti procedano come previsto. 

Seguire [questo collegamento](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) per le istruzioni dettagliate sulla pianificazione dello sprint in Visual Studio Team Services. 


##  3. <a name='AddFeature-3'></a>Aggiungere una funzionalità  

Dopo aver creato il repository di progetto in un progetto team, passare alla pagina del team **Panoramica** e fare clic su **Gestisci il lavoro**.

![2](./media/project-execution/project-execution-2-sprint-team-overview.png)

Per includere una funzionalità nel backlog, fare clic su **Backlog** --> **Funzionalità** --> **Nuovo**, digitare nella funzionalità **Titolo**(in genere il nome del progetto) e quindi fare clic su **Aggiungi**.

![3](./media/project-execution/project-execution-3-sprint-team-add-work.png)

Fare doppio clic sulla funzionalità appena creata. Compilare le descrizioni, assegnare i membri del team per questa funzionalità e impostare i parametri di pianificazione per questa funzionalità. 

È anche possibile collegare questa funzionalità al repository del progetto. Fare clic su **Aggiungi collegamento** nella sezione **Sviluppo**. Dopo aver completato la modifica della funzionalità, fare clic su **Salva e chiudi** per uscire.


##  4. <a name='AddStoryunderfeature-4'></a>Aggiungere una storia in Funzionalità 

Nella funzionalità è possibile aggiungere delle storie per descrivere i passaggi principali necessari per completare il progetto (funzionalità). Per aggiungere una nuova storia, fare clic sulla firma **+** a sinistra della funzionalità nella visualizzazione di backlog.  

![4](./media/project-execution/project-execution-4-sprint-add-story.png)

È possibile modificare i dettagli della storia, ad esempio la stato, la descrizione, i commenti, la pianificazione e la priorità nella finestra popup.

![5](./media/project-execution/project-execution-5-sprint-edit-story.png)

È possibile collegare la storia a un repository esistente facendo clic su **+ Aggiungi collegamento** in **Sviluppo**. 

![6](./media/project-execution/project-execution-6-sprint-link-existing-branch.png)


##  5. <a name='AddTaskunderstory-5'></a>Aggiungere un'attività a una storia 

Le attività sono procedure dettagliate specifiche, necessarie per completare ogni storia. Al termine di tutte le attività di una storia, anche la storia deve essere completata. 

Per aggiungere un'attività a una storia, fare clic sulla firma **+** accanto all'elemento della storia, selezionare **Attività** e quindi immettere le informazioni dettagliate di questa attività nella finestra popup.

![7](./media/project-execution/project-execution-7-sprint-add-task.png)

Dopo aver creato le storie, le funzionalità e le attività, è possibile visualizzarle nelle viste **Backlog** o **Lavagna** per rilevarne lo stato.

![8](./media/project-execution/project-execution-8-sprint-backlog-view.png)

![9](./media/project-execution/project-execution-9-link-to-a-new-branch.png)


##  6. <a name='Linkaworkitemwithagitbranch-6'></a>Collegare un elemento di lavoro a un ramo git 

Visual Studio Team Services fornisce un modo pratico per connettersi a un elemento di lavoro (una storia o attività) a un ramo git. In questo modo è possibile collegare direttamente la storia o l'attività al codice associato. 

Per connettere un elemento di lavoro a un nuovo ramo, fare doppio clic su un elemento di lavoro e, nella finestra popup, fare clic su **Crea un nuovo ramo** in **+ Aggiungi collegamento**.  

![10](./media/project-execution/project-execution-10-sprint-board-view.png)

Fornire le informazioni per il nuovo ramo, ad esempio il nome del ramo, il repository git di base e il ramo. Il repository git scelto deve essere il repository nello stesso progetto team a cui appartiene l'elemento di lavoro. Il ramo di base può essere il ramo master o un altro ramo esistente.

![11](./media/project-execution/project-execution-11-create-a-branch.png)

Una procedura consigliata consiste nel creare un ramo git per ogni elemento di lavoro della storia. Per ogni elemento di lavoro dell'attività creare un ramo di base sul ramo della storia. Organizzare i rami in questo modo gerarchico, che corrisponde alle relazioni della storia-attività è utile quando si dispone di più persone che lavorano su storie diverse dello stesso progetto, o quando si dispone di più persone che lavorano su attività diverse della stessa storia. I conflitti possono essere ridotti quando ogni membro del team lavora su un ramo diverso e quando ogni membro lavora su diversi codici o su altri elementi quando si condivide un ramo. 

Nella figura seguente viene illustrata la strategia consigliata di creazione di rami per TDSP. Potrebbe non essere necessario disporre di tutti i rami mostrati di seguito, in particolare quando si dispone solamente di una o due persone che lavorano sullo stesso progetto, o di una persona che lavora su tutte le attività di una storia. Tuttavia è sempre consigliabile separare il ramo di sviluppo dal ramo master. Questo può aiutare ad impedire che il rilascio del ramo venga interrotto dalle attività di sviluppo. Una descrizione più completa del modello di ramo git è reperibile in [A Successful Git Branching Model](http://nvie.com/posts/a-successful-git-branching-model/) (Un modello efficiente per la creazione di un ramo git).

![12](./media/project-execution/project-execution-12-git-branches.png)

Per passare al ramo che si desidera usare, eseguire il comando seguente in un comando di shell (Windows o Linux). 

    git checkout <branch name>

Modificando il *<nome del ramo\>* in **master** si ritorna al ramo **master**. Dopo essere passati al ramo in funzione, è possibile iniziare a lavorare sullo stesso elemento di lavoro, sviluppando gli elementi di codice o di documentazione necessari per completare l'elemento. 

È anche possibile collegare un elemento di lavoro a un ramo esistente. Nella pagina **Dettaglio** di un elemento di lavoro invece di fare clic su **Crea un nuovo ramo**, fare clic su **+ Aggiungi collegamento**. Quindi, selezionare il ramo che si desidera collegare all'elemento di lavoro. 

![13](./media/project-execution/project-execution-13-link-to-an-existing-branch.png)

È anche possibile creare un nuovo ramo nei comandi di git bash. Se <nome del ramo di base\> è mancante, il <nome del nuovo ramo\> si basa sul ramo _master_. 
    
    git checkout -b <new branch name> <base branch name>


##  7. <a name='WorkonaBranchandCommittheChanges-7'></a>Lavorare su un ramo e salvare le modifiche 

Ora si supponga di apportare alcune modifiche al ramo *dati\_inserimento* per l'elemento di lavoro, ad esempio l'aggiunta di un file R sul ramo nel computer locale. È possibile eseguire il commit del file R aggiunto al ramo per questo elemento di lavoro, purché ci si trovi in quel ramo nel Git shell, usando i seguenti comandi Git:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![14](./media/project-execution/project-execution-14-sprint-push-to-branch.png)

##  8. <a name='CreateapullrequestonVSTS-8'></a>Creare una richiesta pull in Visual Studio Team Services 

Quando si è pronti dopo qualche commit e push, per unire il ramo corrente al relativo ramo di base, è possibile inviare una **richiesta pull** al server di Visual Studio Team Services. 

Passare alla pagina principale del progetto team e fare clic su **CODICE**. Selezionare il ramo da unire e il nome del repository git che si desidera unire al ramo. Quindi fare clic su **Richieste Pull**, fare clic su **Nuova richiesta pull** per creare una revisione della richiesta pull prima che il lavoro sul ramo venga unito al relativo ramo di base.

![15](./media/project-execution/project-execution-15-spring-create-pull-request.png)

Riempire delle descrizioni relative a questa richiesta pull, aggiungere i revisori e inviarle.

![16](./media/project-execution/project-execution-16-spring-send-pull-request.png)

##  9. <a name='ReviewandMerge-9'></a>Revisionare e unire 

Quando viene creata la richiesta pull, i revisori ricevono una notifica di posta elettronica per revisionare le richieste pull. I revisori devono verificare se le modifiche funzionano e testare le modifiche con il richiedente, se possibile. In base alla loro valutazione, i revisori possono approvare o rifiutare la richiesta pull. 

![17](./media/project-execution/project-execution-17-add_comments.png)

![18](./media/project-execution/project-execution-18-spring-approve-pullrequest.png)

Dopo aver completato la revisione, il ramo in funzione viene unito al relativo ramo di base facendo clic sul pulsante **Completa**. È possibile scegliere di eliminare il ramo in funzione dopo che è stato unito. 

![19](./media/project-execution/project-execution-19-spring-complete-pullrequest.png)

Verificare in alto a sinistra che la richiesta sia contrassegnata come **COMPLETATA**. 

![20](./media/project-execution/project-execution-20-spring-merge-pullrequest.png)

Quando si torna indietro al repository in **CODICE**, all'utente viene detto che ci si è spostati al ramo master.

![21](./media/project-execution/project-execution-21-spring-branch-deleted.png)

Inoltre, è possibile usare i seguenti comandi Git per unire il ramo di in funzione al relativo ramo di base ed eliminare il ramo in funzione dopo l'unione:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![22](./media/project-execution/project-execution-22-spring-branch-deleted-commandline.png)


##  10. <a name='DataQualityReportUtility-10'></a>Esplorazione interattiva dei dati, analisi e utilità di report (IDEAR)

Questa utilità basata su R Markdown fornisce uno strumento flessibile e interattivo per valutare ed esaminare i set di dati. Gli utenti possono generare velocemente dei report dal set di dati con codifica minima. Gli utenti possono fare clic su dei pulsanti per esportare i risultati di esplorazione che vedono nello strumento interattivo in un report finale, che può essere recapitato ai client o che può essere usato per prendere delle decisioni in relazione a quali variabili includere nel passaggio di modellazione successivo.

In questo momento, lo strumento funziona solo in frame di dati in memoria. È necessario un file .yaml per specificare i parametri del set di dati da esplorare. Per altre informazioni, vedere [IDEAR in TDSP Data Science Utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils) (IDEAR nelle utilità di data science TDSP).


##  11. <a name='ModelingUtility-11'></a>Modellazione di base e utilità di creazione report

Questa utilità fornisce uno strumento personalizzabile, parzialmente automatizzato, per eseguire la creazione di modelli con sweep di iperparametro, per confrontare l'accuratezza di tali modelli. 

L'utilità della creazione del modello è un file R Markdown che può essere eseguito per produrre un output html autonomo con un sommario per una navigazione facile nelle diverse sezioni. Vengono eseguiti tre algoritmi durante l'esecuzione (organizzata) del file markdown: regressione regolarizzata tramite il glmnet del pacchetto, foresta casuale tramite il pacchetto randomForest e alberi a gradienti tramite il pacchetto xgboost). Ciascuno di questi algoritmi produce un modello di esecuzione del training. Viene confrontata poi l'accuratezza di questi modelli e vengono riportati i relativi tracciati di importanza della funzionalità. Attualmente, sono disponibili due utilità: una è per un'attività di classificazione binaria e una è per un'attività di regressione. Le differenze principali tra di esse è il modo in cui i parametri di controllo e la metrica di accuratezza vengono specificati per queste attività di apprendimento. 

Viene usato un file Yaml per specificare:

- l'input di dati (un'origine SQL o un file di dati R) 
- quale quantità di dati viene usata per il training e quale quantità per il test
- quali algoritmi eseguire 
- la scelta dei parametri di controllo per l'ottimizzazione del modello:
    - convalida incrociata 
    - bootstrap
    - riduzione della convalida incrociata
- l'iperparametro imposta ogni algoritmo. 

Il numero di algoritmi, il numero di riduzioni per l'ottimizzazione, gli iperparametri e il numero di set di iperparametri per l'organizzazione possono anche essere modificati nel file Yaml per eseguire rapidamente i modelli. Ad esempio, possono essere eseguiti con un numero inferiore di riduzioni CV, un numero inferiore di set di parametri. Possono inoltre essere eseguiti in modo più completo con un numero maggiore di riduzioni CV o un numero maggiore di set di parametri, se ciò è possibile.

Per altre informazioni, vedere [Automated Modeling and Reporting Utility in TDSP Data Science Utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) (Modellazione automatizzata e utilità di creazione di report nelle utilità data science di analisi scientifica di TDSP).


##  12. <a name='PowerBI-12'></a>Rilevamento dello stato di avanzamento dei progetti con i dashboard di Power BI

I manager del gruppo, i lead dei team e i lead del progetto di data science devono tenere traccia dell'avanzamento dei rispettivi progetti, che lavoro è stato svolto su di essi e da chi e cosa resta negli elenchi delle attività da eseguire. Se si usa Visual Studio Team Services, si è in grado di compilare dei dashboard di Power BI per tenere traccia delle attività e degli elementi di lavoro associati a un repository Git. Per altre informazioni su come connettere Power BI a Visual Studio Team Services, vedere [Connect Power BI to Team Services](https://www.visualstudio.com/en-us/docs/report/powerbi/connect-vso-pbi-vs) (Connettere Power BI a Team Services). 

Per informazioni su come creare dashboard di Power BI e report per tenere traccia delle attività di repository Git e degli elementi di lavoro dopo che i dati di Visual Studio Team Services vengono connessi a Power BI, vedere [Create Power BI dashboards and reports](https://www.visualstudio.com/en-us/docs/report/powerbi/report-on-vso-with-power-bi-vs) (Creare dashboard e report di Power BI). 

Di seguito sono illustrati due dashboard di esempio semplici che sono stati compilati per tenere traccia delle attività di Git e degli elementi di lavoro. Nel primo dashboard di esempio le attività di commit di git sono elencate da utenti diversi, in date diverse e su repository diversi. È possibile svolgere facilmente un'analisi approfondita degli eventi per filtrare quelli a cui si è interessati.

![23](./media/project-execution/project-execution-23-powerbi-git.png)

Nel secondo dashboard di esempio vengono presentati gli elementi di lavoro (storie e attività) in iterazioni diverse. Sono raggruppati per assegnatari e livelli di priorità e colorati in base allo stato.

![24](./media/project-execution/project-execution-24-powerbi-workitem.png)

 
## <a name="next-steps"></a>Passaggi successivi

Sono anche disponibili esercitazioni dettagliate complete che illustrano tutti i passaggi del processo per **scenari specifici** . Le esercitazioni sono elencate, con collegamenti e brevi descrizioni, nell'argomento [Example walkthroughs](walkthroughs.md) (Procedure dettagliate di esempio). Le esercitazioni spiegano come combinare strumenti cloud e locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 

Per esempi di esecuzione dei passaggi nel processo di data science per i team, che usano Azure Machine Learning Studio, vedere il percorso di apprendimento [Con Azure ML](http://aka.ms/datascienceprocess).