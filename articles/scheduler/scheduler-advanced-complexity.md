<properties 
 pageTitle="Come creare pianificazioni complesse e operazioni ricorrenti avanzate con l'Utilità di pianificazione di Azure" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="12/04/2015" 
 ms.author="krisragh"/>

# Come creare pianificazioni complesse e operazioni ricorrenti avanzate con l'Utilità di pianificazione di Azure  

## Panoramica

Alla base dell’Utilità di pianificazione di Azure c’è la *pianificazione*. La pianificazione determina come e quando l'Utilità di Pianificazione esegue il processo.

L’Utilità di pianificazione di Azure consente di specificare pianificazioni varie, sia monouso che ricorrenti, per un processo. Le pianificazioni *monouso* vengono eseguite una volta sola all’ora specificata – a tutti gli effetti, sono pianificazioni *ricorrenti* eseguite una sola volta. Le pianificazioni ricorrenti vengono eseguite con una frequenza predeterminata.

Con questa flessibilità, l’Utilità di pianificazione di Azure consente di supportare un'ampia gamma di scenari aziendali:

-	Pulizia periodica dei dati – ad esempio, eliminare tutti i post su Twitter più vecchi di 3 mesi
-	Archiviazione – ad esempio, ogni mese, passaggio dello storico fatture al servizio di backup
-	Richieste di dati esterni – ad esempio, ogni 15 minuti, effettuare il prelievo dei nuovi bollettini meteorologici per sciatori dal servizio NOAA
-	Elaborazione immagini – ad esempio, ogni giorno della settimana, durante le ore non di punta, usare Immagine di elaborazione, ad esempio, ogni giorno feriale durante gli orari, utilizzare il cloud computing per comprimere le immagini caricate durante la giornata


