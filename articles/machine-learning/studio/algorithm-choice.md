---
title: Come scegliere gli algoritmi
titleSuffix: Azure Machine Learning Studio
description: Come scegliere gli algoritmi di Azure Machine Learning Studio per l'apprendimento supervisionato e non supervisionato negli esperimenti di clustering, classificazione o regressione.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: 3bb88f2f9546ec25433061a0704bd144730bd34c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57894632"
---
# <a name="how-to-choose-algorithms-for-azure-machine-learning-studio"></a>Come scegliere gli algoritmi di Azure Machine Learning Studio

La risposta alla domanda "Quale algoritmo di Machine Learning devo usare" è sempre "Dipende". Dipende dalla dimensione, dalla qualità e dalla natura dei dati. Dipende da cosa si vuole fare con la risposta. Dipende da come i calcoli dell'algoritmo sono stati convertiti in istruzioni per il computer in uso. E dipende dal tempo a disposizione. Nemmeno gli scienziati dei dati più esperti possono stabilire quale algoritmo funzionerà meglio prima di provarlo.

Machine Learning Studio offre algoritmi all'avanguardia, ad esempio gli alberi delle decisioni con boosting scalabili, i sistemi di raccomandazione bayesiani, le reti neurali basate su Machine Deep Learning e le giungle delle decisioni sviluppati da Microsoft Research. Sono inclusi anche pacchetti open source scalabili di apprendimento automatico come Vowpal Wabbit. Machine Learning Studio supporta gli algoritmi di Machine Learning per la classificazione multiclasse e binaria, la regressione e il clustering. Vedere l'elenco completo dei [moduli di Machine Learning](/azure/machine-learning/studio-module-reference/index).
La documentazione offre alcune informazioni su ogni algoritmo e su come modificare i parametri disponibili per ottimizzare l'algoritmo in base al tipo di utilizzo.  


## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Foglio informativo sugli algoritmi di Machine Learning

Il **[Microsoft Azure Machine Learning Studio foglio informativo sugli algoritmi](algorithm-cheat-sheet.md)** utile nella scelta destra machine algoritmo di apprendimento per le soluzioni di analitica predittiva da Azure Machine Learning Libreria di studio degli algoritmi.
Questo articolo illustra come utilizzare questo foglio informativo.

> [!NOTE]
> Per scaricare il foglio informativo e proseguire con questo articolo, passare a [Foglio informativo sugli algoritmi di Machine Learning per Microsoft Azure Machine Learning Studio](algorithm-cheat-sheet.md).
> 
> 

Questo foglio informativo ha destinatari molto specifici: un scienziato dei dati iniziali con Machine Learning di livello universitario , che tenta di scegliere un algoritmo per iniziare con Azure Machine Learning Studio. Ciò significa che fa alcune generalizzazioni e semplificazioni eccessive, ma indicherà una direzione sicura. Significa inoltre che esistono molti algoritmi non elencati qui.

Queste indicazioni sono commenti e suggerimenti compilati da parte di scienziati dei dati ed esperti di Machine Learning. È non possibile concordare tutti gli elementi, ma abbiamo provato a armonizzazione le nostre opinioni in un consenso approssimativo. La maggior parte delle indicazioni di disaccordo iniziano con "Dipende..."

### <a name="how-to-use-the-cheat-sheet"></a>Come utilizzare il foglio informativo

Leggere il percorso e le etichette degli algoritmi sul grafico come "Per *&lt;etichetta percorso&gt;* usare *&lt;algoritmo&gt;*". Ad esempio, "Per *velocità* usare *regressione logistica a due classi*". A volte è possibile applicare più di una branca.
A volte nessuno di essi sarà la scelta perfetta. Devono essere considerati consigli generali, pertanto non è necessario che siano esatti.
Diversi scienziati dei dati che sono state presentate con detto che l'unico modo sicuro per trovare l'algoritmo migliore consiste nel provare tutti gli elementi.

