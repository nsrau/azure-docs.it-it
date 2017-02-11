---
title: "Come creare pianificazioni complesse e operazioni ricorrenti avanzate con l&quot;Utilità di pianificazione di Azure"
description: "Come creare pianificazioni complesse e operazioni ricorrenti avanzate con l&quot;Utilità di pianificazione di Azure"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e329d152ea6a95c8cdfa6a507504601d4e0957cd


---
# <a name="how-to-build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Come creare pianificazioni complesse e operazioni ricorrenti avanzate con l'Utilità di pianificazione di Azure
## <a name="overview"></a>Overview
Alla base dell’Utilità di pianificazione di Azure c’è la *pianificazione*. La pianificazione determina come e quando l'Utilità di Pianificazione esegue il processo.

L’Utilità di pianificazione di Azure consente di specificare pianificazioni varie, sia monouso che ricorrenti, per un processo. Le pianificazioni *monouso* vengono eseguite una volta sola all'ora specificata. Si tratta in effetti di pianificazioni *ricorrenti* che vengono eseguite solo una volta. Le pianificazioni ricorrenti vengono eseguite con una frequenza predeterminata.

Con questa flessibilità, l’Utilità di pianificazione di Azure consente di supportare un'ampia gamma di scenari aziendali:

* Pulizia periodica dei dati, ad esempio eliminare tutti i post su Twitter più vecchi di 3 mesi
* Archiviazione – ad esempio, ogni mese, passaggio dello storico fatture al servizio di backup
* Richieste di dati esterni – ad esempio, ogni 15 minuti, effettuare il prelievo dei nuovi bollettini meteorologici per sciatori dal servizio NOAA
* Elaborazione immagini – ad esempio, ogni giorno della settimana, durante le ore non di punta, usare Immagine di elaborazione, ad esempio, ogni giorno feriale durante gli orari, utilizzare il cloud computing per comprimere le immagini caricate durante la giornata

