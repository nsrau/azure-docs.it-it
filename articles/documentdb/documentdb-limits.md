<properties 
	pageTitle="Limiti per la versione di anteprima di DocumentDB | Azure" 
	description="Informazioni sull'applicazione di limiti e quote nella versione di anteprima di DocumentDB." 
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
	ms.date="01/29/2015" 
	ms.author="mimig"/>


#Limiti per la versione di anteprima di DocumentDB
La tabella seguente descrive le quote e i limiti applicati nella versione di anteprima di DocumentDB. In gran parte dei casi, i limiti vengono applicati con l'obiettivo di ottenere i commenti dell'utente o in base ai vincoli della capacità corrente. Se l'azienda ha necessità di ridurre questi limiti, potrà contattare Microsoft, che si impegnerà a soddisfare la richiesta entro i vincoli dell'offerta pubblica.    

|Entità |Quota (offerta standard per la versione preliminare)|
|-------|--------|
|Account di database     |5
|Numero di database per ogni account di database     |100
|Numero di utenti per ogni account di database (in tutti i database) |500.000
|Numero di autorizzazioni per ogni account di database (in tutti i database)   |2.000.000
|Archiviazione allegati per ogni account di database      |2 GB
|Numero massimo di unità di capacità per ogni account di database       |50
|Numero di raccolte per ogni unità di capacità      |3
|Archiviazione minima allocata per ogni raccolta con minimo un documento    |3,3 GB
|Velocità effettiva minima allocata per ogni raccolta con minimo un documento |667 unità riservate
|Elasticità di una raccolta    |0-10 GB
|Numero massimo di unità di richiesta/secondi per ogni raccolta   |2000
|Numero di stored procedure, trigger e UDF per ogni raccolta       |25 ciascuna
|Tempo di esecuzione massimo per stored procedure e trigger     |5 secondi
|Archiviazione documenti/unità di capacità con provisioning |10 GB
|Unità di richiesta/secondi/unità di capacità con provisioning     |2000
|Massimo risorse di archiviazione per ogni database (5 unità di capacità)    |500 GB
|Lunghezza massima della proprietà ID    |255 caratteri
|Numero predefinito di elementi per ogni pagina     |100
|Numero massimo di elementi per ogni pagina        |1000
|Dimensioni massime della richiesta di documenti e allegati       |512KB
|Dimensioni massime della richiesta di stored procedure, trigger e UDF        |256KB
|Dimensioni massime di risposta |1MB
|Numero massimo di percorsi univoci per ogni raccolta       |100
|Stringa |Tutte le stringhe devono essere conformi alla codifica UTF-8. Poiché UTF-8 è una codifica di ampiezza della variabile, le dimensioni della stringa vengono determinate con byte UTF-8.
|Lunghezza massima della proprietà o del valore  |Nessun limite pratico
|Numero massimo di UDF per ogni query     |1
|Numero massimo di JOIN per ogni query    |2
|Numero massimo di clausole AND per ogni query      |5
|Numero massimo di clausole OR per ogni query       |5

<!--HONumber=49--> 