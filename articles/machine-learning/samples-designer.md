---
title: Pipeline di esempio e set di dati per la finestra di progettazione (anteprima)
titleSuffix: Azure Machine Learning
description: Usare gli esempi nella finestra di progettazione di Azure Machine Learning per avviare rapidamente le pipeline di Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.custom: designer
ms.openlocfilehash: c1d927583ecf0ac5684c607b7d203c3224fe87b1
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318828"
---
# <a name="example-pipelines--datasets-for-azure-machine-learning-designer-preview"></a>Pipeline e set di dati di esempio per la finestra di progettazione di Azure Machine Learning (anteprima)

Usare gli esempi incorporati nella finestra di progettazione di Azure Machine Learning per iniziare rapidamente a creare le proprie pipeline di Machine Learning. Il [repository GitHub](https://github.com/Azure/MachineLearningDesigner) della finestra di progettazione di Azure Machine Learning contiene la documentazione dettagliata per comprendere alcuni scenari comuni di Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree).
* Un'area di lavoro di Azure Machine Learning con Enterprise SKU.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-sample-pipelines"></a>Usare le pipeline di esempio

La finestra di progettazione salva una copia delle pipeline di esempio nell'area di lavoro dello studio. È possibile modificare la pipeline per adattarla alle proprie esigenze e salvarla come pipeline personalizzata. È possibile usarla come punto di partenza per avviare rapidamente i progetti.

Di seguito viene illustrato come usare un esempio di finestra di progettazione:

1. Accedere a <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> e selezionare l'area di lavoro che si vuole usare.

1. Selezionare **Designer** (Finestra di progettazione).

1. Selezionare una pipeline di esempio nella sezione **Nuova pipeline**.

    Selezionare **Mostra più esempi** per un elenco completo di esempi.

1. Per eseguire una pipeline, è necessario prima di tutto impostare la destinazione di calcolo predefinita in cui eseguirla.

   1. Nel riquadro **Impostazioni** a destra dell'area di disegno selezionare **Seleziona destinazione di calcolo**.

   1. Nella finestra di dialogo visualizzata selezionare una destinazione di calcolo esistente o crearne una nuova. Selezionare **Salva**.

   1. Selezionare **Invia** nella parte superiore del canvas per avviare un'esecuzione della pipeline.

   A seconda della pipeline di esempio e delle impostazioni di calcolo, il completamento delle esecuzioni potrebbe richiedere del tempo. Le impostazioni di calcolo predefinite prevedono una dimensione minima del nodo pari a 0, il che significa che la finestra di progettazione deve allocare risorse dopo l'inattività. Le esecuzioni ripetute della pipeline richiederanno meno tempo, perché le risorse di calcolo sono già allocate. Inoltre, la finestra di progettazione usa i risultati memorizzati nella cache per ogni modulo per migliorare ulteriormente l'efficienza.


1. Al termine dell'esecuzione, è possibile esaminare la pipeline e visualizzare l'output di ogni modulo per acquisire altre informazioni. Per visualizzare l'output dei moduli, procedere come segue:

   1. Selezionare un modulo nel canvas.

   1. Nel riquadro dei dettagli del modulo a destra del canvas selezionare **Output + log**. Selezionare l'icona del grafico ![icona di visualizzazione](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) per visualizzare i risultati di ogni modulo. 

   Usare gli esempi come punti di partenza per alcuni scenari più comuni di Machine Learning.

## <a name="regression"></a>Regressione

Esplorare questi esempi di regressione incorporati.

| Titolo di esempio | Descrizione | 
| --- | --- |
| [Esempio 1: Regressione - Previsione dei prezzi delle automobili (base)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Stimare i prezzi delle automobili usando la regressione lineare. |
| [Esempio 2: Regressione - Previsione dei prezzi delle automobili (avanzata)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Stimare i prezzi delle automobili usando la foresta delle decisioni e i regressori degli alberi delle decisioni con boosting. Confrontare i modelli per trovare l'algoritmo migliore.

## <a name="classification"></a>Classificazione

Esplorare questi esempi di classificazione incorporati. È possibile ottenere altre informazioni sugli esempi senza collegamenti alla documentazione aprendo gli esempi e visualizzando i commenti del modulo.

| Titolo di esempio | Descrizione | 
| --- | --- |
| [Esempio 3: Classificazione binaria con selezione delle funzionalità - Stima del reddito](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Stimare il reddito come alto o basso, usando un albero delle decisioni con boosting a due classi. Usare la correlazione di Pearson per selezionare le funzionalità.
| [Esempio 4: Classificazione binaria con script Python personalizzato - Stima del rischio di credito](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Classificare le applicazioni di credito come ad alto o basso rischio. Usare il modulo di esecuzione dello script Python per ponderare i dati.
| [Esempio 5: Classificazione binaria - Stima delle relazioni con i clienti](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Stimare l'abbandono dei clienti usando alberi delle decisioni con boosting a due classi. Usare SMOTE per campionare i dati distorti.
| [Esempio 7: Classificazione del testo - Set di dati Wikipedia SP 500](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Classificare i tipi di aziende da articoli Wikipedia con regressione logistica multiclasse. |
| Esempio 12: Classificazione multiclasse - Riconoscimento delle lettere | Creare un insieme di classificatori binari per classificare le lettere scritte. |

## <a name="recommender"></a>Moduli di raccomandazione

Esplorare questi esempi di raccomandazione incorporati. È possibile ottenere altre informazioni sugli esempi senza collegamenti alla documentazione aprendo gli esempi e visualizzando i commenti del modulo.

| Titolo di esempio | Descrizione | 
| --- | --- |
| Esempio 10: Raccomandazione - Tweet sulla classificazione dei film | Creare un motore di raccomandazione film da titoli di film e classificazioni. |

## <a name="utility"></a>Utilità

Altre informazioni sugli esempi che illustrano le utilità e le funzionalità di Machine Learning. È possibile ottenere altre informazioni sugli esempi senza collegamenti alla documentazione aprendo gli esempi e visualizzando i commenti del modulo.

| Titolo di esempio | Descrizione | 
| --- | --- |
| [Esempio 6: Usare uno script R personalizzato - Stima dei ritardi dei voli](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Esempio 8: Convalida incrociata per la classificazione binaria - Stima del reddito per adulti | Usare la convalida incrociata per creare un classificatore binario per il reddito degli adulti.
| Esempio 9: Permutation Feature Importance | Usare l'importanza della caratteristica di permutazione per calcolare i punteggi di importanza per il set di dati di test. 
| Esempio 11: Ottimizzare i parametri per la classificazione binaria - Stima del reddito per adulti | Usare gli iperparametri del modello di ottimizzazione per trovare iperparametri ottimali per creare un classificatore binario. |

## <a name="datasets"></a>Set di dati

Quando si crea una nuova pipeline nella finestra di progettazione di Azure Machine Learning, per impostazione predefinita è inclusa una serie di set di dati di esempio. Questi set di dati di esempio vengono usati dalle pipeline di esempio nella home page della finestra di progettazione. 

I set di dati di esempio sono disponibili nella categoria **Set di dati**-**Esempi**. In particolare, è possibile trovarli nella tavolozza dei moduli a sinistra dell'area di disegno della finestra di progettazione. Per usare uno qualsiasi di questi set di dati in una pipeline personalizzata, trascinarlo nell'area di disegno.

| Nome&nbsp;set di dati&nbsp;&nbsp;&nbsp;&nbsp;| Descrizione del set di dati |
|-------------|:--------------------|
| Adult Census Income Binary Classification dataset | Subset del database relativo al censimento del 1994, che usa adulti lavoratori di età superiore ai 16 anni con un indice di reddito adeguato > 100.<br/>**Utilizzo**: classificare le persone usando i dati demografici per prevedere se una persona ha un guadagno superiore a 50.000 dollari all'anno.<br/> **Ricerca correlata**: Kohavi, R., Becker, B., (1996). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science|
|Automobile price data (Raw)|Informazioni sulle automobili in base a marchio e modello, inclusi il prezzo, funzionalità quali il numero di cilindri e il consumo di carburante, oltre a un punteggio relativo al rischio assicurativo.<br/> Il punteggio di rischio viene inizialmente associato al prezzo dell'automobile e quindi adeguato in base al rischio effettivo in un processo noto agli attuari come simbolizzazione. Un valore pari a +3 indica che l'automobile è rischiosa e un valore pari a -3 indica che è probabilmente sicura.<br/>**Utilizzo**:</b> prevedere il punteggio di rischio in base alle funzionalità, usando la regressione o la classificazione multivariata.<br/>**Ricerca correlata**:</b> Schlimmer, J.C. (1987). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science. |
| CRM Appetency Labels Shared |Etichette dalla competizione KDD Cup 2009 di previsione delle relazioni con i clienti ([orange_small_train_appetency.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)).|
|CRM Churn Labels Shared|Etichette dalla competizione KDD Cup 2009 di previsione delle relazioni con i clienti ([orange_small_train_churn.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)).|
|CRM Dataset Shared | Questi dati vengono dalla competizione KDD Cup 2009 di previsione delle relazioni con i clienti ([orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)). <br/>Il set di dati contiene 50.000 clienti della società di telecomunicazioni francese Orange. Ogni cliente dispone di 230 elementi resi anonimi, 190 dei quali numerici e 40 categorici. Gli elementi sono molto sparsi. |
|CRM Upselling Labels Shared|Etichette dalla competizione KDD Cup 2009 di previsione delle relazioni con i clienti ([orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|Flight Delays Data|Dati relativi alle prestazioni nel tempo dei voli passeggeri ottenuti dalla raccolta dati TranStats del Dipartimento dei trasporti degli Stati Uniti ([On-Time](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)).<br/>Il set di dati copre il periodo aprile-ottobre 2013. Prima del caricamento nella finestra di progettazione, il set di dati è stato elaborato come segue: <br/>-    Il set di dati è stato filtrato in modo da coprire solo i 70 aeroporti più trafficati degli Stati Uniti continentali <br/>- I voli cancellati sono stati etichettati in modo da indicare un ritardo superiore a 15 minuti <br/>- I voli deviati sono stati esclusi <br/>- Aggiungere le colonne seguenti nell'ordine: Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled|
|German Credit Card UCI dataset|Set di dati UCI Statlog (German Credit Card) ([Statlog+German+Credit+Data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))), con l'uso del file german.data.<br/>Il set di dati classifica le persone, descritte da un set di attributi, come rischi di credito alti o bassi. Ogni esempio rappresenta una persona. Sono presenti 20 variabili, sia numeriche che relative alle categorie, nonché un'etichetta binaria (il valore del rischio di credito). Le voci che rappresentano un rischio di credito elevato hanno l'etichetta 2, quelle che rappresentano un rischio di credito hanno l'etichetta 1. Classificare erroneamente un cliente come a basso rischio mentre è ad alto rischio implica costi cinque volte più alti.|
|IMDB Movie Titles|Il set di dati contiene informazioni sui film che sono stati valutati nei tweet di Twitter: ID del film nel catalogo IMDB, titolo del film, genere e anno di produzione. Il set di dati contiene 17.000 film. Il set di dati è stato introdotto nel documento di S. Dooms, T. De Pessemier e L. Martens. "MovieTweetings: a Movie Rating Dataset Collected From Twitter. Workshop on Crowdsourcing and Human Computation for Recommender Systems, CrowdRec at RecSys 2013."|
|Classificazioni film|Il set di dati è una versione estesa di quello relativo ai tweet sui film. Il set di dati contiene 170.000 valutazioni di film, estratti da tweet ben strutturati pubblicati su Twitter. Ogni istanza rappresenta un tweet ed è una tupla: ID utente, ID del film nel database IMDB, valutazione, data e ora, numero di preferenze per questo tweet e numero di retweet. Il set di dati è stato messo a disposizione da A. Said, S. Dooms, B. Loni e D. Tikk per Recommender Systems Challenge 2014.|
|Weather Dataset|Le osservazioni meteo sono su base oraria e al suolo e vengono fornite dalla NOAA ([dati uniti dal mese di aprile al mese di ottobre 2013](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>I dati relativi al meteo riguardano le osservazioni effettuate dalle stazioni meteo degli aeroporti nel periodo aprile-ottobre 2013. Prima del caricamento nella finestra di progettazione, il set di dati è stato elaborato come segue:    <br/> -    Gli ID delle stazioni meteo sono stati mappati agli ID degli aeroporti corrispondenti    <br/> -    Le stazioni meteo non associate ai 70 aeroporti più trafficati sono state escluse    <br/> -    La colonna Date è stata suddivisa in colonne Year, Month e Day distinte    <br/> - Aggiungere le colonne seguenti nell'ordine: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter|
|Wikipedia SP 500 Dataset|I dati sono tratti da articoli di Wikipedia (https://www.wikipedia.org/) su ognuna delle società incluse nell'indice S&P 500 e sono archiviati come dati XML.    <br/>Prima del caricamento nella finestra di progettazione, il set di dati è stato elaborato come segue:    <br/> - Estrazione del contenuto di testo per ogni specifica società    <br/> -    Rimozione della formattazione wiki    <br/> -    Rimozione dei caratteri non alfanumerici    <br/> -    Conversione di tutto il testo in minuscolo    <br/> -    Aggiunta delle categorie di società note    <br/>Tenere presente che per alcune società non sono stati trovati articoli, dunque il numero dei record è inferiore a 500.|


## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle nozioni di base sull'analisi predittiva e Machine Learning con l'[Esercitazione: Stimare il prezzo di un'automobile con la finestra di progettazione](tutorial-designer-automobile-price-train-score.md)

