<properties 
	pageTitle="Foglio informativo sugli algoritmi di Machine Learning | Microsoft Azure" 
	description="Un foglio informativo sugli algoritmi di Machine Learning stampabile aiuta a scegliere il giusto algoritmo per il proprio modello predittivo in Azure Machine Learning Studio."
	keywords="algorithm cheat sheet,cheat sheet,machine learning algorithm"	
	services="machine-learning"
	documentationCenter="" 
	authors="brohrer" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="brohrer;garye" />


# Foglio informativo sugli algoritmi di Machine Learning per Microsoft Azure Machine Learning Studio

Il **foglio illustrativo sugli algoritmi di Microsoft Azure Machine Learning** aiuta a scegliere l'algoritmo di Machine Learning appropriato da usare per le proprie soluzioni di analisi predittiva nella libreria di algoritmi di Microsoft Azure Machine Learning. Il foglio informativo pone alcune domande sulla natura dei dati e sul problema che si sta cercando di risolvere e quindi suggerisce un algoritmo da provare.

[Azure Machine Learning Studio](https://studio.azureml.net/) viene fornito con un gran numero di algoritmi di Machine Learning per le soluzioni di analisi predittiva. Questi algoritmi appartengono alle categorie di Machine Learning generali di ***regressione***, ***classificazione***, ***clustering*** e ***rilevamento di anomalie*** e ognuno è progettato per risolvere un tipo diverso di problema di Machine Learning.



## Scaricare il foglio informativo sugli algoritmi di Machine Learning

Scaricare il foglio informativo sugli algoritmi di Machine Learning e ottenere aiuto su come scegliere un algoritmo di Machine Learning per la propria soluzione. Per tenerlo a portata di mano, stampare il foglio informativo in formato tabloid \(27 x 43 cm circa\).

**Download del foglio informativo: [Foglio informativo sugli algoritmi di Microsoft Azure Machine Learning](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v2.pdf)**

![Foglio informativo sugli algoritmi di Machine Learning: informazioni su come scegliere un algoritmo di Machine Learning.][cheat-sheet]

[cheat-sheet]: ./media/machine-learning-algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-microsoft-azure.png



<!-- This is now covered in the first footnote below
[Azure Machine Learning Studio](https://studio.azureml.net/) gives you the flexibility to experiment - 
try one algorithm, and if you're not satisfied with the results, try another.
Here's an example from the [Azure Machine Learning Gallery](http://gallery.azureml.net) of an experiment that tries several algorithms against the same data and compares the results:
[Compare Multi-class Classifiers: Letter recognition](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92).
-->

## Altre informazioni sugli algoritmi 

* Per una trattazione più approfondita dei diversi tipi di algoritmi di Machine Learning e su come vengono usati, vedere [Come scegliere un algoritmo in Azure Machine Learning](machine-learning-algorithm-choice.md).
* Per un elenco per categoria degli algoritmi di Machine Learning disponibili in Machine Learning Studio, vedere [Initialize Model][initialize-model] nella Guida degli algoritmi e dei moduli di Machine Learning Studio.
* Per un elenco completo degli algoritmi e dei moduli disponibili in Machine Learning Studio, vedere [Elenco moduli A-Z di Machine Learning Studio](https://msdn.microsoft.com/library/azure/dn906033.aspx) nella Guida degli algoritmi e dei moduli di Machine Learning Studio.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Note e definizioni terminologiche per il foglio informativo sugli algoritmi di Machine Learning

* I consigli offerti in questo foglio informativo sugli algoritmi sono regole empiriche puramente indicative. Alcuni possono essere modificati, altri totalmente ignorati. Queste informazioni vengono fornite come punto di partenza consigliato. È anche possibile provare un confronto in parallelo tra diversi algoritmi sui dati. Questa può rivelarsi la soluzione migliore per comprendere i principi di ogni algoritmo e il sistema che ha generato i dati. 

* Ogni algoritmo di Machine Learning ha il proprio stile o *bias induttivo*. Per un problema specifico, possono essere appropriati diversi algoritmi, uno dei quali può rivelarsi una scelta più adatta rispetto agli altri. Tuttavia, non sempre è possibile determinare in anticipo quale sia la scelta migliore. In casi simili, nel foglio informativo sono elencati insieme diversi algoritmi. Una strategia appropriata può essere quella di provare un algoritmo e quindi provarne altri se i risultati del primo non sono soddisfacenti. Ecco un esempio tratto dalla [raccolta di Azure Machine Learning](http://gallery.azureml.net/) di un esperimento che prova diversi algoritmi sugli stessi dati e ne confronta i risultati: [Confrontare classificatori multiclasse: riconoscimento di lettere](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92).

* Machine Learning è suddiviso in tre categorie principali: **apprendimento supervisionato**, **apprendimento non supervisionato** e **apprendimento per rinforzo**.

  * Nell'**apprendimento supervisionato** ogni punto dati è etichettato con o associato a una categoria o un valore di interesse. Un esempio di un'etichetta di categoria è l'assegnazione di un'immagine come "gatto" o "cane". Un esempio di un'etichetta di valore è il prezzo di vendita associato a un'auto usata. L'obiettivo dell'apprendimento supervisionato consiste nello studiare molti esempi etichettati come questi e quindi riuscire a eseguire stime sui futuri punti dati, ad esempio per identificare nuove foto con l'animale corretto o assegnare prezzi di vendita accurati ad altre auto usate. Questo tipo di Machine Learning è utile e diffuso. Tutti i moduli di Azure Machine Learning sono algoritmi di apprendimento supervisionato, ad eccezione di [K-Means Clustering][k-means-clustering].

  * Nell'**apprendimento non supervisionato** ai punti dati non sono associate etichette. L'obiettivo di un algoritmo di apprendimento non supervisionato è invece l'organizzazione dei dati in un certo modo o la descrizione della loro struttura. Questo può significare il raggruppamento dei dati in cluster, come fa K-Means, o l'individuazione di modi diversi in cui osservare dati complessi perché appaiano semplici.

  * Nell'**apprendimento per rinforzo** l'algoritmo arriva a scegliere un'azione in risposta a ogni punto dati. Si tratta di un approccio comune in robotica, in cui il set di letture del sensore in un certo momento è un punto dati e l'algoritmo deve scegliere l'azione successiva del robot. Questo approccio è ideale anche per applicazioni "Internet delle cose" \(Internet of Things, IoT\). L'algoritmo di apprendimento riceve anche un segnale di ricompensa poco dopo, a indicare il livello di correttezza della decisione presa. In base a questo segnale, l'algoritmo modifica la propria strategia per ottenere la ricompensa maggiore. Attualmente in Azure ML non sono disponibili moduli di algoritmi di apprendimento per rinforzo.

* I **metodi bayesiani** presuppongono punti dati statisticamente indipendenti. Questo significa che la variabilità non modellata in un punto dati non è correlata agli altri, ovvero non può essere stimata. Ad esempio, se i dati registrati sono il numero di minuti fino all'arrivo del prossimo treno della metropolitana, due misurazioni eseguite a distanza di un giorno sono statisticamente indipendenti. Tuttavia, due misurazioni eseguite a distanza di un minuto non sono statisticamente indipendenti e il valore di una è altamente predittivo rispetto a quello dell'altra.

* La **regressione dell'albero delle decisioni con boosting** trae vantaggio dalla sovrapposizione delle caratteristiche o dall'interazione tra caratteristiche. Questo significa che, in ogni punto dati specifico, il valore di una caratteristica è in parte predittivo del valore di un'altra. Ad esempio, nel caso di dati di bassa/alta temperatura giornaliera, la possibilità di determinare la bassa temperatura del giorno permette di stimare ragionevolmente anche quella alta. Le informazioni contenute nelle due caratteristiche sono in parte ridondanti.

* La classificazione di dati in più di due categorie può essere eseguita usando un classificatore essenzialmente multiclasse o combinando un set di classificatori a due classi in un **insieme**. L'approccio basato su un insieme prevede un classificatore a due classi per ogni classe, ognuno dei quali separa i dati in due categorie "questa classe" e "non questa classe". I classificatori votano quindi l'assegnazione corrente del punto dati. Questo è il principio operativo alla base del modulo [One-vs-All Multiclass][one-vs-all-multiclass].

* Diversi metodi, tra cui la regressione logistica e Bayes Point Machine, presuppongono **limiti di classe lineari**, ovvero che i limiti tra classi siano all'incirca linee rette \(or iperpiani nel caso più generale\). Spesso questa caratteristica dei dati non viene individuata fino a dopo aver provato a separarli, ma può essere determinata in genere visualizzando i dati in anticipo. Se i limiti di classe appaiono molto irregolari, optare per alberi delle decisioni, giungle delle decisioni, macchine a vettori di supporto o reti neurali.

* Le reti neurali possono essere usate con variabili di categoria creando una **variabile fittizia** per ogni categoria e impostandola su 1 nei casi in cui la categoria è applicabile e su 0 quando non lo è.


<!-- This is how you can add a link to the image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-content-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
-->

<!-- Module References -->
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/
 

<!---HONumber=58_postMigration-->