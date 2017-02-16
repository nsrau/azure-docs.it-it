---
title: Come scegliere gli algoritmi di Machine Learning | Documentazione Microsoft
description: Come scegliere gli algoritmi di Azure Machine Learning per l&quot;apprendimento supervisionato e non supervisionato negli esperimenti di clustering, classificazione o regressione.
services: machine-learning
documentationcenter: 
author: brohrer
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: a3b23d7f-f083-49c4-b6b1-3911cd69f1b4
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/09/2016
ms.author: brohrer;garye
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 216152123e4a5c17ec93775adff9fd4c1eb18654


---
# <a name="how-to-choose-algorithms-for-microsoft-azure-machine-learning"></a>Come scegliere gli algoritmi di Microsoft Azure Machine Learning
La risposta alla domanda "Quale algoritmo di Machine Learning devo usare" è sempre "Dipende". Dipende dalla dimensione, dalla qualità e dalla natura dei dati. Dipende da ciò che si desidera fare con la risposta. Dipende da come i calcoli dell'algoritmo sono stati convertiti in istruzioni per il computer in uso. E dipende dal tempo a disposizione. Nemmeno gli scienziati dei dati più esperti possono stabilire quale algoritmo funzionerà meglio prima di provarlo.

## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Foglio informativo sugli algoritmi di Machine Learning
Il **foglio illustrativo sugli algoritmi di Microsoft Azure Machine Learning** aiuta a scegliere l'algoritmo di Machine Learning appropriato da usare per le proprie soluzioni di analisi predittiva nella libreria di algoritmi di Microsoft Azure Machine Learning.
In questo articolo viene illustrato il suo utilizzo.

> [!NOTE]
> Per scaricare il foglio informativo e proseguire con questo articolo, passare a [Foglio informativo sugli algoritmi di Machine Learning per Microsoft Azure Machine Learning Studio](machine-learning-algorithm-cheat-sheet.md).
> 
> 

Questo foglio informativo ha destinatari molto specifici: un scienziato dei dati iniziali con Machine Learning di livello universitario , che tenta di scegliere un algoritmo per iniziare con Azure Machine Learning Studio. Ciò significa che fa alcune generalizzazioni e semplificazioni eccessive, ma indicherà una direzione sicura. Significa inoltre che esistono molti algoritmi non elencati qui. Mentre Azure Machine Learning cresce per includere un set più completo dei metodi disponibili, essi verranno aggiunti.

Queste indicazioni sono commenti e suggerimenti compilati da parte di scienziati dei dati ed esperti di Machine Learning. Non abbiamo raggiunto un accordo su ogni punto, ma ho tentato di armonizzazione le nostre opinioni in un consenso approssimativo. La maggior parte delle indicazioni di disaccordo iniziano con "Dipende..."

### <a name="how-to-use-the-cheat-sheet"></a>Come utilizzare il foglio informativo
Leggere il percorso e le etichette degli algoritmi sul grafico come "Per *&lt;etichetta percorso&gt;* usare *&lt;algoritmo&gt;*". Ad esempio, "Per *velocità* usare *regressione logistica a due classi*". A volte è possibile applicare più di una branca.
A volte nessuno di essi sarà la scelta perfetta. Devono essere considerati consigli generali, pertanto non è necessario che siano esatti.
Diversi scienziati dei dati con cui ho parlato hanno detto che l'unico modo sicuro per trovare l'algoritmo migliore consiste nel provarli tutti.

Ecco un esempio tratto da [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/) di un esperimento che prova diversi algoritmi sugli stessi dati e ne confronta i risultati: [Compare Multi-class Classifiers: Letter recognition](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92) (Confrontare classificatori multiclasse: riconoscimento di lettere).

> [!TIP]
> Per scaricare e stampate un diagramma che offra una panoramica delle funzionalità di Machine Learning Studio, vedere [Diagramma della panoramica delle funzionalità di Azure Machine Learning Studio](machine-learning-studio-overview-diagram.md).
> 
> 

