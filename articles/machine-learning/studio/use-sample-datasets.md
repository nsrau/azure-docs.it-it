---
title: Usare i set di dati di esempio in Azure Machine Learning Studio | Microsoft Docs
description: "Descrizione dei set di dati usati nei modelli di esempio inclusi in Machine Learning Studio. È possibile usare questi set di dati di esempio per gli esperimenti."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 03a0b844-e8a7-4896-996f-d3c7a0db7a50
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 55cec0c711836360c173a67d575f042d0fe86bef
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio"></a>Usare i set di dati di esempio in Azure Machine Learning Studio
[top]: #machine-learning-sample-datasets

Quando si crea una nuova area di lavoro in Azure Machine Learning, per impostazione predefinita è inclusa una serie di set di dati e di esperimenti di esempio. Molti di questi set di dati di esempio vengono usati dai modelli di esempio in [Azure Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/). Altri sono inclusi come esempi di diversi tipi di dati usati in genere per l'apprendimento automatico.

Alcuni di questi set di dati sono disponibili nell'archivio BLOB di Azure. La tabella seguente include un collegamento diretto per questi set di dati. È possibile usare questi set di dati negli esperimenti tramite il modulo [Import Data][import-data] (Importazione dati).

I restanti set di dati di esempio sono disponibili nell'area di lavoro in **Saved Datasets** (Set di dati salvati) nella tavolozza del modulo a sinistra dell'area di disegno dell'esperimento quando si apre o si crea un nuovo esperimento in Machine Learning Studio.
Per usare uno qualsiasi di questi set di dati in un esperimento personalizzato, trascinarlo all'area di disegno dell'esperimento.


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

<table>

<tr>
  <th align=left>Nome del set di dati</th>
  <th align=left>Descrizione del set di dati</th>
</tr>

<tr>
  <td valign=top>Adult Census Income Binary Classification dataset</td>
  <td valign=top>
Subset del database relativo al censimento del 1994, che usa adulti lavoratori di età superiore ai 16 anni con un indice di reddito adeguato > 100.<p> </p><b>Utilizzo:</b> classificare le persone usando i dati demografici per prevedere se una persona ha un guadagno superiore a 50.000 dollari all'anno.<p> </p><b>Ricerca correlata:</b> Kohavi, R., Becker, B., (1996). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science  </td>
</tr>

<tr ID=airport-codes-dataset>
  <td valign=top>Airport Codes Dataset</td>
  <td valign=top>
Codici degli aeroporti degli Stati Uniti.<p> </p>Questo set di dati contiene una riga per ogni aeroporto degli Stati Uniti, contenente il nome e il numero ID dell'aeroporto, la città e lo stato.
  </td>
</tr>

<tr>
  <td valign=top>Automobile price data (Raw)</td>
  <td valign=top>
Informazioni sulle automobili in base a marchio e modello, inclusi il prezzo, funzionalità quali il numero di cilindri e il consumo di carburante, oltre a un punteggio relativo al rischio assicurativo.<p> </p>Il punteggio di rischio viene inizialmente associato al prezzo dell'automobile e quindi viene adeguato in base al rischio effettivo in un processo noto agli attuari come simbolizzazione. Un valore pari a +3 indica che l'automobile è rischiosa e un valore pari a -3 indica che è probabilmente sicura.<p> </p><b>Utilizzo:</b> prevedere il punteggio di rischio in base alle funzionalità, usando la regressione o la classificazione multivariata. <p> </p><b>Ricerca correlata:</b> Schlimmer, J.C. (1987). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science  </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top>Bike Rental UCI dataset</td>
  <td valign=top>
Set di dati UCI relativo al noleggio di biciclette basato su dati reali della società Capital Bikeshare che gestisce una rete di noleggio di biciclette a Washington DC.<p> </p>Il set di dati comprende una riga per ogni ora di ogni giorno del 2011 e del 2012, per un totale di 17.379 righe. Il numero di biciclette noleggiate su base oraria è compreso tra 1 e 977.

  </td>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top>Bill Gates RGB Image</td>
  <td valign=top>
File di immagine pubblicamente disponibile convertito in dati in formato CSV.<p> </p>Il codice per la conversione dell'immagine è disponibile nella pagina descrittiva del modello di <strong>quantizzazione dei colori tramite clustering K-Means</strong>.
  </td>
