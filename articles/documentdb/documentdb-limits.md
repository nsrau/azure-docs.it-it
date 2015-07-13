<properties 
	pageTitle="Limiti e quote di DocumentDB | Azure" 
	description="Informazioni sull'applicazione di limiti e quote in DocumentDB." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="mimig"/>


#Limiti e quote di DocumentDB

La tabella seguente descrive le quote e i limiti applicati in DocumentDB. Le quote elencate con un asterisco (*) [possono essere modificate contattando il supporto tecnico Azure](documentdb-increase-limits.md).

|Entità |Quota (offerta Standard)|
|-------|--------|
|Account di database* |5
|Numero di database per ogni account di database |100
|Numero di utenti per ogni account di database (in tutti i database) |500.000
|Numero di autorizzazioni per ogni account di database (in tutti i database) |2.000.000
|Archiviazione allegati per ogni account di database (funzionalità di anteprima) |2 GB
|Numero massimo di unità di richiesta/secondi per ogni raccolta |2500
|Numero di stored procedure, trigger e UDF per ogni raccolta* |25 ciascuna
|Tempo di esecuzione massimo per stored procedure e trigger |5 secondi
|Archiviazione documenti/raccolta con provisioning |10 GB
|Numero massimo di raccolte per ogni account di database* |100
|Dimensione massima dell'archivio documenti per ogni database (100 raccolte)* |1 TB
|Lunghezza massima della proprietà ID |255 caratteri
|Numero massimo di elementi per ogni pagina |1000
|Dimensioni massime della richiesta di documenti e allegati |512 KB
|Dimensioni massime della richiesta di stored procedure, trigger e UDF |512 KB
|Dimensioni massime di risposta |1 MB
|Stringa |Tutte le stringhe devono essere conformi alla codifica UTF-8. Poiché UTF-8 è una codifica di ampiezza della variabile, le dimensioni della stringa vengono determinate con byte UTF-8.
|Lunghezza massima della proprietà o del valore |Nessun limite pratico
|Numero massimo di UDF per ogni query* |1
|Numero massimo di funzioni predefinite per ogni query |Nessun limite pratico
|Numero massimo di JOIN per ogni query* |2
|Numero massimo di clausole AND per ogni query* |5
|Numero massimo di clausole OR per ogni query* |5
|Numero massimo di valori per ogni espressione IN* |100
|Numero massimo di raccolte create al minuto* |5
|Numero massimo di operazioni di scalabilità al minuto* |5
 

<!---HONumber=July15_HO1-->