<properties title="Azure Machine Learning API service operations" pageTitle="Machine Learning API service operations | Azure" description="Creating and managing Azure Machine Learning web services" metaKeywords="" services="" solutions="" documentationCenter="" authors="derrickv" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="derrickv"></tags>

# Operazioni del servizio API di Azure Machine Learning

Un tipico progetto Microsoft Azure Machine Learning (Azure ML) comprende i seguenti passaggi a livello generale:

1.  Recupero, analisi e preparazione dei dati
2.  Creazione di esperimenti di Machine Learning basate su diversi algoritmi ML
3.  Formazione, test e generazione di un modello sottoposto a training
4.  Creazione di un flusso di lavoro operativo con il modello sottoposto a training e distribuzione del flusso di lavoro in produzione
5.  Monitoraggio delle prestazioni del modello e aggiornamenti successivi

> Il termine “esperimento” viene usato per descrivere un flusso di lavoro interattivo che può includere input e manipolazione dei dati e strumenti di training e di assegnazione dei punteggi sotto forma di un grafo aciclico diretto (DAG). Dopo la pubblicazione come servizio Web di Azure, il flusso di lavoro non è più interattivo. Per modificarlo, quindi, è necessario aggiornare e ripubblicare il modello per poter aggiornare il servizio Web e il relativo comportamento.

I passaggi 1-3 vengono generalmente completati da un data scientist attraverso più iterazioni, alla fine delle quali un modello ML viene passato ai team di progettazione e operazioni per integrarlo e usarlo nei sistemi di produzione.

Il processo tradizionale di integrazione e distribuzione del modello ML in un sistema di produzione richiede settimane o addirittura mesi, a seconda del codice usato per compilare i modelli come R, Python, C\# o Java, le considerazioni relative a infrastruttura e integrazione delle piattaforme e la pianificazione della distribuzione.

Azure ML semplifica e ottimizza il processo rendendo semplice e intuitiva la creazione e la valutazione del modello e offrendo un processo di distribuzione dell'esperimento come servizio Web in Azure, riducendo così il tempo complessivo dalla sperimentazione all'esecuzione del modello nella produzione come servizio Web.

In questo documento vengono descritti i concetti e i passaggi per la configurazione di un servizio Web Azure ML da un esperimento ML.

# Panoramica del processo Azure ML

Azure ML consente la creazione di servizi Web dagli esperimenti ML definiti in Azure Machine Learning Studio (ML Studio). Il servizio Web Azure ML può essere usato per generare previsioni sui dati di input effettivi in tempo reale o in modalità batch.

Nel seguente diagramma vengono visualizzati i passaggi a livello generale in due parti: creazione di un modello e successiva pubblicazione come servizio Web. Questo documento si concentra sulla parte destra del diagramma della figura 1, relativa alla pubblicazione di un servizio Web per l'assegnazione dei punteggi, e spiega i concetti associati a tale processo.

![][]

Figura 1: Provisioning, compilazione e pubblicazione di un servizio Web per l'assegnazione dei punteggi

# Servizi Web Azure ML

Un servizio Web, nel contesto Machine Learning (ML), è un'interfaccia software che fornisce la comunicazione tra le applicazioni esterne e un flusso di lavoro Machine Learning. Offre un metodo per la comunicazione con un modello di assegnazione dei punteggi in tempo reale per ricevere risultati predittivi e incorporarli nelle applicazioni client esterne. Azure ML si basa su Microsoft Azure per la distribuzione, l'hosting e la gestione dei servizi Web Azure ML. Con Azure ML possono essere creati due tipi di servizi.

## Servizio di richiesta-risposta (RRS)

Il servizio di richiesta-risposta (RRS) è un servizio Web a bassa latenza e scalabilità elevata usato per fornire un'interfaccia ai modelli senza stato creati e pubblicati dall'ambiente di sperimentazione.

-   API REST: RRS è un servizio Web RESTFul.
-   Interfaccia del servizio: l'interfaccia del servizio Web RSS viene definita durante la configurazione dell'esperimento con le porte di input/output nell'esperimento Azure ML Studio.
-   Fasi dello sviluppo: nel flusso di lavoro Azure ML il servizio RRS viene generato nell'ambiente di gestione temporanea, in cui è possibile testarlo. Una volta completato e pronto per la produzione, viene distribuito nell'ambiente di produzione.
-   Distribuzione in Azure: il risultato della distribuzione di RRS è un endpoint del servizio Web di Azure.
-   Parametri dell'interfaccia: la richiesta a un servizio RRS comprende i dati per l'assegnazione dei punteggi mediante l'esperimento definito in Studio. La risposta è il risultato della previsione del modello.
-   Valori di risposta: RRS accetta una sola riga di parametri di input e genera una sola riga di output. La riga di output può contenere più colonne.

