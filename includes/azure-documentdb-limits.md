=======
Entità|Quota (offerta Standard)
---|---
Account di database*|50
Numero di database per ogni account di database|100
Numero di utenti per ogni account di database (in tutti i database)|500\.000
Numero di autorizzazioni per ogni account di database (in tutti i database)|2\.000.000
Archiviazione allegati per ogni account di database (funzionalità di anteprima)|2 GB
Numero massimo di unità di richiesta/secondi per ogni raccolta|2500
Numero di stored procedure, trigger e UDF per ogni raccolta* |25 ciascuna
Tempo di esecuzione massimo per stored procedure e trigger|5 secondi
Archiviazione documenti/raccolta con provisioning|10 GB
Numero massimo di raccolte per ogni account di database*|100
Dimensione massima dell'archivio documenti per ogni database (100 raccolte)* |1 TB
Lunghezza massima della proprietà ID|255 caratteri
Numero massimo di elementi per ogni pagina|Nessun limite pratico
Dimensioni massime della richiesta di documenti e allegati |512 KB
Dimensioni massime di stored procedure, trigger e UDF|100 KB
Dimensioni massime di risposta|1 MB
Stringa|Tutte le stringhe devono essere conformi alla codifica UTF-8. Poiché UTF-8 è una codifica di ampiezza della variabile, le dimensioni della stringa vengono determinate con byte UTF-8.
Lunghezza massima della proprietà o del valore|Nessun limite pratico
Numero massimo di UDF per ogni query* |2
Numero massimo di JOIN per ogni query* |5
Numero massimo di clausole AND per ogni query* |20
Numero massimo di clausole OR per ogni query* |20
Numero massimo di valori per ogni espressione IN* |200
Numero massimo di punti in un argomento del poligono in una query ST\_WITHIN* |256
Numero massimo di raccolte create al minuto* |5
Numero massimo di operazioni di scalabilità al minuto* |5

Le quote elencate con un asterisco (*) [possono essere modificate contattando il supporto tecnico Azure](../articles/documentdb/documentdb-increase-limits.md).

<!---HONumber=AcomDC_0316_2016-->