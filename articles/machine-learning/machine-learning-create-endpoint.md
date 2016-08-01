<properties 
	pageTitle="Creazione di endpoint di servizio Web in Machine Learning | Microsoft Azure" 
	description="Creazione di endpoint del servizio Web in Azure Machine Learning" 
	services="machine-learning" 
	documentationCenter="" 
	authors="hiteshmadan" 
	manager="padou" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="07/06/2016"
	ms.author="himad"/>


# Creazione di endpoint

>[AZURE.NOTE] Questo argomento descrive le tecniche applicabili a un servizio Web classico.

Azure Machine Learning consente di creare più endpoint per un servizio Web distribuito. Ogni endpoint è singolarmente indirizzato, limitato e gestito, indipendentemente da altri endpoint del servizio Web. Esistono un URL e una chiave di autorizzazione univoci per ogni endpoint.

Ciò consente di creare servizi Web da vendere ai propri clienti. Ogni endpoint può essere personalizzato singolarmente con specifici modelli sottoposti a training mentre sono ancora collegati all'esperimento con cui si è creato il servizio Web. Inoltre, gli aggiornamenti al test possono essere applicati in modo selettivo a un endpoint, senza sovrascrivere le personalizzazioni.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Processo di creazione degli endpoint
- Aprire [http://manage.windowsazure.com](http://manage.windowsazure.com), fare clic su **Machine Learning** nella colonna a sinistra. Fare clic sull'area di lavoro con il servizio Web a cui si è interessati. ![Passare all'area di lavoro](./media/machine-learning-create-endpoint/figure-1.png)

- Fare clic sulla scheda **Web Services**. ![Passare ai servizi Web](./media/machine-learning-create-endpoint/figure-2.png)

- Fare clic sul servizio Web a cui si è interessati per visualizzare l'elenco degli endpoint disponibili. ![Passare all'endpoint](./media/machine-learning-create-endpoint/figure-3.png)

- Fare clic sul pulsante **Add Endpoint** nella parte inferiore. Inserire un nome e una descrizione, assicurarsi che non siano presenti altri endpoint con lo stesso nome nel servizio Web. Lasciare il livello di limitazione con il valore predefinito, a meno di avere esigenze particolari. Per altre informazioni sulle limitazioni, vedere [Ridimensionamento degli endpoint dell'API](machine-learning-scaling-endpoints.md). ![Creare un endpoint](./media/machine-learning-create-endpoint/figure-4.png)

Dopo aver creato l'endpoint, è possibile utilizzarlo tramite le API sincrone, le API batch e i fogli di lavoro di Excel. Oltre ad aggiungere gli endpoint tramite questa interfaccia, è possibile utilizzare anche le API di gestione Endpoint a livello di codice aggiungere gli endpoint. Per altre informazioni su come usare i servizi Web di Machine Learning, vedere [Come utilizzare un servizio Web di Azure Machine Learning pubblicato](machine-learning-consume-web-services.md).
 
 Si noti che NON È POSSIBILE eliminare l'endpoint predefinito, da Visual Studio o da qui, se vi sono stati aggiunti endpoint. Verrà generato un errore. Fare clic su Salva.

<!---HONumber=AcomDC_0720_2016-->