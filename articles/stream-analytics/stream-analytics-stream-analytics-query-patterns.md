<properties
	pageTitle="Modelli di query di Analisi di flusso di Azure | Microsoft Azure"
	description="Modelli di query comuni di Analisi di flusso di Azure"
	keywords="analisi di flusso, esempio, query, linguaggio, guida, modelli"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="11/23/2015"
	ms.author="jeffstok"/>


# Modelli di query comuni di Analisi di flusso di Azure  #

## Introduzione ##
Le query in Analisi di flusso di Azure sono espresse in un linguaggio di query simile a SQL, documentato [qui](https://msdn.microsoft.com/library/azure/dn834998.aspx). Questo documento descrive soluzioni per vari modelli di query comuni basati su scenari reali. È un lavoro in corso che continuerà a essere aggiornato con nuovi modelli su base continuativa.

## Nozioni di base ##

## Conversioni di tipi di dati ##
**Descrizione**: definire i tipi delle proprietà nel flusso di input. Ad esempio, il peso dell'auto è immesso nel flusso di input come stringa e deve essere convertito in INT per eseguire SUM dei vari valori.

**Input**:

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | "1000" |
| Honda | 2015-01-01T00:00:02.0000000Z | "2000" |

**Output**:

| Casa automobilistica | Peso |
| --- | --- |
| Honda | 3000 |

**Soluzione**:

	SELECT
    	Make,
    	SUM(CAST(Weight AS BIGINT)) AS Weight
	FROM
    	Input TIMESTAMP BY Time
	GROUP BY
		Make,
    	TumblingWindow(second, 10)

**Spiegazione**: usare un'istruzione CAST nel campo Peso per specificarne il tipo (vedere l'elenco dei tipi di dati supportati [qui](https://msdn.microsoft.com/library/azure/dn835065.aspx)).

## Uso di Like/Not like per la corrispondenza dei modelli ##
**Descrizione**: verificare che un valore del campo dell'evento corrisponda a un determinato modello. Ad esempio, restituire le targhe che iniziano per A e terminano con 9.

**Input**:

| Casa automobilistica | Targa | Tempo |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC-369 | 2015-01-01T00:00:03.0000000Z |

**Output**:

| Casa automobilistica | Targa | Tempo |
| --- | --- | --- |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC-369 | 2015-01-01T00:00:03.0000000Z |

**Soluzione**:

	SELECT
    	*
	FROM
    	Input TIMESTAMP BY Time
	WHERE
    	LicensePlate LIKE 'A%9'

**Spiegazione**: usare l'istruzione LIKE per verificare che il valore del campo LicensePlate inizi con la lettera A, contenga una stringa di zeri o altri caratteri e termini con 9.

## Specificare la logica per i diversi casi/valori (istruzioni CASE) ##
**Descrizione**: fornire calcoli differenti per un campo in base ad alcuni criteri. Ad esempio, fornire una stringa descrittiva per il numero di auto passate della stessa casa automobilistica, con un caso speciale per 1.

**Input**:

| Casa automobilistica | Tempo |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output**:

| Auto passate | Tempo |
| --- | --- | --- |
| 1 Honda | 2015-01-01T00:00:10.0000000Z |
| 2 Toyota | 2015-01-01T00:00:10.0000000Z |

**Soluzione**:

    SELECT
    	CASE
			WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
			ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
		END AS CarsPassed,
		System.TimeStamp AS Time
	FROM
		Input TIMESTAMP BY Time
	GROUP BY
		Make,
		TumblingWindow(second, 10)

**Spiegazione**: la clausola CASE consente di fornire un calcolo diverso in base ad alcuni criteri (in questo esempio, il numero di automobili nella finestra di aggregazione).

## Invio di dati a più output ##
**Descrizione**: inviare dati a più destinazioni di output da un singolo processo. Ad esempio, analizzare i dati per un avviso di soglia e archiviare tutti gli eventi nell'archivio blob.

**Input**:

| Casa automobilistica | Tempo |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output1**:

| Casa automobilistica | Tempo |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output2**:

| Casa automobilistica | Tempo | Numero |
| --- | --- | --- |
| Toyota | 2015-01-01T00:00:10.0000000Z | 3 |

**Soluzione**:

	SELECT
		*
	INTO
		ArchiveOutput
	FROM
		Input TIMESTAMP BY Time

	SELECT
		Make,
		System.TimeStamp AS Time,
		COUNT(*) AS [Count]
	INTO
		AlertOutput
	FROM
		Input TIMESTAMP BY Time
	GROUP BY
		Make,
		TumblingWindow(second, 10)
	HAVING
		[Count] >= 3

**Spiegazione**: la clausola INTO indica all'analisi di flusso in quali output scrivere i dati ottenuti con questa istruzione. La prima è una query pass-through dei dati ricevuti per un output denominato ArchiveOutput. La seconda query effettua una semplice aggregazione, filtra e invia i risultati a un sistema di avviso downstream. *Nota*: è inoltre possibile riutilizzare i risultati delle CTE (vale a dire le istruzioni WITH) in più istruzioni di output; questo ha l'ulteriore vantaggio di aprire ad esempio un numero inferiore di lettori nell'origine di input.

	WITH AllRedCars AS (
		SELECT
			*
		FROM
			Input TIMESTAMP BY Time
		WHERE
			Color = 'red'
	)
	SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
	SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## Modelli ##

## Conteggio di valori univoci
**Descrizione**: contare i valori di campo univoci presenti nel flusso all'interno di una finestra temporale. Ad esempio, il numero di auto di una stessa casa automobilistica passate da un casello autostradale in una finestra di 2 secondi.

**Input**:

| Casa automobilistica | Tempo |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output:**

| Numero | Tempo |
| --- | --- |
| 2 | 2015-01-01T00:00:02.000Z |
| 1 | 2015-01-01T00:00:04.000Z |

**Soluzione:**

	WITH Makes AS (
	    SELECT
	        Make,
	        COUNT(*) AS CountMake
	    FROM
	        Input TIMESTAMP BY Time
	    GROUP BY
	          Make,
	          TumblingWindow(second, 2)
	)
	SELECT
	    COUNT(*) AS Count,
	    System.TimeStamp AS Time
	FROM
	    Makes
	GROUP BY
	    TumblingWindow(second, 1)


**Spiegazione:** viene effettuata un'aggregazione iniziale per ottenere le case automobilistiche univoche con il relativo conteggio nell'arco della finestra temporale. Viene quindi effettuata un'aggregazione del numero di case automobilistiche ottenute; ammesso che tutti i valori univoci di una finestra temporale ottengano lo stesso timestamp, la seconda finestra di aggregazione deve essere minimale per non aggregare 2 finestre ottenute dal primo passaggio.

## Determinare la potenziale variazione di un valore ##
**Descrizione**: esaminare un valore precedente per determinarne la potenziale variazione rispetto al valore corrente. Ad esempio, l'auto passata in precedenza dal casello autostradale è della stessa casa automobilistica dell'auto corrente?

**Input**:

| Casa automobilistica | Tempo |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Output**:

| Casa automobilistica | Tempo |
| --- | --- |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Soluzione**:

	SELECT
		Make,
		Time
	FROM
		Input TIMESTAMP BY Time
	WHERE
		LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Spiegazione**: usare LAG per esaminare il flusso di input di un evento precedente e ottenere il valore Casa automobilistica. Quindi confrontarlo con il valore Casa automobilistica dell'evento corrente per restituire l'evento di variazione.

## Individuazione del primo evento in una finestra ##
**Descrizione**: individuare la prima auto in ogni intervallo di 10 minuti?

**Input**:

| Targa | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 | Honda | 27-07-2015T00:00:05.0000000Z |
| YZK 5704 | Ford | 27-07-2015T00:02:17.0000000Z |
| RMV 8282 | Honda | 27-07-2015T00:05:01.0000000Z |
| YHN 6970 | Toyota | 27-07-2015T00:06:00.0000000Z |
| VFE 1616 | Toyota | 27-07-2015T00:09:31.0000000Z |
| QYF 9358 | Honda | 27-07-2015T00:12:02.0000000Z |
| MDR 6128 | BMW | 27-07-2015T00:13:45.0000000Z |

**Output**:

| Targa | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 | Honda | 27-07-2015T00:00:05.0000000Z |
| QYF 9358 | Honda | 27-07-2015T00:12:02.0000000Z |

**Soluzione**:

	SELECT 
		LicensePlate,
		Make,
		Time
	FROM 
		Input TIMESTAMP BY Time
	WHERE 
		IsFirst(minute, 10) = 1

Ridefinire il problema e trovare la prima auto di una particolare casa automobilistica a intervalli di 10 minuti.

| Targa | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 | Honda | 27-07-2015T00:00:05.0000000Z |
| YZK 5704 | Ford | 27-07-2015T00:02:17.0000000Z |
| YHN 6970 | Toyota | 27-07-2015T00:06:00.0000000Z |
| QYF 9358 | Honda | 27-07-2015T00:12:02.0000000Z |
| MDR 6128 | BMW | 27-07-2015T00:13:45.0000000Z |

**Soluzione**:

	SELECT 
		LicensePlate,
		Make,
		Time
	FROM 
		Input TIMESTAMP BY Time
	WHERE 
		IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## Individuare l'ultimo evento in una finestra ##
**Descrizione**: individuare l'ultima auto in ogni intervallo di 10 minuti.

**Input**:

| Targa | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 | Honda | 27-07-2015T00:00:05.0000000Z |
| YZK 5704 | Ford | 27-07-2015T00:02:17.0000000Z |
| RMV 8282 | Honda | 27-07-2015T00:05:01.0000000Z |
| YHN 6970 | Toyota | 27-07-2015T00:06:00.0000000Z |
| VFE 1616 | Toyota | 27-07-2015T00:09:31.0000000Z |
| QYF 9358 | Honda | 27-07-2015T00:12:02.0000000Z |
| MDR 6128 | BMW | 27-07-2015T00:13:45.0000000Z |

**Output**:

| Targa | Casa automobilistica | Tempo |
| --- | --- | --- |
| VFE 1616 | Toyota | 27-07-2015T00:09:31.0000000Z |
| MDR 6128 | BMW | 27-07-2015T00:13:45.0000000Z |

**Soluzione**:

	WITH LastInWindow AS
	(
		SELECT 
			MAX(Time) AS LastEventTime
		FROM 
			Input TIMESTAMP BY Time
		GROUP BY 
			TumblingWindow(minute, 10)
	)
	SELECT 
		Input.LicensePlate,
		Input.Make,
		Input.Time
	FROM
		Input TIMESTAMP BY Time 
		INNER JOIN LastInWindow
		ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
		AND Input.Time = LastInWindow.LastEventTime

**Spiegazione**: nella query esistono due passaggi: il primo rileva il timestamp più recente in finestre di 10 minuti. Il secondo passaggio unisce i risultati della prima query con il flusso originale per trovare gli eventi corrispondenti ai timestamp più recenti in ogni finestra.

## Rilevare l'assenza di eventi ##
**Descrizione**: verificare che in un flusso non sia presente alcun valore corrispondente a determinati criteri. Ad esempio, 2 automobili consecutive della stessa casa automobilistica entrate in autostrada nell'arco di 90 secondi.

**Input**:

| Casa automobilistica | Targa | Tempo |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Honda | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Toyota | DEF-987 | 2015-01-01T00:00:03.0000000Z |
| Honda | GHI-345 | 2015-01-01T00:00:04.0000000Z |

**Output**:

| Casa automobilistica | Tempo | Targa auto corrente | Targa prima auto | Tempo prima auto |
| --- | --- | --- | --- | --- |
| Honda | 2015-01-01T00:00:02.0000000Z | AAA-999 | ABC-123 | 2015-01-01T00:00:01.0000000Z |

**Soluzione**:

	SELECT
	    Make,
	    Time,
	    LicensePlate AS CurrentCarLicensePlate,
	    LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
	    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
	FROM
	    Input TIMESTAMP BY Time
	WHERE
	    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**Spiegazione**: usare LAG per esaminare il flusso di input di un evento precedente e ottenere il valore Casa automobilistica. Quindi confrontare tale valore con il valore Casa automobilistica dell'evento corrente e ottenere l'evento di eventuale corrispondenza, quindi usare LAG per ottenere dati sull'auto precedente.

## Rilevare la durata di una condizione ##
**Descrizione**: scoprire per quanto tempo si è verificata una condizione. Ad esempio, si supponga un bug che ha generato un peso errato per tutte le automobili (oltre 20.000 libbre); si desidera calcolare la durata del bug.

**Input**:

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | 2000 |
| Toyota | 2015-01-01T00:00:02.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:03.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:04.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:05.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:06.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:07.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:08.0000000Z | 2000 |

**Output**:

| Inizio errore | Fine errore | Durata errore in secondi |
| --- | --- | --- |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |

**Soluzione**:

	SELECT
	    PrevGood.Time AS StartFault,
	    ThisGood.Time AS Endfault,
	    DATEDIFF(second, PrevGood.Time, ThisGood.Time) AS FaultDuraitonSeconds
	FROM
	    Input AS ThisGood TIMESTAMP BY Time
	    INNER JOIN Input AS PrevGood TIMESTAMP BY Time
	    ON DATEDIFF(second, PrevGood, ThisGood) BETWEEN 1 AND 3600
	    AND PrevGood.Weight < 20000
	    INNER JOIN Input AS Bad TIMESTAMP BY Time
	    ON DATEDIFF(second, PrevGood, Bad) BETWEEN 1 AND 3600
	    AND DATEDIFF(second, Bad, ThisGood) BETWEEN 1 AND 3600
	    AND Bad.Weight >= 20000
	    LEFT JOIN Input AS MidGood TIMESTAMP BY Time
	    ON DATEDIFF(second, PrevGood, MidGood) BETWEEN 1 AND 3600
	    AND DATEDIFF(second, MidGood, ThisGood) BETWEEN 1 AND 3600
	    AND MidGood.Weight < 20000
	WHERE
	    ThisGood.Weight < 20000
	    AND MidGood.Weight IS NULL

**Spiegazione**: si ricercano 2 eventi corretti con un evento intermedio non valido e senza un evento intermedio valido a indicare che i 2 eventi sono i primi eventi precedente e successivo ad almeno 1 evento non valido. Ottenere 2 eventi validi con 1 evento intermedio non valido è semplice utilizzando 2 JOIN e verificando di aver ottenuto: valido -> non valido -> valido tramite il controllo del peso e il confronto dei timestamp.

Avvalendosi di quanto appreso su "LEFT Outer Join per includere valori NULL o per rilevare l’assenza di eventi", è possibile controllare che non si siano verificati eventi validi tra i 2 eventi validi esaminati.

L'insieme restituisce valido -> non valido -> valido senza altri eventi non validi intermedi. A questo punto è possibile calcolare la durata tra l'inizio e la fine degli eventi validi ottenendo la durata del bug.

## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics)

## Passaggi successivi

- [Introduzione ad Analisi di flusso di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi di flusso di Azure](../stream.analytics.get.started.md)
- [Ridimensionare i processi di Analisi di flusso di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=AcomDC_1125_2015-->