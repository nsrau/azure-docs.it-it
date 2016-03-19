<properties 
	pageTitle="Come interpretare i risultati dei modelli in Machine Learning | Microsoft Azure" 
	description="Come scegliere il set di parametri ottimale per un algoritmo usando e visualizzando gli output del modulo Score Model." 
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
	ms.date="12/04/2015" 
	ms.author="bradsev" />


# Come interpretare i risultati dei modelli in Azure Machine Learning 
 
**Comprensione e visualizzazione dell'output del modulo "Score Model"** Questo argomento illustra come visualizzare e interpretare risultati di stima in Azure Machine Learning Studio. Dopo aver eseguito il training di un modello e averlo sottoposto a una stima, ossia dopo aver assegnato un punteggio a un modello, è necessario comprendere e interpretare il risultato di stima ottenuto.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

In Azure Machine Learning esistono quattro tipi principali di modelli di apprendimento automatico:

* classificazione
* clustering
* regressione
* sistemi di raccomandazione

Per eseguire stime su questi moduli, ovvero per assegnare loro un punteggio a partire da alcuni dati di test, è possibile usare i moduli seguenti:

* Modulo [Score Model][score-model] per la classificazione e la regressione 
* Modulo [Assign to Clusters][assign-to-clusters] per il clustering 
* Modulo [Score Matchbox Recommender][score-matchbox-recommender] per i sistemi di raccomandazione 
 
Questo documento illustra come interpretare i risultati di stima per ognuno di questi moduli. Per una panoramica su questi tipi di modelli, vedere [Come scegliere i parametri per ottimizzare gli algoritmi in Azure Machine Learning](machine-learning-algorithm-parameters-optimize.md).

Questo argomento illustra l'interpretazione delle stime, non la valutazione dei modelli. Per altre informazioni su come valutare un modello, fare riferimento a [Come valutare le prestazioni del modello in Azure Machine Learning](machine-learning-evaluate-model-performance.md).

Se non si ha familiarità con Azure Machine Learning e si desiderano informazioni su come creare un esperimento semplice con cui iniziare, vedere [Creare un semplice esperimento in Azure Machine Learning Studio](machine-learning-create-experiment.md) in Azure Machine Learning Studio.

##Classificazione
I problemi di classificazione possono essere suddivisi in:

* problemi con solo due classi (classificazione a due classi o binaria) 
* problemi con più di due classi (classificazione multiclasse) 

Sebbene in Azure Machine Learning siano disponibili vari moduli per gestire questi tipi di classificazione, le modalità di interpretazione dei risultati di stima sono in realtà molto simili. Verranno affrontati prima i problemi di classificazione a due classi, quindi quelli di classificazione multiclasse.

###Classificazione a due classi
**Esperimento di esempio**

