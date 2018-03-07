---
title: "Creare pianificazioni complesse e operazioni ricorrenti avanzate con l'Utilità di pianificazione di Azure"
description: "Informazioni su come creare pianificazioni complesse e operazioni ricorrenti avanzate con l'Utilità di pianificazione di Azure."
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
ms.openlocfilehash: 4293442e13fc4bae871b1f32a3ed4231d9f32632
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Creare pianificazioni complesse e operazioni ricorrenti avanzate con l'Utilità di pianificazione di Azure

L'Utilità di pianificazione di Azure è basata sul concetto di pianificazione, che determina come e quando viene eseguito il processo. 

È possibile usare l'Utilità di pianificazione per impostare più pianificazioni singole e ricorrenti per un processo. Le pianificazioni singole attivano un processo a un'ora specificata. Si tratta in realtà di pianificazioni ricorrenti che vengono eseguite una sola volta. Le pianificazioni ricorrenti vengono eseguite con una frequenza predeterminata.

Grazie a questa flessibilità, è possibile usare l'Utilità di pianificazione per un'ampia varietà di scenari aziendali:

* **Pulizia periodica dei dati**. Ad esempio, eliminare ogni giorno tutti i tweet più vecchi di tre mesi.
* **Archiviazione**. Ad esempio, eseguire ogni mese il push dello storico fatture a un servizio di backup.
* **Richieste di dati esterni**. Ad esempio, eseguire ogni 15 minuti il pull di un nuovo bollettino meteorologico.
* **Elaborazione di immagini**. Ad esempio, ogni giorno feriale, durante le ore non di punta, usare il cloud computing per comprimere le immagini caricate durante la giornata.

