<properties 
	pageTitle="Registrazione per i servizi Web di Machine Learning | Microsoft Azure" 
	description="Informazioni su come abilitare la registrazione per i servizi Web di Machine Learning. La registrazione fornisce informazioni aggiuntive per risolvere i problemi relativi alle API." 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data" 
	ms.date="06/30/2015"
	ms.author="raymondl;garye"/>

#Abilitare la registrazione per i servizi Web di Machine Learning  

Questo documento include informazioni sulla funzionalità di registrazione dei servizi Web di Azure Machine Learning. L'abilitazione della registrazione nei servizi Web fornisce informazioni aggiuntive per la risoluzione delle API oltre a un semplice numero di errore e un messaggio.

-	Come abilitare la registrazione nei servizi Web:   
	-	Per abilitare la registrazione, è necessario accedere al dashboard del servizio Web nel portale di Azure, fare clic sul pulsante Sì e quindi su Salva.  
-	Effetto dell'abilitazione della registrazione:  
	-	Quando la registrazione è abilitata, tutti i dati di diagnostica e gli errori dall'endpoint predefinito vengono registrati nell'account di archiviazione di Azure collegato con l'area di lavoro dell'utente. È possibile visualizzare questo account di archiviazione nella visualizzazione Dashboard del portale di Azure (nella parte inferiore della sezione Riepilogo rapido) dell'area di lavoro.  
-	Endpoint in cui l'abilitazione è disponibile:  
	-	Attualmente è possibile abilitare la registrazione per l'endpoint predefinito del servizio Web. Questa funzionalità sarà presto disponibile anche per gli endpoint aggiuntivi che è possibile creare nel portale di Azure.  
-	Come visualizzare i log:  
	-	I log possono essere visualizzati mediante uno degli strumenti disponibili per 'esplorare' un account di archiviazione di Azure. Il modo più semplice per visualizzarli è passare all'account di archiviazione nel portale di Azure e quindi fare clic sulla scheda CONTENITORI. Verrà quindi visualizzato un contenitore denominato **ml-diagnostics**. Tale contenitore contiene tutte le informazioni di diagnostica per tutti gli endpoint del servizio Web di tutte le aree di lavoro associate all'account di archiviazione.  
-	Informazioni sui dettagli del BLOB di log:  
	-	Ogni BLOB nel contenitore contiene le informazioni di diagnostica per uno degli elementi seguenti:
		-	Un'esecuzione del metodo Batch-Execution  
		-	Un'esecuzione del metodo Request-Response  
		-	Inizializzazione di un contenitore Request-Response  
	-	Il nome di ogni BLOB include un prefisso nel formato seguente: {ID area di lavoro}-{ID servizio Web}-{ID endpoint}/{Tipo di log}  
-	Il tipo di log accetta uno dei valori seguenti: batch punteggio/richieste punteggio/inizializzazione  

 

<!---HONumber=July15_HO2-->