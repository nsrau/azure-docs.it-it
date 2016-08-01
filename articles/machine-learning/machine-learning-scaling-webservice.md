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
   ms.date="07/06/2016"
   ms.author="neerajkh"/>

# Ridimensionamento di un servizio Web

>[AZURE.NOTE] Questo argomento descrive le tecniche applicabili a un servizio Web classico.

Per impostazione predefinita, ogni servizio Web pubblicato è configurato per supportare 20 richieste simultanee fino a 200 richieste simultanee. Il portale di Azure classico consente di impostare questo valore, mentre Azure Machine Learning ottimizza automaticamente questa impostazione per offrire le prestazioni migliori per il servizio Web e il valore del portale viene ignorato.

Se si prevede di eseguire chiamate all'API con un carico superiore rispetto al carico supportato da un numero massimo di chiamate simultanee pari a 200, è consigliabile creare più endpoint nello stesso servizio Web e distribuire il carico in modo casuale tra tutti gli endpoint.

## Aggiungere nuovi endpoint per lo stesso servizio Web

Il ridimensionamento di un servizio Web è un'attività comune per supportare più di 200 richieste simultanee, aumentare la disponibilità tramite più endpoint o fornire un endpoint separato per consumer diversi del servizio Web. È possibile aumentare la scalabilità mediante l'aggiunta di altri endpoint per lo stesso servizio Web tramite il [portale di Azure classico](https://manage.windowsazure.com/) come illustrato nella figura riportata di seguito:

Si tenga presente che l'uso di un numero molto elevato di chiamate simultanee può essere dannoso se non si raggiunge l'API con una frequenza altrettanto elevata. Se si inserisce un carico relativamente basso in un'API configurata per un carico elevato, è possibile che si verifichino timeout sporadici e/o picchi della latenza.

Le API sincrone in genere vengono usate nelle situazioni in cui si desidera una bassa latenza. La latenza qui indica il tempo impiegato dall'API per completare una richiesta e non tiene in considerazione i ritardi di rete. Si supponga di disporre di un'API con una latenza di 50 ms. Per utilizzare appieno la capacità disponibile con il livello di limitazione Elevato e il numero massimo di chiamate simultanee pari a 20, è necessario chiamare l'API 20 * 1000 / 50 = 400 volte al secondo. Estendendo ulteriormente tale capacità, un numero massimo di chiamate simultanee pari a 200 consentirà di chiamare l'API 4000 volte al secondo, supponendo una latenza di 50 ms.

Passare al [portale di Azure classico](https://manage.windowsazure.com/), fare clic sull'icona Machine Learning a sinistra, selezionare l'area di lavoro usata per la pubblicazione del servizio Web, fare clic sul servizio Web desiderato, fare clic su **Aggiungi endpoint** nel pannello inferiore, quindi specificare un nome, una descrizione e il tipo di concorrenza desiderato per il nuovo endpoint.

Per aggiungere nuovi endpoint, vedere [Creazione di endpoint](machine-learning-create-endpoint.md).

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png

<!---HONumber=AcomDC_0720_2016-->