Un problema di classificazione a due classi può essere costituito, ad esempio, dalla classificazione dei fiori Iris, ovvero dalla necessità di classificare i fiori Iris in base alle loro caratteristiche. Il set di dati Iris disponibile in Azure Machine Learning è un subset del celebre [Set di dati Iris](http://en.wikipedia.org/wiki/Iris_flower_data_set) e contiene istanze di due sole specie di fiori (classi 0 e 1). Ciascun fiore presenta quattro caratteristiche: lunghezza del sepalo, larghezza del sepalo, lunghezza del petalo e larghezza del petalo.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/1.png)

Figura 1 Esperimento del problema di classificazione a due classi relativo ai fiori Iris

Per risolvere il problema è stato eseguito un esperimento, come illustrato nella figura 1. È stato eseguito il training e assegnato un punteggio a un modello degli alberi delle decisioni con boosting a due classi. È possibile visualizzare i risultati di stima dal modulo [Score Model][score-model] facendo clic sulla porta di output del modulo [Score Model][score-model] e quindi su **Visualize** nel menu che appare. Vengono visualizzati i risultati dell'assegnazione del punteggio, come illustrato nella figura 2.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/1_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/2.png)

Figura 2 Visualizzazione dei risultati del modulo Score Model nella classificazione a due classi

**Interpretazione dei risultati**

Nella tabella dei risultati sono presenti sei colonne: le quattro colonne di sinistra rappresentano le quattro caratteristiche dei fiori, mentre le due colonne di destra, Scored Labels e Scored Probabilities, riportano i risultati di stima. La colonna Scored Probabilities indica le probabilità che un fiore appartenga alla classe positiva (classe 1). Il primo numero della colonna, 0.028571, significa ad esempio che la probabilità che il primo fiore appartenga alla classe 1 è pari a 0,028571. La colonna Scored Labels rappresenta invece la classe stimata per ogni fiore, determinata in base alla colonna Scored Probabilities. Se il valore della colonna Scored Probabilities per un fiore è superiore a 0,5, viene stimata l'appartenenza alla classe 1; in caso contrario, viene stimata la classe 0.

**Pubblicazione come servizio Web**

Dopo aver compreso e approvato i risultati della stima, è possibile pubblicare l'esperimento come servizio Web, in modo che possa essere distribuito in varie applicazioni e chiamato per ottenere stime di classe su un nuovo fiore Iris. Per istruzioni su come trasformare un esperimento di training in un esperimento di assegnazione di punteggio e pubblicarlo come servizio Web, vedere [Pubblicare il servizio Web di Azure Machine Learning](machine-learning-walkthrough-5-publish-web-service.md). Questa procedura consente di ottenere un esperimento di assegnazione di punteggio, come illustrato nella figura 3.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/3.png)

Figura 3 Esperimento di assegnazione di punteggio per un problema di classificazione a due classi relativo ai fiori Iris

A questo punto è necessario impostare l'input e output per il servizio Web. L'input è costituito, ovviamente, dalla porta di input di destra del modulo [Score Model][score-model], ovvero dalle caratteristiche del fiore Iris. La scelta dell'output varia a seconda che si sia interessati alla classe stimata (etichetta con punteggio), alla probabilità con punteggio o a entrambe. Si suppone in questo caso di essere interessati a entrambe. Per selezionare le colonne di output desiderate, è necessario usare un modulo [Project Columns][project-columns]. A questo scopo, fare clic sul modulo [Project Columns][project-columns] e quindi su **Launch column selector** nel pannello destro e selezionare **Scored Labels** e **Scored Probabilities**. Dopo aver impostato la porta di output del modulo [Project Columns][project-columns] e averlo eseguito di nuovo, dovrebbe essere possibile pubblicare l'esperimento di assegnazione del punteggio come servizio Web facendo clic sul pulsante **PUBLISH WEB SERVICE** nella parte inferiore. L'esperimento finale si presenta come nella figura 4.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/4.png)

Figura 4 Esperimento finale di assegnazione di punteggio per un problema di classificazione a due classi relativo ai fiori Iris

Dopo aver eseguito il servizio Web e aver immesso alcuni valori funzione di un'istanza di test, verrà restituito un risultato composto da due numeri: il primo numero rappresenta l'etichetta con punteggio, il secondo la probabilità con punteggio. Con una probabilità pari a 0,9655, si stima che il fiore appartenga alla classe 1.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/4_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/5.png)

Figura 5 Risultato del servizio Web relativo alla classificazione a due classi

###Classificazione multiclasse
**Esperimento di esempio**

In questo esercizio verrà considerata un'attività di riconoscimento di lettere come esempio di classificazione multiclasse. Il classificatore tenterà di prevedere una lettera (classe) a partire da alcuni valori di attributo scritti a mano estratti da immagini scritte a mano. Nei dati di training sono presenti sedici funzioni estratte da immagini di lettere scritte a mano. Le ventisei lettere dell'alfabeto compongono le ventisei classi dell'esperimento, il cui scopo è quello di eseguire il training di un modello di classificazione multiclasse per il riconoscimento di lettere e una stima dello stesso set di funzioni su un set di dati di test, come illustrato nella figura 6.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/5_1.png)
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/6.png)

Figura 6 Esperimento per un problema di classificazione multiclasse relativo al riconoscimento di lettere

È possibile visualizzare i risultati dal modulo [Score Model][score-model] facendo clic con il pulsante destro/sinistro del mouse sulla porta di output del modulo [Score Model][score-model] e quindi su **Visualize**. Viene visualizzata una finestra come quella illustrata nella figura 7.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/7.png)

Figura 7 Visualizzazione dei risultati del modulo Score Model nella classificazione multiclasse

**Interpretazione dei risultati**

Le sedici colonne di sinistra rappresentano i valori funzione del set di test. Le colonne denominate Scored Probabilities for Class "XX" corrispondono alla colonna Scored Probabilities nella classificazione a due classi. Indicano infatti la probabilità che la voce corrispondente appartenga a una determinata classe. Per la prima voce, ad esempio, la probabilità che appartenga alla classe "A" è di 0,003571, quella che appartenga alla classe "B" è di 0,000451 e così via. L'ultima colonna, denominata Scored Labels, corrisponde alla colonna Scored Labels nella classificazione a due classi: seleziona la classe con il valore Scored Probability più elevato come classe stimata per la voce corrispondente. Per la prima voce, ad esempio, l'etichetta stimata è "F" poiché la probabilità di appartenere alla classe "F" è quella più elevata (0,916995).

