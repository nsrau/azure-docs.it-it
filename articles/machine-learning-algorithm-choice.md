<properties 
	title="" 
	pageTitle="Come scegliere un algoritmo in Azure Machine Learning | Azure" 
	description="Viene illustrato come scegliere un algoritmo in Azure Machine Learning." 
	services="machine-learning"
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/9/2015" 
	ms.author="bradsev" />


# Come scegliere un algoritmo in Azure Machine Learning

In questo argomento vengono illustrati alcuni aspetti di base sull'approccio dell'apprendimento automatico, sottolineando, in particolare, come selezionare un algoritmo appropriato per analizzare un determinato tipo di dati, in risposta a una domanda posta, per completare un'attività specificata o per fornire criteri per prendere una decisione. Quando si utilizza l'apprendimento automatico per eseguire un'analisi, in genere ci si pone due domande: 

* Qual è il tipo di analisi necessario per completare gli obiettivi con i dati disponibili? 
* Qual è l'algoritmo più appropriato o il modello da utilizzare per eseguire l'analisi?

Vengono illustrati e confrontati tre tipi di analisi e i rispettivi casi di utilizzo: 

* classificazione 
* regressione 
* clustering

In Azure Machine Learning vengono descritti anche i vari algoritmi per ogni tipo di analisi e i moduli che li contengono. 


<a name="anchor-1"></a>
##**Machine Learning**

Machine Learning è una disciplina che studia una classe di algoritmi basata sui dati in quanto sono progettate per apprendere dai dati senza imporre un modello specifico e predeterminato da confrontare con i dati stessi. L'idea è di acquisire conoscenze in modo più induttivo esaminando i modelli in un set di dati anziché utilizzando ciò che viene in genere definito metodo ipotetico deduttivo con cui si tenta prima di indovinare il modello appropriato per l'intero set di dati e poi testarlo empiricamente. Sono disponibili due tipi di apprendimento dai dati: apprendimento controllato e non controllato. 

<a name="anchor-2"></a>
##**Apprendimento supervisionato**  

L'apprendimento supervisionato richiede che la variabile di destinazione sia ben definita e vengano forniti un numero sufficiente di valori. 

L'apprendimento supervisionato è il tipo di apprendimento che si verifica quando i risultati dell'output corretto (o variabili di destinazione) per gli input delle istanze di training sono noti. L'oggetto del training di un algoritmo di apprendimento automatico è di trovare il modello (cioè una regola o funzione) che esegue il mapping degli input nei valori di output noti. Si tratta di una procedura simile a quando si dispone di un supervisore che può indicare all'agente algoritmico di eseguire o meno il mapping per ottenere degli output corretti. Quando, dopo aver completato il processo di apprendimento, si dispone di un modello utilizzabile, questo può essere applicato ai nuovi dati di input per stimare l'output previsto dove, a differenza del set di dati di training, i valori di destinazione non sono  noti in anticipo.

La natura della variabile di destinazione determina il tipo di modello.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help9.png)

Esistono due ampie categorie di analisi che utilizzano l'apprendimento controllato: classificazione e regressione. L'apprendimento controllato è piuttosto comune in *classification problems* poiché l'obiettivo, solitamente, è che il computer apprenda un sistema di classificazione creato. Le risposte solitamente corrispondono ad alcuni valori (etichette) noti, ad esempio 'true' o 'false'. Gli algoritmi di classificazione si applicano ai valori nominali e non di risposta ordinale. Il riconoscimento cifra è un esempio comune di apprendimento di classificazione. Più in generale, l'apprendimento di classificazione è appropriato per problemi in cui è utile dedurre una classificazione e questa è più facile da determinare.

Nell'apprendimento controllato, le variabili prese in esame possono essere suddivise in due gruppi: variabili esplicative (denominate anche predittori) e variabili dipendenti (denominate anche variabili di risposta). L'obiettivo dell'analisi consiste nello specificare una relazione tra variabili esplicative e dipendenti, come avviene in *regression analysis*. Per una parte sufficiente del set di dati, è necessario conoscere i valori della variabile dipendente. Nella regressione, le risposte o variabili di output utilizzano valori continui, ad esempio chilometri al litro per una particolare automobile, l'età di una persona ecc.

L'apprendimento controllato è anche la tecnica più comune per le reti neurali di training e gli alberi decisionali. Entrambe le tecniche sono fortemente dipendenti dalle informazioni fornite dalle classificazioni predeterminate. 

