<properties 
	pageTitle="Funzioni e variabili di sistema di Data Factory | Microsoft Azure" 
	description="Fornisce un elenco delle funzioni e delle variabili di sistema di Azure Data Factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"
	services="data-factory"
/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/25/2016" 
	ms.author="spelluru"/>

# Azure Data Factory - Funzioni e variabili di sistema
In questo articolo vengono fornite informazioni sulle funzioni e le variabili supportate da Azure Data Factory.
  
## Variabili di sistema di Data Factory

Nome variabile | Descrizione | Ambito dell'oggetto | Ambito JSON e casi d'uso
------------- | ----------- | ------------ | ------------------------
WindowStart | Inizio dell'intervallo di tempo relativo alla finestra di esecuzione dell'attività | attività | <ol><li>Definizione delle query di selezione dei dati. Vedere gli articoli connettore a cui fa riferimento l'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md).</li><li>Passaggio dei parametri a uno script Hive (esempio illustrato in precedenza).</li>
WindowEnd | Fine dell'intervallo di tempo relativo alla finestra di esecuzione dell'attività | attività | come sopra
SliceStart | Inizio dell'intervallo di tempo relativo alla sezione di dati in fase di produzione | set di dati<br/>attività | <ol><li>Definizione di nomi di file e percorsi di cartelle dinamici durante l'utilizzo dell'[archivio BLOB di Azure](data-factory-azure-blob-connector.md) e dei [set di dati del file system](data-factory-onprem-file-system-connector.md).</li><li>Definizione delle dipendenze di input con le funzioni della data factory nella raccolta di input dell'attività.</li></ol>
SliceEnd | Fine dell'intervallo di tempo relativo alla sezione di dati in fase di produzione | set di dati<br/>attività | come sopra. 

> [AZURE.NOTE] La data factory richiede attualmente che la pianificazione specificata nell'attività corrisponda esattamente alla pianificazione specificata nella disponibilità del set di dati di output. In questo modo, il mapping delle variabili WindowStart, WindowEnd, SliceStart e SliceEnd verrà sempre eseguito allo stesso periodo di tempo e a un'unica sezione di output.
 
## Funzioni di Data Factory 

È possibile combinare le funzioni della data factory con le variabili di sistema sopra illustrate per gli scopi seguenti:

1.	Definizione delle query di selezione dei dati (vedere gli articoli connettore a cui fa riferimento l'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md).

	La sintassi per chiamare una funzione della data factory è **$$<function>** per le query di selezione dei dati e altre proprietà dell'attività e del set di dati.
2. Definizione delle dipendenze di input con le funzioni della data factory nella raccolta di input dell'attività (vedere l'esempio precedente).

	La sintassi $$ non è necessaria per definire le espressioni delle dipendenze di input.

Nell'esempio seguente la proprietà **sqlReaderQuery** di un file JSON è assegnata a un valore restituito dalla funzione **Text.Format**. Questo esempio usa anche una variabile di sistema denominata **WindowStart**, che rappresenta l'ora di inizio della finestra di esecuzione dell'attività.
	
	{
	    "Type": "SqlSource",
	    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
	}

### Funzioni

Le tabelle seguenti elencano tutte le funzioni di Data factory di Azure.

