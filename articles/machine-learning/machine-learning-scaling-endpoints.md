<properties 
	pageTitle="Ridimensionamento degli endpoint dell'API | Microsoft Azure" 
	description="Ridimensionamento degli endpoint del servizio Web in Azure Machine Learning" 
	services="machine-learning"
	documentationCenter="" 
	authors="hiteshmadan" 
	manager="padou" 
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="02/25/2016"
	ms.author="himad"/>


# Ridimensionamento degli endpoint dell'API

Gli endpoint del servizio Web in Azure Machine Learning dispongono di livelli di limitazione selezionabili in base alla frequenza con cui verrà utilizzato l'endpoint.

Per controllare la quantità di limitazione nell'endpoint, usare il dispositivo di scorrimento nel portale di Azure classico per impostare il numero massimo di chiamate simultanee tra 20-200


Le API sincrone in genere vengono usate nelle situazioni in cui si desidera una bassa latenza. La latenza qui indica il tempo impiegato dall'API per completare una richiesta e non tiene in considerazione i ritardi di rete. Si supponga di disporre di un'API con una latenza di 50 ms. Per utilizzare appieno la capacità disponibile con il livello di limitazione Elevato e il numero massimo di chiamate simultanee pari a 20, è necessario chiamare l'API 20 * 1000 / 50 = 400 volte al secondo. Estendendo ulteriormente tale capacità, un numero massimo di chiamate simultanee pari a 200 consentirà di chiamare l'API 4000 volte al secondo, supponendo una latenza di 50 ms.

Se si prevede di chiamare l'API con un carico superiore rispetto a quanto supportato da un numero massimo di chiamate simultanee pari a 200, è consigliabile creare più endpoint nello stesso servizio Web e distribuire il carico in modo casuale tra tutti gli endpoint.

Si tenga presente che l'uso di un numero molto elevato di chiamate simultanee può essere dannoso se non si raggiunge l'API con una frequenza altrettanto elevata. Se si inserisce un carico relativamente basso in un'API configurata per un carico elevato, è possibile che si verifichino timeout sporadici e/o picchi della latenza.

Si noti che la modifica delle impostazioni di limitazione influisce solo sul comportamento dell'API sincrona (RRS). Se nell'API sincrona vengono visualizzate frequenti risposte di servizio non disponibile (503), è consigliabile modificare tali impostazioni.

L'interfaccia utente di gestione consente di specificare un numero di concorrenza personalizzato per la scalabilità dell'endpoint oltre la concorrenza predefinita di 20.

1. Aprire manage.windowsazure.com
2. Passare alla scheda Machine Learning
3. Fare clic sull'area di lavoro.
4. Passare al servizio Web dove si trova l'endpoint ![Passare al servizio Web](./media/machine-learning-scaling-endpoints/figure-1.png)

5. Fare clic sull'endpoint e quindi sulla scheda Configura ![Passare alla configurazione dell'endpoint](./media/machine-learning-scaling-webservice/machlearn-2.png)


6. Modificare il dispositivo di scorrimento per aumentare il livello di concorrenza e fare clic su Salva.


 

<!---HONumber=AcomDC_0302_2016-->