**Pubblicazione come servizio Web**

Questa volta, anziché avvalersi del modulo [Project Columns][project-columns] per selezionare le colonne da usare come output del servizio Web, per ogni voce si ottiene l'etichetta con punteggio e la relativa probabilità. La logica di base è trovare la probabilità più alta tra tutte le probabilità con punteggio. A questo scopo è necessario usare il modulo [Execute R Script][execute-r-script]. Il codice R è illustrato nella figura 8, l'esperimento nella figura 9.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/8.png)

Figura 8 Codice R per l'estrazione delle etichette con punteggio e delle probabilità associate alle etichette
  
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/9.png)

Figura 9 Esperimento finale di assegnazione di punteggio per un problema di classificazione multiclasse relativo al riconoscimento di lettere

Dopo aver pubblicato ed eseguito il servizio Web, nonché aver immesso alcuni valori funzione di input, verrà restituito un risultato simile a quello della figura 10. Si stima che questa lettera scritta a mano, con le sedici funzioni estratte, appartenga alla classe "T" con una probabilità pari a 0,9715.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/9_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/10.png)

Figura 10 Risultato del servizio Web relativo alla classificazione a due classi Iris

##Regressione

I problemi di regressione differiscono dai problemi di classificazione per alcuni aspetti. Nei problemi di classificazione, infatti, si tenta di stimare classi discrete, ovvero di capire a quale classe appartenga un fiore Iris, mentre nei problemi di regressione si tenta di stimare una variabile continua, ad esempio il prezzo di un'automobile, come illustrato nell'esempio seguente.

**Esperimento di esempio**

Come esempio di regressione si considera la stima del prezzo di un'automobile. Si tenta quindi di stimare il prezzo di un'automobile a partire dalle sue caratteristiche, tra cui la marca, il tipo di carburante, il tipo di telaio, la ruota motrice e così via. L'esperimento è illustrato nella figura 11.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/11.png)

Figura 11 Esperimento di un problema di regressione relativo al prezzo di un'automobile

Visualizzando il modulo [Score Model][score-model] si otterrà un risultato simile a quello della figura 12.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/12.png)

Figura 12 Visualizzazione del risultato di assegnazione del punteggio per un problema relativo alla stima del prezzo di un'automobile

**Interpretazione dei risultati**

In questa schermata con il risultato di assegnazione del punteggio, la colonna dei risultati corrisponde a Scored Labels. I numeri rappresentano invece il prezzo stimato per ogni automobile.

**Pubblicazione come servizio Web**

È possibile pubblicare l'esperimento di regressione in un servizio Web e chiamarlo per eseguire la stima del prezzo dell'automobile seguendo la stessa procedura usata per la classificazione a due classi.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/13.png)

Figura 13 Esperimento di assegnazione di punteggio per un problema di regressione relativo al prezzo di un'automobile

Eseguendo il servizio Web si otterrà un risultato simile a quello della figura 14. Il prezzo stimato per l'automobile è pari a 15085,52.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/13_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/14.png)

Figure 14 Risultato della pubblicazione come servizio Web del problema di regressione relativo al prezzo di un'automobile

##Clustering

**Esperimento di esempio**

Per creare un esperimento di clustering verrà usato di nuovo il set di dati Iris. In questo caso, tuttavia, verranno escluse dal set di dati le etichette di classe, in modo che sia composto solo dalle caratteristiche e possa essere usato per il clustering. Verrà inoltre specificato che dovranno essere due i cluster da usare nel processo di training, ovvero i fiori verranno raggruppati in due classi. L'esperimento è illustrato nella figura 15.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/15.png)

Figura 15 Esperimento del problema relativo al clustering dei fiori Iris

Il clustering differisce dalla classificazione per il fatto che il set di dati di training non dispone di etichette verificate proprie. L'interesse si incentra in questo caso sul modo in cui poter raggruppare le istanze di set di dati di training in cluster distinti. Durante il processo di training, il modello etichetta le voci man mano che apprende le differenze tra le relative caratteristiche. In questo modo, il modello di training potrà essere usato anche per classificare voci future. In un problema di clustering, sono due le parti del risultato di particolare interesse ai fini dell'esperimento: come etichettare il set di dati di training e come classificare un nuovo set di dati nel modello di training.