* Nel caso di reti neurali, la classificazione viene utilizzata per determinare l'errore della rete e quindi modificarla per minimizzare tale errore. 
* Nel caso di alberi delle decisioni, le classificazioni vengono utilizzate per determinare quali attributi forniscono informazioni più complete e utilizzabili per risolvere il puzzle di classificazione.  

Entrambi gli esempi richiedono la presenza di "supervisione" nel senso che dipendono dalla fornitura delle classificazioni predeterminate. 

Il riconoscimento vocale che utilizza i modelli Markov e le reti Bayes nascosti, si basa anche su elementi di controllo regolando i parametri per ridurre l'errore negli input specificati. 

<a name="anchor-3"></a>
##**Apprendimento non controllato**

Nella Machine Learning, il problema dell'apprendimento non supervisionato consiste nel trovare modelli o la struttura nascosta nei dati non etichettati. Il modello non dispone di "risultati corretti" per un set di dati su cui eseguire il training. Poiché gli esempi forniti all'allievo non sono etichettati, non esiste alcun segnale di errore per valutare una soluzione potenziale. L'obiettivo consiste nel far apprendere al computer, in modo esplicito, come eseguire un'operazione non specificata. In situazioni di apprendimento non controllato, tutte le variabili vengono trattate nello stesso modo. Non esiste alcuna distinzione tra variabili esplicative e dipendenti. Tuttavia, esiste ancora un obiettivo da realizzare. Questo obiettivo potrebbe essere generico come ridurre i dati o più specifico come trovare i cluster. 

In Azure Machine Learning è possibile eseguire l'apprendimento controllato e non controllato tramite **Classificazione**, **Clustering** e **Regressione**.

   ![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help2.png)

<a name="anchor-4"></a>
##**Clustering**  
Il clustering è un esempio di apprendimento non controllato. In questo tipo di apprendimento, l'obiettivo consiste nel trovare analogie nei dati di training e nel partizionare il set di dati in subset delimitati da tali analogie. Il comportamento previsto, in base al quale i cluster più significativi rilevati da queste procedure basate sui dati sono coerenti con la classificazione intuitiva è spesso, ma non sempre, soddisfatto. 

Sebbene l'algoritmo non assegni nomi appropriati a tali cluster, li può produrre e quindi utilizzare per prevedere somiglianze previste nei nuovi esempi classificandoli nel cluster più appropriato. Si tratta di un approccio basato sui dati che funziona correttamente quando sono disponibili abbastanza dati. Ad esempio, gli algoritmi di filtro delle informazioni sociali, ad esempio quelli utilizzati da Amazon.com per consigliare i libri, sono basati sulla ricerca di gruppi di utenti simili e quindi assegnare nuovi utenti a questi gruppi per creare raccomandazioni.

L'algoritmo di clustering disponibile in Azure Machine Learning è un clustering K-means.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help10.png)

K-means è uno degli algoritmi di apprendimento non controllato più semplice che risolve i problemi noti di clustering. L'algoritmo KMeans esegue il cluster dei dati tentando di separare esempi in N gruppi di uguale varianza, riducendo al minimo un criterio noto come l'inerzia o la somma dei quadrati all'interno del cluster di dati. Questo algoritmo richiede il numero di cluster da specificare. Viene ben ridimensionato per un numero elevato di campioni ed è stato utilizzato in un'ampia gamma di aree applicative in diversi campi.

Il modulo **Clustering K-Means**, che implementa l'algoritmo K-means, restituisce un modulo di clustering K-means non sottoposto a training che può essere passato al modulo **Modello clustering di formazione** per il training.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/k4.png)

La figura illustra le opzioni da configurare quando si utilizza il Clustering K-Means. Il metodo K-means trova un numero di cluster specificato per un set di punti dati D-dimensional. Iniziando con un *initial set of K centroids*, il metodo utilizza l'algoritmo di Lloyd per migliorare in modo iterativo le posizioni dei baricentri. L'algoritmo termina quando i baricentri si stabilizzano o quando un *specified number of iterations* viene completato.
Il modulo consente di inizializzare una matrice K-by-D con i baricentri finali che definiscono i cluster K trovati nei punti dati N. L'algoritmo utilizza anche un vettore di lunghezza N con l'assegnazione dei punti dati a uno dei cluster K.
In presenza di un numero specifico di cluster (K) da trovare, il modulo assegna i primi punti dati K in ordine di cluster K.
Questo modulo accetta o genera anche punti iniziali per definire la configurazione iniziale del cluster.
*Metrica* definisce il metodo utilizzato per misurare la distanza tra un punto dati e il baricentro.
Ciascun punto dati viene assegnato al cluster che dispone del baricentro più vicino al punto dati. Per impostazione predefinita, il metodo utilizza il *Euclidean metric*. È possibile specificare il *cosine metric* come metrica alternativa per il metodo.
Si noti che il metodo K-means potrebbe trovare solo una configurazione cluster locale ottimale per un set di dati. Il metodo può trovare una configurazione differente, probabilmente superiore, a partire da una diversa configurazione iniziale.

