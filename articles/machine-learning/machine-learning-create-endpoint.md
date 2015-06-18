<properties 
	pageTitle="Creazione di endpoint" 
	description="Creazione di endpoint del servizio Web in Azure Machine Learning" 
	services="machine-learning" 
	authors="hiteshmadan" 
	manager="padou" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="02/19/2015"
	ms.author="himad"/>


# Creazione di endpoint

Azure Machine Learning consente di creare più endpoint per un servizio Web pubblicato. Ogni endpoint è singolarmente indirizzato, limitato e gestito, indipendentemente da altri endpoint del servizio Web. Esistono un URL e una chiave di autorizzazione univoci per ogni endpoint.

Ciò consente agli utenti Azure Machine Learning di creare servizi Web che possono essere venduti a termine ai propri clienti. Ogni endpoint può essere personalizzato singolarmente con specifici modelli di training mentre sono ancora collegati all'esperimento creato dal servizio Web. Inoltre, gli aggiornamenti al test possono essere applicati in modo selettivo a un endpoint, senza sovrascrivere le personalizzazioni.

## Processo di creazione degli endpoint
- Aprire manage.windowsazure.com, fare clic su Machine Learning nella colonna a sinistra. Fare clic sull'area di lavoro con il servizio Web a cui si è interessati.
![Navigate to workspace](./media/machine-learning-create-endpoint/figure-1.png)


- Fare clic sulla scheda "Servizi Web".
![Navigate to web services](./media/machine-learning-create-endpoint/figure-2.png)


- Fare clic sul servizio Web a cui si è interessati per visualizzare l'elenco degli endpoint disponibili.
![Navigate to endpoint](./media/machine-learning-create-endpoint/figure-3.png)


- Fare clic sul pulsante Aggiungi endpoint nella parte inferiore. Inserire un nome e una descrizione, assicurarsi che non siano presenti altri endpoint con lo stesso nome nel servizio Web. Lasciare il livello di limitazione con il valore predefinito, a meno che non si abbiano esigenze particolari.
Per ulteriori informazioni sulle limitazioni, visitare la pagina [Ridimensionamento degli endpoint API](machine-learning-scaling-endpoints.md).
![Create endpoint](./media/machine-learning-create-endpoint/figure-4.png)


Una volta creato l'endpoint, è possibile utilizzarlo tramite le API sincrone, le API batch e fogli di lavoro di Excel.

<!--HONumber=49--> 