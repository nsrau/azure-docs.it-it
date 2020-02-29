---
title: Usare i set di dati di esempio
titleSuffix: Azure Machine Learning
description: Descrizioni dei set di impostazioni utilizzati nei modelli di esempio inclusi in Machine Learning Designer. È possibile usare questi set di impostazioni di esempio per le pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 14e514f0025d474b3afb45524753583b7c2e8a7d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165062"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>Usare i set di impostazioni di esempio in Azure Machine Learning Designer (anteprima)

Quando si crea una nuova pipeline in Azure Machine Learning Designer (anteprima), per impostazione predefinita vengono inclusi alcuni set di impostazioni di esempio. Molti di questi set di impostazioni di esempio vengono usati dai modelli di esempio nella Home page della finestra di progettazione. Altri sono inclusi come esempi di diversi tipi di dati usati in genere per l'apprendimento automatico.

Alcuni di questi set di dati sono disponibili nell'archivio BLOB di Azure. La tabella seguente include un collegamento diretto per questi set di dati. È possibile usare questi set di dati nelle pipeline usando il modulo [Import Data](./algorithm-module-reference/import-data.md) .

Il resto di questi set di impostazioni di esempio è disponibile nella categoria **set di impostazioni**-**esempi** . È possibile trovarlo nella tavolozza dei moduli a sinistra dell'area di disegno nella finestra di progettazione. È possibile usare uno di questi set di impostazioni nella propria pipeline trascinandoli nell'area di disegno.

## <a name="datasets"></a>Set di dati

<table>

<tr>
  <th>Nome del set di dati</th>
  <th>Descrizione del set di dati</th>
</tr>

<tr>
  <td>Adult Census Income Binary Classification dataset</td>
  <td>
Subset del database relativo al censimento del 1994, che usa adulti lavoratori di età superiore ai 16 anni con un indice di reddito adeguato > 100.
<p></p>
<b>Utilizzo:</b> classificare le persone usando i dati demografici per prevedere se una persona ha un guadagno superiore a 50.000 dollari all'anno.
<p></p>
<b>Ricerca correlata:</b> Kohavi, R., Becker, B., (1996). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Automobile price data (Raw)</td>
  <td>
Informazioni sulle automobili in base a marchio e modello, inclusi il prezzo, funzionalità quali il numero di cilindri e il consumo di carburante, oltre a un punteggio relativo al rischio assicurativo.
<p></p>
Il punteggio di rischio viene inizialmente associato al prezzo dell'automobile e quindi adeguato in base al rischio effettivo in un processo noto agli attuari come simbolizzazione. Un valore pari a +3 indica che l'automobile è rischiosa e un valore pari a -3 indica che è probabilmente sicura.
<p></p>
<b>Utilizzo:</b> prevedere il punteggio di rischio in base alle funzionalità, usando la regressione o la classificazione multivariata. 
<p></p>
<b>Ricerca correlata:</b> Schlimmer, J.C. (1987). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>


<tr>
  <td>CRM Appetency Labels Shared</td>
  <td>
