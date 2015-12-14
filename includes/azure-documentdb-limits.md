=======
Entità| Quota (offerta Standard)
---|---
account di Database* | 5
 numero di database per account di database|100 
numero di utenti per account di database – in tutti i database|500,000 
numero di autorizzazioni per ogni account di database – in tutti i database|2,000000 
archiviazione degli allegati per ogni account di database (funzionalità di anteprima)| 2 GB 
richiesta unità massima / secondi per raccolta|2500 
numero di stored procedure, trigger e funzioni definite dall'utente per ogni raccolta* |25 tempo 
di esecuzione massimo per ogni stored procedure e trigger|5 secondi 
archiviazione dei documenti di cui si è eseguito il provisioning / raccolta|10 GB 
numero massimo di raccolte per ogni account di database* | 100 
dimensione massima dell’archivio documenti per ogni database (100 raccolte)* |1 TB 
lunghezza massima della proprietà ID|255 caratteri
caratteri elementi massimi per pagina|Nessun limite pratico 
dimensione massima di richiesta di documento e allegato | 512KB 
dimensione massima di richiesta di stored procedure, trigger e UDF| 512KB 
dimensione massima di risposta| 1MB 
Stringa| Tutte le stringhe devono rispettare la codifica UTF-8. Poiché UTF-8 è una codifica di ampiezza della variabile, le dimensioni della stringa vengono determinate con byte UTF-8. 
Lunghezza massima della proprietà o valore| Nessun limite pratico 
numero massimo di UDF per query* |2
numero massimo di join per ogni query* |5
numero massimo di clausole AND per ogni query |20
numero massimo di clausole OR per query* |10
numero massimo di valori per ogni espressione IN* |100
numero massimo di punti in un argomento del poligono in una query ST\_WITHIN* |16
numero massimo di raccolte create al minuto* |5
numero massimo di operazioni di scalabilità al minuto* | 5

Le quote elencate con un asterisco (*) [possono essere modificate contattando il supporto tecnico Azure](../articles/documentdb/documentdb-increase-limits.md).

<!---HONumber=AcomDC_1203_2015-->