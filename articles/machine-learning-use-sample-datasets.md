<properties 
	pageTitle="Usare i set di dati di esempio in Machine Learning Studio | Azure" 
	description="Descrizioni dei set di dati di esempio inclusi in Azure Machine Learning Studio." 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/16/2014" 
	ms.author="garye"/>


# Usare i set di dati di esempio in Azure Machine Learning Studio

ML Studio include alcuni set di dati di esempio disponibili per l'uso. Si tratta di set di dati standard relativi al Machine Learning provenienti dal dominio pubblico. 

È possibile usarli nelle sperimentazioni per esplorare diversi tipi di modelli di Machine Learning analitico in ML Studio. 

- Per visualizzare un elenco di questi set di dati standard in ML Studio, fare clic sulla scheda **SET DI DATI**. Per ogni set di dati è possibile visualizzare informazioni quali il nome del set di dati, l'autore dell'invio e una breve descrizione.
 
    Per ordinare i set di dati, fare clic su un'intestazione di colonna. Ad esempio, fare clic su **INVIATO DA** per raggruppare tutti i set di dati di esempio forniti da Microsoft Corporation. Questo è anche un modo semplice per visualizzare i set di dati importati dall'utente specifico e da altri utenti nell'area di lavoro. 

- Per usare un set di dati in una sperimentazione, espandere la sezione **Set di dati salvati** del riquadro dei moduli a sinistra dell'area di disegno della sperimentazione oppure cercare un set di dati specifico digitandone il nome nella casella di ricerca sopra il riquadro. Trascinare il set di dati nell'area di disegno per aggiungerlo alla sperimentazione. 

##Set di dati di esempio

La tabella seguente fornisce informazioni aggiuntive sui set di dati che potrebbero risultare utili durante l'esplorazione delle funzionalità di Machine Learning disponibili in ML Studio. Per ogni set di dati, nella tabella sono elencati gli elementi seguenti:
 
- Nome e origine di ogni set di dati.
- Descrizione dei possibili usi del set di dati e indicazioni relative alle ricerche di Machine Learning che hanno usato il set di dati.
- Riepilogo delle colonne importanti incluse nel set di dati e di altri attributi utili.

Alcune descrizioni sono adattate dalla documentazione fornita dall'origine (in genere il [repository di Machine Learning di UC Irvine](http://archive.ics.uci.edu/ml/ "UC Irvine Machine Learning Repository")), mentre altre descrizioni sono basate su investigazione aggiuntiva o riflettono modifiche apportate agli esempi.

