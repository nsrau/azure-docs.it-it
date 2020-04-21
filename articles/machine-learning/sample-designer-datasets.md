---
title: Dati di esempio per la finestra di progettazione
titleSuffix: Azure Machine Learning
description: Altre informazioni sui set di dati di esempio disponibili nella finestra di progettazione di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: b19c1ed5aa556a2cfb633ac6fbc0cf9f95d0337f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256978"
---
# <a name="sample-datasets-in-azure-machine-learning-designer-preview"></a>Set di dati di esempio nella finestra di progettazione di Azure Machine Learning (anteprima)

Quando si crea una nuova pipeline nella finestra di progettazione di Azure Machine Learning (anteprima), per impostazione predefinita è inclusa una serie di set di dati di esempio. Questi set di dati di esempio vengono usati dalle pipeline di esempio nella home page della finestra di progettazione. 

I set di dati di esempio sono disponibili nella categoria **Set di dati**-**Esempi**. In particolare, è possibile trovarli nella tavolozza dei moduli a sinistra dell'area di disegno della finestra di progettazione. Per usare uno qualsiasi di questi set di dati in una pipeline personalizzata, trascinarlo nell'area di disegno.

## <a name="datasets"></a>Set di dati


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

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle nozioni di base sull'analisi predittiva e Machine Learning con l'[Esercitazione: Stimare il prezzo di un'automobile con la finestra di progettazione](tutorial-designer-automobile-price-train-score.md)

* Informazioni su come modificare gli [esempi della finestra di progettazione](samples-designer.md) esistenti per adattarli alle proprie esigenze.