La prima parte del risultato può essere visualizzata facendo clic sulla porta di output sinistra del modulo [Train Clustering Model][train-clustering-model] e quindi facendo clic su **Visualize** La finestra di visualizzazione è illustrata nella figura 16.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/16.png)

Figura 16 Visualizzazione del risultato di clustering per il set di dati di training

Il risultato della seconda parte, in cui viene eseguito il clustering delle nuove voci tramite il modello di clustering di training, è illustrato nella figura 17.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/17.png)

Figura 17 Visualizzazione del risultato di clustering sul nuovo set di dati

**Interpretazione dei risultati**

Sebbene i risultati delle due parti provengano da fasi diverse dell'esperimento, risultano identici e devono essere interpretati nello stesso modo. Le prime quattro colonne corrispondono alle caratteristiche, mentre l'ultima colonna, Assignments, rappresenta il risultato della stima. Le voci a cui è stato assegnato lo stesso numero si stima che appartengano allo stesso cluster, ovvero che presentino alcune analogie (in questo esperimento è stata usata la distanza euclidea come parametro predefinito). Poiché era stato deciso che i cluster da usare dovessero essere due, nella colonna Assignments le voci vengono etichettate con il valore 0 o 1.

**Pubblicazione come servizio Web**

È possibile pubblicare l'esperimento di clustering in un servizio Web e chiamarlo per eseguire stime di clustering seguendo la stessa procedura usata per la classificazione a due classi.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/18.png)

Figura 18 Esperimento di assegnazione di punteggio per un problema di clustering relativo ai fiori Iris

Dopo aver eseguito il servizio Web si otterrà un risultato simile a quello della figura 19. Si stima che questo fiore appartenga al cluster 0.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/18_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/19.png)

Figura 19 Risultato della pubblicazione come servizio Web della classificazione a due classi dei fiori Iris

##Sistema di raccomandazione
**Esperimento di esempio**

Per illustrare i sistemi di raccomandazione verrà usato come esempio un problema di raccomandazione di ristoranti: consigliare i ristoranti ai clienti in base alla relativa cronologia di valutazioni. I dati di input sono costituiti da tre parti:

* valutazioni sui ristoranti espresse dai clienti 
* dati sulle caratteristiche dei clienti 
* dati sulle caratteristiche dei ristoranti 

Il modulo [Train Matchbox Recommender][train-matchbox-recommender] integrato in Azure Machine Learning consente di eseguire varie operazioni:

- Stimare le valutazioni per un determinato utente ed elemento
- Raccomandare elementi a un determinato utente
- Trovare gli utenti correlati a un determinato utente
- Trovare gli elementi correlati a un determinato elemento

È possibile scegliere il tipo di operazione che si desidera eseguire selezionando una delle quattro opzioni disponibili nel menu **Recommender prediction kind** del riquadro destro. In questo caso verranno analizzati tutti i quattro scenari. Un tipico esperimento di Azure Machine Learning per il sistema di raccomandazione è illustrato nella figura 20. Per altre informazioni su come usare i moduli relativi al sistema di raccomandazione, vedere le pagine della guida di [Train Matchbox Recommender][train-matchbox-recommender] e [Score Matchbox Recommender][score-matchbox-recommender].
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/19_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/20.png)

Figura 20 Esperimento per il sistema di raccomandazione

**Interpretazione dei risultati**

*Stimare le valutazioni per un determinato utente ed elemento*

Selezionando la voce Rating Prediction del menu **Recommender prediction kind**, si chiede al sistema di raccomandazione di stimare la valutazione per un determinato utente ed elemento. Verrà visualizzato l'output del modulo [Score Matchbox Recommender][score-matchbox-recommender], simile a quello della figura 21.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/21.png)

Nella figura 21 Visualizzazione del risultato di assegnazione del punteggio del sistema di raccomandazione - Stima della valutazione

Sono presenti tre colonne: le prime due rappresentano le coppie utente-elemento ricavate dai dati di input, mentre la terza indica la valutazione stimata di un utente per un determinato elemento. Nella prima riga, ad esempio, si stima che la valutazione assegnata dal cliente U1048 al ristorante 135026 sia pari a 2.

*Raccomandare elementi a un determinato utente*

