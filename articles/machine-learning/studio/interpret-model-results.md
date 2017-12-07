---
title: Interpretare i risultati dei modelli in Machine Learning | Documentazione Microsoft
description: Come scegliere il set di parametri ottimale per un algoritmo usando e visualizzando gli output del modulo Score Model.
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 6230e5ab-a5c0-4c21-a061-47675ba3342c
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bradsev;garye
ms.openlocfilehash: d6563d411e9f159399f9863a5b572365dc2b05cc
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="interpret-model-results-in-azure-machine-learning"></a>Interpretare i risultati dei modelli in Azure Machine Learning
Questo argomento illustra come visualizzare e interpretare risultati di stima in Azure Machine Learning Studio. Dopo aver eseguito il training di un modello e averlo sottoposto a una stima, ossia dopo aver assegnato un punteggio a un modello, è necessario comprendere e interpretare il risultato di stima.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

In Azure Machine Learning esistono quattro tipi principali di modelli di apprendimento automatico:

* classificazione
* clustering
* regressione
* sistemi di raccomandazione

I moduli usati per eseguire stime sulla base di questi modelli sono:

* Modulo [Score Model][score-model] per la classificazione e la regressione
* Modulo [Assign to Clusters][assign-to-clusters] per il clustering
* Modulo [Score Matchbox Recommender][score-matchbox-recommender] per i sistemi di raccomandazione

Questo documento illustra come interpretare i risultati di stima per ognuno di questi moduli. Per una panoramica di questi moduli, vedere [Come scegliere i parametri per ottimizzare gli algoritmi in Azure Machine Learning](algorithm-parameters-optimize.md).

Questo argomento illustra l'interpretazione delle stime, non la valutazione dei modelli. Per altre informazioni su come valutare un modello, vedere [Come valutare le prestazioni del modello in Azure Machine Learning](evaluate-model-performance.md).

Se non si ha familiarità con Azure Machine Learning e si ha bisogno di assistenza per creare un esperimento semplice con cui iniziare, vedere [Creare un semplice esperimento in Azure Machine Learning Studio](create-experiment.md) in Azure Machine Learning Studio.

## <a name="classification"></a>classificazione
I problemi di classificazione possono essere suddivisi in:

* Problemi con solo due classi (classificazione a due classi o binaria)
* Problemi con più di due classi (classificazione multiclasse)

Azure Machine Learning include vari moduli per gestire ognuno di questi tipi di classificazione, ma le modalità di interpretazione dei risultati di stima sono simili.

### <a name="two-class-classification"></a>Classificazione a due classi
**Esperimento di esempio**