In questo articolo, presenteremo dei processi di esempio che è possibile creare con l’Utilità di pianificazione di Azure. Verranno forniti i dati JSON che descrivono ciascun processo. Se si utilizza l’[API REST dell'Utilità di pianificazione](https://msdn.microsoft.com/library/azure/dn528946.aspx), è possibile utilizzare questo stesso JSON per [creare un processo pianificato con l’Utilità di pianificazione di Azure](https://msdn.microsoft.com/library/azure/dn528937.aspx).

## Scenari Supportati

I molti esempi che verranno forniti illustrano la varietà di scenari che l’Utilità di Pianificazione di Azure supporta. Complessivamente, questi esempi illustrano come creare pianificazioni adeguate a molti tipi di utilizzo, tra cui quelli indicati di seguito:

-	Esecuzione una sola volta in una determinata data e ora
-	Esecuzione ricorrente per un numero esplicito di volte
-	Esecuzione immediata e in seguito ricorrente 
-	Esecuzione ricorrente ogni *n* minuti, ore, giorni, settimane o mesi, a partire da un determinato momento
-	Esecuzione ricorrente con frequenza settimanale o mensile, ma solo in giorni specifici, giorni specifici della settimana, o giorni specifici del mese
-	Esecuzione ricorrente più volte in un dato periodo – ad esempio, gli ultimi venerdì e lunedì di ogni mese, o alle 5:15 am e 5:15 pm di ogni giorno

## Date e Date-Ore

Le date nei processi dell'Utilità di pianificazione di Azure seguono [la specifica ISO-8601](http://en.wikipedia.org/wiki/ISO_8601) e includono solo la data.

I riferimenti di tipo Data-Ora nei processi dell'Utilità di pianificazione di Azure seguono [la specifica ISO-8601](http://en.wikipedia.org/wiki/ISO_8601) e includono sia la data che l’ora. Una Data-Ora che non specifichi un fuso orario si assume essere UTC.

## Procedura: Utilizzare JSON e API REST per la creazione di pianificazioni

Per creare una semplice pianificazione utilizzando gli esempi JSON in questo articolo e l'API REST dell'Utilità di pianificazione di Azure, [innanzitutto creare un servizio cloud](https://msdn.microsoft.com/library/azure/dn528943.aspx), [quindi creare una raccolta di processi](https://msdn.microsoft.com/library/azure/dn528940.aspx), e [infine creare un processo](https://msdn.microsoft.com/library/azure/dn528937.aspx). Quando si crea un processo, è possibile specificare la pianificazione e la sua ricorrenza utilizzando un JSON come quello nell’estratto di seguito:

	{
	    "startTime": "2012-08-04T00:00Z", // optional
	     …
	    "recurrence":                     // optional
	    {
	        "frequency": "week",     // can be "year" "month" "day" "week" "hour" "minute"
	        "interval": 1,                // optional, how often to fire (default to 1)
	        "schedule":                   // optional (advanced scheduling specifics)
	        {
	            "weekDays": ["monday", "wednesday", "friday"],
	            "hours": [10, 22]                      
	        },
	        "count": 10,                  // optional (default to recur infinitely)
	        "endTime": "2012-11-04",      // optional (default to recur infinitely)
	    },
	    …
	}
	
## Panoramica: Nozioni di base sugli schemi dei processi

La tabella seguente fornisce una panoramica di alto livello degli elementi principali correlate alla ricorrenza e pianificazione di un processo:

|**Nome JSON**|**Descrizione**|
|:--|:--|
|**_startTime_**|_startTime_ è in formato Data-Ora. Per le pianificazioni più semplici, _startTime_ è la prima occorrenza, e per le pianificazioni complesse, il processo verrà avviato non prima di _startTime_.|
|**_ricorrenza_**|L’oggetto _ricorrenza_ specifica le regole di ricorrenza per il processo e le tempistiche con la quale il processo viene eseguito. L'oggetto ricorrenza supporta gli elementi _frequenza, intervallo, endTime, conteggio,_ e _pianificazione_. Se si definisce _ricorrenza_, bisogna definire anche _frequenza_, gli altri elementi di _ricorrenza_ sono facoltativi.|
|**_frequenza_**|La stringa _frequenza_ rappresenta l'intervallo di frequenza con cui ricorre il processo. I valori supportati sono _"minuto", "ora", "giorno", "settimana"_ o _"mese"_.|
|**_intervallo_**|L’_intervallo_ è un numero intero positivo e indica l'intervallo per la _frequenza_ e determina la frequenza di esecuzione del processo. Ad esempio, se _intervallo_ è 3 e _frequenza_ è "settimana", il processo si ripete ogni 3 settimane. Utilità di pianificazione di Azure supporta un _intervallo_ massimo di 18 mesi per la frequenza mensile, 78 settimane per la frequenza settimanale, o 548 giorni per la frequenza giornaliera. Per le frequenze di tipo ora e minuto, l’intervallo supportato è 1 < = _intervallo_ < = 1000.|
|**_endTime_**|La stringa _endTime_ specifica data e ora passate le quali il processo non deve più essere eseguito. Non è possibile impostare un _endTime_ nel passato. Se non viene specificato un _endTime_ o un conteggio, il processo viene eseguito all'infinito. Non è possibile includere sia _endTime_ che _conteggio_ in uno stesso processo.|
|**_conteggio_**|<p>Il _conteggio_ è un numero intero positivo (maggiore di zero) che specifica quante volte eseguire un processo prima che finisca la sua pianificazione.</p><p>Il _conteggio_ rappresenta quante volte un processo deve essere eseguito prima di essere considerato come completato. Ad esempio, per un processo che viene eseguito ogni giorno con _conteggio_ 5 e data di inizio lunedì, il processo viene completato dopo l'esecuzione di venerdì. Se la data di inizio è già passata, la prima esecuzione verrà calcolata dall'ora di creazione.</p><p>Se non sono specificati _endTime_ o _conteggio_, il processo verrà eseguito all'infinito. Non è possibile includere sia _endTime_ che _conteggio_ in uno stesso processo.</p>|
|**_pianificazione_**|Un processo con una frequenza specificata modifica la sua ricorrenza in base a una pianificazione di ricorrenza. Una _pianificazione_ contiene modifiche in base a minuti, ore, giorni della settimana, giorni del mese e numero della settimana.|

## Panoramica: Impostazioni predefinite dello schema del processo, limiti ed esempi

Dopo questa panoramica, esaminiamo ciascuno di questi elementi in modo dettagliato.

|**Nome JSON**|**Tipo di valore**|**Obbligatorio?**|**Valore predefinito**|**Valori validi**|**Esempio**|
|:---|:---|:---|:---|:---|:---|
|**_startTime_**|Stringa|No|Nessuno|Date-Ore ISO-8601|<code>"startTime" : "2013-01-09T09:30:00-08:00"</code>|
|**_ricorrenza_**|Object|No|Nessuno|Oggetto ricorrenza|<code>"recurrence" : { "frequency" : "monthly", "interval" : 1 }</code>|
|**_frequency_**|Stringa|Sì|Nessuno|"minute", "hour", "day", "week", "month"|<code>"frequency" : "hour"</code> |
|**_interval_**|Number|No|1|Da 1 a 1000.|<code>"interval":10</code>|
|**_endTime_**|Stringa|No|Nessuno|Il valore Data-Ora fa riferimento a un momento nel futuro|<code>"endTime" : "2013-02-09T09:30:00-08:00"</code> |
|**_count_**|Number|No|Nessuno|>= 1|<code>"count": 5</code>|
|**_schedule_**|Object|No|Nessuno|Oggetto pianificazione|<code>"schedule" : { "minute" : [30], "hour" : [8,17] }</code>|

## Approfondimenti: _startTime_

La tabella seguente illustra come _startTime_ controlla la modalità di esecuzione di un processo.

|**valore startTime**|**Nessuna ricorrenza**|**Ricorrenza. Nessuna pianificazione**|**Ricorrenza con pianificazione**|
|:--|:--|:--|:--|
|**Nessuna ora di inizio**|Eseguire una volta immediatamente|Eseguire una volta immediatamente. Lanciare le esecuzioni successive basandosi sul calcolo dall'ultima esecuzione|<p>Eseguire una volta immediatamente</p><p>Lanciare le esecuzioni successive in base alla pianificazione di ricorrenza</p>|
|**Ora di inizio nel passato**|Eseguire una volta immediatamente|<p>Calcolare il momento della prima esecuzione dopo l'ora di inizio ed eseguire in quel momento</p><p>Lanciare le esecuzioni successiva basandosi sul calcolo dall’ultima esecuzione</p><p>Vedere l’esempio dopo questa tabella per ulteriori spiegazioni</p>|<p>Il processo inizia _non prima_ del momento d’inizio specificato. La prima occorrenza è basata sulla pianificazione calcolata dall'ora di inizio</p><p>Lanciare le esecuzioni successive in base alla pianificazione di ricorrenza</p>|
|**Ora di inizio nel futuro o nel presente**|Eseguire una sola volta all'ora di inizio specificata|<p>Eseguire una sola volta all'ora di inizio specificata</p><p>Lanciare le esecuzioni successive basandosi sul calcolo dall'ultima esecuzione</p>|<p>Il processo inizia _non prima_ del momento d’inizio specificato. La prima occorrenza è basata sulla pianificazione calcolata dall'ora di inizio</p><p>Lanciare le esecuzioni successive in base alla pianificazione di ricorrenza</p>|

Esaminiamo un esempio di ciò che accade quando _startTime_ è nel passato, con _ricorrenza_ ma senza _pianificazione_. Si supponga che l'ora corrente sia 2015-04-08 13:00, _startTime_ è 2015-04-07 14:00, e _ricorrenza_ è ogni 2 giorni (definito con _frequenza_: giorno e _intervallo_: 2.) Si noti che lo _startTime_ è nel passato e si verifica prima dell'ora corrente

In queste condizioni, la _prima esecuzione_ sarà il 2015-04-09 alle 14:00. Il motore dell'utilità di pianificazione calcola le occorrenze dall'ora di inizio dell'esecuzione. Vengono eliminate tutte le istanze in passato. Il motore utilizza l'istanza successiva che si verifica in futuro. Pertanto, in questo caso, _startTime_ è il 2015-04-07 alle 2:00, per cui l'istanza successiva è a 2 giorni da quel momento, ovvero il 2015-04-09 alle 2:00.

Si noti che la prima esecuzione sarebbe la stessa anche se startTime fosse 2015-04-05 alle 14:00 o 2015-04-01 14:00. Dopo la prima esecuzione, le esecuzioni successive vengono calcolate secondo la pianificazione – per cui sono il 2015-04-11 alle 14:00, poi il 2015-04-13 alle 14:00, poi il 2015-04-15 alle 14:00, e così via.

Infine, quando un processo ha una pianificazione, se non sono impostate ore e/o minuti nella pianificazione, per impostazione predefinita vengono usate le ore e/o minuti della prima esecuzione.

## Approfondimenti: _pianificazione_

Da un lato, una _pianificazione_ può _limitare_ il numero di esecuzioni di un processo. Ad esempio, se un processo con una frequenza "mese" ha una _pianificazione_ per essere eseguito solo il giorno 31, il processo viene eseguito solo nei mesi con un 31<sup>mo</sup> giorno.

D'altra parte, una _pianificazione_ può anche _aumentare_ il numero di esecuzioni di un processo. Ad esempio, se un processo con frequenza "mese" ha una _pianificazione_ per essere eseguita il primo e secondo giorno del mese, il processo viene eseguito il 1<sup>mo</sup> e 2<sup>do</sup> giorno del mese invece che solo una volta al mese.

Se vengono specificati più parametri di pianificazione, l'ordine di valutazione è dal più grande al più piccolo – numero della settimana, giorno del mese, giorno della settimana, ora, e minuto.

Nella tabella seguente sono illustrati nel dettaglio gli elementi della _pianificazione_:

|**Nome JSON**|**Descrizione**|**Valori validi**|
|:---|:---|:---|
|**minutes**|Minuti dell'ora in cui verrà eseguito il processo|<ul><li>Numero intero, o</li><li>Matrice di numeri interi</li></ul>|
|**hours**|Ora del giorno in cui verrà eseguito il processo|<ul><li>Numero intero, o</li><li>Matrice di numeri interi</li></ul>|
|**weekDays**|Giorni della settimana in cui verrà eseguito il processo. Può essere specificato solo con una frequenza settimanale.|<ul><li>"Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", o "Sunday"</li><li>Matrice di qualsiasi dei valori precedenti (dimensione massima della matrice: 7)</li></ul>\_Non\_ distingue tra maiuscole e minuscole|
|**monthlyOccurrences**|Determina in quali giorni del mese verrà eseguito il processo. Può essere specificato solo con una frequenza mensile.|<ul><li>Matrice di oggetti monthlyOccurence:</li></ul> <pre>{"day": _giorno_,<br /> "occurrence": _occorrenza_<br />}</pre><p> _giorno_ è il giorno della settimana in cui verrà eseguito il processo, ad esempio {Sunday} per ogni domenica del mese. Richiesto.</p><p>Occorrenza è l’_occorrenza_ del giorno durante il mese, ad esempio {Sunday, -1} è l'ultima domenica del mese. Facoltativo.</p>|
|**monthDays**|Giorno del mese in cui verrà eseguito il processo. Può essere specificato solo con una frequenza mensile.|<ul><li>Qualsiasi valore < = -1 e > = -31.</li><li>Qualsiasi valore > = 1 e < = 31.</li><li>Una matrice di valori come i sopraindicati</li></ul>|

## Esempi: Pianificazioni di ricorrenza

Di seguito sono riportati diversi esempi di pianificazioni di ricorrenza – concentrarsi sull'oggetto pianificazione e i relativi elementi secondari.

Le pianificazioni di seguito assumono che l’_intervallo_ sia impostato su 1. Inoltre, bisogna assumere che la frequenza sia corretta in base al contenuto della _pianificazione_ – ad esempio, non si può utilizzare frequenza "day" e modificare "monthDays" nella pianificazione. Tali limitazioni sono state descritte in precedenza.

|**Esempio**|**Descrizione**|
|:---|:---|
|<code>{"hours":[5]}</code>|Eseguire alle 5 di mattina di ogni giorno. L’Utilità di pianificazione di Azure fa corrispondere ogni valore in "ore" con ogni valore in "minuti", uno per uno, per creare un elenco di tutte le volte in cui deve essere eseguito il processo.|
|<code>{"minutes":[15],"hours":[5]}</code>|Eseguire alle 5:15 di mattina di ogni giorno.|
|<code>{"minutes":[15],"hours":[5,17]}</code>|Eseguire alle 5:15 di mattina e alle 17:15 ogni giorno|
|<code>{"minutes":[15,45],"hours":[5,17]}</code>|Eseguire alle 5:15, 5:45, 17:15 e 17:45 ogni giorno|
|<code>{"minutes":[0,15,30,45]}</code>|Eseguire ogni 15 minuti|
|<code>{hours":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23]}</code>|Eseguire ogni ora. Questo processo viene eseguito ogni ora. Il minuto è controllato dallo _startTime_, se è stato specificato o, se non è specificato, dal momento della creazione. Ad esempio, se l'ora di inizio o l’ora di creazione (qualunque delle due si applichi) è 12:25, il processo verrà eseguito a 00:25, 01:25, 02:25, ..., 23:25. La pianificazione è equivalente all’avere un processo con _frequenza_ di "ora", un _intervallo_ di 1, e nessuna _pianificazione_. La differenza è che questa pianificazione può essere utilizzata con _frequenza_ e _intervallo_ diversi per creare anche altri processi. Ad esempio, se _frequenza_ fosse "mese", la pianificazione verrebbe eseguita solo una volta al mese anziché ogni giorno, se la _frequenza_ fosse "giorno"|
|<code>{minutes:[0]}</code>|Eseguire ogni ora all’inizio dell’ora. Anche questo processo viene eseguito ogni ora, ma al suo inizio (ad esempio 12:00, 13:00, 14:00, etc.) Ciò equivale a un processo con frequenza di "ora", uno startTime con zero minuti, e nessuna pianificazione se la frequenza è "giorno", ma se la frequenza è "settimana" o "mese", la pianificazione verrebbe eseguita un solo giorno alla settimana o al mese, rispettivamente.|
|<code>{"minutes":[15]}</code>|Eseguire 15 minuti dopo l’inizio di ogni ora. Viene eseguito ogni ora, a partire da 00:15, poi 01:15, 02:15, e così via fino a 22:15 e 23:15.|
|<code>{"hours":[17],"weekDays":["saturday"]}</code>|Eseguire alle 17.00 di ogni sabato|
|<code>{hours":[17],"weekDays":["monday","wednesday","friday"]}</code>|Eseguire alle 17 di ogni lunedì, mercoledì e venerdì|
|<code>{"minutes":[15,45],"hours":[17],"weekDays":["monday","wednesday","friday"]}</code>|Eseguire alle 17:15 e alle 17:45 di ogni lunedì, mercoledì e venerdì|
|<code>{"hours":[5,17],"weekDays":["monday","wednesday","friday"]}</code>|Eseguire alle 05:00 e alle 17:00 di ogni lunedì, mercoledì e venerdì|
|<code>{"minutes":[15,45],"hours":[5,17],"weekDays":["monday","wednesday","friday"]}</code>|Eseguire alle 05:15, alle 05:45, alle 17:15 e alle 17:45 di ogni lunedì, mercoledì e venerdì|
|<code>{"minutes":[0,15,30,45], "weekDays":["monday","tuesday","wednesday","thursday","friday"]}</code>|Eseguire ogni 15 minuti nei giorni feriali|
|<code>{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday","tuesday","wednesday","thursday","friday"]}</code>|Eseguire ogni 15 minuti nei giorni feriali tra le 09:00 e le 16:45|
|<code>{"weekDays":["sunday"]}</code>|Eseguire ogni domenica all'ora di inizio|
|<code>{"giorni feriali": ["martedì", "giovedì"]}</code>|Eseguire ogni martedì e giovedì all'ora di inizio|
|<code>{"minutes":[0],"hours":[6],"monthDays":[28]}</code>|Eseguire alle 06:00 il ventottesimo giorno di ogni mese (assumendo che la frequenza sia mensile)|
|<code>{"minutes":[0],"hours":[6],"monthDays":[-1]}</code>|Eseguire alle 06:00 dell’ultimo giorno di ogni mese. Se si desidera eseguire un processo l'ultimo giorno del mese, utilizzare -1 anziché il giorno 28, 29, 30 o 31.|
|<code>{"minutes":[0],"hours":[6],"monthDays":[1,-1]}</code>|Eseguire alle 06:00 il primo e l’ultimo giorno di ogni mese|
|<code>{monthDays":[1,-1]}</code>|Eseguire il primo e l’ultimo giorno di ogni mese all’ora di inizio|
|<code>{monthDays":[1,14]}</code>|Eseguire il primo e il quattordicesimo giorno di ogni mese all’ora di inizio|
|<code>{monthDays":[2]}</code>|Eseguire il secondo giorno di ogni mese all’ora di inizio|
|<code>{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday","occurrence":1}]}</code>|Eseguire il primo venerdì di ogni mese alle 05:00|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":1}]}</code>|: Eseguire il primo venerdì di ogni mese all’ora di inizio|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":-3}]}</code>|Eseguire il terzultimo venerdì di ogni mese, all'ora di inizio|
|<code>{"minutes":[15],"hours":[5],"monthlyOccurrences":[{"day":"friday","occurrence":1},{"day":"friday","occurrence":-1}]}</code>|Eseguire il primo e l’ultimo venerdì di ogni mese alle 05:15|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":1},{"day":"friday","occurrence":-1}]}</code>|Eseguire il primo e l’ultimo venerdì di ogni mese all’ora di inizio|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":5}]}</code>|Eseguire il quinto venerdì di ogni mese all’ora di inizio Se non ci sono cinque venerdì nel mese, il processo non viene eseguito, dato che è pianificato per essere lanciato solamente al quinto venerdì del mese. È consigliabile utilizzare -1 anziché 5 per l'occorrenza se si desidera eseguire il processo all'ultimo venerdì del mese.|
|<code>{"minutes":[0,15,30,45],"monthlyOccurrences":[{"day":"friday","occurrence":-1}]}</code>|Eseguire ogni 15 minuti all’ultimo venerdì del mese|
|<code>{"minutes":[15,45],"hours":[5,17],"monthlyOccurrences":[{"day":"wednesday","occurrence":3}]}</code>|Eseguire alle 05:15, 05:45, 17:15 e 17:45 il terzo mercoledì di ogni mese|

## Vedere anche
 

 [Che cos'è l'Utilità di pianificazione?](scheduler-intro.md)
 
 [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure](scheduler-concepts-terms.md)

 [Introduzione all'uso dell'Utilità di pianificazione di Azure nel portale di Azure](scheduler-get-started-portal.md)

 [Piani e fatturazione nell'utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione di Azure](https://msdn.microsoft.com/library/dn528946)

 [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione di Azure](scheduler-powershell-reference.md)

 [Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione di Azure](scheduler-high-availability-reliability.md)

 [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione di Azure](scheduler-limits-defaults-errors.md)

 [Autenticazione in uscita dell'Utilità di pianificazione di Azure](scheduler-outbound-authentication.md)
 
  

<!---HONumber=AcomDC_1210_2015-->