<properties
   pageTitle="Ridimensionamento di un servizio Web | Microsoft Azure"
   description="Informazioni su come ridimensionare un servizio Web mediante l'aumento della concorrenza e l'aggiunta di nuovi endpoint."
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="azure machine learning, servizi Web, messa in funzione, scalabilità, endpoint, concorrenza"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="10/27/2015"
   ms.author="neerajkh"/>

# Ridimensionamento di un servizio Web

## Aumentare la concorrenza

Per impostazione predefinita, ogni servizio Web pubblicato è configurato per supportare 20 richieste simultanee. È possibile aumentare la concorrenza a 200 richieste simultanee tramite il [portale di Azure classico](https://manage.windowsazure.com/), come illustrato nella figura riportata di seguito.

Passare al [portale di Azure classico](https://manage.windowsazure.com/), fare clic sull'icona Machine Learning a sinistra, selezionare l'area di lavoro usata per la pubblicazione del servizio Web, fare clic sul servizio Web desiderato, selezionare l'endpoint che richiede l'aumento di concorrenza e quindi fare clic su **CONFIGURA**. Usare il dispositivo di scorrimento per aumentare la concorrenza e quindi fare clic su **SALVA** nel pannello inferiore.

Per migliorare la concorrenza, vedere [Ridimensionamento degli endpoint dell'API](machine-learning-scaling-endpoints.md).

   ![Machine Learning, ridimensionamento degli endpoint.][1]

## Aggiungere nuovi endpoint per lo stesso servizio Web

Il ridimensionamento del servizio Web è un'attività comune sia per supportare più di 200 richieste simultanee, aumentare la disponibilità tramite più endpoint o per fornire un endpoint separato per consumer diversi del servizio Web. È possibile eseguire il ridimensionamento aggiungendo ulteriori endpoint per lo stesso servizio Web. Per aggiungere altri endpoint, è possibile usare il [portale di Azure classico](https://manage.windowsazure.com/), come illustrato nella figura riportata di seguito.

Passare al [portale di Azure classico](https://manage.windowsazure.com/), fare clic sull'icona Machine Learning a sinistra, selezionare l'area di lavoro usata per la pubblicazione del servizio Web, fare clic sul servizio Web desiderato, fare clic su **Aggiungi endpoint** nel pannello inferiore, quindi specificare un nome, una descrizione e il tipo di concorrenza desiderato per il nuovo endpoint.

Per aggiungere nuovi endpoint, vedere [Creazione di endpoint](machine-learning-create-endpoint.md).

   ![Machine Learning, aggiungere nuovi endpoint.][2]

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png

<!---HONumber=AcomDC_1203_2015-->