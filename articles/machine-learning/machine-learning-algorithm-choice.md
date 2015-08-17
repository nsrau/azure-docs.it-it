<properties 
	pageTitle="Come scegliere gli algoritmi di Machine Learning | Microsoft Azure" 
	description="Come scegliere gli algoritmi di Azure Machine Learning per l'apprendimento supervisionato e non supervisionato negli esperimenti di clustering, classificazione o regressione." 
	services="machine-learning"
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="bradsev;garye" />


# Come scegliere gli algoritmi di Azure Machine Learning per il clustering, la classificazione o la regressione

Questo argomento illustra alcuni aspetti di base sull'approccio dell'apprendimento automatico, sottolineando, in particolare, come scegliere gli algoritmi appropriati di Machine Learning per analizzare un determinato tipo di dati, rispondere a una domanda posta, completare un'attività specificata o fornire i criteri per prendere una decisione.

> [AZURE.TIP]Il [Foglio informativo sugli algoritmi di Microsoft Azure Machine Learning](machine-learning-algorithm-cheat-sheet.md) è un utile riferimento a complemento di questo articolo.

Quando si utilizza l'apprendimento automatico per eseguire un'analisi, in genere ci si pone due domande:

* Qual è il tipo di analisi necessario per completare gli obiettivi con i dati disponibili? 
* Qual è l'algoritmo più appropriato o il modello da utilizzare per eseguire l'analisi?

Vengono illustrati tre tipi di analisi di Machine Learning e vengono confrontati i rispettivi casi di utilizzo:

* **Clustering**
* **Classificazione** 
* **Regressione** 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<a name="anchor-1"></a>
## Gli algoritmi di Machine Learning apprendono dai dati

Machine Learning è una disciplina che studia una classe di algoritmi progettati per apprendere dai dati senza imporre un modello specifico e predeterminato da confrontare con i dati stessi. L'idea è di acquisire conoscenze in modo più induttivo esaminando i modelli in un set di dati anziché usando il metodo *ipotetico deduttivo* con cui si tenta prima di indovinare il modello appropriato per l'intero set di dati e poi testarlo empiricamente.

Sono disponibili due tipi di Machine Learning, ovvero di apprendimento dai dati: *apprendimento supervisionato* e *apprendimento non supervisionato*.

<a name="anchor-2"></a>
## Apprendimento supervisionato  

L'apprendimento supervisionato richiede che la variabile di destinazione sia ben definita e che venga fornito un numero sufficiente di valori.

L'apprendimento supervisionato è il tipo di Machine Learning che si verifica quando i risultati dell'output corretto (o variabili di destinazione) per gli input delle istanze di training sono noti. L'obiettivo del training di un algoritmo di Machine Learning è di trovare il modello (ovvero una regola o una funzione) che mappa gli input ai valori di output noti. Si tratta di una procedura simile a quando si dispone di un supervisore che può indicare all'agente algoritmico se gli input vengono mappati correttamente agli output. Quando, dopo aver completato il processo di apprendimento, si dispone di un modello utilizzabile, questo può essere applicato ai nuovi dati di input per stimare l'output previsto dove, a differenza del set di dati di training, i valori di destinazione non sono noti in anticipo.

Il tipo di modello viene determinato dalla natura della variabile di destinazione.