In questo articolo vengono presentati alcuni processi di esempio che è possibile creare con l'Utilità di pianificazione. Verranno forniti i dati JSON che descrivono ciascun processo. Se si usa l'[API REST dell'Utilità di pianificazione](https://msdn.microsoft.com/library/mt629143.aspx), è possibile usare questo stesso codice JSON per [creare un processo dell'Utilità di pianificazione](https://msdn.microsoft.com/library/mt629145.aspx).

## <a name="supported-scenarios"></a>Scenari supportati
Gli esempi riportati in questo articolo illustrano i diversi scenari supportati dall'Utilità di pianificazione. In linea generale, questi esempi illustrano come creare pianificazioni per molti tipi di utilizzo, inclusi i seguenti:

* Singola esecuzione in una data e a un'ora specifiche.
* Esecuzione ricorrente per un numero specifico di volte.
* Esecuzione immediata e in seguito ricorrente.
* Esecuzione ricorrente ogni *n* minuti, ore, giorni, settimane o mesi, a partire da un momento specifico.
* Esecuzione ricorrente con frequenza settimanale o mensile, ma solo in giorni specifici della settimana o del mese.
* Esecuzione ricorrente, più volte in un determinato periodo. Ad esempio, l'ultimo venerdì e l'ultimo lunedì di ogni mese oppure alle 05:15 e alle 17:15 di ogni giorno.

## <a name="date-and-date-time"></a>Data e data-ora
I riferimenti di tipo data nei processi dell'Utilità di pianificazione sono definiti in base alla [specifica ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) e includono solo la data.

I riferimenti di tipo data-ora nei processi dell'Utilità di pianificazione sono definiti in base alla [specifica ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) e includono sia la data che l'ora. Se un valore data-ora non specifica il fuso orario, si presuppone che sia UTC.  

## <a name="use-json-and-the-rest-api-to-create-a-schedule"></a>Usare JSON e l'API REST per creare una pianificazione
Per creare una pianificazione di base usando l'[API REST dell'Utilità di pianificazione](https://msdn.microsoft.com/library/mt629143), prima di tutto [registrare la sottoscrizione con un provider di risorse](https://msdn.microsoft.com/library/azure/dn790548.aspx). Il nome del provider per l'Utilità di pianificazione è **Microsoft.Scheduler**. Dopo questa operazione, [creare una raccolta di processi](https://msdn.microsoft.com/library/mt629159.aspx) e infine [creare un processo](https://msdn.microsoft.com/library/mt629145.aspx). 

Quando si crea un processo, è possibile specificare la pianificazione e la ricorrenza usando codice JSON, come in questo estratto:

    {
        "startTime": "2012-08-04T00:00Z", // Optional
         …
        "recurrence":                     // Optional
        {
            "frequency": "week",     // Can be "year", "month", "day", "week", "hour", or "minute"
            "interval": 1,                // How often to fire
            "schedule":                   // Optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // Optional (default to recur infinitely)
            "endTime": "2012-11-04",      // Optional (default to recur infinitely)
        },
        …
    }

## <a name="job-schema-basics"></a>Nozioni di base sullo schema del processo
La tabella seguente presenta una panoramica generale degli elementi principali usati per impostare la ricorrenza e la pianificazione di un processo:

| Nome JSON | Descrizione |
|:--- |:--- |
| **startTime** |Valore data-ora. Per le pianificazioni di base, **startTime** indica la prima occorrenza. Per le pianificazioni complesse, il processo viene attivato non prima del valore di **startTime**. |
| **recurrence** |Specifica le regole di ricorrenza per il processo e la ricorrenza in base alla quale viene eseguito il processo. L'oggetto recurrence supporta gli elementi **frequency**, **interval**, **endTime**, **count** e **schedule**. Se è definito l'oggetto **recurrence**, l'elemento **frequency** è obbligatorio. Gli altri elementi di **recurrence** sono facoltativi. |
| **frequency** |Stringa che rappresenta l'intervallo di frequenza con cui si ripete il processo. I valori supportati sono "minute", "hour", "day", "week" e "month". |
| **interval** |Numero intero positivo. L'elemento **interval** indica l'intervallo per il valore di **frequency** che determina la frequenza di esecuzione del processo. Se ad esempio **interval** è 3 e **frequency** è "week", il processo si ripete ogni tre settimane.<br /><br />L'Utilità di pianificazione supporta un elemento **interval** con un valore massimo di 18 mesi per la frequenza mensile, di 78 settimane per la frequenza settimanale o di 548 giorni per la frequenza giornaliera. Per le frequenze di tipo ora e minuto, l'intervallo supportato è 1 <= **interval** <= 1000. |
| **endTime** |Stringa che specifica il valore data-ora oltre il quale non viene eseguito il processo. Per **endTime** è possibile impostare un valore nel passato. Se **endTime** e **count** non sono specificati, il processo viene eseguito all'infinito. Non è possibile includere **endTime** e **count** nello stesso processo. |
| **count** |Numero intero positivo (maggiore di zero) che specifica il numero di volte in cui viene eseguito il processo prima del completamento.<br /><br />L'elemento **count** rappresenta il numero di volte in cui viene eseguito il processo prima di essere considerato completato. Ad esempio, nel caso di un processo giornaliero con un valore di **count** pari a 5 e lunedì come data di inizio, il completamento avviene dopo l'esecuzione del venerdì. Se la data di inizio è già passata, la prima esecuzione verrà calcolata dall'ora di creazione.<br /><br />Se non viene specificato un oggetto **endTime** o un oggetto **count**, il processo viene eseguito all'infinito. Non è possibile includere **endTime** e **count** nello stesso processo. |
| **schedule** |Un processo con una frequenza specificata modifica la sua ricorrenza in base a una pianificazione. Un valore di **schedule** contiene modifiche in base a minuti, ore, giorni della settimana, giorni del mese e numero della settimana. |

## <a name="job-schema-defaults-limits-and-examples"></a>Impostazioni predefinite, limiti ed esempi dello schema del processo
Ciascuno degli elementi seguenti verrà illustrato in dettaglio più avanti in questo articolo:

| Nome JSON | Tipo di valore | Obbligatorio? | Valore predefinito | Valori validi | Esempio |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** |stringa |No  |Nessuno |Date-ore ISO 8601 |`"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** |oggetto |No  |Nessuno |Oggetto recurrence |`"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **frequency** |stringa |Sì |Nessuno |"minute", "hour", "day", "week", "month" |`"frequency" : "hour"` |
| **interval** |numero |Sì |Nessuno |Da 1 a 1000 |`"interval":10` |
| **endTime** |stringa |No  |Nessuno |Valore data-ora che rappresenta un momento futuro |`"endTime" : "2013-02-09T09:30:00-08:00"` |
| **count** |numero |No  |Nessuno |>= 1 |`"count": 5` |
| **schedule** |oggetto |No  |Nessuno |Oggetto schedule |`"schedule" : { "minute" : [30], "hour" : [8,17] }` |

## <a name="deep-dive-starttime"></a>Approfondimenti: startTime
La tabella seguente descrive come **startTime** controlla la modalità di esecuzione di un processo:

| Valore startTime | Nessuna ricorrenza | Ricorrenza senza pianificazione | Ricorrenza con pianificazione |
|:--- |:--- |:--- |:--- |
| **Nessuna ora di inizio** |Eseguire una volta immediatamente. |Eseguire una volta immediatamente. Avviare le esecuzioni successive calcolate a partire dall'ora dell'ultima esecuzione. |Eseguire una volta immediatamente.<br /><br />Avviare le esecuzioni successive in base alla pianificazione di ricorrenza. |
| **Ora di inizio nel passato** |Eseguire una volta immediatamente. |Calcolare l'ora della prima esecuzione futura dopo l'ora di inizio e avviare l'esecuzione a tale ora.<br /><br />Avviare le esecuzioni successive calcolate a partire dall'ora dell'ultima esecuzione. <br /><br />Per altre informazioni, vedere l'esempio riportato dopo questa tabella. |Il processo inizia *non prima* dell'ora di inizio specificata. La prima occorrenza è basata sulla pianificazione calcolata dall'ora di inizio.<br /><br />Avviare le esecuzioni successive in base alla pianificazione di ricorrenza. |
| **Ora di inizio nel futuro o nel presente** |Eseguire una sola volta all'ora di inizio specificata. |Eseguire una sola volta all'ora di inizio specificata.<br /><br />Avviare le esecuzioni successive calcolate a partire dall'ora dell'ultima esecuzione.|Il processo inizia *non prima* dell'ora di inizio specificata. La prima occorrenza è basata sulla pianificazione, calcolata a partire dall'ora di inizio.<br /><br />Avviare le esecuzioni successive in base alla pianificazione di ricorrenza. |

Si esamini, ad esempio, uno scenario in cui il valore di **startTime** è nel passato, con ricorrenza, ma senza pianificazione.  Si supponga che l'ora corrente sia 2015-04-08 13:00, **startTime** sia 2015-04-07 14:00 e **recurrence** sia pari a una frequenza di due giorni, definita con **frequency**: day e **interval**: 2. È possibile notare che il valore di **startTime** è nel passato e quindi precede l'ora corrente.

In queste condizioni, la prima esecuzione avverrà il giorno 2015-04-09 alle 14:00. Il motore dell'Utilità di pianificazione calcola le occorrenze dall'ora di inizio dell'esecuzione. Vengono eliminate tutte le istanze in passato. Il motore utilizza l'istanza successiva che si verifica in futuro. In questo caso, il valore di **startTime** è 2015-04-07 14:00 e quindi l'istanza successiva viene eseguita due giorni dopo, ovvero il giorno 2015-04-09 alle 14:00.

È possibile notare che la prima esecuzione è la stessa indipendentemente dal fatto che il valore di **startTime** sia 2015-04-05 14:00 o 2015-04-01 14:00\. Dopo la prima esecuzione, le esecuzioni successive vengono calcolate in base alla pianificazione, ovvero il 2015-04-11 alle 14:00, quindi il 2015-04-13 alle 14:00, quindi il 2015-04-15 alle 14:00 e così via.

Infine, se nella pianificazione di un processo non sono specificati i minuti e le ore, per impostazione predefinita vengono usati i minuti e le ore della prima esecuzione.

## <a name="deep-dive-schedule"></a>Approfondimenti: schedule
È possibile usare **schedule** per *limitare* il numero di esecuzioni di un processo. Se ad esempio un processo con **frequency** "month" ha una pianificazione che prevede l'esecuzione solo il giorno 31, il processo viene eseguito solo nei mesi di 31 giorni.

L'oggetto **schedule** può essere usato anche per *espandere* il numero di esecuzioni di un processo. Se ad esempio un processo con **frequency** "month" prevede l'esecuzione nei giorni 1 e 2 del mese, il processo viene eseguito il primo e il secondo giorno del mese invece che una sola volta al mese.

Se si specificano più elementi di pianificazione, l'ordine di valutazione va dal più grande al più piccolo, ovvero numero della settimana, giorno del mese, giorno della settimana, ora e minuto.

La tabella seguente illustra in modo dettagliato gli elementi dell'oggetto schedule.

| Nome JSON | Descrizione | Valori validi |
|:--- |:--- |:--- |
| **minutes** |Minuti dell'ora in cui viene eseguito il processo. |Matrice di numeri interi. |
| **hours** |Ora del giorno in cui viene eseguito il processo. |Matrice di numeri interi. |
| **weekDays** |Giorni della settimana in cui viene eseguito il processo. Può essere specificato solo se la frequenza è settimanale. |Matrice di uno dei valori seguenti (la dimensione massima della matrice è 7):<br />- "Monday"<br />- "Tuesday"<br />- "Wednesday"<br />- "Thursday"<br />- "Friday"<br />- "Saturday"<br />- "Sunday"<br /><br />Non viene applicata la distinzione tra maiuscole e minuscole. |
| **monthlyOccurrences** |Determina in quali giorni del mese viene eseguito il processo. Può essere specificato solo con una frequenza mensile. |Matrice di oggetti **monthlyOccurrences**:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **day** indica il giorno della settimana in cui viene eseguito il processo. Ad esempio, *{Sunday}* corrisponde a ogni domenica del mese. Obbligatorio.<br /><br />**occurrence** indica l'occorrenza del giorno durante il mese. Ad esempio, *{Sunday, -1}* corrisponde all'ultima domenica del mese. Facoltativo. |
| **monthDays** |Giorno del mese in cui viene eseguito il processo. Può essere specificato solo con una frequenza mensile. |Matrice dei valori seguenti:<br />- Qualsiasi valore <= -1 e >= -31<br />- Qualsiasi valore >= 1 e <= 31|

## <a name="examples-recurrence-schedules"></a>Esempi: pianificazioni di ricorrenza
Gli esempi seguenti mostrano varie pianificazioni di ricorrenza, con particolare attenzione all'oggetto schedule e ai relativi sottoelementi.

In queste pianificazioni si presuppone che **interval** sia impostato su 1\. Negli esempi si presuppone inoltre l'uso di valori di **frequency** corretti nell'oggetto **schedule**. Non è ad esempio possibile usare un valore di **frequency** uguale a "day" e al tempo stesso avere una modifica **monthDays** in **schedule**. Queste restrizioni sono descritte in una sezione precedente di questo articolo.

| Esempio | Descrizione |
|:--- |:--- |
| `{"hours":[5]}` |Viene eseguito alle 05:00 ogni giorno.<br /><br />L'Utilità di pianificazione stabilisce una corrispondenza uno-a-uno tra ogni valore in "ore" e ogni valore in "minuti" per creare un elenco di tutte le occorrenze del processo. |
| `{"minutes":[15], "hours":[5]}` |Viene eseguito alle 05:15 ogni giorno. |
| `{"minutes":[15], "hours":[5,17]}` |Viene eseguito alle 05:15 e alle 17:15 ogni giorno. |
| `{"minutes":[15,45], "hours":[5,17]}` |Viene eseguito alle 05:15, alle 05:45, alle 17:15 e alle 17:45 ogni giorno. |
| `{"minutes":[0,15,30,45]}` |Viene eseguito ogni 15 minuti. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Viene eseguito ogni ora.<br /><br />Questo processo viene eseguito ogni ora. Il valore per i minuti viene controllato da **startTime**, se specificato. Se il valore di **startTime** non è specificato, i minuti vengono controllati dall'ora di creazione. Se ad esempio l'ora di inizio o l'ora di creazione (qualunque delle due sia applicabile) è 12:25, il processo viene eseguito alle 00:25, 01:25, 02:25, ..., 23:25.<br /><br />La pianificazione equivale a un processo con **frequency** "hour", **interval** 1 e nessun valore di **schedule**. È tuttavia possibile usare questa pianificazione con valori di **frequency** e **interval** diversi per creare altri processi. Se ad esempio il valore di **frequency** è "month", la pianificazione viene eseguita una sola volta al mese anziché ogni giorno, come accadrebbe se il valore di **frequency** fosse "day". |
| `{minutes:[0]}` |Viene eseguito ogni ora all'inizio dell'ora.<br /><br />Anche questo processo viene eseguito con frequenza oraria, ma all'inizio dell'ora, ovvero alle 24:00, 01:00, 02:00 e così via. La pianificazione equivale a un processo con **frequency** "hour", **startTime** pari a zero minuti e nessun oggetto **schedule**, se la frequenza è giornaliera. Se invece il valore di **frequency** è "week" o "month", la pianificazione viene eseguita rispettivamente una sola volta alla settimana o al mese. |
| `{"minutes":[15]}` |Viene eseguito 15 minuti dopo l'inizio di ogni ora,<br /><br />ovvero viene avviato alle 00:15 e prosegue con frequenza oraria alle 01:15, 02:15 e così via. Termina alle 23:15. |
| `{"hours":[17], "weekDays":["saturday"]}` |Viene eseguito alle 17:00 di ogni sabato. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Viene eseguito alle 17:00 di ogni lunedì, mercoledì e venerdì. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Viene eseguito alle 17:15 e alle 17:45 di ogni lunedì, mercoledì e venerdì. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Viene eseguito alle 05:00 e alle 17:00 di ogni lunedì, mercoledì e venerdì. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Viene eseguito alle 05:15, 5:45, 17:15 e 17:45 di ogni lunedì, mercoledì e venerdì. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Viene eseguito ogni 15 minuti nei giorni feriali. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Viene eseguito ogni 15 minuti nei giorni feriali tra le 09:00 e le 16:45. |
| `{"weekDays":["sunday"]}` |Viene eseguito ogni domenica all'ora di inizio. |
| `{"weekDays":["tuesday", "thursday"]}` |Viene eseguito ogni martedì e giovedì all'ora di inizio. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Viene eseguito alle 06:00 del ventottesimo giorno di ogni mese, supponendo che il valore di **frequency** sia "month". |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Viene eseguito alle 06:00 dell'ultimo giorno di ogni mese.<br /><br />Se si vuole eseguire un processo l'ultimo giorno del mese, usare -1 anziché il numero del giorno, ovvero 28, 29, 30 o 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Viene eseguito alle 06:00 del primo e dell'ultimo giorno di ogni mese. |
| `{monthDays":[1,-1]}` |Viene eseguito il primo e l'ultimo giorno di ogni mese all'ora di inizio. |
| `{monthDays":[1,14]}` |Viene eseguito il primo e il quattordicesimo giorno di ogni mese all'ora di inizio. |
| `{monthDays":[2]}` |Viene eseguito il secondo giorno di ogni mese all'ora di inizio. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Viene eseguito il primo venerdì di ogni mese alle 05:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Viene eseguito il primo venerdì di ogni mese all'ora di inizio. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Viene eseguito il terzultimo venerdì di ogni mese, all'ora di inizio. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Viene eseguito il primo e l'ultimo venerdì di ogni mese alle 05:15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Viene eseguito il primo e l'ultimo venerdì di ogni mese all'ora di inizio. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Viene eseguito il quinto venerdì di ogni mese all'ora di inizio.<br /><br />Se in un mese non sono inclusi cinque venerdì, il processo non viene eseguito. Se si vuole eseguire il processo l'ultimo venerdì del mese, è consigliabile usare -1 anziché 5. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Viene eseguito ogni 15 minuti l'ultimo venerdì del mese. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Viene eseguito alle 05:15, 05:45, 17:15 e 17:45 il terzo mercoledì di ogni mese. |

## <a name="see-also"></a>Vedere anche 

- [Che cos'è l'Utilità di pianificazione?](scheduler-intro.md)
- [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure](scheduler-concepts-terms.md)
- [Introduzione all'uso dell'Utilità di pianificazione di Azure nel portale di Azure](scheduler-get-started-portal.md)
- [Piani e fatturazione nell'utilità di pianificazione di Azure](scheduler-plans-billing.md)
- [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione di Azure](https://msdn.microsoft.com/library/mt629143)
- [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione di Azure](scheduler-powershell-reference.md)
- [Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione di Azure](scheduler-high-availability-reliability.md)
- [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione di Azure](scheduler-limits-defaults-errors.md)
- [Autenticazione in uscita dell'Utilità di pianificazione di Azure](scheduler-outbound-authentication.md)