## Servizio Esecuzione batch (BES)

Il servizio Esecuzione batch (BES) è un servizio per l'assegnazione dei punteggi asincrona di un batch di record di dati. L'input per BES è simile all'input dei dati usati in RRS. La differenza principale consiste nel fatto che BES legge un batch di record da diverse origini, ad esempio BLOB, tabelle di Azure, SQL Azure, HD Insight (query Hive) e HTTP. L'output dei risultati dell'assegnazione dei punteggi viene inserito in un file nell'archiviazione BLOB di Azure e i dati dall'endpoint di archiviazione vengono restituiti nella risposta.

BES fornisce anche le interfacce per ottenere lo stato del processo di assegnazione dei punteggi in esecuzione e per annullare la richiesta. BES può eseguire pacchetti di modelli in esecuzione su notevoli quantità di dati.

-   API REST: BES è un servizio Web RESTFul.
-   Interfaccia del servizio: analogamente a RRS, l'interfaccia del servizio Web BES viene definita durante la configurazione del modello con le porte di input/output nell'esperimento Azure ML Studio.
-   Fasi dello sviluppo: nel flusso di lavoro di creazione, BES viene generato nell'ambiente di gestione temporanea, in cui è possibile testarlo. Una volta completato e pronto per la produzione, viene distribuito nell'ambiente di produzione.
-   Distribuzione in Azure: il risultato della distribuzione di BES è un endpoint del servizio Web di Azure.
-   Parametri dell'interfaccia: la richiesta a un servizio BES è l'URL di un file nel servizio BLOB di Azure o un input SAS dei record a cui assegnare un punteggio. La risposta viene scritta nel servizio BLOB di Azure e viene restituito l'URL dell'endpoint di archiviazione della risposta.

# Pubblicazione di un servizio Web Azure ML

Azure ML Studio fornisce un'applicazione basata su browser per creare ed eseguire facilmente esperimenti Machine Learning in un'interfaccia utente grafica usando diverse origini dati, moduli di manipolazione e convalida dei dati e algoritmi ML. Un esperimento in ML Studio viene costruito come grafo aciclico diretto (DAG) dei moduli di elaborazione dei dati.

Dopo la configurazione e la corretta esecuzione dell'esperimento per sottoporlo a training in base ai dati, è possibile salvarlo come modello sottoposto a training e usarlo per l'assegnazione dei punteggi. Il modello sottoposto a training viene quindi usato in un esperimento di assegnazione dei punteggi o in un flusso di lavoro, quindi pubblicato come servizio Web di Azure.

## Esperimento di training

Un esperimento può includere diversi moduli per il caricamento e la manipolazione dei dati, per l'applicazione degli algoritmi di Machine Learning e per la valutazione dei risultati. Un modello di training usa il set di dati di training e un algoritmo di Machine Learning per prevedere una risposta.

Dopo aver completato ed eseguito correttamente il modello, è possibile salvare un modello di training come componente riutilizzabile per l'assegnazione dei punteggi nei set di dati di test e nelle query.

![][1]

Figura 2: Esempio che mostra come salvare un modello sottoposto a training nell'esperimento

Il modello sottoposto a training salvato è disponibile nella sezione dei modelli sottoposti a training dell'applicazione.

![][2]

Figura 3: Sezione dei modelli sottoposti a training che visualizza l'elenco di modelli

## Esperimento di assegnazione dei punteggi

Un esperimento di assegnazione dei punteggi genera previsioni in base al modello sottoposto a training e ai dati di esempio.

Nella figura 1 in alto viene mostrato l'uso di un modello di punteggio nell'esperimento. In Studio il modello è incluso nei moduli di Machine Learning.

![][3]

Figura 4: Modello di punteggio

### Servizio richiesta-risposta e servizio Esecuzione batch

Quando si compilano gli esperimenti di assegnazione dei punteggi da pubblicare come servizi Web, è possibile selezionare uno dei due servizi a seconda dello scenario di assegnazione dei punteggi. Se la richiesta comprende l'assegnazione dei punteggi per un solo record, ad esempio una richiesta per determinare se il cliente A sta per cambiare vettore (previsione con varianza cliente), è possibile eseguire in tempo reale l'assegnazione dei punteggi, che verrà creata come servizio Web RSS. Il servizio restituisce il risultato del modello di previsione in tempo reale. Nell'esempio di previsione con varianza riportato in precedenza viene generata una risposta Sì o No.