Etichette dalla competizione KDD Cup 2009 di previsione delle relazioni con i clienti (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>CRM Churn Labels Shared</td>
  <td>
Etichette dalla competizione KDD Cup 2009 di previsione delle relazioni con i clienti (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>CRM Dataset Shared</td>
  <td>
Questi dati vengono dalla competizione KDD Cup 2009 di previsione delle relazioni con i clienti (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
Il set di dati contiene 50.000 clienti della società di telecomunicazioni francese Orange. Ogni cliente dispone di 230 elementi resi anonimi, 190 dei quali numerici e 40 categorici. Gli elementi sono molto sparsi.
  </td>
</tr>

<tr>
  <td>CRM Upselling Labels Shared</td>
  <td>
Etichette dalla competizione KDD Cup 2009 di previsione delle relazioni con i clienti (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Flight Delays Data</td>
  <td>
Dati relativi alle prestazioni in fase di volo passeggero ricavati dalla raccolta di dati TranStats del Ministero del trasporto statunitense (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">on-time</a>).
<p></p>
Il set di dati copre il periodo aprile-ottobre 2013. Prima di caricare nella finestra di progettazione, il set di dati è stato elaborato come segue:
<ul>
  <li>Il set di dati è stato filtrato in modo da coprire solo i 70 aeroporti più trafficati degli Stati Uniti continentali</li>
  <li>I voli cancellati sono stati etichettati in modo da indicare un ritardo superiore a 15 minuti</li>
  <li>I voli deviati sono stati esclusi</li>
  <li>Sono state selezionate le colonne seguenti: Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled</li>
</ul>
</td>
</tr>

<tr>
  <td>German Credit Card UCI dataset</td>
  <td>
Set di dati UCI Statlog (German Credit Card) (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog+German+Credit+Data</a>), con l'uso del file german.data.
<p></p>
Il set di dati classifica le persone, descritte da un set di attributi, come rischi di credito alti o bassi. Ogni esempio rappresenta una persona. Sono presenti 20 variabili, sia numeriche che relative alle categorie, nonché un'etichetta binaria (il valore del rischio di credito). Le voci che rappresentano un rischio di credito elevato hanno l'etichetta 2, quelle che rappresentano un rischio di credito hanno l'etichetta 1. Classificare erroneamente un cliente come a basso rischio mentre è ad alto rischio implica costi cinque volte più alti.
  </td>
</tr>

<tr>
  <td>IMDB Movie Titles</td>
  <td>
Il set di dati contiene informazioni sui film che sono stati valutati nei tweet di Twitter: ID del film nel database IMDB, nome, genere e anno di produzione del film. Il set di dati contiene 17.000 film. Il set di dati è stato introdotto nel documento di S. Dooms, T. De Pessemier e L. Martens. "MovieTweetings: a Movie Rating Dataset Collected From Twitter. Workshop on Crowdsourcing and Human Computation for Recommender Systems, CrowdRec at RecSys 2013."
  </td>
</tr>

<tr>
  <td>Classificazioni film</td>
  <td>
Il set di dati è una versione estesa di quello relativo ai tweet sui film. Il set di dati contiene 170.000 valutazioni di film, estratti da tweet ben strutturati pubblicati su Twitter. Ogni istanza rappresenta un tweet ed è una tupla: ID utente, ID film IMDB, classificazione, timestamp, numero di preferiti per questo tweet e numero di Retweet del tweet. Il set di dati è stato messo a disposizione da A. Said, S. Dooms, B. Loni e D. Tikk per Recommender Systems Challenge 2014.
  </td>
</tr>


<tr>
  <td>Weather Dataset</td>
  <td>
Le osservazioni meteo sono su base oraria e al suolo e vengono fornite dalla NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">dati uniti dal mese di aprile al mese di ottobre 2013</a>).
<p></p>
I dati relativi al meteo riguardano le osservazioni effettuate dalle stazioni meteo degli aeroporti nel periodo aprile-ottobre 2013. Prima di caricare nella finestra di progettazione, il set di dati è stato elaborato come segue:
<ul>
  <li>Gli ID delle stazioni meteo sono stati mappati agli ID degli aeroporti corrispondenti</li>
  <li>Le stazioni meteo non associate ai 70 aeroporti più trafficati sono state escluse</li>
  <li>La colonna Date è stata suddivisa in colonne Year, Month e Day distinte</li>
  <li>Sono state selezionate le seguenti colonne: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 Dataset</td>
  <td>
I dati sono tratti da articoli di Wikipedia (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>) su ognuna delle società incluse nell'indice S&P 500 e sono archiviati come dati XML.
<p></p>
Prima di caricare nella finestra di progettazione, il set di dati è stato elaborato come segue:
<ul>
  <li>Estrazione del contenuto di testo per ogni specifica società</li>
  <li>Rimozione della formattazione wiki</li>
  <li>Rimozione dei caratteri non alfanumerici</li>
  <li>Conversione di tutto il testo in minuscolo</li>
  <li>Aggiunta delle categorie di società note</li>
</ul>
<p></p>
Tenere presente che per alcune società non sono stati trovati articoli, dunque il numero dei record è inferiore a 500.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Passaggi successivi

* Scopri le nozioni di base sull'analisi predittiva e l'apprendimento automatico con [l'esercitazione: stima del prezzo dell'automobile con la finestra di progettazione](tutorial-designer-automobile-price-train-score.md)

* Usare il modulo [Import Data](./algorithm-module-reference/import-data.md) per importare set di dati di esempio