## <a name="flavors-of-machine-learning"></a>Varianti di Machine Learning
### <a name="supervised"></a>Supervisionato
Gli algoritmi di apprendimento supervisionato fanno previsioni in base a un set di esempi. Ad esempio, l’andamento storico dei titoli può essere utilizzato per azzardare ipotesi sui prezzi futuri. Ogni esempio utilizzato per l’addestramento viene etichettato con il valore di interesse: in questo caso, il prezzo dei titoli. Un algoritmo di apprendimento supervisionato cerca modelli ripetitivi nelle etichette dei valori. Può utilizzare tutte le informazioni che potrebbero essere rilevanti, ovvero il giorno della settimana, la stagione, i dati finanziari dell'azienda, il tipo di settore, la presenza di eventi geopolitici di disturbo, e ogni algoritmo cerca tipi diversi di modelli ripetitivi. Dopo che l'algoritmo ha trovato il modello ripetitivo migliore possibile, lo utilizza per eseguire stime per i dati di prova senza etichetta: i prezzi di domani.

Questo tipo di Machine Learning è utile e diffuso. Con una eccezione, tutti i moduli di Azure Machine Learning sono algoritmi di apprendimento supervisionato. Sono presenti diversi tipi specifici di apprendimento supervisionato rappresentati all'interno di Azure Machine Learning: classificazione, regressione e rilevamento di anomalie.

* **Classificazione**. Quando i dati vengono utilizzati per prevedere una categoria, l’apprendimento supervisionato è chiamato anche classificazione. Ciò avviene quando si assegna un'immagine, come un’immagine di "gatto" o "cane". Quando sono disponibili solo due opzioni, è chiamato **a due classi** o **classificazione binomiale**. Quando sono presenti più categorie, come quando si prevede il vincitore di un torneo, questo problema è noto come **classificazione multiclasse**.
* **Regressione**. Quando un valore viene previsto, come con i prezzi dei titoli, l’apprendimento supervisionato viene chiamato regressione.
* **Rilevamento delle anomalie**. Talvolta l'obiettivo consiste nell'identificare punti dati che sono semplicemente insoliti. Nel rilevamento delle frodi, ad esempio, i modelli di spesa della carta di credito altamente insoliti sono sospetti. Le possibili variazioni sono così numerose e gli esempi di formazione così pochi, che non è possibile conoscere l’aspetto delle attività illecite. L'approccio del rilevamento delle anomalie è semplicemente apprendere l’aspetto delle attività normali (utilizzando la cronologia delle transazioni non fraudolente) e identificare ciò che è molto diverso.

### <a name="unsupervised"></a>Non supervisionato
Nell'apprendimento non supervisionato, ai punti dati non sono associate etichette. L'obiettivo di un algoritmo di apprendimento non supervisionato è invece l'organizzazione dei dati in un certo modo o la descrizione della loro struttura. Questo può significare il raggruppamento dei dati in cluster o l'individuazione di modi diversi con cui osservare dati complessi, in modo che appaiano più semplici o più organizzati.

### <a name="reinforcement-learning"></a>Apprendimento per rinforzo
Nell'apprendimento per rinforzo l'algoritmo arriva a scegliere un'azione in risposta a ogni punto dati. L'algoritmo di apprendimento riceve anche un segnale di ricompensa poco dopo, a indicare il livello di correttezza della decisione presa.
In base a questo segnale, l'algoritmo modifica la propria strategia per ottenere la ricompensa maggiore. Attualmente in Azure Machine Learning non sono disponibili moduli di algoritmi di apprendimento per rinforzo. L’apprendimento per rinforzo è un approccio comune in robotica, in cui il set di letture del sensore in un certo momento è un punto dati e l'algoritmo deve scegliere l'azione successiva del robot. Questo approccio è ideale anche per applicazioni "Internet delle cose" (Internet of Things, IoT).

## <a name="considerations-when-choosing-an-algorithm"></a>Considerazioni relative alla scelta di un algoritmo
### <a name="accuracy"></a>Precisione
Ottenere la risposta più precisa possibile non è sempre necessario.
Talvolta un'approssimazione è sufficiente, a seconda di ciò per cui si desidera utilizzarla. In tal caso, è possibile ridurre drasticamente il tempo di elaborazione utilizzando più metodi approssimativi. Un altro vantaggio di metodi più approssimativi è la tendenza naturale a evitare il [sovradattamento](https://youtu.be/DQWI1kvmwRg).