Un esempio di un problema di classificazione a due classi è costituito dalla classificazione dei fiori Iris, ovvero dalla necessità di classificare i fiori Iris in base alle loro caratteristiche. Il set di dati Iris disponibile in Azure Machine Learning è un subset del celebre [Set di dati Iris](http://en.wikipedia.org/wiki/Iris_flower_data_set) e contiene istanze di due sole specie di fiori (classi 0 e 1). Ciascun fiore presenta quattro caratteristiche: lunghezza del sepalo, larghezza del sepalo, lunghezza del petalo e larghezza del petalo.

![Schermata dell'esperimento relativo ai fiori Iris](./media/interpret-model-results/1.png)

Figura 1. Esperimento del problema di classificazione a due classi relativo ai fiori Iris

Per risolvere il problema è stato eseguito un esperimento, come illustrato nella figura 1. È stato eseguito il training e assegnato un punteggio a un modello degli alberi delle decisioni con boosting a due classi. Per visualizzare i risultati di stima tramite il modulo [Score Model][score-model], fare clic sulla porta di output del modulo [Score Model][score-model] e quindi fare clic su **Visualize** (Visualizza).

![Modulo Score Model](./media/interpret-model-results/1_1.png)

Vengono visualizzati i risultati dell'assegnazione del punteggio, come illustrato nella Figura 2.

![Risultati dell'esperimento di classificazione a due classi relativo ai fiori Iris](./media/interpret-model-results/2.png)

Figura 2. Visualizzazione dei risultati del modulo Score Model nella classificazione a due classi

**Interpretazione dei risultati**

Nella tabella dei risultati sono presenti sei colonne: le quattro colonne di sinistra rappresentano le quattro caratteristiche dei fiori, mentre le due colonne di destra, Scored Labels (Etichette con punteggio) e Scored Probabilities (Probabilità con punteggio), riportano i risultati di stima. La colonna Scored Probabilities (Probabilità con punteggio) indica le probabilità che un fiore appartenga alla classe positiva (Classe 1). Il primo numero della colonna (0.028571) significa ad esempio che la probabilità che il primo fiore appartenga alla Classe 1 è pari a 0,028571. La colonna Scored Labels rappresenta invece la classe stimata per ogni fiore, determinata in base alla colonna Scored Probabilities. Se il valore della colonna Scored Probabilities (Probabilità con punteggio) per un fiore è superiore a 0,5, viene stimata l'appartenenza alla Classe 1; in caso contrario, viene stimata la Classe 0.

**Pubblicazione come servizio Web**

Dopo aver compreso e approvato i risultati della stima, è possibile pubblicare l'esperimento come servizio Web, in modo che possa essere distribuito in varie applicazioni e chiamato per ottenere stime di classe su un nuovo fiore Iris. Per informazioni su come trasformare un esperimento di training in un esperimento di assegnazione dei punteggi e pubblicarlo come servizio Web, vedere [Pubblicare il servizio Web di Azure Machine Learning](walkthrough-5-publish-web-service.md). Questa procedura consente di ottenere un esperimento di assegnazione dei punteggi, come illustrato nella figura 3.

![Schermata dell'esperimento di assegnazione dei punteggi](./media/interpret-model-results/3.png)

Figura 3. Esperimento di assegnazione dei punteggi per un problema di classificazione a due classi relativo ai fiori Iris

A questo punto è necessario impostare l'input e l'output per il servizio Web. L'input è costituito dalla porta di input di destra del modulo [Score Model][score-model], ovvero dalle caratteristiche del fiore Iris. La scelta dell'output varia a seconda che si sia interessati alla classe stimata (etichetta con punteggio), alla probabilità con punteggio o a entrambe. In questo esempio si suppone di essere interessati a entrambe. Per selezionare le colonne di output desiderate, usare un modulo [Select Columns in Data set][select-columns]. Fare clic su [Select Columns in Data set][select-columns], su **Launch column selector** (Avvia selettore colonne) e selezionare **Scored Labels** (Etichette con punteggio) e **Scored Probabilities** (Probabilità con punteggio). Dopo aver impostato la porta di output di [Select Columns in Data set][select-columns] e averlo eseguito di nuovo, fare clic su **PUBLISH WEB SERVICE** (PUBBLICA SERVIZIO WEB) per provare a pubblicare l'esperimento di assegnazione dei punteggi. L'esperimento finale si presenta come nella figura 4.

![Esperimento di classificazione a due classi relativo ai fiori Iris](./media/interpret-model-results/4.png)

Figura 4. Esperimento finale di assegnazione dei punteggi per un problema di classificazione a due classi relativo ai fiori Iris

Dopo aver eseguito il servizio Web e aver immesso alcuni valori funzione di un'istanza di test, il risultato sarà composto da due numeri: il primo numero rappresenta l'etichetta con punteggio e il secondo la probabilità con punteggio. Con una probabilità pari a 0,9655, si stima che il fiore appartenga alla Classe 1.

![Modello del test di interpretazione del punteggio](./media/interpret-model-results/4_1.png)

![Risultati del test di assegnazione dei punteggi](./media/interpret-model-results/5.png)

Figura 5. Risultato del servizio Web relativo alla classificazione a due classi dei fiori Iris

### <a name="multi-class-classification"></a>Classificazione multiclasse
**Esperimento di esempio**

In questo esercizio viene considerata un'attività di riconoscimento di lettere come esempio di classificazione multiclasse. Il classificatore tenta di prevedere una lettera (classe) in base ad alcuni valori di attributo scritti a mano estratti da immagini scritte a mano.

![Esempio di riconoscimento delle lettere](./media/interpret-model-results/5_1.png)

Nei dati di training sono presenti 16 funzioni estratte da immagini di lettere scritte a mano. Le 26 lettere compongono le 26 classi. Nella figura 6 è illustrato un esperimento che eseguirà il training di un modello di classificazione multiclasse per il riconoscimento delle lettere e una stima dello stesso set di funzioni su un set di dati di test.

![Esperimento di classificazione multiclasse relativo al riconoscimento delle lettere](./media/interpret-model-results/6.png)

Figura 6. Esperimento per un problema di classificazione multiclasse relativo al riconoscimento delle lettere

Per visualizzare i risultati del modulo [Score Model][score-model] come illustrato nella figura 7, fare clic sulla porta di output del modulo [Score Model][score-model] e quindi fare clic su **Visualize** (Visualizza).

![Risultati del modulo Score Model](./media/interpret-model-results/7.png)

Figura 7. Visualizzazione dei risultati del modulo Score Model nella classificazione multiclasse

**Interpretazione dei risultati**

Le 16 colonne di sinistra rappresentano i valori funzione del set di test. Le colonne denominate Scored Probabilities for Class "XX" (Probabilità con punteggio per classe "XX") corrispondono alla colonna Scored Probabilities (Probabilità con punteggio) nella classificazione a due classi. Indicano infatti la probabilità che la voce corrispondente appartenga a una determinata classe. Per la prima voce, ad esempio, la probabilità che appartenga alla classe "A" è di 0,003571, quella che appartenga alla classe "B" è di 0,000451 e così via. L'ultima colonna, denominata Scored Labels (Etichette con punteggio), corrisponde alla colonna Scored Labels (Etichette con punteggio) nella classificazione a due classi: seleziona la classe con il valore Scored Probability più elevato come classe stimata per la voce corrispondente. Per la prima voce, ad esempio, l'etichetta stimata è "F" poiché la probabilità di appartenere alla classe "F" è quella più elevata (0,916995).

**Pubblicazione come servizio Web**

È anche possibile ottenere l'etichetta con punteggio per ogni voce e la relativa probabilità. La logica di base è trovare la probabilità più alta tra tutte le probabilità con punteggio. A tale scopo, usare il modulo [Execute R Script][execute-r-script]. Il codice R è illustrato nella figura 8, il risultato dell'esperimento nella figura 9.

![Esempio di codice R](./media/interpret-model-results/8.png)

Figura 8. Codice R per l'estrazione delle etichette con punteggio e delle probabilità associate alle etichette

![Risultato dell'esperimento](./media/interpret-model-results/9.png)

Figura 9. Esperimento finale di assegnazione dei punteggi per un problema di classificazione multiclasse relativo al riconoscimento delle lettere

Dopo aver pubblicato ed eseguito il servizio Web, nonché aver immesso alcuni valori funzione di input, verrà restituito un risultato simile a quello della figura 10. Si stima che questa lettera scritta a mano, con le 16 funzioni estratte, appartenga alla classe "T" con una probabilità pari a 0,9715.

![Modulo del test di interpretazione del punteggio](./media/interpret-model-results/9_1.png)

![Risultato del test](./media/interpret-model-results/10.png)

Figura 10. Risultato del servizio Web relativo alla classificazione multiclasse

## <a name="regression"></a>regressione
I problemi di regressione differiscono dai problemi di classificazione per alcuni aspetti. Nei problemi di classificazione, infatti, si tenta di stimare classi discrete, ovvero di capire a quale classe appartenga un fiore Iris, mentre, come illustrato nell'esempio seguente, in un problema di regressione si tenta di stimare una variabile continua, ad esempio il prezzo di un'automobile.

**Esperimento di esempio**

Come esempio di regressione si considera la stima del prezzo di un'automobile. Si tenta quindi di stimare il prezzo di un'automobile a partire dalle sue caratteristiche, tra cui la marca, il tipo di carburante, il tipo di telaio e la ruota motrice. L'esperimento è illustrato nella figura 11.

![Esperimento di regressione relativo al prezzo di un'automobile](./media/interpret-model-results/11.png)

Figura 11. Esperimento di un problema di regressione relativo al prezzo di un'automobile

Se si visualizza il modulo [Score Model][score-model], si otterrà un risultato simile a quello illustrato nella figura 12.

![Risultato di assegnazione dei punteggi per un problema relativo alla stima del prezzo di un'automobile](./media/interpret-model-results/12.png)

Figura 12. Risultato di assegnazione dei punteggi per un problema relativo alla stima del prezzo di un'automobile

**Interpretazione dei risultati**

Scored Labels (Etichette con punteggio) è la colonna dei risultati in questa schermata con il risultato di assegnazione dei punteggi. I numeri rappresentano invece il prezzo stimato per ogni automobile.

**Pubblicazione come servizio Web**

È possibile pubblicare l'esperimento di regressione in un servizio Web e chiamarlo per eseguire la stima del prezzo dell'automobile seguendo la stessa procedura usata per la classificazione a due classi.

![Esperimento di assegnazione dei punteggi per un problema di regressione relativo al prezzo di un'automobile](./media/interpret-model-results/13.png)

Figura 13. Esperimento di assegnazione dei punteggi di un problema di regressione relativo al prezzo di un'automobile

Eseguendo il servizio Web si otterrà un risultato simile a quello della figura 14. Il prezzo stimato per l'automobile è pari a $15.085,52.

![Modulo del test di interpretazione del punteggio](./media/interpret-model-results/13_1.png)

![Risultati del modulo di assegnazione dei punteggi](./media/interpret-model-results/14.png)

Figura 14. Risultato del servizio Web del problema di regressione relativo al prezzo di un'automobile

## <a name="clustering"></a>clustering
**Esperimento di esempio**

Per creare un esperimento di clustering verrà usato di nuovo il set di dati Iris. In questo caso è possibile escludere dal set di dati le etichette di classe, in modo che sia composto solo dalle caratteristiche e possa essere usato per il clustering. Verrà inoltre specificato che dovranno essere due i cluster da usare nel processo di training, ovvero i fiori verranno raggruppati in due classi. L'esperimento è illustrato nella figura 15.

![Esperimento del problema relativo al clustering dei fiori Iris](./media/interpret-model-results/15.png)

Figura 15. Esperimento del problema relativo al clustering dei fiori Iris

Il clustering differisce dalla classificazione per il fatto che il set di dati di training non dispone di etichette verificate proprie. Il clustering raggruppa le istanze di set di dati di training in cluster distinti. Durante il processo di training, il modello etichetta le voci man mano che apprende le differenze tra le relative caratteristiche. In questo modo, il modello di training potrà essere usato per classificare ulteriormente voci future. In un problema di clustering, sono due le parti del risultato di particolare interesse ai fini dell'esperimento: la prima è etichettare il set di dati di training e la seconda classificare un nuovo set di dati nel modello di training.

Per visualizzare la prima parte del risultato, fare clic sulla porta di output sinistra di [Train Clustering Model][train-clustering-model] e quindi su **Visualize** (Visualizza). La visualizzazione è illustrata nella figura 16.

![Risultato di clustering](./media/interpret-model-results/16.png)

Figura 16. Visualizzazione del risultato di clustering per il set di dati di training

Il risultato della seconda parte, in cui viene eseguito il clustering delle nuove voci tramite il modello di clustering di training, è illustrato nella figura 17.

![Visualizzazione del risultato di clustering](./media/interpret-model-results/17.png)

Figura 17. Visualizzazione del risultato di clustering su un nuovo set di dati

**Interpretazione dei risultati**

Sebbene i risultati delle due parti provengano da fasi diverse dell'esperimento, risultano identici e devono essere interpretati nello stesso modo. Le prime quattro colonne corrispondono alle caratteristiche, mentre l'ultima colonna, denominata Assignments (Assegnazioni), rappresenta il risultato della stima. Le voci a cui è stato assegnato lo stesso numero si stima che appartengano allo stesso cluster, ovvero che presentino alcune analogie (in questo esperimento viene usata la distanza euclidea come parametro predefinito). Poiché è stato specificato che i cluster da usare dovessero essere due, nella colonna Assignments (Assegnazioni) le voci vengono etichettate con il valore 0 o 1.

**Pubblicazione come servizio Web**

È possibile pubblicare l'esperimento di clustering in un servizio Web e chiamarlo per eseguire stime di clustering seguendo la stessa procedura usata per la classificazione a due classi.

![Esperimento di assegnazione dei punteggi per un problema di clustering relativo ai fiori Iris](./media/interpret-model-results/18.png)

Figura 18. Esperimento di assegnazione dei punteggi di un problema di clustering relativo ai fiori Iris

Dopo aver eseguito il servizio Web si otterrà un risultato simile a quello della figura 19. Si stima che questo fiore appartenga al cluster 0.

![Modulo del test di interpretazione del punteggio](./media/interpret-model-results/18_1.png)

![Risultato del modulo di assegnazione dei punteggi](./media/interpret-model-results/19.png)

Figura 19. Risultato del servizio Web relativo alla classificazione a due classi dei fiori Iris

## <a name="recommender-system"></a>Sistema di raccomandazione
**Esperimento di esempio**

Per illustrare i sistemi di raccomandazione viene usato come esempio un problema di raccomandazione di ristoranti: consigliare i ristoranti ai clienti in base alla relativa cronologia di valutazioni. I dati di input sono costituiti da tre parti:

* Valutazioni sui ristoranti espresse dai clienti
* Dati sulle caratteristiche dei clienti
* Restaurant feature data

Il modulo [Train Matchbox Recommender][train-matchbox-recommender] di Azure Machine Learning consente di eseguire diverse operazioni:

* Stimare le valutazioni per un determinato utente ed elemento
* Raccomandare elementi a un determinato utente
* Trovare gli utenti correlati a un determinato utente
* Trovare gli elementi correlati a un determinato elemento

È possibile scegliere il tipo di operazione da eseguire selezionando una delle quattro opzioni disponibili nel menu **Recommender prediction kind** (Tipo di stima del sistema di raccomandazione). In questo caso vengono analizzati tutti i quattro scenari.

![Matchbox Recommender](./media/interpret-model-results/19_1.png)

Un tipico esperimento di Azure Machine Learning per un sistema di raccomandazione è illustrato nella figura 20. Per informazioni su come usare i moduli del sistema di raccomandazione, vedere [Train matchbox recommender][train-matchbox-recommender] e [Score matchbox recommender][score-matchbox-recommender].

![Esperimento per il sistema di raccomandazione](./media/interpret-model-results/20.png)

Figura 20. Esperimento per il sistema di raccomandazione

**Interpretazione dei risultati**

**Stimare le valutazioni per un determinato utente ed elemento**

Selezionando la voce **Rating Prediction** (Stima valutazione) in **Recommender prediction kind** (Tipo di stima del sistema di raccomandazione), si chiede al sistema di raccomandazione di stimare la valutazione per un determinato utente ed elemento. Viene visualizzato l'output del modulo [Score Matchbox Recommender][score-matchbox-recommender], simile a quello della figura 21.

![Risultato di assegnazione dei punteggi del sistema di raccomandazione - Stima della valutazione](./media/interpret-model-results/21.png)

Figura 21. Visualizzazione del risultato di assegnazione dei punteggi del sistema di raccomandazione - Stima della valutazione

le prime due rappresentano le coppie utente-elemento ricavate dai dati di input, mentre la terza indica la valutazione stimata di un utente per un determinato elemento. Nella prima riga, ad esempio, si stima che la valutazione assegnata dal cliente U1048 al ristorante 135026 sia pari a 2.

**Raccomandare elementi a un determinato utente**

Selezionando la voce **Item Recommendation** (Raccomandazione elemento) in **Recommender prediction kind** (Tipo di stima del sistema di raccomandazione), si chiede al sistema di raccomandazione di consigliare elementi a un determinato utente. L'ultimo parametro da scegliere in questo scenario è *Recommended item selection* (Selezione elementi consigliati). Mentre l'opzione **From Rated Items (for model evaluation)** (Da elementi valutati (per valutazione modello)) viene principalmente usata per la valutazione del modello durante il processo di training, per questa fase di stima si usa l'opzione **From All Items** (Da tutti gli elementi). Viene visualizzato l'output del modulo [Score Matchbox Recommender][score-matchbox-recommender], simile a quello della figura 22.

![Risultato di assegnazione dei punteggi del sistema di raccomandazione - Raccomandazione dell'elemento](./media/interpret-model-results/22.png)

Figura 22. Visualizzazione del risultato di assegnazione dei punteggi del sistema di raccomandazione - Raccomandazione dell'elemento

La prima delle sei colonne rappresenta gli ID utente per i quali consigliare gli elementi, ricavati dai dati di input, mentre le altre cinque contengono gli elementi consigliati all'utente, in ordine decrescente di rilevanza. Nella prima riga, ad esempio, il ristorante più consigliato per il cliente U1048 è il numero 134986, seguito da 135018, 134975, 135021 e 132862.

**Trovare gli utenti correlati a un determinato utente**

Selezionando la voce **Related Users** (Utenti correlati) in **Recommender prediction kind** (Tipo di stima del sistema di raccomandazione), si chiede al sistema di raccomandazione di trovare utenti correlati a un determinato utente. Per utenti correlati si intendono tutti gli utenti con preferenze simili. L'ultimo parametro da scegliere in questo scenario è *Related user selection* (Selezione utenti correlati). Mentre l'opzione **From Users That Rated Items (for model evaluation)** (Da utenti che hanno valutato gli elementi (per valutazione modello)) viene principalmente usata per la valutazione del modello durante il processo di training, per questa fase di stima si usa l'opzione **From All Users** (Da tutti gli utenti). Viene visualizzato l'output del modulo [Score Matchbox Recommender][score-matchbox-recommender], simile a quello della figura 23.

![Risultato di assegnazione dei punteggi del sistema di raccomandazione - Utenti correlati](./media/interpret-model-results/23.png)

Figura 23. Visualizzazione del risultato di assegnazione dei punteggi del sistema di raccomandazione - Utenti correlati

La prima delle sei colonne rappresenta gli ID utente necessari per trovare utenti correlati, ricavati dai dati di input, mentre le altre cinque contengono gli utenti correlati stimati per l'utente, in ordine decrescente di rilevanza. Nella prima riga, ad esempio, il cliente più rilevante per il cliente U1048 è il numero U1051, seguito da U1066, U1044, U1017 e U1072.

**Trovare gli elementi correlati a un determinato elemento**

Selezionando la voce **Related Items** (Elementi correlati) in **Recommender prediction kind** (Tipo di stima del sistema di raccomandazione), si chiede al sistema di raccomandazione di trovare elementi correlati a un determinato elemento. Per elementi correlati si intendono tutti gli elementi che con più probabilità sono apprezzati dallo stesso utente. L'ultimo parametro da scegliere in questo scenario è *Related item selection* (Selezione elementi correlati). Mentre l'opzione **From Rated Items (for model evaluation)** (Da elementi valutati (per valutazione modello)) viene principalmente usata per la valutazione del modello durante il processo di training, per questa fase di stima si usa l'opzione **From All Items** (Da tutti gli elementi). Vine visualizzato l'output del modulo [Score Matchbox Recommender][score-matchbox-recommender], simile a quello della figura 24.

![Risultato di assegnazione dei punteggi del sistema di raccomandazione - Elementi correlati](./media/interpret-model-results/24.png)

Figura 24. Visualizzazione del risultato di assegnazione dei punteggi del sistema di raccomandazione - Elementi correlati

La prima delle sei colonne rappresenta gli ID elemento necessari per trovare elementi correlati, ricavati dai dati di input, mentre le altre cinque contengono gli elementi correlati stimati per l'elemento, in ordine decrescente in termini di rilevanza. Nella prima riga, ad esempio, l'elemento più rilevante per l'elemento 135026 è il numero 135074, seguito da 135035, 132875, 135055 e 134992.

**Pubblicazione come servizio Web**

Il processo di pubblicazione di questi esperimenti come servizi Web per ottenere stime è molto simile per tutti i quattro scenari. In questo caso viene preso ad esempio il secondo scenario: raccomandare elementi a un determinato utente. È possibile seguire la stessa procedura anche per gli altri tre scenari.

Salvando il sistema di raccomandazione di training come modello di training e filtrando i dati di input in un'unica colonna ID utente, come richiesto, è possibile collegare l'esperimento come illustrato nella figura 25 e pubblicarlo come servizio Web.

![Esperimento di assegnazione dei punteggi per un problema di raccomandazione di ristoranti](./media/interpret-model-results/25.png)

Figura 25. Esperimento di assegnazione dei punteggi per un problema di raccomandazione di ristoranti

Eseguendo il servizio Web si otterrà un risultato simile a quello della figura 26. I cinque ristoranti consigliati per l'utente U1048 sono 134986, 135018, 134975, 135021 e 132862.

![Esempio di servizio di sistema di raccomandazione](./media/interpret-model-results/25_1.png)

![Risultati dell'esperimento di esempio](./media/interpret-model-results/26.png)

Figura 26. Risultato del servizio Web relativo al problema di raccomandazione di ristoranti

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
