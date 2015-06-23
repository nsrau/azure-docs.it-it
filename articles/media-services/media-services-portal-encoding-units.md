<properties 
	pageTitle="Come scalare le unità riservate di codifica" 
	description="Informazioni su come scalare servizi multimediali specificando il numero di unità riservate di streaming on demand e di unità riservate di codifica di cui eseguire il provisioning nell'account in uso." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


# Come scalare la codifica

Questo articolo fa parte della serie [Flusso di lavoro Video on Demand di Servizi multimediali](media-services-video-on-demand-workflow.md).

## Informazioni generali

Un account di Servizi multimediali è associato a un tipo di unità riservata che determina la velocità di elaborazione dei processi di codifica. È possibile scegliere uno dei seguenti tipi di unità riservata: **Basic**, **Standard** o **Premium**. Ad esempio, lo stesso processo di codifica viene eseguito più velocemente quando si usa il tipo di unità riservata **Standard** rispetto a quando si usa il tipo **Basic**. Per altre informazioni, vedere il post di blog relativo ai [tipi di unità riservate di codifica](http://azure.microsoft.com/blog/author/milanga).

Oltre al tipo di unità riservata, è possibile specificare il provisioning dell'account con unità riservate di codifica. Il numero delle unità riservate di codifica sottoposte a provisioning determina il numero di attività multimediali che possono essere elaborate contemporaneamente in un determinato account. Se, ad esempio, il proprio account dispone di cinque unità riservate, è possibile eseguire simultaneamente cinque attività multimediali, purché siano presenti attività da elaborare. Le attività rimanenti verranno messe in coda e prelevate in sequenza per l'elaborazione non appena un'attività in esecuzione viene completata. Se per un account non sono state fornite unità riservate, le attività verranno prelevate in sequenza. In questo caso, il tempo di attesa tra il completamento di un'attività e l'avvio di quella successiva dipende dalle risorse disponibili nel sistema.

Per cambiare il tipo di unità riservata e il numero di unità riservate di codifica, seguire questa procedura:

1. Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Servizi multimediali**. Fare quindi clic sul nome del servizio multimediale.

2. Selezionare la pagina **CODIFICA**. 

	Per cambiare il **TIPO DI UNITÀ RISERVATA**, scegliere BASIC, STANDARD o PREMIUM. 

	Per cambiare il numero di unità riservate per il tipo selezionato, usare il dispositivo di scorrimento **CODIFICA**. 
	
	
	![Processors page](./media/media-services-portal-encoding-units/media-services-encoding-scale.png)

	  
	>[Azure.Note] I seguenti data center non offrono il tipo di unità riservata Premium: Singapore, Hong Kong, Osaka, Pechino, Shanghai.

3. Fare clic sul pulsante SALVA per salvare le modifiche apportate.

	Le nuove unità riservate di codifica vengono allocate non appena si fa clic su SALVA.

	>[Azure.Note] Il numero più alto di unità specificato in un periodo di 24 ore è quello che verrà usato per il calcolo del costo.

## Quote e limitazioni

Per informazioni su quote e limitazioni e su come aprire un ticket di supporto, vedere [Quote e limitazioni](media-services-quotas-and-limitations.md).






<!--HONumber=52--> 