Un esempio tratto da [Azure AI Gallery](https://gallery.azure.ai/) di un esperimento che prova diversi algoritmi sugli stessi dati e ne confronta i risultati è disponibile in: [Compare Multi-class Classifiers: Letter recognition](https://gallery.azure.ai/Details/a635502fc98b402a890efe21cec65b92) (Confrontare classificatori multiclasse: riconoscimento di lettere).

> [!TIP]
> Per scaricare una panoramica infografica di facile comprensione delle nozioni fondamentali di Machine Learning per ottenere informazioni sugli algoritmi comuni usati per rispondere a domande frequenti su Machine Learning, vedere [Nozioni fondamentali di Machine Learning con esempi di algoritmi](basics-infographic-with-algorithm-examples.md).

## <a name="flavors-of-machine-learning"></a>Varianti di Machine Learning

### <a name="supervised"></a>Supervisionato

Gli algoritmi di apprendimento supervisionato fanno previsioni in base a un set di esempi. Ad esempio, quotazioni di titoli cronologici utilizzabile per fare supposizioni sui prezzi futuri. Ogni esempio utilizzato per l’addestramento viene etichettato con il valore di interesse: in questo caso, il prezzo dei titoli. Un algoritmo di apprendimento supervisionato cerca modelli ripetitivi nelle etichette dei valori. Può usare tutte le informazioni che potrebbero essere rilevanti, ovvero il giorno della settimana, la stagione, i dati finanziari dell'azienda, il tipo di settore, la presenza di eventi geopolitici di disturbo, e ogni algoritmo cerca tipi diversi di modelli ripetitivi. Dopo che l'algoritmo ha trovato il modello ripetitivo migliore possibile, lo utilizza per eseguire stime per i dati di prova senza etichetta: i prezzi di domani.

L'apprendimento supervisionato è un tipo di Machine Learning è utile e diffuso. Con una eccezione, tutti i moduli di Azure Machine Learning Studio sono algoritmi di apprendimento supervisionato. Sono presenti diversi tipi specifici di apprendimento supervisionato rappresentati all'interno di Azure Machine Learning Studio: classificazione, regressione e rilevamento di anomalie.

* **Classificazione**. Quando i dati vengono utilizzati per prevedere una categoria, l’apprendimento supervisionato è chiamato anche classificazione. Ciò avviene quando si assegna un'immagine, come un’immagine di "gatto" o "cane". Quando sono disponibili solo due opzioni, è chiamato **a due classi** o **classificazione binomiale**. Quando sono presenti più categorie, come quando si prevede il vincitore di un torneo, questo problema è noto come **classificazione multiclasse**.
* **Regressione**. Quando un valore viene previsto, come con i prezzi dei titoli, l’apprendimento supervisionato viene chiamato regressione.
* **Rilevamento delle anomalie**. Talvolta l'obiettivo consiste nell'identificare punti dati che sono semplicemente insoliti. Nel rilevamento delle frodi, ad esempio, i modelli di spesa della carta di credito altamente insoliti sono sospetti. Le possibili variazioni sono così numerose e gli esempi di formazione così pochi, che non è possibile conoscere l’aspetto delle attività illecite. L'approccio di rilevamento delle anomalie è semplicemente altre attività normali è simile a (tramite una cronologia delle transazioni non fraudolente) e identificare ciò che è molto diverso.

### <a name="unsupervised"></a>Non supervisionato

Nell'apprendimento non supervisionato, ai punti dati non sono associate etichette. L'obiettivo di un algoritmo di apprendimento non supervisionato è invece l'organizzazione dei dati in un certo modo o la descrizione della loro struttura. Questo può significare il raggruppamento dei dati in cluster o l'individuazione di modi diversi con cui osservare dati complessi, in modo che appaiano più semplici o più organizzati.

### <a name="reinforcement-learning"></a>Apprendimento per rinforzo

Nell'apprendimento per rinforzo l'algoritmo arriva a scegliere un'azione in risposta a ogni punto dati. L'algoritmo di apprendimento riceve anche un segnale di ricompensa poco dopo, a indicare il livello di correttezza della decisione presa.
In base a questo segnale, l'algoritmo modifica la propria strategia per ottenere la ricompensa maggiore. Attualmente in Azure Machine Learning Studio non sono disponibili moduli di algoritmi di apprendimento per rinforzo. L’apprendimento per rinforzo è un approccio comune in robotica, in cui il set di letture del sensore in un certo momento è un punto dati e l'algoritmo deve scegliere l'azione successiva del robot. Questo approccio è ideale anche per applicazioni "Internet delle cose" (Internet of Things, IoT).

## <a name="considerations-when-choosing-an-algorithm"></a>Considerazioni relative alla scelta di un algoritmo

### <a name="accuracy"></a>Precisione

Ottenere la risposta più precisa possibile non è sempre necessario.
Talvolta un'approssimazione è sufficiente, a seconda di ciò per cui si desidera utilizzarla. In tal caso, è possibile ridurre drasticamente il tempo di elaborazione utilizzando più metodi approssimativi. Un altro vantaggio di metodi più approssimativi è la tendenza naturale a evitare l'overfitting.

### <a name="training-time"></a>Tempo di formazione

Il numero di minuti o ore necessarie per eseguire l’addestramento di un modello varia notevolmente tra gli algoritmi. Il tempo di formazione spesso è strettamente correlato alla precisione, uno in genere accompagna l'altra. Inoltre, alcuni algoritmi sono più sensibili al numero di punti dati rispetto ad altri.
Quando il tempo è limitato, può guidare la scelta dell'algoritmo, soprattutto quando il set di dati è grande.

### <a name="linearity"></a>Linearità

Un numero elevato di algoritmi di Machine Learning utilizza la linearità. Gli algoritmi di classificazione lineare ipotizzano che le classi possano essere separate da una linea retta (o dal suo equivalente della dimensione superiore). Queste includono la regressione logistica e le macchine a vettori di supporto (come implementate in Azure Machine Learning Studio).
Gli algoritmi di regressione lineare ipotizzano che le tendenze dei dati seguano una linea retta. Queste ipotesi non sono errate per alcuni problemi, ma per altri riducono la precisione.

![Limite di classe non lineare](./media/algorithm-choice/image1.png)

***Limite di classe non lineare*** *: basarsi su un algoritmo di classificazione lineare produce una precisione ridotta*

![Dati con una tendenza non lineare](./media/algorithm-choice/image2.png)

***Dati con una tendenza non lineare*** *: l'uso di un metodo di regressione lineare genera errori più grandi del necessario*

Nonostante i loro pericoli, gli algoritmi lineari sono molto popolari come prima linea di attacco. Tendono ad essere algoritmicamente semplici e rapidi da addestrare.

### <a name="number-of-parameters"></a>Numero di parametri

I parametri sono i pulsanti che uno scienziato dei dati deve utilizzare per configurare un algoritmo. Sono numeri che influiscono sul comportamento dell'algoritmo, ad esempio sulla tolleranza degli errori o sul numero di iterazioni, o sulle opzioni tra le varianti del comportamento dell'algoritmo. Il tempo dell’addestramento e la precisione dell'algoritmo possono talvolta risultare abbastanza cruciali per ottenere le impostazioni corrette. In genere, gli algoritmi con un numero elevato di parametri richiedono più prove empiriche per trovare una combinazione ottimale.

In alternativa, in Azure Machine Learning Studio è disponibile un blocco di moduli per lo [sweep dei parametri](algorithm-parameters-optimize.md) che prova automaticamente tutte le combinazioni di parametri alla granularità scelta. Sebbene questo sia un ottimo modo per assicurarsi di aver utilizzato al massimo lo spazio di parametri, il tempo necessario per eseguire l’addestramento di un modello aumenta in misura esponenziale con il numero di parametri.

Il vantaggio è un numero elevato di parametri in genere indica che un algoritmo ha maggiore flessibilità. Può raggiungere spesso un'alta precisione, purché è possibile trovare la giusta combinazione di impostazioni dei parametri.

### <a name="number-of-features"></a>Numero di caratteristiche

Per alcuni tipi di dati, il numero di caratteristiche può essere molto grande rispetto al numero di punti dati. Questo accade spesso con i dati testuali o sulla genetica. L’elevato numero di caratteristiche può rallentare alcuni algoritmi di apprendimento, rendendo il tempo di addestramento esageratamente lungo. Le macchine a vettori di supporto sono particolarmente adatte in questo caso (vedere di seguito).

### <a name="special-cases"></a>Casi speciali

Alcuni algoritmi di apprendimento fanno ipotesi particolari sulla struttura dei dati o sui risultati desiderati. Se è possibile trovarne uno adatto alle proprie esigenze, può fornire risultati più utili, previsioni più accurate o tempi di addestramento più rapidi.

| **Algoritmo** | **Precisione** | **Tempo di addestramento** | **Linearità** | **Parametri** | **Note** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Classificazione a due classi** | | | | | |
| [regressione logistica](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) | |● |● |5 | |
| [foresta delle decisioni](/azure/machine-learning/studio-module-reference/two-class-decision-forest) |● |○ | |6 | |
| [giungla delle decisioni](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) |● |○ | |6 |Footprint della memoria ridotto |
| [albero delle decisioni con boosting scalabile](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree) |● |○ | |6 |Footprint della memoria di grandi dimensioni |
| [rete neurale](/azure/machine-learning/studio-module-reference/two-class-neural-network) |● | | |9 |[È possibile un’ulteriore personalizzazione](azure-ml-netsharp-reference-guide.md) |
| [percezione media](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) |○ |○ |● |4 | |
| [macchina a vettori di supporto](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) | |○ |● |5 |Particolarmente valido per set di funzioni di grandi dimensioni |
| [macchina a vettori di supporto localmente approfondito](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) |○ | | |8 |Particolarmente valido per set di funzioni di grandi dimensioni |
| [Bayes Point Machine](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine) | |○ |● |3 | |
| **Classificazione multiclasse** | | | | | |
| [regressione logistica](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) | |● |● |5 | |
| [foresta delle decisioni](/azure/machine-learning/studio-module-reference/multiclass-decision-forest) |● |○ | |6 | |
| [giungla delle decisioni](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle) |● |○ | |6 |Footprint della memoria ridotto |
| [rete neurale](/azure/machine-learning/studio-module-reference/multiclass-neural-network) |● | | |9 |[È possibile un’ulteriore personalizzazione](azure-ml-netsharp-reference-guide.md) |
| [one-v-all](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass) |- |- |- |- |Visualizzare le proprietà del metodo a due classi selezionato |
| **Regressionee** | | | | | |
| [lineare](/azure/machine-learning/studio-module-reference/linear-regression) | |● |● |4 | |
| [Regressione lineare Bayesiana](/azure/machine-learning/studio-module-reference/bayesian-linear-regression) | |○ |● |2 | |
| [foresta delle decisioni](/azure/machine-learning/studio-module-reference/decision-forest-regression) |● |○ | |6 | |
| [albero delle decisioni con boosting scalabile](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) |● |○ | |5 |Footprint della memoria di grandi dimensioni |
| [Quantile della foresta rapida](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) |● |○ | |9 |Distribuzioni invece di previsioni punti |
| [rete neurale](/azure/machine-learning/studio-module-reference/neural-network-regression) |● | | |9 |[È possibile un’ulteriore personalizzazione](azure-ml-netsharp-reference-guide.md) |
| [Poisson](/azure/machine-learning/studio-module-reference/poisson-regression) | | |● |5 |Tecnicamente logaritmica-lineare. Per la previsione di conteggi. |
| [Ordinale](/azure/machine-learning/studio-module-reference/ordinal-regression) | | | |0 |Per la previsione dell'ordinamento delle classificazioni |
| **Rilevamento anomalie** | | | | | |
| [macchina a vettori di supporto](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) |○ |○ | |2 |Particolarmente valido per set di funzioni di grandi dimensioni |
| [Rilevamento delle anomalie basato su PCA](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) | |○ |● |3 | |
| [K-means](/azure/machine-learning/studio-module-reference/k-means-clustering) | |○ |● |4 |Algoritmo di clustering |

**Proprietà algoritmo:**

**●** : mostra eccellente precisione, tempi di addestramento rapidi e uso della linearità

**○** : mostra una buona precisione e tempi di training moderati

## <a name="algorithm-notes"></a>Note algoritmo

### <a name="linear-regression"></a>Linear regression

Come accennato in precedenza, la [regressione lineare](/azure/machine-learning/studio-module-reference/linear-regression) adatta una riga (o piano o iperpiano) al set di dati. È un ottimo strumento di lavoro, semplice e veloce, ma potrebbe essere troppo semplicistica per alcuni problemi.

![Dati con una tendenza lineare](./media/algorithm-choice/image3.png)

***Dati con una tendenza lineare***

### <a name="logistic-regression"></a>Regressione logistica

Sebbene includa "regressione" nel nome, la regressione logistica è effettivamente uno strumento potente per [due classi](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) e [multiclass](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) classificazione. È semplice e veloce. Il fatto che utilizzi una curva a forma di 'S' anziché una linea retta la rende ideale per dividere i dati in gruppi. La regressione logistica fornisce limiti della classe lineari, pertanto quando la si utilizza, assicurarsi che un'approssimazione lineare sia accettabile.

![Regressione logistica a dati a due classi con una sola caratteristica](./media/algorithm-choice/image4.png)

***Regressione logistica a dati a due classi con una sola funzionalità*** *: il limite della classe è il punto in cui la curva logistica è alla stessa distanza da entrambe le classi*

### <a name="trees-forests-and-jungles"></a>Alberi, foreste e giungle

Le foreste delle decisioni ([regressione](/azure/machine-learning/studio-module-reference/decision-forest-regression), [a due classi](/azure/machine-learning/studio-module-reference/two-class-decision-forest) e [multiclasse](/azure/machine-learning/studio-module-reference/multiclass-decision-forest)), le giungle delle decisioni ([a due classi](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) e [multiclasse](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle)) e gli alberi delle decisioni con boosting ([regressione](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) e [a due classi](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree)) sono tutti basati sugli alberi delle decisioni, un concetto fondamentale del Machine Learning. Esistono molte varianti di alberi delle decisioni, ma tutti svolgono lo stesso compito: suddividere lo spazio di elementi in aree con essenzialmente la stessa etichetta. Possono essere aree di una categoria coerente o di un valore costante, a seconda che si esegua la classificazione o la regressione.

![L’albero delle decisioni suddivide uno spazio di caratteristiche](./media/algorithm-choice/image5.png)

***Un albero delle decisioni suddivide uno spazio di elementi in aree di valori approssimativamente uniformi***

Poiché uno spazio di elementi può essere suddiviso in aree arbitrariamente piccole, è facile immaginare la sua divisione con precisione per disporre di un punto dati per ogni area, un esempio estremo di sovradattamento. Per evitare questo problema, un ampio set di strutture ad albero vengono costruiti con attenzione matematica per assicurarsi che gli alberi non sono correlati. La media di questa "foresta delle decisioni" è un albero che consente di evitare il sovradattamento. Le foreste delle decisioni possono utilizzare molta memoria. Le giungle delle decisioni sono una variante che utilizza meno memoria a discapito di un tempo di addestramento leggermente superiore.

Gli alberi delle decisioni con boosting consentono di evitare il sovradattamento limitando il numero di volte in cui possono suddividersi e il modo in cui pochi punti dati sono consentiti in ogni area. L'algoritmo costruisce una sequenza di alberi, ognuno dei quali apprende come compensare l'errore dell'albero precedente. Il risultato è uno strumento di apprendimento molto accurato che tende a utilizzare una grande quantità di memoria. Per la descrizione tecnica completa, consultare il [documento originale di Friedman](https://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[regressione quantile della foresta rapida](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) è una variante degli alberi delle decisioni per il caso speciale in cui si desidera conoscere non solo il valore tipico (mediano) dei dati all'interno di un'area, ma anche la sua distribuzione sotto forma di quantili.

### <a name="neural-networks-and-perceptrons"></a>Reti neurali e percezione

Le reti neurali sono algoritmi di apprendimento ispirati al cervello che coprono problemi [multiclasse](/azure/machine-learning/studio-module-reference/multiclass-neural-network), [a due classi](/azure/machine-learning/studio-module-reference/two-class-neural-network) e di [regressione](/azure/machine-learning/studio-module-reference/neural-network-regression). Sono disponibili per un'ampia varietà, ma le reti neurali all'interno di Azure Machine Learning Studio sono tutte sotto forma di grafici aciclici diretti. Ciò significa che le caratteristiche di input vengono passate in avanti (mai indietro) tramite una sequenza di livelli prima di essere convertiti in output. In ogni livello, gli input vengono ponderati in varie combinazioni, sommati e passati al livello successivo. Questa combinazione di calcoli semplici comporta la possibilità di apprendere limiti di classi e tendenze dei dati sofisticati, apparentemente per magia. Reti con molti livelli di questo tipo eseguono l’"apprendimento approfondito" che fornisce il fondamento di molti rapporti tecnici e fantascienza.

Queste alte prestazioni non sono però possibili senza un costo. Le reti neurali possono richiedere molto tempo per l’addestramento, in particolare per grandi set di dati con molte caratteristiche. Dispongono anche di più parametri rispetto alla maggior parte degli algoritmi, il che significa che lo sweep dei parametri dilata notevolmente il tempo di addestramento.
Per le persone ambiziose che desiderano [specificare la propria struttura di rete](azure-ml-netsharp-reference-guide.md), le possibilità sono infinite.

![Limiti appresi dalle reti neurali](./media/algorithm-choice/image6.png)

***I limiti appresi dalle reti neurali possono essere complessi e irregolari***

La [percezione media a due classi](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) è la risposta delle reti neurali a tempi di addestramento lunghissimi. Utilizza una struttura di rete che fornisce limiti di classe lineari. È quasi primitiva per gli standard odierni, ma dispone di una lunga storia di funzionamento affidabile ed è sufficientemente piccola per apprendere rapidamente.

### <a name="svms"></a>SVM

Le macchine a vettori di supporto (SVM) consentono di individuare il limite che separa le classi con un margine più ampio possibile. Quando le due classi non possono essere separate nettamente, gli algoritmi trovano il limite migliore possibile. Come scritto in Azure Machine Learning Studio, il [SVM a due classi](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) farlo con solo una linea retta (in parlando di SVM, utilizza un kernel lineare).
Poiché rende quest’approssimazione lineare, è in grado di funzionare molto rapidamente. Dove eccelle davvero è con funzionalità con un uso intensivo dei dati, ad esempio testo o dati di genomica. In questi casi le SVM sono in grado di separare le classi più rapidamente e con meno sovradattamento rispetto alla maggior parte degli altri algoritmi, oltre a richiedere solo una modesta quantità di memoria.

![Limite di classe della macchina a vettori di supporto ](./media/algorithm-choice/image7.png)

***Un limite di classe tipico della macchina a vettore di supporto ottimizza il margine separando due classi***

Un altro prodotto di Microsoft Research, la [SVM localmente approfondita a due classi](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) è una variante non lineare di SVM che mantiene la maggior parte della velocità e dell'efficienza della memoria della versione non lineare. È ideale per i casi in cui l'approccio lineare non fornisce risposte sufficientemente precise. Gli sviluppatori mantenuta rapida per scomporre il problema in un numero di piccoli problemi di SVM lineari. Leggere la [descrizione completa](http://proceedings.mlr.press/v28/jose13.html) per i dettagli su come hanno ottenuto questo risultato.

Usando un'estensione intelligente delle SVM non lineari, la [SVM a una classe](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) consente di tracciare un limite che delinea strettamente l'intero set di dati. È utile per il rilevamento delle anomalie. Eventuali nuovi punti dati che superano di molto tale limite sono sufficientemente insoliti da essere degni di nota.

### <a name="bayesian-methods"></a>Metodi bayesiani

I metodi bayesiani hanno una qualità estremamente utile: evitare il sovradattamento. Ottengono questo risultato facendo ipotesi in anticipo sulla probabile distribuzione della risposta. Un altro risultato di questo approccio è che dispongono di parametri in numero molto ridotto. Azure Machine Learning Studio ha algoritmi Bayesiani per entrambe le classificazioni ([Two-class Bayes point machine](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine)) e la regressione ([regressione lineare Bayesiana](/azure/machine-learning/studio-module-reference/bayesian-linear-regression)).
Si noti che questi ipotizzano che i dati possano essere suddivisi o adattati con una linea retta.

Dal punto di vista storico, le Bayes Point Machine sono state sviluppate presso Microsoft Research. Si basano su un lavoro teorico estremamente interessante. Lo studente interessato viene indirizzato all'[articolo originale in JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) e a un [blog dettagliato di Chris Bishop](https://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Algoritmi specializzati
Se si dispone di un obiettivo molto specifico si potrebbe essere fortunati. All'interno della raccolta di Azure Machine Learning Studio, sono disponibili algoritmi specifici per:

- stima di classificazione ([regressione ordinale](/azure/machine-learning/studio-module-reference/ordinal-regression)),
- stima di conteggio ([regressione Poisson](/azure/machine-learning/studio-module-reference/poisson-regression)),
- rilevamento di anomalie (uno basato sull'[analisi dei componenti principali](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) e l'altro basato sulle [macchine a vettori di supporto](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine))
- Clustering ([K-means](/azure/machine-learning/studio-module-reference/k-means-clustering))

![Rilevamento delle anomalie basato su PCA](./media/algorithm-choice/image8.png)

***Rilevamento delle anomalie basato su PCA*** *: la maggior parte dei dati rientra in una distribuzione stereotipica; i punti che differiscono notevolmente da tale distribuzione sono sospetti*

![Set di dati raggruppati utilizzando K-means](./media/algorithm-choice/image9.png)

***Un set di dati viene raggruppato in 5 cluster usando K-means***

Inoltre è disponibile un insieme [classificatore multiclasse one-v-all](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass)che interrompe il problema di classificazione di classe N in problemi di classificazione a due classi N-1. Precisione, tempi di addestramento e proprietà di linearità dipendono dai classificatori a due classi utilizzati.

![Classificatori a due classi combinati per formare un classificatore a tre classi](./media/algorithm-choice/image10.png)

***Una coppia di classificatori a due classi si combina per formare un classificatore a tre classi***

Azure Machine Learning Studio include inoltre l'accesso a una struttura di Machine Learning potente con il titolo di [Vowpal Wabbit](/azure/machine-learning/studio-module-reference/train-vowpal-wabbit-version-7-4-model).
VW sfugge alla categorizzazione in questo caso, poiché può apprendere problemi sia di classificazione che di regressione e può apprendere anche dati parzialmente senza etichetta. È possibile configurarlo in modo che utilizzi uno di una serie di algoritmi di apprendimento, funzioni di perdita e algoritmi di ottimizzazione. È stato progettato da zero per essere efficace, parallelo ed estremamente veloce. Consente di gestire insiemi di caratteristiche estremamente grandi con uno sforzo minimo.
Avviato e condotto da John Langford di Microsoft Research, VW è un elemento da Formula Uno in un campo di algoritmi pari a vetture di serie. Non tutti i problemi sono adatti a VW, ma per quelli che lo sono, potrebbe valere la pena superare la curva di apprendimento della relativa interfaccia. È inoltre disponibile come [codice open source autonomo](https://github.com/JohnLangford/vowpal_wabbit) in diverse lingue.

## <a name="next-steps"></a>Fasi successive

* Per scaricare una panoramica infografica di facile comprensione delle nozioni fondamentali di Machine Learning per ottenere informazioni sugli algoritmi comuni usati per rispondere a domande frequenti su Machine Learning, vedere [Nozioni fondamentali di Machine Learning con esempi di algoritmi](basics-infographic-with-algorithm-examples.md).

* Per un elenco per categoria degli algoritmi di Machine Learning disponibili in Machine Learning Studio, vedere l'argomento relativo al [modello di inizializzazione](/azure/machine-learning/studio-module-reference/machine-learning-initialize-model) nella Guida degli algoritmi e dei moduli di Machine Learning Studio.

* Per un elenco alfabetico completo degli algoritmi e dei moduli disponibili in Machine Learning Studio, vedere l'argomento relativo all'[elenco alfabetico dei moduli di Machine Learning Studio](/azure/machine-learning/studio-module-reference/a-z-module-list) nella Guida degli algoritmi e dei moduli di Machine Learning Studio.