<table>
<tr valign=top>
<th>Nome del set di dati</th>
<th>Descrizione del set di dati</th>
<th>Ricerche correlate</th>
</tr>
<tr valign=top>
<td>Set di dati relativo alla classificazione binaria del censimento del reddito di persone adulte</td>
<td>
<p>Sottoinsieme del database relativo al censimento del 1994, che usa adulti lavoratori di età superiore ai 16 anni con un indice di reddito adeguato > 100.</p>
<p><strong>Utilizzo:</strong> classificare le persone usando dati demografici per prevedere se una persona ha un guadagno superiore a 50.000 all'anno.</p>
</td>
<td>
<p>Kohavi, R., Becker, B., (1996). Repository di Machine Learning UCI [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Dati relativi ai prezzi delle automobili (non elaborati)</p>
</td>
<td>
<p>Informazioni sulle automobili in base a marchio e modello, inclusi il prezzo, funzionalità quali il numero di cilindri e il consumo di carburante, oltre a un punteggio relativo al rischio assicurativo.</p>
<p>Il punteggio di rischio viene inizialmente associato al prezzo dell'automobile e quindi viene adeguato in base al rischio effettivo in un processo noto agli attuari come <legacyItalic>simbolizzazione</legacyItalic>. Un valore pari a +3 indica che l'automobile è rischiosa e un valore pari a -3 indica che è probabilmente abbastanza sicura.</p>
<p><strong>Utilizzo:</strong> prevedere il punteggio di rischio in base alle funzionalità, usando la regressione o la classificazione multivariata.</p>
</td>
<td>
<p>
Schlimmer, J.C. (1987). Repository di Machine Learning UCI [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Dati relativi alla donazione di sangue</p>
</td>
<td>
<p>Sottoinsieme di dati dal database di donatori di sangue del Blood Transfusion Service Center di Hsin-Chu City, Taiwan.</p>
  <p>I dati relativi al donatore includono i mesi trascorsi dopo l'ultima donazione, la frequenza o il numero totale di donazioni, il tempo trascorso dopo l'ultima donazione e la quantità di sangue donata.</p>
  <p><strong>Utilizzo:</strong> l'obiettivo consiste nel prevedere tramite classificazione se il donatore ha donato sangue nel mese di marzo 2007, dove 1 indica un donatore durante il periodo specifico e 0 un non donatore.</p>
</td>
<td>
<list class="bullet">
<listItem>
<p>
Yeh, I.C., (2008). Repository di Machine Learning UCI [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</listItem>
<listItem>
<p>
Yeh, I-Cheng, Yang, King-Jang, and Ting, Tao-Ming, "Knowledge discovery on RFM model using Bernoulli sequence, "Expert Systems with Applications, 2008, [<a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>]
</p>
</listItem>
</list>
</td>
</tr>

<tr valign=top>
<td>
<p>Dati relativi al tumore al seno</p>
</td>
<td>
<p>Uno dei tre set di dati relativi al tumore fornito dall'istituto oncologico e usato spesso nella letteratura di Machine Learning. Combina informazioni diagnostiche con funzionalità relative ad analisi di laboratorio effettuate su circa 300 campioni di tessuto.</p>
<p><strong>Utilizzo:</strong> classificare il tipo di tumore, in base a 9 attributi, alcuni dei quali sono lineari e altri categorici.</p>
</td>
<td>
<p>
Wohlberg, W.H., Street, W.N. e Mangasarian, O.L. (1995). Repository di Machine Learning UCI [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Dati relativi alla regressione dell'efficienza energetica</p>
</td>
<td>
<p>Raccolta di profili energetici simulati, basati su 12 forme di edifici diverse. Gli edifici si differenziano in base a 8 caratteristiche specifiche, ad esempio il numero di finestre e la distribuzione e l'orientamento delle finestre.</p>
<p><strong>Utilizzo:</strong> usare la regressione o la valutazione per prevedere il livello di efficienza energetica in base a una delle due risposte con valori reali. Per la classificazione a più classi, la variabile di risposta verrà arrotondata al valore Integer più vicino.</p>
</td>
<td>
<p>
Xifara, A. e Tsanas, A. (2012). Repository di Machine Learning UCI  [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Prestazioni relative alla puntualità dei voli (non elaborati)</p>
</td>
<td>
<p>Record degli arrivi e delle partenze dei voli all'interno degli Stati Uniti da ottobre 2011.</p>
<p><strong>Utilizzo:</strong> prevedere i ritardi dei voli.</p>
</td>
<td>
<p>
Dal dipartimento dei trasporti degli Stati Uniti [<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time</a>]
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Dati relativi agli incendi nei boschi</p>
</td>
<td>
<p>Contiene dati climatici, ad esempio temperatura, indici di umidità e velocità del vento, relativi a un'area nella parte nordorientale del Portogallo, combinati con record relativi agli incendi nei boschi.</p>
<p><strong>Utilizzo:</strong> si tratta di un'attività di regressione complessa, il cui scopo consiste nel prevedere l'area bruciata degli incendi nei boschi.</p>
</td>
<td>
<list class="bullet">
<listItem>
<p>
Cortez, P. e Morais, A. (2008). Repository di Machine Learning UCI [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</listItem>
<listItem>
<p>
[Cortez e Morais, 2007] P. Cortez e A. Morais. 
Approccio di data mining per la previsione degli incendi nei boschi usando i dati meteorologici. 
In J. Neves, M. F. Santos e J. Machado Eds., New Trends in Artificial Intelligence, 
Tratto dalla 13° EPIA 2007 - Portuguese Conference on Artificial Intelligence, 
Dicembre, Guimar&#227;es, Portugal, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Disponibile presso:
[<a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>]
</p>
</listItem>
</list>
</td>
</tr>

<tr valign=top>
<td>
<p>Dati relativi a due classi di iris</p>
</td>
<td>
<p>Si tratta probabilmente del database più conosciuto disponibile nella letteratura relativa al riconoscimento di schemi. Il set di dati è relativamente piccolo, poiché contiene 50 esempi di misurazione di ogni petalo di tre varietà di iris.</p>
<p><strong>Utilizzo:</strong> prevedere il tipo di iris in base alle misurazioni. 
<!-- I believe the following doesn't apply anymore, but I'm not sure so I'll remove it for now.
One class is linearly separable from the other two; but the latter are not linearly separable from each other.
-->
</p>
</td>
<td>
<p>
Fisher, R.A. (1988). Repository di Machine Learning UCI  [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Dati relativi al consumo di carburante per diverse automobili</p>
</td>
<td>
<p>Questo set di dati è una versione leggermente modificata del set di dati disponibile nella raccolta StatLib della Carnegie Mellon University. Il set di dati è stato usato presso la American Statistical Association Exposition del 1983.</p>
<p>I dati elencano il consumo di carburante per diverse automobili, in miglia per gallone, oltre a informazioni quali numero di cilindri, cilindrata, potenza, peso totale e accelerazione.</p>
<p><strong>Utilizzo:</strong> prevedere il risparmio di carburante in base a 3 attributi discreti a più valori e 5 attributi continui.</p>
</td>
<td>
<p>
StatLib, Carnegie Mellon University, (1993). Repository di Machine Learning UCI [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Set di dati relativo alla classificazione binaria dei casi di diabete tra i nativi americani Pima</p>
</td>
<td>
<p>Sottoinsieme di dati del database del National Institute of Diabetes and Digestive and Kidney Diseases. Il set di dati è stato filtrato in modo da evidenziare solo i pazienti di genere femminile di etnia Pima. I dati includono dati medici quali i livelli di glucosio e di insulina, oltre a fattori relativi allo stile di vita.</p>
<p><strong>Utilizzo:</strong> prevedere se il soggetto è diabetico (classificazione binaria).</p>
</td>
<td>
<p>
Sigillito, V. (1990). Repository di Machine Learning UCI [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Dati relativi ai clienti dei ristoranti</p>
</td>
<td>
<p>Set di metadati relativi ai clienti, inclusi dati demografici e preferenze.</p>
<p><strong>Utilizzo:</strong> usare questo set di dati, insieme ad altri due set di dati relativi ai ristoranti, per il training e il test di un sistema di suggerimenti.</p>
</td>
<td>
<p>
Bache, K. and Lichman, M. (2013). Repository di Machine Learning UCI 
[<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>].
Irvine, CA: University of California, School of Information and Computer Science.
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Dati relativi alle caratteristiche dei ristoranti</p>
</td>
<td>
<p>Set di metadati relativi ai ristoranti e alle rispettive caratteristiche, ad esempio tipo di cibo, stile del ristorante e ubicazione.</p>
<p><strong>Utilizzo:</strong> usare questo set di dati, insieme ad altri due set di dati relativi ai ristoranti, per il training e il test di un sistema di suggerimenti.</p>
</td>
<td>
<p>
Bache, K. and Lichman, M. (2013). Repository di Machine Learning UCI 
[<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>].
Irvine, CA: University of California, School of Information and Computer Science.
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Valutazioni dei ristoranti</p>
</td>
<td>
<p>Include le valutazioni assegnate dagli utenti ai ristoranti in una scala da 0 a 2.</p>
<p><strong>Utilizzo:</strong> usare questo set di dati, insieme ad altri due set di dati relativi ai ristoranti, per il training e il test di un sistema di suggerimenti.</p>
</td>
<td>
<p>
Bache, K. and Lichman, M. (2013). Repository di Machine Learning UCI 
[<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>].
Irvine, CA: University of California, School of Information and Computer Science.
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Set di dati a più classi relativo alla ricottura dell'acciaio</p>
</td>
<td>
<p>Questo set di dati include una serie di record relativi a tentativi di ricottura di acciaio, con gli attributi fisici (larghezza, spessore e tipo, ad esempio spirale, lamina e così via) dei tipi di acciaio risultanti.</p>
<p><strong>Utilizzo:</strong> prevedere uno dei due attributi numerici della classe, ovvero durezza o forza. È anche possibile analizzare le correlazioni tra gli attributi.</p>
<p>Le designazioni dell'acciaio sono basate su uno standard definito da SAE e da altre organizzazioni. Si cerca una 'designazione' specifica (variabile della classe) e si vogliono comprendere i valori necessari.</p>
</td>
<td>
<p>
Sterling, D. e Buntine, W., (NA). Repository di Machine Learning UCI  [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
<p>Guida utile alle designazioni dell'acciaio:</p>
<p>[<a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf>]
  </p>
</td>
</tr>

<tr valign=top>
<td>
<p>Dati relativi ai telescopi</p>
</td>
<td>
<p>Record di esplosioni di particelle gamma a energia elevata insieme alla radiazione di fondo, simulate entrambe tramite un processo Monte Carlo.</p>
<p>Lo scopo della simulazione consiste nel migliorare la precisione dei telescopi gamma Cherenkov posizionati a terra, usando metodi statistici per rilevare la differenza tra il segnale desiderato (pioggia di radiazioni Cherenkov) e la radiazione di fondo (piogge adroniche generate da raggi cosmici nella parte superiore dell'atmosfera).</p>
<p>I dati sono stati pre-elaborati in modo da creare un cluster allungato il cui asse longitudinale è orientato verso il centro della fotocamera. Le caratteristiche di questa ellissi, spesso definite parametri Hillas, si trovano tra i parametri dell'immagine che possono essere usati per la discriminazione.</p>
<p><strong>Utilizzo:</strong> prevedere se l'immagine di una pioggia rappresenta un segnale o radiazioni di fondo.</p>
<p><strong>Note:</strong> la semplice precisione della classificazione non è significativa per questi dati, poiché la classificazione di un evento di fondo come segnale è ritenuta più grave della classificazione di un evento di segnale come evento di fondo. Per un confronto dei diversi classificatori, è consigliabile usare il grafico ROC. La probabilità di accettazione di un evento di fondo come un segnale deve essere inferiore a una delle soglie seguenti: 0,01 , 0,02 , 0,05 , 0,1 oppure 0,2.</p>
<p>Si noti anche che il numero di eventi di fondo (h per piogge adroniche) è sottostimato, mentre nelle misurazioni reali la classe h o noise rappresenta la maggior parte degli eventi.</p>
</td>
<td>
<p>
Bock, R.K. (1995). Repository di Machine Learning UCI [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</td>
</tr>

</table>

Di seguito sono elencati i tipi di set di dati che è possibile incontrare.

<table>
<tr>
<th>Formato dati</th>
<th>Descrizione</th>
</tr>
<tr>
<td>
Valori separati da virgole (CSV, Comma Separated Values)
</td>
<td>
Formato di interscambio noto per tutte le piattaforme di dati. ML Studio carica questi dati e incorpora un'utilità di determinazione del tipo a livello di colonna che applica i metadati in base ai dati campionati da ogni colonna.
</td>
</tr>
<tr>
<td>
Formato ARFF (Attribute Relationship File Format)
</td>
<td>
Formato di dati di Machine Learning definito da WEKA. Contiene metadati (per tipi nominali, numerici e stringa) che possono essere convertiti direttamente in tabelle dati di ML Studio.
</td>
</tr>
<tr>
<td>
Testo normale
</td>
<td>
Il testo normale può essere letto e quindi suddiviso in colonne tramite moduli di pre-elaborazione downstream. Il principale formato di testo normale supportato è JSON.
</td>
</tr>
<tr>
<td>
DotNetTable
</td>
<td>
Versione serializzata del contenitore primario passato tra moduli in ML Studio (il <i>set di dati</i>).
</td>
</tr>
</table>

<!--HONumber=46--> 

<!--HONumber=46--> 