</tr>

<tr>
  <td valign=top>Blood donation data</td>
  <td valign=top>
Sottoinsieme di dati dal database di donatori di sangue del Blood Transfusion Service Center di Hsin-Chu City, Taiwan.<p> </p>I dati relativi al donatore includono i mesi trascorsi dopo l'ultima donazione, la frequenza o il numero totale di donazioni, il tempo trascorso dopo l'ultima donazione e la quantità di sangue donata.<p> </p><b>Utilizzo:</b> l'obiettivo consiste nel prevedere tramite classificazione se il donatore abbia donato sangue nel mese di marzo 2007; 1 indica un donatore nel periodo in esame e 0 un non donatore. <p> </p><b>Ricerca correlata:</b> Yeh, I.C., (2008). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science  <p> </p>Yeh, I-Cheng, Yang, King-Jang, and Ting, Tao-Ming, "Knowledge discovery on RFM model using Bernoulli sequence", Expert Systems with Applications, 2008, <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top>Book Reviews from Amazon</td>
  <td valign=top>
Revisioni di libri in Amazon, estratte dal sito Amazon dai ricercatori della University of Pennsylvania (<a href="http://www.cs.jhu.edu/~mdredze/datasets/sentiment/">sentiment</a>). Vedere il documento di ricerca “Biographies, Bollywood, Boom-boxes and Blenders: Domain Adaptation for Sentiment Classification” di John Blitzer, Mark Dredze e Fernando Pereira; Association of Computational Linguistics (ACL), 2007.<p> </p>Il set di dati originale ha 975.000 revisioni con classificazioni 1, 2, 3, 4 o 5. Le revisioni sono in inglese e vanno dal 1997 al 2007. Il set di dati è stato ridotto a 10.000 revisioni.
  </td>
</tr>

<tr>
  <td valign=top>Breast cancer data</td>
  <td valign=top>
Uno dei tre set di dati relativi al tumore fornito dall'istituto oncologico e usato spesso nella letteratura di Machine Learning. Combina informazioni diagnostiche con funzionalità relative ad analisi di laboratorio effettuate su circa 300 campioni di tessuto.<p> </p><b>Utilizzo:</b> classificare il tipo di tumore, in base a 9 attributi, alcuni dei quali lineari e altri categorici. <p> </p><b>Ricerca correlata:</b> Wohlberg, W.H., Street, W.N., & Mangasarian, O.L. (1995). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science  </td>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Breast Cancer Features <td valign=top>
Il set di dati contiene le informazioni relative a 102.000 aree sospette (candidati) di radiografie, ognuna descritta mediante 117 caratteristiche. Le caratteristiche sono proprietarie e il loro significato non è stato rivelato dagli autori del set di dati (Siemens Healthcare). 
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Breast Cancer Info</td>
  <td valign=top>
Il set di dati contiene informazioni aggiuntive su ogni area sospetta di una radiografia. Per ogni esempio vengono fornite le informazioni, ad esempio etichetta, ID paziente, coordinate della lesione in relazione all'intera immagine, sul numero di riga corrispondente nel set di dati delle caratteristiche del tumore al seno. Per ogni paziente sono disponibili diversi esempi. Per i pazienti in cui è stato riscontrato un tumore, alcuni esempio sono positivi ed altri sono negativi. Per i pazienti sani, tutti gli esempi sono negativi. Il set di dati contiene 102.000 esempi. Al set di dati è stata applicata la compensazione: lo 0,6% dei punti è positivo, mentre il resto è negativo. Il set di dati è stato messo a disposizione da Siemens Healthcare.
  </td>
</tr>

<tr ID=crm-appetency-labels-shared>
  <td valign=top>CRM Appetency Labels Shared</td>
  <td valign=top>
