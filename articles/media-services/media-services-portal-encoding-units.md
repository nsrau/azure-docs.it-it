<properties
	pageTitle="Come applicare scalabilità all'elaborazione di contenuti mediante il portale di Azure classico"
	description="Informazioni su come scalare servizi multimediali specificando il numero di unità riservate di streaming on demand e di unità riservate di codifica di cui eseguire il provisioning nell'account in uso."
	services="media-services"
	documentationCenter=""
	authors="juliako,milangada"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/29/2016"
	ms.author="juliako"/>


# Come applicare scalabilità all'elaborazione di contenuti mediante il portale di Azure classico

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [Portal](media-services-portal-encoding-units.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

## Panoramica

Un account di Servizi multimediali è associato a un tipo di unità riservata che determina la velocità dei processi di elaborazione dei multimedia. È possibile scegliere uno dei seguenti tipi di unità riservata: **S1**, **S2** o **S3**. Ad esempio, lo stesso processo di codifica viene eseguito più velocemente quando si usa il tipo di unità riservata **S2** rispetto al tipo **S1**. Per altre informazioni, vedere il post di blog relativo ai [tipi di unità riservate di codifica](https://azure.microsoft.com/blog/author/milanga/).

Oltre al tipo di unità riservata, è possibile specificare il provisioning dell'account con unità riservate di codifica. Il numero delle unità riservate di codifica sottoposte a provisioning determina il numero di attività multimediali che possono essere elaborate contemporaneamente in un determinato account. Se, ad esempio, il proprio account dispone di cinque unità riservate, è possibile eseguire simultaneamente cinque attività multimediali, purché siano presenti attività da elaborare. Le attività rimanenti verranno messe in coda e prelevate in sequenza per l'elaborazione non appena un'attività in esecuzione viene completata. Se per un account non sono state fornite unità riservate, le attività verranno prelevate in sequenza. In questo caso, il tempo di attesa tra il completamento di un'attività e l'avvio di quella successiva dipende dalle risorse disponibili nel sistema.

>[AZURE.IMPORTANT] Unità riservate di lavoro per la parallelizzazione di tutta l'elaborazione di supporti di memorizzazione, tra cui l'indicizzazione di processi tramite Azure Media Indexer. Tuttavia, a differenza della codifica, l'indicizzazione di processi non elaborerà più velocemente con unità riservate più veloci.

Per cambiare il tipo di unità riservata e il numero di unità riservate di codifica, seguire questa procedura:

1. Nel [portale di Azure classico](https://manage.windowsazure.com/) fare clic su **Servizi multimediali**. Fare quindi clic sul nome del servizio multimediale.

2. Selezionare la pagina **CODIFICA**.

	Per cambiare il **TIPO DI UNITÀ RISERVATA**, scegliere S1, S2 o S3.

	Per cambiare il numero di unità riservate per il tipo selezionato, usare il dispositivo di scorrimento **CODIFICA**.


	![Pagina relativa ai processori](./media/media-services-portal-encoding-units/media-services-encoding-scale.png)


	>[AZURE.NOTE] I seguenti data center non offrono il tipo di unità riservata Premium: Singapore, Hong Kong, Osaka, Beijing, Shanghai.

3. Fare clic sul pulsante SALVA per salvare le modifiche apportate.

	Le nuove unità riservate di codifica vengono allocate non appena si fa clic su SALVA.

	>[AZURE.NOTE] Il numero più alto di unità specificato in un periodo di 24 ore è quello che verrà usato per il calcolo del costo.

##Quote e limitazioni

Per informazioni su quote e limitazioni e su come aprire un ticket di supporto, vedere [Quote e limitazioni](media-services-quotas-and-limitations.md).



##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0204_2016-->