<a name="anchor-5"></a>
##**Classificazione**  
Nell'analisi di classificazione, gli esempi si dividono in classi e si utilizza un set di training di dati precedentemente etichettati. La tecnica viene utilizzata per stimare l'appartenenza al gruppo di istanze di dati. 
In Azure Machine Learning sono disponibili i seguenti algoritmi di classificazione.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help3.png)

I *Two-Class algorithms* vengono utilizzati per le variabili di risposta binaria (sì o no, 0 o 1, true o false ecc) mentre *Multiclass algorithms* vengono utilizzati per qualsiasi variabile di risposta nominale che classifica le istanze in più di due classi

### Linee guida per la selezione di un algoritmo di classificazione
Questo lungo elenco di algoritmi dà origine a una serie di domande: 

* Come è possibile sapere quale classificatore, tra i molti presenti, conviene utilizzare in un determinato set di dati? 
* Esistono casi in cui uno di essi rappresenta una scelta "naturale"? 
* Quali sono i principi di scelta?

Un approccio consigliato è di testare vari e diversi classificatori, nonché set di parametri diversi all'interno di ciascun algoritmo e quindi selezionare quello più adatto tramite la convalida incrociata. Di seguito sono riportate delle linee guida generali che possono fornire un modo per avviare l'elaborazione. Quando si sceglie l'algoritmo da utilizzare, prendere in considerazione le seguenti informazioni:

**Quanto sono pesanti i dati del training?**
Se il set di training è piccolo e si desidera eseguire il training di un classificatore controllato, la teoria dell'apprendimento automatico afferma che è necessario utilizzare un classificatore con classificatori ad alto/basso livello di varianza, ad esempio Naive Bayes. Questi presentano un vantaggio rispetto ai classificatori con compensazione bassa/varianza elevata come kNN, poiché il secondo tende al sovradattamento. Ma i classificatori con compensazione bassa/varianza elevata avviano il processo all'aumentare del set di training (presentano un errore asintotico minore), poiché i classificatori con compensazione elevata non sono sufficientemente potenti da fornire modelli accurati. Sono disponibili risultati teorici ed empirici che indicano che Naive Bayes funziona correttamente in tali circostanze. Considerare però che, in genere, dati migliori sono più utili rispetto ad algoritmi migliori e che la progettazione di buone funzionalità fornisce un vantaggio significativo. Se si dispone di un set di dati di grandi dimensioni, l'algoritmo di classificazione utilizzato potrebbe non essere adatto per le prestazioni di classificazione. Pertanto, può essere più utile scegliere l'algoritmo basato su  buona scalabilità, velocità o facilità di utilizzo.

**È necessario eseguire il training incrementale o in modalità batch?** 
Se è necessario aggiornare spesso il classificatore con nuovi dati (o se si dispone di una grande quantità di dati), probabilmente si desidera utilizzare gli algoritmi di Bayes. Le reti neurali e SVM devono lavorare su dati di training in modalità batch.

**I dati presenti sono esclusivamente relativi alle categorie, esclusivamente numerici o una combinazione di entrambi i tipi?** 
Bayes funziona meglio con i dati relativi alle categorie/binomiale. Gli alberi delle decisioni non possono stimare i valori numerici.

**L'utente o i destinatari deve comprendere il funzionamento del classificatore?**  Utilizzare Bayes o gli alberi delle decisioni, poiché possono essere spiegati facilmente alla maggior parte degli utenti. Le reti neurali e SVM sono "scatole nere" nel senso che non è realmente possibile visualizzare la classificazione dei dati.

**A quale velocità è necessario generare la classificazione?** 
Durante la classificazione, le SVM sono veloci poiché devono determinare solamente quale lato della "riga" dei dati è attiva. Gli alberi decisionali possono essere lenti, soprattutto se sono complessi (ad esempio, in presenza di molti rami).

**Quale livello di complessità presenta o richiede il problema?** Le reti neutrali e SVM possono gestire classificazioni complesse e non lineari.