Selezionando la voce **Item Recommendation** del menu **Recommender prediction kind**, si chiede al sistema di raccomandazione di consigliare elementi a un determinato utente. In questo scenario è necessario scegliere un parametro aggiuntivo: Recommended item selection. Mentre l'opzione **From Rated Items (for model evaluation)** viene principalmente usata per la valutazione del modello durante il processo di training, per questa fase di stima si usa l'opzione **From All Items**. Verrà visualizzato l'output del modulo [Score Matchbox Recommender][score-matchbox-recommender], simile a quello della figura 22.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/22.png)

Figura 22 Visualizzazione del risultato di assegnazione del punteggio del sistema di raccomandazione - Raccomandazione dell'elemento

Sono presenti sei colonne: la prima rappresenta gli ID utente, ricavati dai dati di input, per i quali consigliare gli elementi, mentre le altre cinque contengono gli elementi consigliati all'utente, in ordine discendente in termini di rilevanza. Nella prima riga, ad esempio, il ristorante più consigliato per il cliente U1048 è il numero 134986, seguito da 135018, 134975, 135021 e 132862.

*Trovare gli utenti correlati a un determinato utente*

Selezionando la voce Related Users del menu "Recommender prediction kind", si chiede al sistema di raccomandazione di trovare utenti correlati a un determinato utente. Per utenti correlati si intendono tutti gli utenti con preferenze simili. In questo scenario è necessario scegliere un parametro aggiuntivo: Related user selection. Mentre l'opzione "From Users That Rated Items (for model evaluation)" viene principalmente usata per la valutazione del modello durante il processo di training, per questa fase di stima si usa l'opzione "From All Users" Verrà visualizzato l'output del modulo [Score Matchbox Recommender][score-matchbox-recommender], simile a quello della figura 23.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/23.png)

Figura 23 Visualizzazione del risultato di assegnazione del punteggio del sistema di raccomandazione - Utenti correlati

Sono presenti sei colonne: la prima rappresenta gli ID utente, ricavati dai dati di input, per i quali trovare utenti correlati, mentre le altre cinque contengono gli utenti correlati stimati per l'utente, in ordine discendente in termini di rilevanza. Nella prima riga, ad esempio, il cliente più rilevante per il cliente U1048 è il numero U1051, seguito da U1066, U1044, U1017 e U1072.

**Trovare gli elementi correlati a un determinato elemento**

Selezionando la voce **Related Items** del menu **Recommender prediction kind**, si chiede al sistema di raccomandazione di trovare elementi correlati a un determinato elemento. Per elementi correlati si intendono tutti gli elementi che con più probabilità sono apprezzati dallo stesso utente. In questo scenario è necessario scegliere un parametro aggiuntivo: Related item selection. Mentre l'opzione **From Rated Items (for model evaluation)** viene principalmente usata per la valutazione del modello durante il processo di training, per questa fase di stima si usa l'opzione **From All Items** Verrà visualizzato l'output del modulo [Score Matchbox Recommender][score-matchbox-recommender], simile a quello della figura 24.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/24.png)

Figura 24 Visualizzazione del risultato di assegnazione del punteggio del sistema di raccomandazione - Elementi correlati

Sono presenti sei colonne: la prima rappresenta gli ID elemento, ricavati dai dati di input, per i quali trovare elementi correlati, mentre le altre cinque contengono gli elementi correlati stimati per l'elemento, in ordine discendente in termini di rilevanza. Nella prima riga, ad esempio, l'elemento più rilevante per l'elemento 135026 è il numero 135074, seguito da 135035, 132875, 135055 e 134992. Pubblicazione come servizio Web

Il processo di pubblicazione di questi esperimenti come servizi Web per ottenere stime è molto simile per tutti i quattro scenari. In questo caso verrà preso ad esempio il secondo scenario: raccomandare elementi a un determinato utente. È possibile seguire la stessa procedura anche per gli altri tre scenari.

Salvando il sistema di raccomandazione di training come modello di training e filtrando i dati di input in un'unica colonna ID utente, come richiesto, è possibile collegare l'esperimento come illustrato nella figura 25 e pubblicarlo come servizio Web.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/25.png)
 
Figura 25 Esperimento di assegnazione di punteggio per un problema di raccomandazione di ristoranti

Eseguendo il servizio Web si otterrà un risultato simile a quello della figura 14. I cinque ristoranti consigliati per l'utente U1048 sono 134986, 135018, 134975, 135021 e 132862.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/25_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/26.png)

Figura 26 Risultato della pubblicazione come servizio Web del problema relativo alla raccomandazione di ristoranti


<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
 

<!---HONumber=AcomDC_1210_2015-->