Categoria | Funzione | Parametri | Descrizione
-------- | -------- | ---------- | ----------- 
Time | AddHours(X,Y) | X: DateTime <br/><br/>Y: int | Aggiunge Y ore all'ora X specificata. <br/><br/>Esempio: 05/09/2013 12:00:00 + 2 ore = 05/09/2013 14:00:00
Time | AddMinutes(X,Y) | X: DateTime <br/><br/>Y: int | Aggiunge Y minuti a X.<br/><br/>Esempio: 15/09/2013 12:00:00 + 15 minuti = 15/09/2013 12:15:00
Time | StartOfHour(X) | X: DateTime | Ottiene l'ora di inizio per l'ora rappresentata dal componente ora di X. <br/><br/>Esempio: StartOfHour di 15/09/2013 17:10:23 è 15/09/2013 17:00:00
Date | AddDays(X,Y) | X: DateTime<br/><br/>Y: int | Aggiunge Y giorni a X.<br/><br/>Esempio: 15/09/2013 12:00:00 + 2 giorni = 17/09/2013 12:00:00
Date | AddMonths(X,Y) | X: DateTime<br/><br/>Y: int | Aggiunge Y mesi a X.<br/><br/>Esempio: 15/09/2013 12:00:00 + 1 mese = 15/10/2013 12:00:00 
Date | AddQuarters(X,Y) | X: DateTime <br/><br/>Y: int | Aggiunge Y * 3 mesi a X.<br/><br/>Esempio: 15/09/2013 12:00:00 + 1 trimestre = 15/12/2013 12:00:00
Date | AddWeeks(X,Y) | X: DateTime<br/><br/>Y: int | Aggiunge Y * 7 giorni a X.<br/><br/>Esempio: 15/09/2013 12:00:00 + 1 settimana = 22/09/2013 12:00:00
Date | AddYears(X,Y) | X: DateTime<br/><br/>Y: int | Aggiunge Y anni a X.<br/><br/>Esempio: 15/09/2013 12:00:00 + 1 anno = 15/09/2014 12:00:00
Date | Day(X) | X: DateTime | Ottiene il componente giorno di X.<br/><br/>Esempio: Day di 15/09/2013 12:00:00 è 9. 
Date | DayOfWeek(X) | X: DateTime | Ottiene il giorno del componente settimana di X.<br/><br/>Esempio: DayOfWeek di 15/09/2013 12:00:00 è domenica.
Date | DayOfYear(X) | X: DateTime | Ottiene il giorno dell'anno rappresentato dal componente anno di X.<br/><br/>Esempi:<br/>01/12/2015: giorno 335 di 2015<br/>31/12/2015: giorno 365 di 2015<br/>31/12/2016: giorno 366 di 2016 (anno bisestile)
Date | DaysInMonth(X) | X: DateTime | Ottiene i giorni del mese rappresentati dal componente mese del parametro X.<br/><br/>Esempio: DaysInMonth di 15/09/2013 corrisponde a 30 perché sono presenti 30 giorni nel mese di settembre.
Date | EndOfDay(X) | X: DateTime | Ottiene la data e ora che rappresenta la fine del giorno (componente giorno) X.<br/><br/>Esempio: EndOfDay di 15/09/2013 17:10:23 è 15/09/2013 23:59:59.
Date | EndOfMonth(X) | X: DateTime | Ottiene la fine del mese rappresentato dal componente mese del parametro X. <br/><br/>Esempio: EndOfMonth di 15/09/2013 17:10:23 è 30/09/2013 23:59:59 (data e ora che rappresentano la fine del mese di settembre)
Date | StartOfDay(X) | X: DateTime | Ottiene l'inizio del giorno rappresentato dal componente giorno del parametro X.<br/><br/>Esempio: StartOfDay di 15/09/2013 17:10:23 è 15/09/2013 00:00:00.
DateTime | From(X) | X: String | Analizza la stringa X fino a una data/ora.
DateTime | Ticks(X) | X: DateTime | Ottiene la proprietà dei tick del parametro X. Un tick equivale a 100 nanosecondi. Il valore di questa proprietà rappresenta il numero di tick trascorsi dalla mezzanotte 00.00.00 del 1ª gennaio 0001. 
Text | Format(X) | X: variabile stringa | Formatta il testo.

#### Esempio di Text.Format

	"defines": { 
	    "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
	    "Month" : "$$Text.Format('{0:MM}',WindowStart)",
	    "Day" : "$$Text.Format('{0:dd}',WindowStart)",
	    "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
	}

Vedere l'argomento [Stringhe di formato di data e ora personalizzato](https://msdn.microsoft.com/library/8kb3ddd4.aspx) che descrive diverse opzioni di formattazione che è possibile usare, ad esempio: AA e aaaa.

> [AZURE.NOTE] Quando si usa una funzione all'interno di un'altra funzione, non è necessario usare il prefisso **$$** per la funzione interna. Ad esempio: $$Text.Format('PartitionKey eq \\'my\_pkey\_filter\_value\\' and RowKey ge \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). In questo esempio, il prefisso **$$** non viene usato per la funzione **Time.AddHours**.
  

<!---HONumber=AcomDC_0727_2016-->