### <a name="training-time"></a>Tempo di formazione
Il numero di minuti o ore necessarie per eseguire l’addestramento di un modello varia notevolmente tra gli algoritmi. Il tempo di formazione spesso è strettamente correlato alla precisione, uno in genere accompagna l'altra. Inoltre, alcuni algoritmi sono più sensibili al numero di punti dati rispetto ad altri.
Quando il tempo è limitato, può guidare la scelta dell'algoritmo, soprattutto quando il set di dati è grande.

### <a name="linearity"></a>Linearità
Un numero elevato di algoritmi di Machine Learning utilizza la linearità. Gli algoritmi di classificazione lineare ipotizzano che le classi possano essere separate da una linea retta (o dal suo equivalente della dimensione superiore). Queste includono la regressione logistica e le macchine a vettori di supporto (come implementate in Azure Machine Learning).
Gli algoritmi di regressione lineare ipotizzano che le tendenze dei dati seguano una linea retta. Queste ipotesi non sono errate per alcuni problemi, ma per altri riducono la precisione.

![Limite di classe non lineare][1]

***Limite di classe non lineare*** *: basarsi su un algoritmo di classificazione lineare produce una precisione ridotta*

![Dati con una tendenza non lineare][2]

***Dati con una tendenza non lineare*** *: l'uso di un metodo di regressione lineare genera errori più grandi del necessario*

Nonostante i loro pericoli, gli algoritmi lineari sono molto popolari come prima linea di attacco. Tendono ad essere algoritmicamente semplici e rapidi da addestrare.

### <a name="number-of-parameters"></a>Numero di parametri
I parametri sono i pulsanti che uno scienziato dei dati deve utilizzare per configurare un algoritmo. Sono numeri che influiscono sul comportamento dell'algoritmo, ad esempio sulla tolleranza degli errori o sul numero di iterazioni, o sulle opzioni tra le varianti del comportamento dell'algoritmo. Il tempo dell’addestramento e la precisione dell'algoritmo possono talvolta risultare abbastanza cruciali per ottenere le impostazioni corrette. Di solito, gli algoritmi con parametri in numero elevato richiedono molti tentativi ed errori per trovare una combinazione ottimale.

In alternativa, in Azure Machine Learning è disponibile un blocco di moduli per lo [sweep dei parametri](machine-learning-algorithm-parameters-optimize.md) che prova automaticamente tutte le combinazioni di parametri alla granularità scelta. Sebbene questo sia un ottimo modo per assicurarsi di aver utilizzato al massimo lo spazio di parametri, il tempo necessario per eseguire l’addestramento di un modello aumenta in misura esponenziale con il numero di parametri.

Il vantaggio è un numero elevato di parametri in genere indica che un algoritmo ha maggiore flessibilità. Può raggiungere spesso un’alta precisione. Ammesso che si trovi la giusta combinazione di impostazioni dei parametri.

### <a name="number-of-features"></a>Numero di caratteristiche
Per alcuni tipi di dati, il numero di caratteristiche può essere molto grande rispetto al numero di punti dati. Questo accade spesso con i dati testuali o sulla genetica. L’elevato numero di caratteristiche può rallentare alcuni algoritmi di apprendimento, rendendo il tempo di addestramento esageratamente lungo. Le macchine a vettori di supporto sono particolarmente adatte in questo caso (vedere di seguito).

### <a name="special-cases"></a>Casi speciali
Alcuni algoritmi di apprendimento fanno ipotesi particolari sulla struttura dei dati o sui risultati desiderati. Se è possibile trovarne uno adatto alle proprie esigenze, può fornire risultati più utili, previsioni più accurate o tempi di addestramento più rapidi.

