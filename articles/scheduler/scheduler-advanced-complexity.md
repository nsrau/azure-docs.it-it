---
title: Compilare pianificazioni di processo avanzate e ricorrenze - Utilità di pianificazione di Azure
description: Informazioni su come creare pianificazioni avanzate e ricorrenze per i processi in Utilità di pianificazione di Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.suite: infrastructure-services
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: a413261d251c8dfc1de9209168ee8137b85009f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531818"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Compilare pianificazioni avanzate e ricorrenze per i processi in Utilità di pianificazione di Azure

> [!IMPORTANT]
> [App per la logica di Azure](../logic-apps/logic-apps-overview.md) sostituirà Utilità di pianificazione di Azure di cui è in corso il ritiro. Per pianificare i processi, [provare App per la logica di Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

All'interno di un processo in [Utilità di pianificazione Azure](../scheduler/scheduler-intro.md), la pianificazione è la componente principale che determina come e quando il servizio Utilità di pianificazione esegue il processo. Con Utilità di pianificazione è possibile configurare più pianificazioni singole e ricorrenti per un processo. Le pianificazioni singole vengono eseguite una sola volta a un'ora specificata e sono essenzialmente pianificazioni ricorrenti eseguite una sola volta. Le pianificazioni ricorrenti vengono eseguite con una frequenza specificata. Grazie a questa flessibilità, è possibile usare l'Utilità di pianificazione per diversi scenari aziendali, ad esempio:

* **Pulire regolarmente i dati**: creare un processo giornaliero che elimina tutti i tweet più vecchi di tre mesi.

* **Archiviare i dati**: creare un processo mensile che esegue il push della cronologia delle fatture per un servizio di backup.

* **Richiedere dati esterni**: creare un processo che viene eseguito ogni 15 minuti ed esegue il pull di un nuovo report meteo da NOAA.

* **Elaborare immagini**: creare un processo dei giorni feriali che viene eseguito nelle ore non di punta e usa il cloud computing per la compressione delle immagini caricate durante la giornata.

Questo articolo descrive i processi di esempio che si possono creare usando l'Utilità di pianificazione e l'[API REST dell'Utilità di pianificazione di Azure](/rest/api/scheduler) e include la definizione JSON (JavaScript Object Notation) per ogni pianificazione. 

## <a name="supported-scenarios"></a>Scenari supportati

Questi esempi illustrano la gamma di scenari supportati dall'Utilità di pianificazione di Azure e spiegano come creare pianificazioni per vari modelli di comportamento, ad esempio:

* Singola esecuzione in una data e a un'ora specifiche.
* Esecuzione ricorrente per un numero specifico di volte.
* Esecuzione immediata e in seguito ricorrente.
* Esecuzione ricorrente ogni *n* minuti, ore, giorni, settimane o mesi, a partire da un momento specifico.
* Esecuzione ricorrente con frequenza settimanale o mensile, ma solo in giorni specifici della settimana o del mese.
* Esecuzione ricorrente più di una volta per un periodo specifico. Ad esempio, l'ultimo venerdì e l'ultimo lunedì di ogni mese oppure tutti i giorni alle 05:15 e alle 17:15.

In seguito, questo articolo descrive gli scenari in modo più dettagliato.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Creare pianificazione con l'API REST

Per creare una pianificazione di base con l'[API REST dell'Utilità di pianificazione di Azure](/rest/api/scheduler), seguire questa procedura:

1. Registrare la sottoscrizione di Azure con un provider di risorse usando il [Registro operazione - API REST di Resource Manager](https://docs.microsoft.com/rest/api/resources/providers). Il nome del provider per l'Utilità di pianificazione di Azure è **Utilità di pianificazione.Microsoft**. 

1. Creare una raccolta di processi usando l'[operazione Crea o aggiorna per le raccolte di processi](https://docs.microsoft.com/rest/api/scheduler/jobcollections) nell'API REST dell'Utilità di pianificazione. 

1. Creare un processo tramite l'[operazione Crea o aggiorna per i processi](https://docs.microsoft.com/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Elementi dello schema di processo

Questa tabella fornisce una panoramica generale degli elementi JSON principali che è possibile usare durante la configurazione di ricorrenze e pianificazioni per i processi. 

| Elemento | Obbligatorio | DESCRIZIONE | 
|---------|----------|-------------|
| **startTime** | No  | Valore di stringa DateTime nel [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) che specifica quando il processo inizia per le prima volta in una pianificazione di base. <p>Per le pianificazioni complesse, il processo viene attivato non prima del valore di **startTime**. | 
| **recurrence** | No  | Regole di ricorrenza per l'esecuzione del processo. L'oggetto **recurrence** supporta i seguenti elementi: **frequency**, **interval**, **schedule**, **count**, e **endTime**. <p>Se si usa l'elemento **recurrence**, è necessario usare anche l’elemento **frequency**, mentre altri elementi **recurrence** sono facoltativi. |
| **frequency** | Sì, quando si usa **recurrence** | L'unità di tempo tra le occorrenze supporta questi valori: "Minute", "Hour", "Day", "Week", "Month" e "Year" | 
| **interval** | No  | Un numero intero positivo che determina il numero di unità di tempo tra le occorrenze sulla base della **frequency**. <p>Se ad esempio **interval** è 10 e **frequency** è "Week", il processo si ripete ogni 10 settimane. <p>Di seguito il numero massimo di intervalli per ogni frequenza: <p>- 18 mesi <br>- 78 settimane <br>- 548 giorni <br>Per ore e minuti, l'intervallo è 1 <= <*interval*><= 1000. | 
| **schedule** | No  | Definisce le modifiche alla ricorrenza in base agli indicatori di minuti, ore, giorni della settimana e giorni del mese | 
| **count** | No  | Numero intero positivo che specifica il numero di volte in cui viene eseguito il processo prima del completamento. <p>Ad esempio, quando il **count** di un processo giornaliero è impostato su 7, e la data di inizio è lunedì, il processo viene completato di domenica. Se la data di inizio è già passata, la prima esecuzione verrà calcolata dall'ora di creazione. <p>Senza **endTime** o un **count**, il processo viene eseguito all'infinito. Non è possibile usare sia **count** che **endTime** in uno stesso processo, ma viene applicata la regola che termina per prima. | 
| **endTime** | No  | Valore di stringa Date o DateTime nel [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) che specifica quando avviene l'arresto del processo. Per **endTime** è possibile impostare un valore nel passato. <p>Senza **endTime** o un **count**, il processo viene eseguito all'infinito. Non è possibile usare sia **count** che **endTime** in uno stesso processo, ma viene applicata la regola che termina per prima. |
|||| 

Ad esempio, questo schema JSON descrive una pianificazione di base e la ricorrenza di un processo: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z", 
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]                      
      },
      "count": 10,       
      "endTime": "2012-11-04"
   },
},
``` 

*Valori di Date and DateTime*

* Le date nei processi dell'Utilità di pianificazione di Azure includono solo la data e seguono la [specifica ISO-8601](https://en.wikipedia.org/wiki/ISO_8601).

* Data e ora nei processi dell'Utilità di pianificazione includono sia data che ora, seguono la [specifica ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e si presuppone che siano in formato UTC quando non viene specificata alcuna differenza dall'ora UTC. 

Per altre informazioni, vedere [Concetti, terminologia ed entità](../scheduler/scheduler-concepts-terms.md).

<a name="start-time"></a>

## <a name="details-starttime"></a>Dettagli: startTime

La tabella seguente descrive come **startTime** controlla la modalità di esecuzione di un processo:

| startTime | Nessuna ricorrenza | Ricorrenza senza pianificazione | Ricorrenza con pianificazione |
|-----------|---------------|-------------------------|--------------------------|
| **Nessuna ora di inizio** | Eseguire una volta immediatamente. | Eseguire una volta immediatamente. Avviare le esecuzioni successive calcolate a partire dall'ora dell'ultima esecuzione. | Eseguire una volta immediatamente. Avviare le esecuzioni successive in base alla pianificazione di ricorrenza. | 
| **Ora di inizio nel passato** | Eseguire una volta immediatamente. | Calcolare l'ora della prima esecuzione futura dopo l'ora di inizio e avviare l'esecuzione a tale ora. <p>Avviare le esecuzioni successive calcolate a partire dall'ora dell'ultima esecuzione. <p>Vedere l'esempio riportato dopo la tabella. | Avviare il processo *non prima* dell'ora di inizio specificata. La prima occorrenza è basata sulla pianificazione calcolata dall'ora di inizio. <p>Avviare le esecuzioni successive in base alla pianificazione di ricorrenza. | 
| **Ora di inizio nel futuro o nel presente** | Eseguire una sola volta all'ora di inizio specificata. | Eseguire una sola volta all'ora di inizio specificata. <p>Avviare le esecuzioni successive calcolate a partire dall'ora dell'ultima esecuzione. | Avviare il processo *non prima* dell'ora di inizio specificata. La prima occorrenza è basata sulla pianificazione, calcolata a partire dall'ora di inizio. <p>Avviare le esecuzioni successive in base alla pianificazione di ricorrenza. |
||||| 

Si supponga che l'esempio presenti le condizioni seguenti: un'ora di inizio nel passato con una ricorrenza, ma nessuna pianificazione.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* Data e ora correnti sono 8 aprile 2015 alle 13:00.

* La data e l'ora di inizio sono 7 aprile 2015 alle 14:00, cioè prima della data e ora correnti.

* La ricorrenza è ogni due giorni.

1. In queste condizioni, la prima esecuzione avviene il 9 aprile 2015 alle 14:00. 

   L'Utilità di pianificazione calcola le occorrenze di esecuzione in base all'ora di inizio, rimuove tutte le istanze del passato e usa l'istanza successiva in futuro. 
   In questo caso, il valore di **startTime** corrisponde al 7 aprile 2015 alle 14:00 e quindi l'istanza successiva viene eseguita due giorni dopo, ovvero il giorno 9 aprile 2015 alle 14:00.

   Si noti che la prima esecuzione è la stessa sia che il valore di **startTime** sia 05/04/2017 14:00 o 01/04/2017 14:00. Dopo la prima esecuzione, le esecuzioni successive vengono calcolate in base alla pianificazione. 
   
1. Le esecuzioni seguono in questo ordine: 
   
   1. 11/04/2015 alle 14:00
   1. 13/04/2015 alle 14:00 
   1. 15/04/2015 alle 14:00
   1. E così via.

1. Infine, se nella pianificazione di un processo non sono specificati i minuti e le ore, per impostazione predefinita vengono usati i minuti e le ore della prima esecuzione.

<a name="schedule"></a>

## <a name="details-schedule"></a>Dettagli: pianificazione

È possibile usare **schedule** per *limitare* il numero di esecuzioni di un processo. Se ad esempio un processo con **frequency** "month" ha una pianificazione che prevede l'esecuzione solo il giorno 31, il processo viene eseguito solo nei mesi di 31 giorni.

L'oggetto **schedule** può essere usato anche per *espandere* il numero di esecuzioni di un processo. Se ad esempio un processo con **frequency** "month" prevede l'esecuzione nei giorni 1 e 2 del mese, il processo viene eseguito il primo e il secondo giorno del mese invece che una sola volta al mese.

Se si specifica più di un elemento di pianificazione, l'ordine di valutazione va dal più grande al più piccolo, ovvero numero della settimana, giorno del mese, giorno della settimana, ora e minuto.

La tabella seguente illustra in modo dettagliato gli elementi dell'oggetto schedule.

| Nome JSON | DESCRIZIONE | Valori validi |
|:--- |:--- |:--- |
| **minutes** |Minuti dell'ora in cui viene eseguito il processo. |Matrice di numeri interi. |
| **hours** |Ora del giorno in cui viene eseguito il processo. |Matrice di numeri interi. |
| **weekDays** |Giorni della settimana in cui viene eseguito il processo. Può essere specificato solo se la frequenza è settimanale. |Matrice di uno dei valori seguenti (la dimensione massima della matrice è 7):<br />- "Monday"<br />- "Tuesday"<br />- "Wednesday"<br />- "Thursday"<br />- "Friday"<br />- "Saturday"<br />- "Sunday"<br /><br />Non viene applicata la distinzione tra maiuscole e minuscole. |
| **monthlyOccurrences** |Determina in quali giorni del mese viene eseguito il processo. Può essere specificato solo con una frequenza mensile. |Matrice di oggetti **monthlyOccurrences**:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **day** indica il giorno della settimana in cui viene eseguito il processo. Ad esempio, *{Sunday}* corrisponde a ogni domenica del mese. Richiesto.<br /><br />**occurrence** indica l'occorrenza del giorno durante il mese. Ad esempio, *{Sunday, -1}* corrisponde all'ultima domenica del mese. facoltativo. |
| **monthDays** |Giorno del mese in cui viene eseguito il processo. Può essere specificato solo con una frequenza mensile. |Matrice dei valori seguenti:<br />- Qualsiasi valore <= -1 e >= -31<br />- Qualsiasi valore >= 1 e <= 31|

## <a name="examples-recurrence-schedules"></a>Esempi: pianificazioni di ricorrenza

Gli esempi seguenti mostrano varie pianificazioni di ricorrenza, con particolare attenzione all'oggetto schedule e ai relativi sottoelementi.

In queste pianificazioni si presuppone che **interval** sia impostato su 1\. Negli esempi si presuppone inoltre l'uso di valori di **frequency** corretti nell'oggetto **schedule**. Non è ad esempio possibile usare un valore di **frequency** uguale a "day" e al tempo stesso avere una modifica **monthDays** in **schedule**. Queste restrizioni sono descritte in una sezione precedente di questo articolo.

| Esempio | DESCRIZIONE |
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
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Viene eseguito alle 6 il ventottesimo giorno di ogni mese (presumendo che **frequency** sia "month"). |
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

* [Informazioni su Utilità di pianificazione di Azure](scheduler-intro.md)
* [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure](scheduler-concepts-terms.md)
* [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione di Azure](scheduler-limits-defaults-errors.md)