Etichette dalla competizione KDD Cup 2009 di previsione delle relazioni con i clienti (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr ID=crm-churn-labels-shared>
  <td valign=top>CRM Churn Labels Shared</td>
  <td valign=top>
Etichette dalla competizione KDD Cup 2009 di previsione delle relazioni con i clienti (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td valign=top>CRM Dataset Shared</td>
  <td valign=top>
Questi dati vengono dalla competizione KDD Cup 2009 di previsione delle relazioni con i clienti (<a href="http://www.sigkdd.org/kdd-cup-2009-customer-relationship-prediction - orange_small_train.data.zip">orange_small_train.data.zip</a>).<p> </p>Il set di dati contiene 50.000 clienti della società di telecomunicazioni francese Orange. Ogni cliente dispone di 230 elementi resi anonimi, 190 dei quali numerici e 40 categorici. Gli elementi sono molto sparsi.
  </td>
</tr>

<tr ID=crm-upselling-labels-shared>
  <td valign=top>CRM Upselling Labels Shared</td>
  <td valign=top>
Etichette dalla competizione KDD Cup 2009 di previsione delle relazioni con i clienti (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td valign=top>Energy Efficiency Regression data</td>
  <td valign=top>
Raccolta di profili energetici simulati, basati su 12 forme di edifici diverse. Gli edifici si differenziano in base a 8 caratteristiche specifiche, ad esempio il numero di finestre e la distribuzione e l'orientamento delle finestre.<p> </p><b>Utilizzo:</b> usare la regressione o la classificazione per prevedere il livello di efficienza energetica in base a una delle due risposte con valori reali. Per la classificazione a più classi, la variabile di risposta verrà arrotondata al valore Integer più vicino. <p> </p><b>Ricerca correlata:</b> Xifara, A. & Tsanas, A. (2012). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science  </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Flight Delays Data</td>
  <td valign=top>
Dati relativi alle prestazioni nel tempo dei voli passeggeri ottenuti dalla raccolta dati TranStats del Dipartimento dei trasporti degli Stati Uniti (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">On-Time</a>).<p> </p>Il set di dati copre il periodo aprile-ottobre 2013. Prima del caricamento in Azure Machine Learning Studio, il set di dati è stato elaborato come segue:<ul><li>Il set di dati è stato filtrato in modo da coprire solo i 70 aeroporti più trafficati degli Stati Uniti continentali</li><li>I voli cancellati sono stati etichettati in modo da indicare un ritardo superiore a 15 minuti</li><li>I voli deviati sono stati esclusi</li><li>Sono state selezionate le colonne seguenti: Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled</li></ul>
</td>
</tr>

<tr>
  <td valign=top>Flight on-time performance (Raw)</td>
  <td valign=top>
Record degli arrivi e delle partenze dei voli all'interno degli Stati Uniti da ottobre 2011.<p> </p><b>Utilizzo:</b> prevedere i ritardi dei voli. <p> </p><b>Ricerca correlata:</b> dal Ministero dei Trasporti statunitense <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>.
  </td>
</tr>

<tr>
  <td valign=top>Forest fires data</td>
  <td valign=top>
Contiene dati climatici, ad esempio temperatura, indici di umidità e velocità del vento, relativi a un'area nella parte nordorientale del Portogallo, combinati con record relativi agli incendi nei boschi.<p> </p><b>Utilizzo:</b> si tratta di un'attività di regressione complessa, il cui scopo consiste nel prevedere l'area bruciata degli incendi boschivi. <p> </p><b>Ricerca correlata:</b> Cortez, P., & Morais, A. (2008). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science  <p> </p>[Cortez e Morais, 2007] P. Cortez e A. Morais. Approccio di data mining per la previsione degli incendi nei boschi usando i dati meteorologici. In J. Neves, M. F. Santos e J. Machado Eds., New Trends in Artificial Intelligence, Proceedings of the 13th EPIA 2007 - Portuguese Conference on Artificial Intelligence, dicembre, Guimarães, Portugal, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Disponibile all'indirizzo: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top>German Credit Card UCI dataset</td>
  <td valign=top>
Set di dati UCI Statlog (German Credit Card) (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog+German+Credit+Data</a>), con l'uso del file german.data.<p> </p>Il set di dati classifica le persone, descritte da un set di attributi, come rischi di credito alti o bassi. Ogni esempio rappresenta una persona. Sono presenti 20 variabili, sia numeriche che relative alle categorie, nonché un'etichetta binaria (il valore del rischio di credito). Le voci che rappresentano un rischio di credito elevato hanno l'etichetta 2, quelle che rappresentano un rischio di credito hanno l'etichetta 1. Classificare erroneamente un cliente come a basso rischio mentre è ad alto rischio implica costi cinque volte più alti.
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>IMDB Movie Titles</td>
  <td valign=top>
Il set di dati contiene informazioni sui film che sono stati valutati nei tweet di Twitter: ID del film nel database IMDB, nome, genere e anno di produzione del film. Il set di dati contiene 17.000 film. Il set di dati è stato introdotto nel documento di S. Dooms, T. De Pessemier e L. Martens. "MovieTweetings: a Movie Rating Dataset Collected From Twitter. Workshop on Crowdsourcing and Human Computation for Recommender Systems, CrowdRec at RecSys 2013."
  </td>
</tr>

<tr>
  <td valign=top>Iris two class data</td>
  <td valign=top>
Si tratta probabilmente del database più conosciuto disponibile nella letteratura relativa al riconoscimento di schemi. Il set di dati è relativamente piccolo, perché contiene 50 esempi di misurazione di ogni petalo di tre varietà di iris.<p> </p><b>Utilizzo:</b> prevedere il tipo di iris in base alle misurazioni.  <p> </p><b>Ricerca correlata:</b> Fisher, R.A. (1988). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science  </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>Movie Tweets</td>
  <td valign=top>
Il set di dati è una versione estesa di quello relativo ai tweet sui film. Il set di dati contiene 170.000 valutazioni di film, estratti da tweet ben strutturati pubblicati su Twitter. Ogni istanza rappresenta un tweet ed è una tupla: ID utente, ID del film nel database IMDB, valutazione, data e ora, numero di preferenze per questo tweet e numero di retweet. Il set di dati è stato messo a disposizione da A. Said, S. Dooms, B. Loni e D. Tikk per Recommender Systems Challenge 2014.
  </td>
</tr>

<tr>
  <td valign=top>MPG data for various automobiles</td>
  <td valign=top>
Questo set di dati è una versione leggermente modificata del set di dati disponibile nella raccolta StatLib della Carnegie Mellon University. Il set di dati è stato usato presso la American Statistical Association Exposition del 1983.<p> </p>I dati elencano il consumo di carburante per diverse automobili, in miglia per gallone, oltre a informazioni quali numero di cilindri, cilindrata, potenza, peso totale e accelerazione.<p> </p><b>Utilizzo:</b> prevedere il risparmio di carburante in base a 3 attributi discreti multivalore e 5 attributi continui. <p> </p><b>Ricerca correlata:</b> StatLib, Carnegie Mellon University, (1993). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science  </td>
</tr>

<tr>
  <td valign=top>Pima Indians Diabetes Binary Classification dataset</td>
  <td valign=top>
Sottoinsieme di dati del database del National Institute of Diabetes and Digestive and Kidney Diseases. Il set di dati è stato filtrato in modo da evidenziare solo i pazienti di genere femminile di etnia Pima. I dati includono dati medici quali i livelli di glucosio e di insulina, oltre a fattori relativi allo stile di vita.<p> </p><b>Utilizzo:</b> prevedere se il soggetto è diabetico (classificazione binaria). <p> </p><b>Ricerca correlata:</b> Sigillito, V. (1990). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml"</a>. Irvine, CA: University of California, School of Information and Computer Science  </td>
</tr>

<tr>
  <td valign=top>Restaurant customer data</td>
  <td valign=top>
Set di metadati relativi ai clienti, inclusi dati demografici e preferenze.<p> </p><b>Utilizzo:</b> usare questo set di dati, con altri due set di dati relativi ai ristoranti, per il training e il test di un sistema di raccomandazione. <p> </p><b>Ricerca correlata:</b> Bache, K. e Lichman, M. (2013). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td valign=top>Restaurant feature data</td>
  <td valign=top>
Set di metadati relativi ai ristoranti e alle rispettive caratteristiche, ad esempio tipo di cibo, stile del ristorante e ubicazione.<p> </p><b>Utilizzo:</b> usare questo set di dati, con altri due set di dati relativi ai ristoranti, per il training e il test di un sistema di raccomandazione. <p> </p><b>Ricerca correlata:</b> Bache, K. e Lichman, M. (2013). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td valign=top>Restaurant ratings</td>
  <td valign=top>
Include le valutazioni assegnate dagli utenti ai ristoranti in una scala da 0 a 2.<p> </p><b>Utilizzo:</b> usare questo set di dati, con altri due set di dati relativi ai ristoranti, per il training e il test di un sistema di raccomandazione. <p> </p><b>Ricerca correlata:</b> Bache, K. e Lichman, M. (2013). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td valign=top>Steel Annealing multi-class dataset</td>
  <td valign=top>
Questo set di dati include una serie di record relativi a tentativi di ricottura di acciaio, con gli attributi fisici (larghezza, spessore e tipo, ad esempio spirale, lamina e così via) dei tipi di acciaio risultanti.<p> </p><b>Utilizzo:</b> prevedere uno dei due attributi numerici della classe, ovvero durezza o forza. È anche possibile analizzare le correlazioni tra gli attributi.<p> </p>Le designazioni dell'acciaio sono basate su uno standard definito da SAE e da altre organizzazioni. Si cerca una 'designazione' specifica (variabile della classe) e si vogliono comprendere i valori necessari. <p> </p><b>Ricerca correlata:</b> Sterling, D. & Buntine, W. (NA). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science  <p> </p>Una guida utile alle designazioni dell'acciaio è disponibile qui: <a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td valign=top>Telescope data</td>
  <td valign=top>
Record di esplosioni di particelle gamma a energia elevata insieme alla radiazione di fondo, simulate entrambe tramite un processo Monte Carlo.<p> </p>Lo scopo della simulazione consiste nel migliorare la precisione dei telescopi gamma Cherenkov posizionati a terra, usando metodi statistici per rilevare la differenza tra il segnale desiderato (pioggia di radiazioni Cherenkov) e la radiazione di fondo (piogge adroniche generate da raggi cosmici nella parte superiore dell'atmosfera).<p> </p>I dati sono stati pre-elaborati in modo da creare un cluster allungato il cui asse longitudinale è orientato verso il centro della fotocamera. Le caratteristiche di questa ellissi, spesso definite parametri Hillas, si trovano tra i parametri dell'immagine che possono essere usati per la discriminazione.<p> </p><b>Utilizzo:</b> prevedere se l'immagine di una pioggia rappresenta un segnale o radiazioni di fondo.<p> </p><b>Note:</b> la semplice precisione della classificazione non è significativa per questi dati, poiché la classificazione di un evento di fondo come segnale è ritenuta peggiore della classificazione di un evento di segnale come evento di fondo. Per un confronto dei diversi classificatori, è consigliabile usare il grafico ROC. La probabilità di accettazione di un evento di fondo come un segnale deve essere inferiore a una delle soglie seguenti: 0,01 , 0,02 , 0,05 , 0,1 oppure 0,2.<p> </p>Si noti anche che il numero di eventi di fondo (h per piogge adroniche) è sottostimato, mentre nelle misurazioni reali la classe h o noise rappresenta la maggior parte degli eventi. <p> </p><b>Ricerca correlata:</b> Bock, R.K. (1995). UCI Machine Learning Repository <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information </td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Weather Dataset</td>
  <td valign=top>
Le osservazioni meteo sono su base oraria e al suolo e vengono fornite dalla NOAA (<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">dati uniti dal mese di aprile al mese di ottobre 2013</a>).<p> </p>I dati relativi al meteo riguardano le osservazioni effettuate dalle stazioni meteo degli aeroporti nel periodo aprile-ottobre 2013. Prima del caricamento in Azure Machine Learning Studio, il set di dati è stato elaborato come segue:<ul><li>Gli ID delle stazioni meteo sono stati mappati agli ID degli aeroporti corrispondenti</li><li>Le stazioni meteo non associate ai 70 aeroporti più trafficati sono state escluse</li><li>La colonna Date è stata suddivisa in colonne Year, Month e Day distinte</li><li>Sono state selezionate le seguenti colonne: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li></ul>
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top>Wikipedia SP 500 Dataset</td>
  <td valign=top>
I dati sono tratti da articoli di Wikipedia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>)su ognuna delle società incluse nell'indice S&P 500, archiviati come dati XML.<p> </p>Prima del caricamento in Azure Machine Learning Studio, il set di dati è stato elaborato come segue:<ul><li>Estrazione del contenuto di testo per ogni specifica società</li><li>Rimozione della formattazione wiki</li><li>Rimozione dei caratteri non alfanumerici</li><li>Conversione di tutto il testo in minuscolo</li><li>Aggiunta delle categorie di società note</li></ul><p> </p>Tenere presente che per alcune società non sono stati trovati articoli, dunque il numero dei record è inferiore a 500.
  </td>
</tr>





<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>
Il set di dati contiene i dati dei clienti e le indicazioni sulle risposte ottenute in seguito a una campagna di mailing diretto. Ogni riga rappresenta un cliente. Il set di dati contiene 9 caratteristiche sui dati personali degli utenti e sui comportamenti passati, oltre a tre 3 colonne con etichetta (visita, conversione e spesa).  La visita è una colonna binaria usata per indicare che un cliente è stato visitato dopo la campagna di marketing, la conversione indica che il cliente ha effettuato un acquisto e la spesa corrisponde all'importo speso.  Il set di dati è stato messo a disposizione da Kevin Hillstrom per MineThatData E-Mail Analytics And Data Mining Challenge.
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>
Caratteristiche degli esempi di test nel set di dati relativi alle notizie RCV1-V2 Reuters. Il set di dati contiene 781.000 articoli, a ognuno dei quali è associato un ID (prima colonna del set di dati). Ogni articolo è stato analizzato per identificare token, parole non significative e sottoposto a stemming. Il set di dati è stato messo a disposizione da David. D. Lewis.
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top>
Funzionalità degli esempi di training nel set di dati relativi alle notizie RCV1-V2 Reuters. Il set di dati contiene 23.000 articoli, a ognuno dei quali è associato un ID (prima colonna del set di dati). Ogni articolo è stato analizzato per identificare token, parole non significative e sottoposto a stemming. Il set di dati è stato messo a disposizione da David. D. Lewis.
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
Set di dati dalla KDD Cup 1999 Knowledge Discovery and Data Mining Tools Competition (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).<p> </p>Questo set di dati è stato scaricato e memorizzato nell'archiviazione BLOB di Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) e include set di dati sia di training che di test. Il set di dati di training contiene circa 126K righe e 43 colonne, comprese le etichette. Tre colonne fanno parte delle informazioni sulle etichette e 40 colonne, composte da funzioni numeriche, stringa o categoriali, sono disponibili per il training del modello. I dati di test contengono circa 225.000 esempi di test con le stesse 43 colonne nei dati di training.

  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td valign=top>
Assegnazioni degli argomenti per gli articoli del set di dati relativo alle notizie RCV1-V2 Reuters. Un articolo può essere assegnato a più argomenti. Il formato di ogni riga è "&lt;nome argomento&gt; &lt;ID documento&gt; 1". Il set di dati contiene 2,6 milioni di assegnazioni di argomenti. Il set di dati è stato messo a disposizione da David. D. Lewis.
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
Questi dati provengono dalla competizione KDD Cup 2010 Student performance evaluation (<a href="http://www.kdd.org/kdd-cup-2010-student-performance-evaluation">student performance evaluation</a>). Il set di dati usato è il training set Algebra_2008_2009 (Stamper, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, K.R. (2010). Algebra I 2008-2009. Set di dati di competizione dalla KDD Cup 2010 dedicata al data mining in ambito didattico. Il training è disponibile in <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a> o <a href="http://www.kdd.org/sites/default/files/kddcup/site/2010/files/algebra_2008_2009.zip">algebra_2008_2009.zip</a>.<p> </p>Il set di dati è stato scaricato e memorizzato Archiviazione BLOB di Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) e contiene i file di log provenienti dal sistema relativo alle lezioni private per gli studenti. Le funzionalità fornite includono: ID del problema e breve descrizione, ID dello studente, timestamp e numero di tentativi effettuati dallo studente prima di risolvere il problema nel modo corretto. Il set di dati originale contiene 8,9 milioni di record e questo set di dati è stato ridotto alle prime 100.000 righe. Nel set di dati sono presenti 23 colonne separate da tabulazioni, di vari tipi: numerico, categorico e timestamp.

  </td>
</tr>




</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