### Vantaggi e svantaggi degli algoritmi di classificazione
Ognuno di questi algoritmi di classificazione presenta vantaggi e  svantaggi:

<a name="anchor-5a"></a>
**Vantaggi e svantaggi della regressione logistica:**   
L'analisi della regressione logistica è basata sul calcolo delle probabilità del risultato come il rapporto della probabilità che questo sia diviso per la probabilità che avvenga. Il modello logistico  è parametrico e pertanto offre il vantaggio di fornire una visione approfondita sull'impatto di ogni variabile predittiva sulla variabile di risposta. Con le interpretazioni probabilistiche naturali disponibili (a differenza degli alberi decisionali o SVM), è possibile aggiornare facilmente il modello per comprendere nuovi dati. Sono presenti molti modi per regolarizzare il modello e non è necessario preoccuparsi troppo delle funzionalità correlate, come avviene con Naive Bayes. La regressione logistica è utile se si desidera 

* un framework probabilistico che consenta di regolare facilmente le soglie di classificazione per indicare quando non vi è certezza o per ottenere intervalli di confidenza 
* nel caso si preveda di ricevere in futuro ulteriori dati di training da incorporare rapidamente nel modello. 

La regressione logistica funziona meglio degli alberi delle decisioni per i dati di dimensioni elevate. Ad esempio, nella classificazione di testo, potrebbero essere presenti oltre centomila documenti con 500.000 parole distinte (funzionalità). Si consiglia una semplice regola come l'apprendimento di un hyperplane, poiché gli alberi delle decisioni presentano troppi gradi di libertà e sono soggetti al sovradattamento. È possibile selezionare la funzionalità per l'utilizzo di un albero delle decisioni su dati di testo. Prelevando un sottoinsieme di funzionalità ridotto molte informazioni utili per la classificazione di testo andranno perse. Quando si utilizzano modelli di apprendimento con dati di grandi dimensioni, è molto probabile che le dimensioni degli errori basati sulla varianza aumentino e che i modelli semplici con compensazione elevata siano gli elementi con maggiore rilevanza. 

Uno svantaggio della regressione logistica è la sua instabilità quando un predittore fatica a spiegare la variabile di risposta perché il coefficiente di questa variabile diventerà molto elevato.

<a name="anchor-5b"></a>
**Vantaggi e svantaggi degli alberi decisionali:**   
[Gli alberi decisionali](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf) sono facili da interpretare e spiegare. Gestiscono facilmente le interazioni della funzionalità e non sono parametrici, quindi non è necessario preoccuparsi degli outlier o se i dati sono separabili in modo lineare (ad esempio, gli alberi delle decisioni si occupano facilmente dei casi in cui si dispone di classe A all'estremità inferiore di alcune funzionalità x, la classe B a livello intermedio della funzionalità x e A nuovamente all'estremità superiore). Gli alberi delle decisioni generano l'output come regole e le metriche come supporto, probabilità e accuratezza. 

Uno svantaggio è che gli alberi delle decisioni non supportano l'apprendimento online, pertanto è necessario generare nuovamente la struttura ad albero quando arrivano nuovi esempi. Un altro svantaggio è che vengono facilmente sovradattati, ma in questo caso i metodi di insieme come foreste casuali (o alberi incrementati) sono molto utili. Inoltre, le foreste casuali risultano essere la soluzione migliore in molti casi. In genere risultano leggermente più utili delle SVM: sono veloci e scalabili e non è necessario preoccuparsi della sintonizzazione di una serie di parametri come avviene con le SVM.


<a name="anchor-5c"></a>
**Vantaggi e svantaggi delle SVM:**   
Le Support Vector Machine (SVM) sono efficaci in spazi dimensionali elevati. È efficace anche nei casi in cui il numero di dimensioni è maggiore del numero di campioni. Tuttavia, se il numero di funzionalità è molto maggiore del numero dei campioni, le prestazioni del metodo possono essere scarse. La memoria è molto efficiente in quanto utilizza un sottoinsieme di punti di formazione nella funzione di decisione (nota come vettori di supporto).  È estremamente versatile: è possibile specificare diverse funzioni Kernel per la funzione di decisione. Sono disponibili kernel comuni, ma è anche possibile specificare kernel personalizzati. Le funzione kernel vengono utilizzate per trasformare da bassa ad alta la dimensionalità degli esempi. Procedura utile per il problema della separabilità lineare. 

