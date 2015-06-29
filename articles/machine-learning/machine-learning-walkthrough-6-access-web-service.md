<properties 
	pageTitle="Passaggio 6: Accedere al servizio Web di Machine Learning | Microsoft Azure" 
	description="Passaggio 6 della procedura dettagliata Sviluppare una soluzione predittiva: Accedere a un servizio Web attivo di Azure Machine Learning" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="garye"/>


# Passaggio 6 della procedura dettagliata: Accedere al servizio Web di Azure Machine Learning

Questo è l'ultimo passaggio della procedura dettagliata [Sviluppare una soluzione predittiva con Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)


1.	[Creare un'area di lavoro di Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Caricare i dati esistenti](machine-learning-walkthrough-2-upload-data.md)
3.	[Creare un nuovo esperimento](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Eseguire il training e valutare i modelli](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Pubblicare il servizio Web](machine-learning-walkthrough-5-publish-web-service.md)
6.	**Accedere al servizio Web**

----------

Perché un servizio Web sia utile, gli utenti devono essere in grado di inviare dati e ricevere risultati. Questo è un servizio Web di Azure che può ricevere e restituire dati in due modi:

-	**Richiesta/risposta**: l'utente invia un singolo set di dati di credito al servizio usando un protocollo HTTP e il servizio risponde con un singolo set di risultati.
-	**Esecuzione batch**: l'utente invia al servizio l'URL di un BLOB di Azure che contiene una o più righe di dati di credito. Il servizio archivia i risultati in un altro BLOB e restituisce l'URL di quel contenitore.  

Nella scheda **DASHBOARD** del servizio Web sono presenti due collegamenti a informazioni utili agli sviluppatori per scrivere codice per l'accesso al servizio. Fare clic sul collegamento alla **pagina della guida per l'API** nella riga **RICHIESTA/RISPOSTA**. Verrà visualizzata una pagina contenente codice di esempio per l'uso del protocollo di richiesta/risposta del servizio. Analogamente, il collegamento nella riga **ESECUZIONE BATCH** offre accesso a codice di esempio per l'esecuzione di una richiesta batch al servizi.

La pagina della guida dell'API contiene esempi per i linguaggi di programmazione R, C\# e Python.

<!-- Add link to Derrick's articles on web services -->

<!---HONumber=58_postMigration-->