Per le operazioni in cui è necessaria l'assegnazione dei punteggi di più record in una richiesta, ad esempio se un batch di record contenenti i dati dei clienti viene inviato al servizio per l'assegnazione dei punteggi, il servizio appropriato è BES. In questo caso, la richiesta sarà di tipo asincrono. Tutti i record vengono elaborati e salvati in un servizio BLOB di Azure prima di restituire una risposta al termine dell'elaborazione.

### Uso del modello sottoposto a training

Per configurare l'esperimento di assegnazione dei punteggi, il modello sottoposto a training (“Adult Income Predictor” nella figura 3) viene aggiunto all'esperimento. Gli altri moduli usati per il training del modello sottoposto a training possono essere rimossi. Il flusso di lavoro finale è simile a quello illustrato nella figura 5.

### Porte di input e output

Dopo aver configurato l'esperimento con il modello sottoposto a training (vedere la sezione precedente per i dettagli) e l'esperimento di assegnazione dei punteggi aggiornato, è necessario impostare le porte di input e di output che specificano i punti di ingresso e di uscita dei dati nel modello di previsione e nel risultato della previsione e che fungono da definizioni di interfaccia per il servizio Web pubblicato. La porta di input del modello di punteggio può essere impostata come mostrato di seguito facendo clic con il pulsante destro del mouse sul punto di ingresso.

![][4]

Figura 5: Impostazione della porta di input per l'assegnazione dei punteggi

Anche la porta di output del modello di punteggio può essere impostata come mostrato di seguito.

![][5]

Figura 6: Impostazione della porta di output

## Pubblicazione del servizio

Dopo aver impostato le porte ed aver eseguito l'esperimento, il modello può essere pubblicato come servizio Web. Il primo passaggio consiste nella pubblicazione del servizio nell'ambiente di gestione temporanea e l'esecuzione del test per verificare che restituisca i risultati previsti prima di contrassegnarlo come pronto per la distribuzione in produzione.

### Pubblicazione nella gestione temporanea

Se si fa clic sull'icona Pubblica servizio Web viene distribuito il servizio Web nell'ambiente di gestione temporanea.

![][6]

Figura 7: Pulsante Pubblica servizio Web

Dopo aver pubblicato il modello come servizio Web nell'ambiente di gestione temporanea, è possibile testarlo con i parametri di input e contrassegnarlo per la distribuzione in produzione. Nel dashboard viene visualizzato il collegamento per i test.

![][7]

Figura 8: Dashboard del servizio Web

Facendo clic sul collegamento Test e inserendo i parametri per l'assegnazione dei punteggi, il servizio Web può essere testato nell'ambiente di gestione temporanea. L'assegnazione dei punteggi alla richiesta di test viene eseguita usando il modello e i dati inseriti e viene restituito il risultato relativo all'assegnazione.

### Pubblicazione in produzione

La pubblicazione di un servizio Web in produzione lo rende disponibile ad altre applicazioni per la previsione e l'assegnazione dei punteggi. Dopo aver completato e testato correttamente la distribuzione nella gestione temporanea, il servizio Web viene contrassegnato per la distribuzione nell'ambiente di produzione.

![][8]

Figura 9: Servizio Web contrassegnato come pronto per la distribuzione in produzione

In realtà non viene eseguita la distribuzione effettiva, ma viene creata una notifica per l'utente con le necessarie autorizzazioni per la distribuzione dei servizio in produzione.

![][9]

Figura 10: Notifica della distribuzione e opzione per la distribuzione in produzione

## Chiamata del servizio Web

#### RRS

Il servizio Web RRS è un endpoint REST e può essere chiamato dalle applicazioni client mediante diversi linguaggi di programmazione. La pagina della guida API fornisce un collegamento al codice di esempio per chiamare il nuovo servizio Web che include esempi in C\#, R e Python.

![][10]

Figura 11: Codice di esempio per chiamare RRS

## Esperimenti diversi da quelli per l'assegnazione dei punteggi

Oltre alla compilazione del servizio Web per l'assegnazione dei punteggi, gli esperimenti possono essere creati per eseguire altre attività, ad esempio l'estrazione e la trasformazione dei dati. In questo caso il servizio Web non esegue operazioni di Machine Learning. Usa le capacità di manipolazione dei dati di Azure ML Studio per leggere diverse origini dati, convertire i tipi di dati o filtrare e applicare i dati e le operazioni matematiche.

### Pubblicazione di un servizio Web diverso da quello per l'assegnazione dei punteggi

I passaggi per la pubblicazione di un servizio Web diverso da quello per l'assegnazione dei punteggi sono simili a quelli relativi al servizio per l'assegnazione dei punteggi descritti in precedenza. La differenza principale riguarda la porta di output, che non è definita nel modello di punteggio.

# Aggiornamento di un servizio pubblicato