Le SVM, tuttavia, non forniscono direttamente le stime di probabilità. Queste vengono calcolate utilizzando una costosa convalida incrociata a cinque livelli. Con elevata precisione, le garanzie teoriche solide relative al sovradattamento e un kernel appropriato, possono funzionare correttamente anche se i dati non sono separabili linearmente nello spazio della funzionalità di base. È particolarmente utilizzato per problemi di classificazione del testo in cui gli spazi di dimensioni molto elevate sono la norma. A differenza delle foreste, le SVM provenienti da classificatori a due classi, sono state recentemente adattate a lavorare con più classi. È possibile utilizzare qualcosa di simile al training "one-vs-rest" per realizzare un classificatore a più classi che potrebbe essere meno ottimale. Poiché le SVM sono in grado di gestire solo output a due classi (ad esempio, una variabile di output categorica con varietà 2), con classi N, apprende N SVM (SVM 1 apprende "Output==1" rispetto a "Output != 1", SVM 2 apprende "Output==2" rispetto a "Output != 2", ..., SVM N apprende "Output==N" vs "Output != N"). Quindi per stimare l'output per un nuovo input, esegue la stima solo con ciascuna SVM e individua quello che inserisce la stima più distante nell'area positiva.

<a name="anchor-5d"></a>
**Vantaggi e svantaggi di Naive Bayes:**   
I classificatori [Naive Bayes](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf) (NB) sono una scelta popolare per problemi di classificazione. Si presuppone che le funzionalità siano indipendenti, questo è ciò che effettua la tecnica 'naive'. Se l'assunzione di indipendenza condizionale NB è attualmente in attesa, un classificatore Naive Bayes convergerà più rapidamente dei modelli discriminativi come la regressione logistica. Quindi, in questi casi, sono necessari meno dati di training. 

Anche se l'assunzione NB non è in attesa,un classificatore NB spesso esegue un ottimo lavoro pratico.  In pratica, Bayes è adatto non solo quando le funzionalità sono indipendenti, ma anche quando le dipendenze delle funzionalità sono simili tra loro. NB pertanto rappresenta una buona soluzione se si desidera qualcosa di rapido e semplice che funziona correttamente.

Il principale svantaggio è che non può apprendere le interazioni tra le funzionalità (ad esempio, non può comprendere che sebbene all'utente piacciano film con Brad Pitt e Tom Cruise, non vuole vedere quelli dove recitano insieme).


<a name="anchor-5e"></a>
**One vs all:**  
"One vs all" è una strategia per ridurre il problema della classificazione a più classi su un set di diversi problemi di classificazione binaria. La strategia comporta il training di un singolo classificatore per classe, con gli esempi di tale classe come campioni positivi e tutti gli altri esempi come negativi. Tale strategia richiede che i classificatori di base producano una valutazione della confidenza con valori reali per la propria decisione anziché un'etichetta di classe. Solo le etichette di classi discrete possono causare ambiguità, dove più classi vengono stimate per un solo campione.	


<a name="anchor-6"></a>
##**Regressione**  
Nell'analisi di regressione si prevedono nuovi valori in base all'inferenza precedente. I nuovi valori per una variabile dipendente vengono calcolati in base al valore di uno o più attributi misurati. I vari algoritmi di regressione disponibili in Azure Machine Learning sono:

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help4.png)

A seconda del caso di utilizzo e dei dati disponibili è possibile scegliere un algoritmo/modulo al posto di un altro. Di seguito vengono descritti alcuni algoritmi di regressione e i relativi casi di utilizzo principali.

<a name="anchor-6b"></a>
**Regressione lineare Bayes**                      
La regressione lineare Bayes è un approccio di regressione lineare in cui l'analisi statistica viene effettuata all'interno del contesto di inferenza Bayes. I risultati espliciti sono disponibili per le distribuzioni di probabilità posteriore dei parametri del modello quando il modello di regressione presenta errori normalmente distribuiti e può assumere una particolare forma di distribuzione precedente.

<a name="anchor-6f"></a>
**Regressione albero decisionale incrementata**  
La regressione calcola una relazione tra le stime e le variabili di risposta. La struttura ad albero di regressione è simile a un albero di classificazione. I nodi terminali sono i valori (modello) di funzione stimati. I valori stimati sono limitati ai valori nei nodi terminali. Alcuni dei vantaggi dell'utilizzo di alberi decisionali sono: 

