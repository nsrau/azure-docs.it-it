<properties
   pageTitle="Distribuzione hash e relativo effetto sulle prestazioni delle query in SQL Data Warehouse | Microsoft Azure"
   description="Informazioni sulle tabelle con distribuzione hash e relativa influenza sulle prestazioni delle query in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Distribuzione hash e relativo effetto sulle prestazioni delle query in SQL Data Warehouse

Prendere decisioni intelligenti per la distribuzione hash è uno dei modi più importanti per migliorare le prestazioni delle query.

I fattori principali sono infatti tre:

1. Ridurre al minimo lo spostamento dei dati.
2. Evitare differenze dei dati.
3. Fornire un'esecuzione bilanciata.

## Ridurre al minimo lo spostamento dei dati
In genere lo spostamento dei dati si verifica quando le tabelle vengono unite in join o vengono eseguite aggregazioni nelle tabelle. La distribuzione hash di tabelle con una chiave condivisa è uno dei metodi più efficaci per ridurre al minimo lo spostamento.

Perché la distribuzione hash riduca efficacemente al minimo lo spostamento, devono essere rispettati tutti i criteri seguenti:

1. Entrambe le tabelle devono essere distribuite con il metodo hash e unite in join con la chiave di distribuzione condivisa.
2. I tipi di dati di entrambe le colonne devono corrispondere.
3. Le colonne da unire devono essere equivalenti, ovvero i valori nella colonna della tabella sinistra devono essere uguali ai valori nella colonna della tabella destra.
4. Il join **non** è un `CROSS JOIN`.

> [AZURE.NOTE] Le colonne usate nelle clausole `JOIN`, `GROUP BY`, `DISTINCT` e `HAVING` sono ottimi canditati per la colonna HASH. Le colonne nella clausola `WHERE` **non** sono invece consigliabili per la colonna Hash. Vedere di seguito la sezione relativa all'esecuzione bilanciata.

Lo spostamento dei dati può anche dipendere dalla sintassi della query (`COUNT DISTINCT` e la clausola `OVER` sono ottimi esempi), quando viene usata con colonne che non includono la chiave di distribuzione hash.

> [AZURE.NOTE] Le tabelle round robin generano di solito uno spostamento di dati. I dati nella tabella sono stati allocati in modo non deterministico e quindi devono essere spostati prima del completamento della maggior parte delle query.

## Evitare differenze dei dati
Perché la distribuzione hash sia efficace, è importante che le colonne scelte abbiano le proprietà seguenti:

1. La colonna contiene un numero significativo di valori distinct.
2. La colonna non è soggetta a **differenze dei dati**.

Ogni valore distinct verrà allocato a una distribuzione. Di conseguenza, i dati richiederanno un numero ragionevole di valori distinct per assicurare la generazione di una quantità sufficiente di valori hash univoci. In caso contrario si potrebbero ottenere hash di scarsa qualità. Se ad esempio il numero di distribuzioni supera il numero di valori distinct, alcune distribuzioni saranno lasciate vuote, influendo negativamente sulle prestazioni.

In modo analogo, se tutte le righe per la colonna con hash contengono lo stesso valore, si dice che i dati **presentano differenze**. In questo caso estremo, verrebbe creato un solo valore hash, con la conseguenza che tutte le righe saranno inserite in una singola distribuzione. Idealmente, ogni valore distinct nella colonna con hash avrebbe lo stesso numero di righe.

> [AZURE.NOTE] Le tabelle round robin non presentano differenze, perché i dati sono archiviati in modo uniforme tra le distribuzioni.

## Fornire un'esecuzione bilanciata
L'esecuzione bilanciata si ottiene quando ogni distribuzione presenta la stessa quantità di lavoro da eseguire. L'elaborazione parallela massiva è un gioco di squadra, ognuno deve arrivare al traguardo prima che qualcuno possa essere dichiarato vincitore. Se ogni distribuzione include la stessa quantità di lavoro, cioè dati da elaborare, tutte le query saranno completate quasi contemporaneamente. Questo comportamento è noto come esecuzione bilanciata.

Come detto in precedenza, le differenze dei dati possono influire sull'esecuzione bilanciata. Anche la scelta della chiave di distribuzione può avere questo effetto. Se è stata scelta una colonna che viene usata nella clausola `WHERE` di una query, è molto probabile che la query non sia bilanciata.

> [AZURE.NOTE] La clausola `WHERE` in genere consente di identificare le colonne ideali per il partizionamento.

Un buon esempio di colonna usata nella clausola `WHERE` è un campo data. I campi data sono classici esempi di colonne di partizionamento appropriate, ma sono spesso colonne di distribuzione hash inadatte. Le query sul data warehouse riguardano in genere in un periodo di tempo specificato, ad esempio giorno, settimana o mese. La distribuzione hash per data può in effetti limitare la scalabilità e ridurre le prestazioni. Se ad esempio l'intervallo di date specificato è per una settimana di 7 giorni, il numero massimo di hash sarà 7, cioè uno al giorno. Ciò significa che solo 7 distribuzioni conterranno dati. Le rimanenti distribuzioni saranno vuote. In questo caso, l'esecuzione della query non sarà bilanciata, perché solo 7 distribuzioni elaborano dati.

> [AZURE.NOTE] Le tabelle round robin forniscono in genere un'esecuzione bilanciata, perché i dati sono archiviati in modo uniforme tra le distribuzioni.

## Indicazioni
Per ottimizzare le prestazioni e la velocità effettiva complessiva della query, verificare che le tabelle con distribuzione hash seguano questo modello quanto più possibile:

Chiave di distribuzione hash

1. È un valore statico poiché non è possibile aggiornare la colonna hash.
2. Viene usata nella clausola `JOIN`, `GROUP BY`, `DISTINCT` o `HAVING` delle query.
2. Non viene usata nelle clausole `WHERE`.
3. Presenta molti valori diversi, almeno 1000.
4. Non ha un numero eccessivamente elevato di righe per cui viene eseguito l'hashing a un numero ridotto di distribuzioni.
5. È definita come NOT NULL. Le righe NULL verranno aggregate in una sola distribuzione.

## Riepilogo

La distribuzione hash può essere riepilogata come segue:

- La funzione hash è deterministica. Lo stesso valore è sempre assegnato alla stessa distribuzione.
- Una colonna viene usata come colonna di distribuzione. La funzione hash usa la colonna specificata per calcolare le assegnazioni di riga alle distribuzioni.
- La funzione hash si basa sul tipo di colonna, non sui valori stessi.
- La distribuzione di una tabella hash può talvolta generare tabelle con differenze dei dati.
- Le tabelle con distribuzione hash richiedono in genere meno spostamento di dati durante la risoluzione di query e quindi migliorano le prestazioni delle query per le tabelle dei fatti di grandi dimensioni.
- Osservare le indicazioni per la selezione della colonna con distribuzione hash per migliorare la velocità effettiva della query.

> [AZURE.NOTE] La coerenza del tipo di dati in SQL Data Warehouse è importante. Assicurarsi che lo schema esistente usi in modo coerente lo stesso tipo per una colonna. Questo aspetto è particolarmente importante per la chiave di distribuzione. Se i tipi di dati della chiave di distribuzione non sono sincronizzati e le tabelle vengono unite in join, verrà generato un'inutile spostamento di dati. Questo comportamento può risultare dispendioso se le tabelle sono di grandi dimensioni e comporterebbe una riduzione della velocità effettiva e delle prestazioni.


## Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Panoramica sullo sviluppo per SQL Data Warehouse]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0629_2016-->