![Diagramma dell'apprendimento supervisionato: creare un modello dai dati etichettati e usarlo per stimare i risultati da nuovi dati.](./media/machine-learning-algorithm-choice/supervised-learning-using-known-data-to-model-solution.png)

Esistono due ampie categorie di analisi che utilizzano l'apprendimento supervisionato: *classificazione* e *regressione*. L'apprendimento supervisionato è piuttosto comune nei problemi di classificazione poiché l'obiettivo, solitamente, è che il computer apprenda un sistema di classificazione creato. Le risposte solitamente corrispondono ad alcuni valori (etichette) noti, ad esempio 'true' o 'false' oppure 'high', 'medium' o 'low'. Gli algoritmi di classificazione si applicano ai valori nominali e non di risposta ordinale. Il riconoscimento cifra è un esempio comune di apprendimento di classificazione. Più in generale, l'apprendimento di classificazione è appropriato per problemi in cui la determinazione della classificazione non è solo utile ma è anche semplice.

Nell'apprendimento supervisionato, le variabili prese in esame possono essere suddivise in due gruppi: variabili esplicative (denominate anche predittori) e variabili dipendenti (denominate anche variabili di risposta). L'obiettivo dell'analisi consiste nel definire una relazione tra le variabili esplicative e le variabili dipendenti come avviene nell'*analisi di regressione*. Per una parte sufficientemente grande del set di dati, è necessario conoscere i valori della variabile dipendente. Nella regressione, le risposte o variabili di output utilizzano valori continui, ad esempio chilometri al litro per una particolare automobile, l'età di una persona ecc.

L'apprendimento supervisionato è anche la tecnica più comune per le reti neurali di training e gli alberi delle decisioni:

> Entrambe le tecniche sono fortemente dipendenti dalle informazioni fornite dalle classificazioni predeterminate. Nel caso di reti neurali, la classificazione viene usata per determinare l'errore della rete e quindi modificarla per minimizzare tale errore, mentre nel caso di alberi delle decisioni, le classificazioni vengono usate per determinare quali attributi forniscono informazioni più complete e utilizzabili per risolvere il puzzle di classificazione. ... entrambi gli esempi richiedono la presenza di una "supervisione" nel senso che dipendono dalla fornitura delle classificazioni predeterminate.

>  Il riconoscimento vocale che usa i modelli Markov e le reti bayesiane nascosti si basa anche su elementi di controllo regolando i parametri per ridurre l'errore negli input specificati. [[Machine Learning, Part II: Supervised and Unsupervised Learning](http://www.aihorizon.com/essays/generalai/supervised_unsupervised_machine_learning.htm), [AI Horizon](http://www.aihorizon.com/)]


<a name="anchor-3"></a>
##Apprendimento non supervisionato

Nella Machine Learning, il problema dell'apprendimento non supervisionato consiste nel determinare se sono presenti modelli o una struttura nascosta nei dati non etichettati. Il modello non dispone di "risultati corretti" per un set di dati su cui eseguire il training. Poiché all'allievo vengono forniti esempi non etichettati, non esistono commenti, né in caso di errore né in caso di benefici, per valutare una soluzione potenziale. L'obiettivo consiste nel far apprendere al computer come eseguire un'operazione anche se non viene indicato esplicitamente il modo in cui farlo. In situazioni di apprendimento non supervisionato, tutte le variabili vengono trattate nello stesso modo. Non esiste alcuna distinzione tra variabili esplicative e variabili dipendenti. Tuttavia, esiste ancora un obiettivo da realizzare. Questo obiettivo potrebbe essere generico come ridurre i dati o più specifico come trovare i cluster.

In Azure Machine Learning è possibile eseguire l'apprendimento supervisionato e non supervisionato tramite **Clustering**, **Classificazione** e **Regressione**.

   ![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help2.png)

<a name="anchor-4"></a>
##Clustering
Il clustering è un esempio di apprendimento non supervisionato. In questo tipo di apprendimento, l'obiettivo consiste nel trovare analogie nei dati di training e nel partizionare il set di dati in subset delimitati da tali analogie. Il comportamento previsto, in base al quale i cluster più significativi rilevati da queste procedure basate sui dati sono coerenti con la classificazione intuitiva è spesso, ma non sempre, soddisfatto.

Sebbene l'algoritmo di clustering non assegni nomi appropriati a tali cluster, li può produrre e quindi usare per prevedere somiglianze previste nei nuovi esempi classificandoli nel cluster più appropriato. Questo approccio basato sui dati può essere utile quando è disponibile una quantità di dati sufficiente. Ad esempio, gli algoritmi di filtro delle informazioni sociali, ad esempio quelli utilizzati da Amazon.com per consigliare i libri, sono basati sulla ricerca di gruppi di utenti simili e quindi assegnare nuovi utenti a questi gruppi per creare raccomandazioni.

L'algoritmo di clustering disponibile in Azure Machine Learning è un [clustering K-Means][k-means-clustering].

![Esperimento dell'algoritmo di clustering K-Means: schermata](./media/machine-learning-algorithm-choice/k-means-clustering-algorithm-menu.png)

K-Means è uno degli algoritmi di clustering non supervisionato più semplici che risolvono i problemi noti di clustering. L'algoritmo KMeans esegue il cluster dei dati provando a separare gli esempi in N gruppi di uguale varianza, riducendo al minimo il criterio di "inerzia" o "somma dei quadrati all'interno del cluster". Questo algoritmo richiede il numero di cluster da specificare. K-Means può essere scalato per includere un numero elevato di campioni ed è stato usato in un'ampia gamma di aree applicative in diversi campi.

Il modulo dell'algoritmo di [clustering K-Means][k-means-clustering] restituisce un modello di clustering K-Means senza training che può essere passato al modulo [Modello di clustering di training][train-clustering-model] per il training.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/k4.png)

La figura illustra le opzioni da configurare quando si usa il clustering K-Means. Il metodo K-means trova un numero di cluster specificato per un set di punti dati D-dimensional. Iniziando con un *set iniziale di baricentri K*, il metodo usa l'algoritmo di Lloyd per migliorare in modo iterativo le posizioni dei baricentri. L'algoritmo termina quando i baricentri si stabilizzano o quando viene completato un *numero specificato di iterazioni*. Il modulo consente di inizializzare una matrice K-by-D con i baricentri finali che definiscono i cluster K trovati nei punti dati N. L'algoritmo utilizza anche un vettore di lunghezza N con l'assegnazione dei punti dati a uno dei cluster K. In presenza di un numero specifico di cluster (K) da trovare, il modulo assegna i primi punti dati K in ordine di cluster K.


Questo modulo accetta o genera anche punti iniziali per definire la configurazione iniziale del cluster. La *metrica* definisce il metodo usato per misurare la distanza tra un punto dati e il baricentro. Ciascun punto dati viene assegnato al cluster che dispone del baricentro più vicino al punto dati. Per impostazione predefinita, il metodo usa la *metrica euclidea*. È possibile specificare la *metrica coseno* come metrica alternativa per il metodo. Si noti che il metodo K-means potrebbe trovare solo una configurazione cluster locale ottimale per un set di dati. Il metodo può trovare una configurazione differente, probabilmente superiore, a partire da una diversa configurazione iniziale.

<a name="anchor-5"></a>
##Classificazione 
Nell'analisi di classificazione, gli esempi si dividono in classi e si usa un set di training di dati precedentemente etichettati. La tecnica viene utilizzata per stimare l'appartenenza al gruppo di istanze di dati. In Azure Machine Learning sono disponibili i seguenti algoritmi di classificazione.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help3.png)

Gli *algoritmi a due classi* vengono usati per le variabili di risposta binaria (sì o no, 0 o 1, true o false e così via), mentre gli *algoritmi multiclasse* vengono usati per qualsiasi variabile di risposta nominale che classifica le istanze in più di due classi.

### Linee guida per la selezione di un algoritmo di classificazione
Questo lungo elenco di algoritmi dà origine a una serie di domande:

* Come è possibile sapere quale classificatore, tra i molti presenti, conviene usare in un determinato set di dati? 
* Esistono casi in cui un classificatore rappresenta una scelta "naturale"? 
* Quali sono i principi di scelta?

Un approccio consigliato è di testare vari e diversi classificatori, nonché set di parametri diversi all'interno di ciascun algoritmo e quindi selezionare quello più adatto tramite la convalida incrociata.

> [AZURE.TIP] [Azure Machine Learning Studio](https://studio.azureml.net/) consente di provare più algoritmi affiancati con gli stessi dati e di confrontarne i risultati. Di seguito è riportato un esempio tratto dalla [Raccolta di Azure Machine Learning](http://gallery.azureml.net/): [Confrontare classificatori multiclasse: riconoscimento di lettere](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92).

Di seguito sono riportate delle linee guida generali che possono fornire un modo per avviare l'elaborazione. Quando si sceglie l'algoritmo da usare, prendere in considerazione gli elementi seguenti: [descrizione proposta dall'articolo [Scelta di un classificatore di Machine Learning](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen]

**Quanto sono pesanti i dati del training?** Se il set di training è piccolo e si vuole eseguire il training di un classificatore supervisionato, la teoria dell'apprendimento automatico afferma che è necessario usare un classificatore con compensazione elevata/varianza bassa, ad esempio Naive Bayes. Questi presentano un vantaggio rispetto ai classificatori con compensazione bassa/varianza elevata come kNN, poiché il secondo tende al sovradattamento. Ma i classificatori con compensazione bassa/varianza elevata sono più appropriati con un set di training di grandi dimensioni perché presentano un errore asintotico minore, e in questi casi un classificatore con compensazione elevata non è sufficientemente potente da fornire un modello accurato. Sono disponibili risultati teorici ed empirici che indicano che Naive Bayes funziona correttamente in tali circostanze. Considerare però che con dati migliori e funzionalità utili si può ottenere in genere un vantaggio maggiore rispetto a un algoritmo migliore. Con un set di dati di grandi dimensioni, inoltre, le prestazioni di classificazione potrebbero comunque non essere interessate dall'algoritmo usato. In tal caso, pertanto, è preferibile scegliere l'algoritmo in base a determinati fattori quali scalabilità, velocità o facilità d'uso.

**È necessario eseguire il training incrementale o in modalità batch?** Con una grande quantità di dati o se è necessario aggiornare spesso i dati, sarà preferibile usare gli algoritmi bayesiani. Le reti neurali e le SVM devono essere usate con i dati di training in modalità batch.

**I dati presenti sono esclusivamente relativi alle categorie, esclusivamente numerici o una combinazione di entrambi i tipi?** Bayes funziona meglio con i dati relativi alle categorie/binomiale. Gli alberi delle decisioni non possono stimare i valori numerici.

**L'utente o i destinatari devono comprendere il funzionamento del classificatore?** Gli alberi bayesiani o delle decisioni possono essere spiegati più facilmente. È molto più difficile illustrare o spiegare il modo in cui le reti neurali e le SVM classificano i dati.

**A quale velocità è necessario generare la classificazione?** Gli alberi delle decisioni possono essere lenti soprattutto se sono complessi. Le SVM invece sono più veloci durante la classificazione perché devono determinare solamente quale lato della "riga" dei dati è attiva.

**Quale livello di complessità presenta o richiede il problema?** Le reti neutrali e le SVM possono gestire classificazioni complesse non lineari.

### Vantaggi e svantaggi degli algoritmi di classificazione
Ognuno di questi algoritmi di classificazione presenta vantaggi e svantaggi:

<a name="anchor-5a"></a> **Vantaggi e svantaggi della regressione logistica:** "L'analisi della regressione logistica è basata sul calcolo delle probabilità del risultato come rapporto della probabilità che il risultato venga diviso rispetto alla probabilità che ciò non avvenga". [[Logistic Regression and Linear Discriminant Analyses in Evaluating Factors Associated with Asthma Prevalence among 10- to 12-Years-Old Children: Divergence and Similarity of the Two Statistical Methods](http://www.hindawi.com/journals/ijpedi/2009/952042/), George Antonogeorgos, et al (International Journal of Pediatrics, 2009) ID articolo 952042]
 
Il modello logistico è parametrico e pertanto offre il vantaggio di fornire una visione approfondita sull'impatto di ogni variabile predittiva sulla variabile di risposta.


Con le interpretazioni probabilistiche naturali disponibili (a differenza degli alberi delle decisioni o delle SVM), è possibile aggiornare facilmente il modello per incorporare i nuovi dati. Sono presenti molti modi per regolarizzare il modello e, diversamente da Naive Bayes, non è necessario preoccuparsi troppo delle funzionalità correlate. La regressione logistica è utile nei casi seguenti:

* quando si vuole un framework probabilistico per regolare le soglie di classificazione
* per incorporare rapidamente dati di training aggiuntivi  

La regressione logistica funziona meglio degli alberi delle decisioni per i dati di dimensioni elevate. Ad esempio, nella classificazione di testo, potrebbero essere presenti oltre centomila documenti con 500.000 parole distinte (funzionalità). Si consiglia una semplice regola come l'apprendimento di un iperpiano, poiché gli alberi delle decisioni presentano troppi gradi di libertà e sono soggetti al sovradattamento. È possibile applicare la selezione delle funzionalità per usare un albero delle decisioni con i dati di testo ma selezionando un subset di funzionalità ridotto molte informazioni utili per la classificazione del testo andranno perse. Quando si usano modelli di apprendimento con dati altamente dimensionali, è molto probabile che gli errori basati sulla varianza aumentino. In questo caso è preferibile usare modelli semplici con compensazione più elevata.

Uno svantaggio della regressione logistica è dato dall'instabilità quando un predittore non riesce a spiegare bene la variabile di risposta perché il coefficiente di questa variabile diventa molto elevato.

<a name="anchor-5b"></a> **Vantaggi e svantaggi degli alberi delle decisioni:** Gli [alberi delle decisioni](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf) sono facili da interpretare e spiegare.

> [Gli alberi delle decisioni] gestiscono facilmente le interazioni della funzionalità e non sono parametrici, quindi non è necessario preoccuparsi degli outlier o se i dati sono separabili in modo lineare (ad esempio, gli alberi delle decisioni si occupano facilmente dei casi in cui si ha la classe A all'estremità inferiore di alcune funzionalità x, la classe B a livello intermedio della funzionalità x e nuovamente la classe A all'estremità superiore). Uno svantaggio è che gli alberi delle decisioni non supportano l'apprendimento online, pertanto è necessario generare nuovamente l'albero quando arrivano nuovi esempi. Un altro svantaggio è che vengono facilmente sovradattati, ma in questo caso sarà possibile usare i metodi di insieme come foreste casuali (o alberi incrementati). Inoltre, le foreste casuali risultano essere la soluzione migliore in molti casi, in genere leggermente più utili delle SVM, essendo veloci e scalabili e non è necessario preoccuparsi della sintonizzazione di una serie di parametri come avviene con le SVM. [[Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen]

Gli alberi delle decisioni generano l'output come regole insieme alle metriche come *supporto*, *probabilità* e *accuratezza*.


<a name="anchor-5c"></a> **Vantaggi e svantaggi delle SVM:** Le Support Vector Machine (SVM) sono efficaci in spazi altamente dimensionali. Sono efficaci anche nei casi in cui il numero di dimensioni è maggiore del numero di campioni. Tuttavia, se il numero di funzionalità è molto più grande del numero dei campioni, le prestazioni del metodo possono essere scarse. Anche la memoria è molto efficiente in quanto usa un sottoinsieme di punti di formazione nella funzione di decisione (nota come vettori di supporto). Sono estremamente versatili: è possibile specificare diverse funzioni Kernel, sia standard che personalizzate, per la funzione di decisione. Le funzioni kernel vengono usate per trasformare da bassa ad alta la dimensionalità degli esempi. Procedura utile per i problemi relativi alla separabilità lineare.

Le SVM, tuttavia, non forniscono direttamente le stime di probabilità. Queste vengono calcolate utilizzando una costosa convalida incrociata a cinque livelli.

>[Con] elevata precisione, le garanzie teoriche solide relative al sovradattamento e un kernel appropriato, possono funzionare correttamente anche se i dati non sono separabili linearmente nello spazio della funzionalità di base. [Le SVM sono] particolarmente usate per problemi di classificazione del testo in cui gli spazi altamente dimensionali sono la norma. [[Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen]

A differenza delle foreste, le SVM provenienti da classificatori a due classi, sono state recentemente adattate a lavorare con più classi. È possibile utilizzare qualcosa di simile al training "one-vs-rest" per realizzare un classificatore a più classi che potrebbe essere meno ottimale. Poiché le SVM sono in grado di gestire solo output a due classi (ad esempio, una variabile di output categorica con varietà 2), con classi N, apprende N SVM (SVM 1 apprende “Output==1” rispetto a “Output != 1”, SVM 2 apprende “Output==2” rispetto a “Output != 2”, ..., SVM N apprende “Output==N” vs “Output != N”). Quindi per stimare l'output per un nuovo input, esegue la stima solo con ciascuna SVM e individua quello che inserisce la stima più distante nell'area positiva. [[Support Vector Machines](http://www.astro.caltech.edu/~george/aybi199/AMooreTutorials/svm.ppt), Andrew W. Moore (Carnegie Mellon University 2001)]

<a name="anchor-5d"></a> **Vantaggi e svantaggi di Naive Bayes:** I classificatori [Naive Bayes (NB)](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf) sono una scelta diffusa per i problemi di classificazione. Presuppongono che le funzionalità siano indipendenti, questo è ciò che rende la tecnica 'naive'.

> Se l'assunzione di indipendenza condizionale NB è attualmente in attesa, un classificatore Naive Bayes convergerà più rapidamente dei modelli discriminativi come la regressione logistica, quindi, in questi casi, sono necessari meno dati di training. Anche se l'assunzione NB non è in attesa,un classificatore NB spesso esegue un ottimo lavoro pratico. ... Il principale svantaggio è che non può apprendere le interazioni tra le funzionalità (ad esempio, non può comprendere che sebbene all'utente piacciano film con Brad Pitt e Tom Cruise, non vuole vedere quelli dove recitano insieme). [[Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen]


<a name="anchor-5e"></a> **One-vs-all:** One-vs-all è una strategia per ridurre il problema della classificazione a più classi a un set di più problemi di classificazione binaria. La strategia comporta il training di un singolo classificatore per classe, con gli esempi di tale classe come campioni positivi e tutti gli altri esempi come negativi. Tale strategia richiede che i classificatori di base producano una valutazione della confidenza con valori reali per la propria decisione anziché un'etichetta di classe. Solo le etichette di classi discrete possono causare ambiguità, dove più classi vengono stimate per un solo campione. [[Multiclass classification](http://en.wikipedia.org/wiki/Multiclass_classification) (Wikipedia 2006)]


<a name="anchor-6"></a>
##Regressione
 
Nell'analisi di regressione si prevedono nuovi valori in base all'inferenza precedente. I nuovi valori per una variabile dipendente vengono calcolati in base al valore di uno o più attributi misurati. I vari algoritmi di regressione disponibili in Azure Machine Learning sono:

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help4.png)

A seconda del caso di utilizzo e dei dati disponibili è possibile scegliere un algoritmo al posto di un altro. Di seguito vengono descritti alcuni algoritmi di regressione e i relativi casi di utilizzo principali.

<a name="anchor-6b"></a> **[Regressione lineare bayesiana][bayesian-linear-regression]** La regressione lineare bayesiana è un approccio di regressione lineare in cui l'analisi statistica viene effettuata all'interno del contesto di inferenza bayesiana. I risultati espliciti sono disponibili per le distribuzioni di probabilità posteriore dei parametri del modello quando il modello di regressione presenta errori normalmente distribuiti e può assumere una particolare forma di distribuzione precedente. [[Linear regression](http://en.wikipedia.org/wiki/Bayesian_linear_regression) ([Wikipedia](http://en.wikipedia.org))]

<a name="anchor-6f"></a> **[Regressione dell'albero delle decisioni con boosting][boosted-decision-tree-regression]** La regressione dell'albero delle decisioni con boosting calcola una relazione tra le stime e le variabili di risposta. La struttura ad albero di regressione è simile a un albero di classificazione. I nodi terminali sono i valori (modello) di funzione stimati. I valori stimati sono limitati ai valori nei nodi terminali. Alcuni dei vantaggi dell'utilizzo di alberi decisionali sono:

* facilità di interpretazione delle regole di decisione 
* assenza di parametrica per facilitare l'uso di un intervallo di livelli di dati numerici o categorici senza la necessità di selezionare dati di training unimodali
* affidabilità riguardo gli outlier nei dati di training 
* classificazione veloce dopo lo sviluppo delle regole 

Tuttavia, esistono alcuni svantaggi legati all'uso degli alberi delle decisioni:

* tendono alla sovradattabilità dei dati di training che possono fornire risultati poco soddisfacenti se applicati al set di dati completo
* non è possibile effettuare previsioni oltre il limite minimo e massimo della variabile di risposta nei dati di training


<a name="anchor-6g"></a> **[Regressione delle foreste delle decisioni][decision-forest-regression]** Le foreste delle decisioni possono essere usate per applicazioni di classificazione (variabili categoriche) o regressione (variabili continue). Le foreste di regressione possono essere usate per la regressione non lineare di variabili dipendenti dato un input indipendente e sia gli input che gli output possono essere multidimensionali. Le foreste di regressione non vengono usate tanto quanto le relative controparti di classificazione. La differenza principale è che le etichette di output delle foreste delle decisioni associate ai dati di input (e alle etichette di training) devono essere continue. Di conseguenza, la funzione dell'obiettivo deve essere adattata in modo appropriato. Le foreste di regressione condividono molti dei vantaggi delle foreste di classificazione, ad esempio l'efficienza e la flessibilità.

<a name="anchor-6a"></a> **[Regressione lineare][linear-regression]** 
La regressione lineare è ampiamente usata per modellare la relazione tra una variabile dipendente scalare Y e una o più variabili esplicative contrassegnate X. Può essere, e solitamente lo è, applicata per stime, previsioni o riduzione. Può essere utilizzata per adattare un modello di stima a un set di dati osservati dei valori X e Y. La regressione lineare presuppone che la struttura sottostante di Y sia una combinazione lineare delle variabili di X. Se un valore aggiuntivo di X viene quindi specificato senza il relativo valore y, il modello di regressione lineare adattato può essere utilizzato per stimare il valore di Y. I modelli di regressione lineare vengono in genere adattati tramite l'approccio dei minimi quadrati. Esistono tuttavia anche altre opzioni per misurare l'adattamento migliore. [[Linear regression](http://en.wikipedia.org/wiki/Bayesian_linear_regression) ([Wikipedia](http://en.wikipedia.org))]

<a name="anchor-6c"></a> **[Regressione della rete neurale][neural-network-regression]** 
Le reti neurali sono uno strumento statistico utile per la regressione non parametrica. La regressione non parametrica risolve il problema del tentativo di adattamento di un modello per una variabile Y su un set di variabili esplicative possibili X1; : : : ;Xp e dove la relazione tra X e Y può essere più complicata di una relazione lineare semplice. \[[Articolo su una struttura per la regressione non parametrica con reti neurali](http://ftp.isds.duke.edu/WorkingPapers/00-32.pdf), Herbert K. H. Lee \(ISDS, Duke University\)\]

<a name="anchor-6d"></a> **[Regressione ordinale][ordinal-regression]** 
La regressione ordinale è un tipo di analisi di regressione usata per modellare o stimare una variabile dipendente ordinale. Per le variabili dipendenti ordinali, è possibile classificare i valori, ma la distanza reale tra le categorie è sconosciuta. È significativo solo l'ordinamento relativo tra valori differenti. Poiché le etichette o i valori di destinazione dispongono di un ordinamento o classificazione naturale, è possibile utilizzare qualsiasi colonna numerica come destinazione ordinale. L'ordinamento naturale dei numeri viene utilizzato per la loro classificazione. Ad esempio, le patologie vengono valutate in base a una scala che va dalla meno grave alla più grave. Gli intervistati possono scegliere le risposte in base a una scala che va da "molto d'accordo" a "per nulla d'accordo". Gli studenti vengono valutati in base a una scale da A a F. Essenzialmente, la regressione ordinale è un'estensione della regressione logistica basata sul modello di *probabilità proporzionali*.


<a name="anchor-6e"></a> **[Regressione Poisson][poisson-regression]** 
La regressione Poisson viene spesso usata per modellare i dati di conteggio. La regressione Poisson presuppone che la variabile di risposta disponga di una distribuzione Poisson. I dati della distribuzione Poisson vengono valutati intrinsecamente con numeri interi, discreti e positivi, che hanno senso per i dati di conteggio. Dato un set di dati di training, la regressione Poisson tenta di trovare valori ottimali massimizzando la probabilità in forma logaritmica dei parametri presenti negli input. La probabilità dei parametri è la probabilità che i dati di training siano stati campionati da una distribuzione con tali parametri. Ad esempio, la regressione Poisson, potrebbe essere utile per:

* Modellare il numero di colds associati ai voli aerei 
* Stimare il numero di chiamate relative a un evento o promozione 
* Creare tabelle di emergenza

## Riferimenti

Per un elenco completo con i tipi di algoritmi di Machine Learning disponibili in Machine Learning Studio, vedere [Initialize Model](https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/) nella [Guida degli algoritmi e dei moduli di Machine Learning Studio](https://msdn.microsoft.com/library/azure/dn905974.aspx).

Per informazioni più dettagliate su tutti i tipi di algoritmi di Machine Learning, vedere i riferimenti ai seguenti, molti dei quali sono stati usati per la creazione di questo articolo.

* [Post di blog sulla scelta di un classificatore di Machine Learning](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen.

* [Articolo sulle foreste delle decisioni per la classificazione, la regressione, la stima della densità, l'apprendimento basato sulle varietà e l'apprendimento semi-supervisionato](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf), A. Criminisi1, J. Shotton2 e E. Konukoglu \(Microsoft Research, 2011\) report tecnico TR-2011-114.

* [Articolo su una struttura per la regressione non parametrica con reti neurali](http://ftp.isds.duke.edu/WorkingPapers/00-32.pdf), Herbert K. H. Lee \(ISDS, Duke University\).

* Manuale sui concetti e i metodi della statistica computazionale di James E. Gentle, Wolfgang Karl Härdle, Yuichi Mori \(Springer-Verlag Berlin Heidelberg New York, 2004\).

* [Articolo sulle divergenze e le analogie tra i due metodi statistici regressione logistica e analisi discriminante lineare nei fattori di valutazione associati alla prevalenza dell'asma in bambini di età compresa tra i 10 e i 12 anni](http://www.hindawi.com/journals/ijpedi/2009/952042/), George Antonogeorgos, Demosthenes B. Panagiotakos, Kostas N. Priftis e Anastasia Tzonou \(International Journal of Pediatrics, 2009\) ID articolo 952042.

* [Articolo sull'ottimalità di Naive Bayes](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf) \(University of New Brunswick 2004\) Harry Zhang.

* [Articolo sulle macchine a vettori di supporto](http://www.astro.caltech.edu/~george/aybi199/AMooreTutorials/svm.ppt), Andrew W. Moore \(Carnegie Mellon University 2001\).

* [Parte II dell'articolo su Machine Learning: apprendimento supervisionato e non supervisionato](http://www.aihorizon.com/essays/generalai/supervised_unsupervised_machine_learning.htm), [AI Horizon](http://www.aihorizon.com/).

* [Articolo sui vantaggi della regressione logistica rispetto agli alberi delle decisioni](http://www.quora.com/What-are-the-advantages-of-logistic-regression-over-decision-trees) \([Quora](http://www.quora.com/)\).

* [Articolo sulla differenza tra apprendimento supervisionato e apprendimento non supervisionato](http://stackoverflow.com/questions/1832076/what-is-the-difference-between-supervised-learning-and-unsupervised-learning) \([Stackoverflow](http://stackoverflow.com/)\).

* [Articolo sui vantaggi offerti dalla scelta di un classificatore di Machine Learning](http://stackoverflow.com/questions/2595176/when-to-choose-which-machine-learning-classifier) \([Stackoverflow](http://stackoverflow.com/)\).

* [Wikipedia](http://en.wikipedia.org):
	* [Pagina sulla regressione lineare Bayesiana](http://en.wikipedia.org/wiki/Bayesian_linear_regression)
	* [Regressione lineare](https://it.wikipedia.org/wiki/Regressione_lineare)
	* [Pagina sulla classificazione multiclasse](http://en.wikipedia.org/wiki/Multiclass_classification)
	* [Apprendimento non supervisionato](https://it.wikipedia.org/wiki/Apprendimento_non_supervisionato))

Vedere anche:

* [Foglio informativo sugli algoritmi di Microsoft Azure Machine Learning](machine-learning-algorithm-cheat-sheet.md) (Microsoft).

* [Choosing the right estimator](http://scikit-learn.org/stable/tutorial/machine_learning_map/) ([scikit-learn](http://scikit-learn.org/stable/index.html)).


<!-- Module References -->
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[decision-forest-regression]: https://msdn.microsoft.com/library/azure/562988b2-e740-4e3a-8131-358391bad755/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[neural-network-regression]: https://msdn.microsoft.com/library/azure/d7ee222c-669f-4200-a576-a761a9c1a928/
[ordinal-regression]: https://msdn.microsoft.com/library/azure/ffb557f8-dc7f-44bd-8fd0-b25666dd23f1/
[poisson-regression]: https://msdn.microsoft.com/library/azure/80e21b9d-3827-40d8-b733-b53148becbc2/

 

<!---HONumber=August15_HO6-->