* facilità di interpretazione delle regole di decisione 
* assenza di parametrica per facilitare l'incorporazione di un intervallo di livelli di dati numerici o categorici senza la necessità di selezionare dati di training unimodali 
* affidabilità riguardo gli outlier nei dati di training 
* classificazione veloce dopo lo sviluppo delle regole 

Alcuni svantaggi dell'utilizzo degli alberi decisionali sono che tendono alla sovradattabilità dei dati di training che possono fornire risultati poco soddisfacenti se applicati al set di dati completo. Inoltre non è possibile effettuare previsioni oltre il limite minimo e massimo della variabile di risposta nei dati di training.

<a name="anchor-6g"></a>
**Regressione insieme di strutture di decisione**  
Gli insiemi delle strutture di decisione possono essere utilizzati per applicazioni di classificazione (variabili categoriche) o regressione (variabili continue). Gli insiemi di strutture di regressione possono essere utilizzati per la regressione non lineare di variabili dipendenti date da input indipendenti. Input e output possono essere multidimensionali. Gli insiemi di strutture di regressione non vengono utilizzati tanto quanto le relative controparti di classificazione. La differenza principale è che l'etichetta di output degli insiemi delle strutture di decisione da associare ai dati di input quindi, le etichette di training devono essere continue. Di conseguenza, la funzione dell'obiettivo deve essere adattata in modo appropriato. Gli insiemi di strutture di regressione condividono molti dei vantaggi degli insiemi di strutture di classificazione, ad esempio l'efficienza e la flessibilità.

<a name="anchor-6a"></a>
**Regressione lineare**  
La regressione lineare è ampiamente utilizzata per modellare la relazione tra una variabile dipendente scalare Y e una o più variabili esplicative contrassegnata X. Può essere, e solitamente lo è, applicata per stime, previsioni o riduzione. Può essere utilizzata per adattare un modello di stima a un set di dati osservati dei valori X e Y. La regressione lineare presuppone che la struttura sottostante di Y sia una combinazione lineare delle variabili di X. Se un valore aggiuntivo di X viene quindi specificato senza il relativo valore y, il modello di regressione lineare adattato può essere utilizzato per stimare il valore di Y. I modelli di regressione lineare vengono in genere adattati tramite l'approccio dei minimi quadrati. Esistono tuttavia anche altre opzioni per misurare l'adattamento migliore.

<a name="anchor-6c"></a>
**Regressione rete neurale**  
Le reti neurali sono uno strumento statistico utile per la regressione non parametrica. La regressione non parametrica risolve il problema del tentativo di adattamento di un modello per una variabile Y su un set di variabili esplicative possibili X1; : : : ;Xp e dove la relazione tra X e Y può essere più complicata di una relazione lineare semplice.

<a name="anchor-6d"></a>
**Regressione ordinale**   
La regressione ordinale è un tipo di analisi di regressione utilizzata per modellare o stimare una variabile dipendente ordinale. Per le variabili dipendenti ordinali, è possibile classificare i valori, ma la distanza reale tra le categorie è sconosciuta. È significativo solo l'ordinamento relativo tra valori differenti. Poiché le etichette o i valori di destinazione dispongono di un ordinamento o classificazione naturale, è possibile utilizzare qualsiasi colonna numerica come destinazione ordinale. L'ordinamento naturale dei numeri viene utilizzato per la loro classificazione. Le patologie vengono valutate su scale che vanno da meno grave a più grave. Gli intervistati possono scegliere le risposte su scale che vanno da concordo pienamente a rifiuto pienamente. Gli studenti vengono valutati su scale da A a F. Essenzialmente, la regressione ordinale è un'estensione della regressione logistica basata sul modello di probabilità proporzionale.


<a name="anchor-6e"></a>
**Regressione Poisson**  
La regressione Poisson viene spesso utilizzata per modellare i dati di conteggio. La regressione Poisson presuppone che la variabile di risposta disponga di una distribuzione Poisson. I dati della distribuzione Poisson vengono valutati intrinsecamente con numeri interi, discreti e positivi, che hanno senso per i dati di conteggio. Dato un set di dati di training, la regressione Poisson tenta di trovare valori ottimali massimizzando la probabilità in forma logaritmica dei parametri presenti negli input. La probabilità dei parametri è la probabilità che i dati di training siano stati campionati da una distribuzione con tali parametri. Ad esempio, la regressione Poisson, potrebbe essere utile per: 

* Modellare il numero di colds associati ai voli aerei 
* Stimare il numero di chiamate relative a un evento o promozione 
* Creare tabelle di emergenza



<!--HONumber=49-->