| **Algoritmo** | **Precisione** | **Tempo di addestramento** | **Linearità** | **Parametri** | **Note** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Classificazione a due classi** | | | | | |
| [regressione logistica](https://msdn.microsoft.com/library/azure/dn905994.aspx) | |● |● |5 | |
| [foresta delle decisioni](https://msdn.microsoft.com/library/azure/dn906008.aspx) |● |○ | |6 | |
| [giungla delle decisioni](https://msdn.microsoft.com/library/azure/dn905976.aspx) |● |○ | |6 |Footprint della memoria ridotto |
| [albero delle decisioni con boosting scalabile](https://msdn.microsoft.com/library/azure/dn906025.aspx) |● |○ | |6 |Footprint della memoria di grandi dimensioni |
| [rete neurale](https://msdn.microsoft.com/library/azure/dn905947.aspx) |● | | |9 |[È possibile un’ulteriore personalizzazione](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [percezione media](https://msdn.microsoft.com/library/azure/dn906036.aspx) |○ |○ |● |4 | |
| [macchina a vettori di supporto](https://msdn.microsoft.com/library/azure/dn905835.aspx) | |○ |● |5 |Particolarmente valido per set di funzioni di grandi dimensioni |
| [macchina a vettori di supporto localmente approfondito](https://msdn.microsoft.com/library/azure/dn913070.aspx) |○ | | |8 |Particolarmente valido per set di funzioni di grandi dimensioni |
| [Bayes Point Machine](https://msdn.microsoft.com/library/azure/dn905930.aspx) | |○ |● |3 | |
| **Classificazione multiclasse** | | | | | |
| [regressione logistica](https://msdn.microsoft.com/library/azure/dn905853.aspx) | |● |● |5 | |
| [foresta delle decisioni](https://msdn.microsoft.com/library/azure/dn906015.aspx) |● |○ | |6 | |
| [giungla delle decisioni ](https://msdn.microsoft.com/library/azure/dn905963.aspx) |● |○ | |6 |Footprint della memoria ridotto |
| [rete neurale](https://msdn.microsoft.com/library/azure/dn906030.aspx) |● | | |9 |[È possibile un’ulteriore personalizzazione](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [one-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx) |- |- |- |- |Visualizzare le proprietà del metodo a due classi selezionato |
| **Regressionee** | | | | | |
| [Lineare](https://msdn.microsoft.com/library/azure/dn905978.aspx) | |● |● |4 | |
| [Regressione lineare Bayesiana](https://msdn.microsoft.com/library/azure/dn906022.aspx) | |○ |● |2 | |
| [foresta delle decisioni](https://msdn.microsoft.com/library/azure/dn905862.aspx) |● |○ | |6 | |
| [albero delle decisioni con boosting scalabile](https://msdn.microsoft.com/library/azure/dn905801.aspx) |● |○ | |5 |Footprint della memoria di grandi dimensioni |
| [Quantile della foresta rapida](https://msdn.microsoft.com/library/azure/dn913093.aspx) |● |○ | |9 |Distribuzioni invece di previsioni punti |
| [rete neurale](https://msdn.microsoft.com/library/azure/dn905924.aspx) |● | | |9 |[È possibile un’ulteriore personalizzazione](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [Poisson ](https://msdn.microsoft.com/library/azure/dn905988.aspx) | | |● |5 |Tecnicamente logaritmica-lineare. Per la previsione di conteggi. |
| [Ordinale](https://msdn.microsoft.com/library/azure/dn906029.aspx) | | | |0 |Per la previsione dell'ordinamento delle classificazioni |
| **Rilevamento anomalie** | | | | | |
| [macchina a vettori di supporto](https://msdn.microsoft.com/library/azure/dn913103.aspx) |○ |○ | |2 |Particolarmente valido per set di funzioni di grandi dimensioni |
| [Rilevamento delle anomalie basato su PCA ](https://msdn.microsoft.com/library/azure/dn913102.aspx) | |○ |● |3 | |
| [K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/) | |○ |● |4 |Algoritmo di clustering |

**Proprietà algoritmo:**

**●** : mostra eccellente precisione, tempi di addestramento rapidi e uso della linearità

**○** : mostra buona precisione e tempi di addestramento moderati

## <a name="algorithm-notes"></a>Note algoritmo
### <a name="linear-regression"></a>Linear regression
Come accennato in precedenza, la [regressione lineare](https://msdn.microsoft.com/library/azure/dn905978.aspx) adatta una riga (o piano o iperpiano) al set di dati. È un ottimo strumento di lavoro, semplice e veloce, ma potrebbe essere troppo semplicistica per alcuni problemi.
Qui è disponibile un' [esercitazione sulla regressione lineare](machine-learning-linear-regression-in-azure.md).

![Dati con una tendenza lineare][3]

***Dati con una tendenza lineare***

### <a name="logistic-regression"></a>Regressione logistica
Sebbene includa "regressione" nel nome, la regressione logistica in realtà è uno strumento potente per la classificazione [a due classi](https://msdn.microsoft.com/library/azure/dn905994.aspx) e [multiclasse](https://msdn.microsoft.com/library/azure/dn905853.aspx). È semplice e veloce. Il fatto che utilizzi una curva a forma di 'S' anziché una linea retta la rende ideale per dividere i dati in gruppi. La regressione logistica fornisce limiti della classe lineari, pertanto quando la si utilizza, assicurarsi che un'approssimazione lineare sia accettabile.

![Regressione logistica a dati a due classi con una sola caratteristica][4]

***Regressione logistica a dati a due classi con una sola funzionalità*** *: il limite della classe è il punto in cui la curva logistica è alla stessa distanza da entrambe le classi*

### <a name="trees-forests-and-jungles"></a>Alberi, foreste e giungle
Le foreste delle decisioni ([regressione](https://msdn.microsoft.com/library/azure/dn905862.aspx), [a due classi](https://msdn.microsoft.com/library/azure/dn906008.aspx) e [multiclasse](https://msdn.microsoft.com/library/azure/dn906015.aspx)), le giungle delle decisioni ([a due classi](https://msdn.microsoft.com/library/azure/dn905976.aspx) e [multiclasse](https://msdn.microsoft.com/library/azure/dn905963.aspx)) e gli alberi delle decisioni con boosting ([regressione](https://msdn.microsoft.com/library/azure/dn905801.aspx) e [a due classi](https://msdn.microsoft.com/library/azure/dn906025.aspx)) sono tutti basati sugli alberi delle decisioni, un concetto fondamentale del Machine Learning. Esistono molte varianti di alberi delle decisioni, ma tutti svolgono lo stesso compito: suddividere lo spazio di elementi in aree con essenzialmente la stessa etichetta. Possono essere aree di una categoria coerente o di un valore costante, a seconda che si esegua la classificazione o la regressione.

![L’albero delle decisioni suddivide uno spazio di caratteristiche][5]

***Un albero delle decisioni suddivide uno spazio di elementi in aree di valori approssimativamente uniformi***

Poiché uno spazio di elementi può essere suddiviso in aree arbitrariamente piccole, è facile immaginare la sua divisione con precisione per disporre di un punto dati per ogni area, un esempio estremo di sovradattamento. Per evitare questo, un elevato numero di alberi viene realizzato con attenzione matematica poiché gli alberi non sono correlati. La media di questa "foresta delle decisioni" è un albero che consente di evitare il sovradattamento. Le foreste delle decisioni possono utilizzare molta memoria. Le giungle delle decisioni sono una variante che utilizza meno memoria a discapito di un tempo di addestramento leggermente superiore.

Gli alberi delle decisioni con boosting consentono di evitare il sovradattamento limitando il numero di volte in cui possono suddividersi e il modo in cui pochi punti dati sono consentiti in ogni area. L'algoritmo costruisce una sequenza di alberi, ognuno dei quali apprende come compensare l'errore dell'albero precedente. Il risultato è uno strumento di apprendimento molto accurato che tende a utilizzare una grande quantità di memoria. Per la descrizione tecnica completa, consultare il [documento originale di Friedman](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[regressione quantile della foresta rapida](https://msdn.microsoft.com/library/azure/dn913093.aspx) è una variante degli alberi delle decisioni per il caso speciale in cui si desidera conoscere non solo il valore tipico (mediano) dei dati all'interno di un'area, ma anche la sua distribuzione sotto forma di quantili.

### <a name="neural-networks-and-perceptrons"></a>Reti neurali e percezione
Le reti neurali sono algoritmi di apprendimento ispirati al cervello che coprono problemi [multiclasse](https://msdn.microsoft.com/library/azure/dn906030.aspx), [a due classi](https://msdn.microsoft.com/library/azure/dn905947.aspx) e di [regressione](https://msdn.microsoft.com/library/azure/dn905924.aspx). Sono disponibili per un'ampia varietà, ma le reti neurali all'interno di Azure Machine Learning sono tutte sotto forma di grafici aciclici diretti. Ciò significa che le caratteristiche di input vengono passate in avanti (mai indietro) tramite una sequenza di livelli prima di essere convertiti in output. In ogni livello, gli input vengono ponderati in varie combinazioni, sommati e passati al livello successivo. Questa combinazione di calcoli semplici comporta la possibilità di apprendere limiti di classi e tendenze dei dati sofisticati, apparentemente per magia. Reti con molti livelli di questo tipo eseguono l’"apprendimento approfondito" che fornisce il fondamento di molti rapporti tecnici e fantascienza.

Queste alte prestazioni non sono però possibili senza un costo. Le reti neurali possono richiedere molto tempo per l’addestramento, in particolare per grandi set di dati con molte caratteristiche. Dispongono anche di più parametri rispetto alla maggior parte degli algoritmi, il che significa che lo sweep dei parametri dilata notevolmente il tempo di addestramento.
Per le persone ambiziose che desiderano [specificare la propria struttura di rete](http://go.microsoft.com/fwlink/?LinkId=402867), le possibilità sono infinite.

## <a name="boundaries-learned-by-neural-networks6"></a>![Limiti appresi dalle reti neurali][6]
***I limiti appresi dalle reti neurali possono essere complessi e irregolari***

La [percezione media a due classi](https://msdn.microsoft.com/library/azure/dn906036.aspx) è la risposta delle reti neurali a tempi di addestramento lunghissimi. Utilizza una struttura di rete che fornisce limiti di classe lineari. È quasi primitiva per gli standard odierni, ma dispone di una lunga storia di funzionamento affidabile ed è sufficientemente piccola per apprendere rapidamente.

### <a name="svms"></a>SVM
Le macchine a vettori di supporto (SVM) consentono di individuare il limite che separa le classi con un margine più ampio possibile. Quando le due classi non possono essere separate nettamente, gli algoritmi trovano il limite migliore possibile. Come scritto in Azure Machine Learning, la [SVM a due classi](https://msdn.microsoft.com/library/azure/dn905835.aspx) esegue questa operazione solo con una linea retta. (Nel linguaggio dell’SVM, utilizza un kernel lineare). Poiché rende quest’approssimazione lineare, è in grado di funzionare molto rapidamente. Dove eccelle davvero è con dati ricchi di caratteristiche, come testi o genomica. In questi casi le SVM sono in grado di separare le classi più rapidamente e con meno sovradattamento rispetto alla maggior parte degli altri algoritmi, oltre a richiedere solo una modesta quantità di memoria.

![Limite di classe della macchina a vettori di supporto ][7]

***Un limite di classe tipico della macchina a vettore di supporto ottimizza il margine separando due classi***

Un altro prodotto di Microsoft Research, la [SVM localmente approfondita a due classi](https://msdn.microsoft.com/library/azure/dn913070.aspx) è una variante non lineare di SVM che mantiene la maggior parte della velocità e dell'efficienza della memoria della versione non lineare. È ideale per i casi in cui l'approccio lineare non fornisce risposte sufficientemente precise. Gli sviluppatori l’hanno mantenuta rapida per scomporre il problema in una serie di piccoli problemi di SVM lineare. Leggere la [descrizione completa](http://research.microsoft.com/um/people/manik/pubs/Jose13.pdf) per i dettagli su come hanno ottenuto questo risultato.

Usando un'estensione intelligente delle SVM non lineari, la [SVM a una classe](https://msdn.microsoft.com/library/azure/dn913103.aspx) consente di tracciare un limite che delinea strettamente l'intero set di dati. È utile per il rilevamento delle anomalie. Eventuali nuovi punti dati che superano di molto tale limite sono sufficientemente insoliti da essere degni di nota.

### <a name="bayesian-methods"></a>Metodi bayesiani
I metodi bayesiani hanno una qualità estremamente utile: evitare il sovradattamento. Ottengono questo risultato facendo ipotesi in anticipo sulla probabile distribuzione della risposta. Un altro risultato di questo approccio è che dispongono di parametri in numero molto ridotto. Azure Machine Learning ha algoritmi bayesiani per entrambe le classificazioni ([la Bayes Point Machine a due classi](https://msdn.microsoft.com/library/azure/dn905930.aspx)) e la regressione ([regressione lineare bayesiana](https://msdn.microsoft.com/library/azure/dn906022.aspx)).
Si noti che questi ipotizzano che i dati possano essere suddivisi o adattati con una linea retta.

Dal punto di vista storico, le Bayes Point Machine sono state sviluppate presso Microsoft Research. Si basano su un lavoro teorico estremamente interessante. Lo studente interessato viene indirizzato all'[articolo originale in JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) e a un [blog dettagliato di Chris Bishop](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Algoritmi specializzati
Se si dispone di un obiettivo molto specifico si potrebbe essere fortunati. All'interno della raccolta di Azure Machine Learning sono disponibili algoritmi specializzati nella previsione delle classifiche ([regressione ordinale](https://msdn.microsoft.com/library/azure/dn906029.aspx)), nella previsione dei conteggi ([regressione di Poisson](https://msdn.microsoft.com/library/azure/dn905988.aspx)) e nel rilevamento delle anomalie (uno basato sull'[analisi dei componenti principali](https://msdn.microsoft.com/library/azure/dn913102.aspx) e uno basato sulle [macchine a vettori di supporto](https://msdn.microsoft.com/library/azure/dn913103.aspx)s).
Esiste anche un algoritmo di clustering unico ([K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)).

![Rilevamento delle anomalie basato su PCA][8]

***Rilevamento delle anomalie basato su PCA*** *: la maggior parte dei dati rientra in una distribuzione stereotipica; i punti che differiscono notevolmente da tale distribuzione sono sospetti*

![Set di dati raggruppati utilizzando K-means][9]

***Un set di dati viene raggruppato in 5 cluster usando K-means***

Inoltre è disponibile un insieme [classificatore multiclasse one-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx)che interrompe il problema di classificazione di classe N in problemi di classificazione a due classi N-1. Precisione, tempi di addestramento e proprietà di linearità dipendono dai classificatori a due classi utilizzati.

![Classificatori a due classi combinati per formare un classificatore a tre classi][10]

***Una coppia di classificatori a due classi si combina per formare un classificatore a tre classi***

Azure Machine Learning include inoltre l'accesso a una struttura di Machine Learning potente con il titolo di [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf).
VW sfugge alla categorizzazione in questo caso, poiché può apprendere problemi sia di classificazione che di regressione e può apprendere anche dati parzialmente senza etichetta. È possibile configurarlo in modo che utilizzi uno di una serie di algoritmi di apprendimento, funzioni di perdita e algoritmi di ottimizzazione. È stato progettato da zero per essere efficace, parallelo ed estremamente veloce. Consente di gestire insiemi di caratteristiche estremamente grandi con uno sforzo minimo.
Avviato e condotto da John Langford di Microsoft Research, VW è un elemento da Formula Uno in un campo di algoritmi pari a vetture di serie. Non tutti i problemi sono adatti a VW, ma per quelli che lo sono, potrebbe valere la pena superare la curva di apprendimento della relativa interfaccia. È inoltre disponibile come [codice open source autonomo](https://github.com/JohnLangford/vowpal_wabbit) in diverse lingue.

<!-- Media -->

[1]: ./media/machine-learning-algorithm-choice/image1.png
[2]: ./media/machine-learning-algorithm-choice/image2.png
[3]: ./media/machine-learning-algorithm-choice/image3.png
[4]: ./media/machine-learning-algorithm-choice/image4.png
[5]: ./media/machine-learning-algorithm-choice/image5.png
[6]: ./media/machine-learning-algorithm-choice/image6.png
[7]: ./media/machine-learning-algorithm-choice/image7.png
[8]: ./media/machine-learning-algorithm-choice/image8.png
[9]: ./media/machine-learning-algorithm-choice/image9.png
[10]: ./media/machine-learning-algorithm-choice/image10.png



<!--HONumber=Nov16_HO3-->