In questo articolo, presenteremo dei processi di esempio che è possibile creare con l’Utilità di pianificazione di Azure. Verranno forniti i dati JSON che descrivono ciascun processo. Se si usa l'[API REST dell'Utilità di pianificazione](https://msdn.microsoft.com/library/mt629143.aspx), è possibile usare questo stesso JSON per [creare un processo dell'Utilità di pianificazione di Azure](https://msdn.microsoft.com/library/mt629145.aspx).

## <a name="supported-scenarios"></a>Scenari Supportati
I numerosi esempi forniti in questo argomento illustrano i diversi scenari che l'Utilità di Pianificazione di Azure supporta. Complessivamente, questi esempi illustrano come creare pianificazioni adeguate a molti tipi di utilizzo, tra cui quelli indicati di seguito:

* Esecuzione una sola volta in una determinata data e ora
* Esecuzione ricorrente per un numero esplicito di volte
* Esecuzione immediata e in seguito ricorrente
* Esecuzione ricorrente ogni *n* minuti, ore, giorni, settimane o mesi, a partire da un determinato momento
* Esecuzione ricorrente con frequenza settimanale o mensile, ma solo in giorni specifici, giorni specifici della settimana o giorni specifici del mese
* Esecuzione ricorrente più volte in un dato periodo – ad esempio, gli ultimi venerdì e lunedì di ogni mese, o alle 5:15 am e 5:15 pm di ogni giorno

## <a name="dates-and-datetimes"></a>Date e Date-Ore
Le date nei processi dell'Utilità di pianificazione di Azure seguono [la specifica ISO-8601](http://en.wikipedia.org/wiki/ISO_8601) e includono solo la data.

I riferimenti di tipo Data-Ora nei processi dell'Utilità di pianificazione di Azure seguono [la specifica ISO-8601](http://en.wikipedia.org/wiki/ISO_8601) e includono sia la data che l’ora. Una Data-Ora che non specifichi un fuso orario si assume essere UTC.  

## <a name="how-to-use-json-and-rest-api-for-creating-schedules"></a>Procedura: Utilizzare JSON e API REST per la creazione di pianificazioni
Per creare una pianificazione semplice con l'[API REST dell'Utilità di pianificazione di Azure](https://msdn.microsoft.com/library/mt629143), è prima di tutto necessario [registrare la sottoscrizione con un provider di risorse](https://msdn.microsoft.com/library/azure/dn790548.aspx) (il nome del provider per l'Utilità di pianificazione è *Microsoft.Scheduler*), quindi [creare una raccolta processi](https://msdn.microsoft.com/library/mt629159.aspx) e infine [creare un processo](https://msdn.microsoft.com/library/mt629145.aspx). Quando si crea un processo, è possibile specificare la pianificazione e la sua ricorrenza utilizzando un JSON come quello nell’estratto di seguito:

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

## <a name="overview-job-schema-basics"></a>Panoramica: Nozioni di base sugli schemi dei processi
La tabella seguente fornisce una panoramica di alto livello degli elementi principali correlate alla ricorrenza e pianificazione di un processo:

| **Nome JSON** | **Descrizione** |
|:--- |:--- |
| ***startTime*** |*startTime* è in formato Data-Ora. Per le pianificazioni semplici *startTime* è la prima occorrenza e per le pianificazioni complesse il processo verrà avviato non prima di *startTime*. |
| ***recurrence*** |L'oggetto *recurrence* specifica le regole di ricorrenza per il processo e le tempistiche con la quale il processo viene eseguito. L'oggetto recurrence supporta gli elementi *frequency, interval, endTime, count* e *schedule*. Se viene definito l'oggetto *recurrence*, è necessario definire anche *frequency*. Gli altri elementi di *recurrence* sono facoltativi. |
| ***frequency*** |La stringa *frequency* rappresenta l'intervallo di frequenza con cui ricorre il processo. I valori supportati sono *"minute", "hour", "day", "week",* o *"month"*. |
| ***interval*** |L'oggetto *interval* è un numero intero positivo e indica l'intervallo per il valore *frequency*, che determina la frequenza di esecuzione del processo. Se ad esempio *interval* è 3 e *frequency* è "week", il processo si ripete ogni 3 settimane. L'Utilità di pianificazione di Azure supporta un oggetto *interval* con valore massimo di 18 mesi per la frequenza mensile, 78 settimane per la frequenza settimanale o 548 giorni per la frequenza giornaliera. Per le frequenze di tipo ora e minuto, l'intervallo supportato è 1 <= *interval* <= 1000. |
| ***endTime*** |La stringa *endTime* specifica data e ora passate le quali il processo non deve più essere eseguito. Non è possibile impostare un *endTime* nel passato. Se non viene specificato un *endTime* o un oggetto count, il processo viene eseguito all'infinito. Non è possibile includere sia *endTime* e *count* in uno stesso processo. |
| ***count*** |<p>L'oggetto *count* è un numero intero positivo (maggiore di zero) che specifica il numero di volte per cui il processo deve essere eseguito prima del completamento.</p><p>L'oggetto *count* rappresenta il numero di volte per cui viene eseguito un processo prima di essere considerato come completato. Ad esempio, per un processo che viene eseguito ogni giorno con *count* 5 e data di inizio lunedì, il processo viene completato dopo l'esecuzione di venerdì. Se la data di inizio è già passata, la prima esecuzione verrà calcolata dall'ora di creazione.</p><p>Se non viene specificato un oggetto *endTime* o un oggetto *count*, il processo viene eseguito all'infinito. Non è possibile includere sia *endTime* e *count* in uno stesso processo.</p> |
| ***schedule*** |Un processo con una frequenza specificata modifica la sua ricorrenza in base a una pianificazione di ricorrenza. Un oggetto *schedule* contiene modifiche in base a minuti, ore, giorni della settimana, giorni del mese e numero della settimana. |

## <a name="overview-job-schema-defaults-limits-and-examples"></a>Panoramica: Impostazioni predefinite dello schema del processo, limiti ed esempi
Dopo questa panoramica, esaminiamo ciascuno di questi elementi in modo dettagliato.

| **Nome JSON** | **Tipo di valore** | **Obbligatorio?** | **Valore predefinito** | **Valori validi** | **Esempio** |
|:--- |:--- |:--- |:--- |:--- |:--- |
| ***startTime*** |String |No |None |Date-Ore ISO-8601 |<code>"startTime" : "2013-01-09T09:30:00-08:00"</code> |
| ***recurrence*** |Oggetto |No |None |Oggetto ricorrenza |<code>"recurrence" : { "frequency" : "monthly", "interval" : 1 }</code> |
| ***frequency*** |String |Sì |None |"minute", "hour", "day", "week", "month" |<code>"frequency" : "hour"</code> |
| ***interval*** |Number |No |1 |Da 1 a 1000. |<code>"interval":10</code> |
| ***endTime*** |String |No |None |Il valore Data-Ora fa riferimento a un momento nel futuro |<code>"endTime" : "2013-02-09T09:30:00-08:00"</code> |
| ***count*** |Number |No |None |>= 1 |<code>"count": 5</code> |
| ***schedule*** |Oggetto |No |None |Oggetto pianificazione |<code>"schedule" : { "minute" : [30], "hour" : [8,17] }</code> |

## <a name="deep-dive-starttime"></a>Approfondimenti: *startTime*
La tabella seguente illustra come *startTime* controlla la modalità di esecuzione di un processo.

| **valore startTime** | **Nessuna ricorrenza** | **Ricorrenza. Nessuna pianificazione** | **Ricorrenza con pianificazione** |
|:--- |:--- |:--- |:--- |
| **Nessuna ora di inizio** |Eseguire una volta immediatamente |Eseguire una volta immediatamente. Lanciare le esecuzioni successive basandosi sul calcolo dall'ultima esecuzione |<p>Eseguire una volta immediatamente</p><p>Avviare le esecuzioni successive in base alla pianificazione di ricorrenza</p> |
| **Ora di inizio nel passato** |Eseguire una volta immediatamente |<p>Calcolare l'ora della prima esecuzione futura dopo l'ora di inizio e avviare l'esecuzione in corrispondenza di tale orario</p><p>Avviare le esecuzione successive in base al calcolo relativo all'ora dell'ultima esecuzione</p><p>Per altre informazioni, vedere l'esempio disponibile dopo la tabella</p> |<p>Il processo inizia *non prima* dell'ora di inizio specificata. La prima occorrenza è basata sulla pianificazione calcolata dall'ora di inizio</p><p>Avviare le esecuzioni successive in base alla pianificazione di ricorrenza</p> |
| **Ora di inizio nel futuro o nel presente** |Eseguire una sola volta all'ora di inizio specificata |<p>Eseguire una sola volta all'ora di inizio specificata</p><p>Lanciare le esecuzioni successive basandosi sul calcolo dall'ultima esecuzione</p> |<p>Il processo inizia *non prima* dell'ora di inizio specificata. La prima occorrenza è basata sulla pianificazione calcolata dall'ora di inizio</p><p>Avviare le esecuzioni successive in base alla pianificazione di ricorrenza</p> |

È possibile esaminare lo scenario che prevede un valore per *startTime* nel passato, un oggetto *recurrence* ma nessun oggetto *schedule*.  Si supponga che l'ora corrente sia 2015-04-08 13:00, *startTime* sia 2015-04-07 14:00 e *recurrence* sia pari a ogni 2 giorni, in base a *frequency*: day e *interval*: 2. Si noti che il valore di *startTime* è nel passato e si verifica prima dell'ora corrente.

In base a queste condizioni, la *prima esecuzione* verrà avviata il giorno 2015-04-09 alle ore 14:00\. Il motore dell'Utilità di pianificazione calcola le ricorrenze dell'esecuzione a partire dall'ora di inizio.  Vengono eliminate tutte le istanze in passato. Il motore utilizza l'istanza successiva che si verifica in futuro.  In questo caso, quindi, il valore per *startTime* è 2015-04-07 alle 14:00, quindi l'istanza successiva viene eseguita due giorni dopo tale orario, ovvero il giorno 2015-04-09 alle 14:00.

Si noti che la prima esecuzione rimane invariata anche se il valore di startTime è 2015-04-05 14:00 o 2015-04-01 14:00\. Dopo la prima esecuzione vengono calcolate le esecuzioni successive sulla base della pianificazione. In questo caso le esecuzioni successive verranno avviate il giorno 2015-04-11 alle 14:00, quindi il giorno 2015-04-13 alle 14:00, il giorno 2015-04-15 alle 14:00 e così via.

Infine, quando un processo ha una pianificazione, se non sono impostate ore e/o minuti nella pianificazione, per impostazione predefinita vengono usate le ore e/o minuti della prima esecuzione.

## <a name="deep-dive-schedule"></a>Approfondimenti: *schedule*
Da un lato, un oggetto *schedule* può *limitare* il numero di esecuzioni di un processo.  Se ad esempio l'oggetto *schedule* di un processo con frequenza "month" prevede l'esecuzione solo il giorno 31,<sup></sup> il processo viene eseguito solo nei mesi che includono 31 giorni.

Un oggetto *schedule* tuttavia può anche *aumentare* il numero di esecuzioni di un processo. Se, ad esempio l'oggetto *schedule* di un processo con frequenza "month" prevede l'esecuzione nei giorni 1 e 2 del mese, il processo viene eseguito il 1<sup>°</sup> e il 2<sup>°</sup> giorno del mese, invece che una sola volta al mese.

Se vengono specificati più parametri di pianificazione, l'ordine di valutazione è dal più grande al più piccolo – numero della settimana, giorno del mese, giorno della settimana, ora, e minuto.

La tabella seguente illustra in modo dettagliato gli elementi dell'oggetto *schedule*.

| **Nome JSON** | **Descrizione** | **Valori validi** |
|:--- |:--- |:--- |
| **minutes** |Minuti dell'ora in cui verrà eseguito il processo |<ul><li>Numero intero o</li><li>Matrice di numeri interi</li></ul> |
| **hours** |Ora del giorno in cui verrà eseguito il processo |<ul><li>Numero intero o</li><li>Matrice di numeri interi</li></ul> |
| **weekDays** |Giorni della settimana in cui verrà eseguito il processo. Può essere specificato solo con una frequenza settimanale. |<ul><li>I valori consentiti sono "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday" o "Sunday"</li><li>Matrice dei valori precedenti (dimensione massima della matrice: 7)</li></ul>*Non* viene applicata la distinzione tra maiuscole e minuscole |
| **monthlyOccurrences** |Determina in quali giorni del mese verrà eseguito il processo. Può essere specificato solo con una frequenza mensile. |<ul><li>Matrice di oggetti monthlyOccurence:</li></ul> <pre>{ "day": *giorno*,<br />  "occurrence": *occorrenza*<br />}</pre><p> *day* è il giorno della settimana in cui verrà eseguito il processo, ad esempio {Sunday} corrisponde a ogni domenica del mese. Richiesto.</p><p>*occurrence* è l'occorrenza del giorno durante il mese, ad esempio {Sunday, -1} corrisponde all'ultima domenica del mese. Facoltativo.</p> |
| **monthDays** |Giorno del mese in cui verrà eseguito il processo. Può essere specificato solo con una frequenza mensile. |<ul><li>Qualsiasi valore <= -1 e >= -31.</li><li>Qualsiasi valore >= 1 e <= 31.</li><li>Matrice dei valori precedenti</li></ul> |

## <a name="examples-recurrence-schedules"></a>Esempi: Pianificazioni di ricorrenza
Di seguito sono riportati diversi esempi di pianificazioni di ricorrenza – concentrarsi sull'oggetto pianificazione e i relativi elementi secondari.

Tutte le pianificazioni seguenti presuppongono che *interval* sia impostato su 1\. È anche necessario configurare la frequenza corretta in base al valore di *schedule*. Non è ad esempio possibile usare un valore "day" per la frequenza con la modifica "monthDays" nell'oggetto schedule. Tali limitazioni sono state descritte in precedenza.

| **Esempio** | **Descrizione** |
|:--- |:--- |
| <code>{"hours":[5]}</code> |Eseguire alle 5 di mattina di ogni giorno. L’Utilità di pianificazione di Azure fa corrispondere ogni valore in "ore" con ogni valore in "minuti", uno per uno, per creare un elenco di tutte le volte in cui deve essere eseguito il processo. |
| <code>{"minutes":[15], "hours":[5]}</code> |Eseguire alle 5:15 di mattina di ogni giorno. |
| <code>{"minutes":[15], "hours":[5,17]}</code> |Eseguire alle 5:15 di mattina e alle 17:15 ogni giorno |
| <code>{"minutes":[15,45], "hours":[5,17]}</code> |Eseguire alle 5:15, 5:45, 17:15 e 17:45 ogni giorno |
| <code>{"minutes":[0,15,30,45]}</code> |Eseguire ogni 15 minuti |
| <code>{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}</code> |Eseguire ogni ora. Questo processo viene eseguito ogni ora. Il valore per i minuti viene controllato dall'oggetto *startTime*, se specificato, oppure dall'ora di creazione, se tale oggetto non è specificato. Ad esempio, se l'ora di inizio o l’ora di creazione (qualunque delle due si applichi) è 12:25, il processo verrà eseguito a 00:25, 01:25, 02:25, ..., 23:25. La pianificazione equivale a un processo con *frequency* pari a "hour", *interval* di 1 e nessun oggetto *schedule*. Questa pianificazione, tuttavia, può essere usata con valori diversi per *frequency* e *interval*, per creare anche altri processi. Se, ad esempio, il valore per *frequency* fosse "month", la pianificazione verrebbe eseguita solo una volta al mese invece di ogni giorno, come accadrebbe se il valore di *frequency* fosse "day". |
| <code>{minutes:[0]}</code> |Eseguire ogni ora all’inizio dell’ora. Anche questo processo viene eseguito ogni ora, ma al suo inizio (ad esempio 12:00, 13:00, 14:00, etc.) Ciò equivale a un processo con frequenza di "ora", uno startTime con zero minuti, e nessuna pianificazione se la frequenza è "giorno", ma se la frequenza è "settimana" o "mese", la pianificazione verrebbe eseguita un solo giorno alla settimana o al mese, rispettivamente. |
| <code>{"minutes":[15]}</code> |Eseguire 15 minuti dopo l’inizio di ogni ora. Viene eseguito ogni ora, a partire da 00:15, poi 01:15, 02:15, e così via fino a 22:15 e 23:15. |
| <code>{"hours":[17], "weekDays":["saturday"]}</code> |Eseguire alle 17.00 di ogni sabato |
| <code>{hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |Eseguire alle 17 di ogni lunedì, mercoledì e venerdì |
| <code>{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |Eseguire alle 17:15 e alle 17:45 di ogni lunedì, mercoledì e venerdì |
| <code>{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |Eseguire alle 05:00 e alle 17:00 di ogni lunedì, mercoledì e venerdì |
| <code>{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |Eseguire alle 05:15, alle 05:45, alle 17:15 e alle 17:45 di ogni lunedì, mercoledì e venerdì |
| <code>{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |Eseguire ogni 15 minuti nei giorni feriali |
| <code>{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |Eseguire ogni 15 minuti nei giorni feriali tra le 09:00 e le 16:45 |
| <code>{"weekDays":["sunday"]}</code> |Eseguire ogni domenica all'ora di inizio |
| <code>{"weekDays":["tuesday", "thursday"]}</code> |Eseguire ogni martedì e giovedì all'ora di inizio |
| <code>{"minutes":[0], "hours":[6], "monthDays":[28]}</code> |Eseguire alle 06:00 il ventottesimo giorno di ogni mese (assumendo che la frequenza sia mensile) |
| <code>{"minutes":[0], "hours":[6], "monthDays":[-1]}</code> |Eseguire alle 06:00 dell’ultimo giorno di ogni mese. Se si desidera eseguire un processo l'ultimo giorno del mese, utilizzare -1 anziché il giorno 28, 29, 30 o 31. |
| <code>{"minutes":[0], "hours":[6], "monthDays":[1,-1]}</code> |Eseguire alle 06:00 il primo e l’ultimo giorno di ogni mese |
| <code>{monthDays":[1,-1]}</code> |Eseguire il primo e l’ultimo giorno di ogni mese all’ora di inizio |
| <code>{monthDays":[1,14]}</code> |Eseguire il primo e il quattordicesimo giorno di ogni mese all’ora di inizio |
| <code>{monthDays":[2]}</code> |Eseguire il secondo giorno di ogni mese all’ora di inizio |
| <code>{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |Eseguire il primo venerdì di ogni mese alle 05:00 |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |: Eseguire il primo venerdì di ogni mese all’ora di inizio |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}</code> |Eseguire il terzultimo venerdì di ogni mese, all'ora di inizio |
| <code>{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |Eseguire il primo e l’ultimo venerdì di ogni mese alle 05:15 |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |Eseguire il primo e l’ultimo venerdì di ogni mese all’ora di inizio |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}</code> |Eseguire il quinto venerdì di ogni mese all’ora di inizio Se non ci sono cinque venerdì nel mese, il processo non viene eseguito, dato che è pianificato per essere lanciato solamente al quinto venerdì del mese. È consigliabile utilizzare -1 anziché 5 per l'occorrenza se si desidera eseguire il processo all'ultimo venerdì del mese. |
| <code>{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}</code> |Eseguire ogni 15 minuti all’ultimo venerdì del mese |
| <code>{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}</code> |Eseguire alle 05:15, 05:45, 17:15 e 17:45 il terzo mercoledì di ogni mese |

## <a name="see-also"></a>Vedere anche
 [Che cos'è l'Utilità di pianificazione?](scheduler-intro.md)

 [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure](scheduler-concepts-terms.md)

 [Introduzione all'uso dell'Utilità di pianificazione di Azure nel portale di Azure](scheduler-get-started-portal.md)

 [Piani e fatturazione nell'utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione di Azure](https://msdn.microsoft.com/library/mt629143)

 [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione di Azure](scheduler-powershell-reference.md)

 [Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione di Azure](scheduler-high-availability-reliability.md)

 [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione di Azure](scheduler-limits-defaults-errors.md)

 [Autenticazione in uscita dell'Utilità di pianificazione di Azure](scheduler-outbound-authentication.md)




<!--HONumber=Nov16_HO3-->