È possibile che un servizio Web pubblicato debba essere aggiornato per diversi motivi, ad esempio l'aggiornamento dei dati di training, le modifiche allo schema dei dati usato per il training e l'assegnazione dei punteggi, la necessità di migliorare l'algoritmo o altre modifiche al modello ML. Queste modifiche hanno effetti sul modello sottoposto a training e sui risultati dell'assegnazione dei punteggi e richiedono la pubblicazione di un servizio Web aggiornato.

![][11]

Figura 12: Modifica del modello e pubblicazione del servizio Web aggiornato per l'assegnazione dei punteggi

## Aggiornamento del modello sottoposto a training

Le modifiche all'esperimento di training richiedono un nuovo training del modello sottoposto a training. A tale scopo è necessario modificare il modello pubblicato. L'esempio seguente mostra il flusso di lavoro dell'assegnazione dei punteggi visualizzato nella figura 5 precedente dopo la rimozione del modello sottoposto a training esistente.

![][12]

Figura 13: Modello sottoposto a training rimosso dal flusso di lavoro

Il passaggio successivo consiste nell'aggiunta dei moduli necessari per dividere i dati in segmenti di training e di test, nell'applicazione degli algoritmi di Machine Learning, nel training del modello, nell'assegnazione dei punteggi per i dati di training e nella valutazione dei risultati. I risultati possono variare a seconda delle altre modifiche necessarie per l'esperimento, ad esempio l'applicazione di un diverso algoritmo di Machine Learning. (Figura 14)

Dopo aver aggiunto i nuovi moduli, configurarli come necessario prima di eseguire di nuovo l'esperimento. Ad esempio, il cerchio rosso nella figura seguente indica che la colonna Etichetta non è stata impostata per modello di training.

![][13]

Figura 14: Moduli aggiunti per il nuovo training del modello.

Il problema viene risolto facendo clic sul modello di training e impostando il nome della colonna Etichetta.

![][14]

Figura 15: Selezione della colonna del reddito come etichetta

## Salvataggio del modello sottoposto a training aggiornato

Dopo aver configurato correttamente tutti i nuovi moduli, il salvataggio e l'esecuzione dell'esperimento dovrebbero riuscire correttamente. Il modello di training può quindi essere salvato (come mostrato nella figura 2 precedente). La differenza consiste nel fatto che, in questo caso, la casella di controllo deve essere selezionata per aggiornare il modello sottoposto a training esistente.

![][15]

Figura 16: Aggiornare il modello sottoposto a training esistente

## Pubblicazione del servizio aggiornato

Una volta aggiornato il modello sottoposto a training, vengono ripetuti i passaggi descritti nella sezione precedente sulla pubblicazione di un **servizio Web Azure ML**:

-   Usare il modello sottoposto a training (ora aggiornato) nell'esperimento di assegnazione dei punteggi
-   Impostare le porte di input/output
-   Eseguire la pubblicazione nella gestione temporanea
-   Eseguire la pubblicazione in produzione

Dopo aver aggiornato l'esperimento e creato e assegnato i punteggi al nuovo modello sottoposto a training, pubblicare il servizio facendo clic su Pubblica servizio Web. Il servizio appena creato sovrascrive quello esistente.

<!--Image references-->

<!--Link references-->

  []: ./media/machine-learning-overview-of-azure-ml-process/oamlp1.png
  [1]: ./media/machine-learning-overview-of-azure-ml-process/oamlp2.png
  [2]: ./media/machine-learning-overview-of-azure-ml-process/oamlp3.png
  [3]: ./media/machine-learning-overview-of-azure-ml-process/oamlp4.png
  [4]: ./media/machine-learning-overview-of-azure-ml-process/oamlp5.png
  [5]: ./media/machine-learning-overview-of-azure-ml-process/oamlp6.png
  [6]: ./media/machine-learning-overview-of-azure-ml-process/oamlp7.png
  [7]: ./media/machine-learning-overview-of-azure-ml-process/oamlp8.png
  [8]: ./media/machine-learning-overview-of-azure-ml-process/oamlp9.png
  [9]: ./media/machine-learning-overview-of-azure-ml-process/oamlp10.png
  [10]: ./media/machine-learning-overview-of-azure-ml-process/oamlp11.png
  [11]: ./media/machine-learning-overview-of-azure-ml-process/oamlp12.png
  [12]: ./media/machine-learning-overview-of-azure-ml-process/oamlp13.png
  [13]: ./media/machine-learning-overview-of-azure-ml-process/oamlp14.png
  [14]: ./media/machine-learning-overview-of-azure-ml-process/oamlp15.png
  [15]: ./media/machine-learning-overview-of-azure-ml-process/oamlp16.png
