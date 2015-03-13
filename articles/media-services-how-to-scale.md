<properties 
	pageTitle="Come scalare un servizio multimediale" 
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


# Come scalare un servizio multimediale  

Questo articolo fa parte della serie [Flusso di lavoro Video on Demand di Servizi multimediali](../media-services-video-on-demand-workflow).

## Informazioni generali

È possibile scalare servizi multimediali specificando il numero di **unità riservate di streaming** e di **unità riservate di codifica** di cui si desidera eseguire il provisioning nell'account in uso. 

## Unità riservate di streaming

Per altre informazioni, vedere l'articolo su come [scalare le unità di streaming](../media-services-manage-origins#scale_streaming_endpoints).

## <a id="encoding_reserved_units"></a>Unità riservate di codifica

Un account di Servizi multimediali è associato a un tipo di unità riservata che determina la velocità di elaborazione dei processi di codifica. È possibile scegliere uno dei tipi di unità riservata seguenti: Basic, Standard o Premium. Ad esempio, lo stesso processo di codifica viene eseguito più velocemente quando si usa il tipo di unità riservata Standard rispetto a quando si usa il tipo Di base. Per altre informazioni, vedere l'articolo sui tipi di unità riservata di codifica nel blog di [Milan Gada](http://azure.microsoft.com/blog/author/milanga).

Oltre al tipo di unità riservata, è possibile specificare il provisioning dell'account con unità riservate di codifica. Il numero delle unità riservate di codifica sottoposte a provisioning determina il numero di attività multimediali che possono essere elaborate contemporaneamente in un determinato account. Se, ad esempio, il proprio account dispone di cinque unità riservate, è possibile eseguire simultaneamente cinque attività multimediali, purché siano presenti attività da elaborare. Le attività rimanenti verranno messe in coda e prelevate in sequenza per l'elaborazione non appena un'attività in esecuzione viene completata. Se per un account non sono state fornite unità riservate, le attività verranno prelevate in sequenza. In questo caso, il tempo di attesa tra il completamento di un'attività e l'avvio di quella successiva dipende dalle risorse disponibili nel sistema.

Per cambiare il tipo di unità riservata e il numero di unità riservate di codifica, effettuare le seguenti operazioni:

1. Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Servizi multimediali**. Fare quindi clic sul nome del servizio multimediale.

2. Selezionare la pagina **CODIFICA**. 

	Per cambiare il **TIPO DI UNITÀ RISERVATA**, scegliere BASIC, STANDARD o PREMIUM. 

	Per cambiare il numero di unità riservate per il tipo selezionato, usare il dispositivo di scorrimento **CODIFICA**. 
	
	
	![Processors page](./media/media-services-how-to-scale/media-services-encoding-scale.png)

	  
	>[Azure.Note] I seguenti data center non offrono il tipo di unità riservata Premium: Singapore, Hong Kong, Osaka, Pechino, Shanghai.

3. Fare clic sul pulsante SALVA per salvare le modifiche apportate.

	Le nuove unità riservate di codifica vengono allocate non appena si fa clic su SALVA.

	>[Azure.Note] Il numero più alto di unità specificato in un periodo di 24 ore è quello che verrà usato per il calcolo del costo.

## Apertura di un ticket di supporto

Per impostazione predefinita, ogni account di Media Services può includere fino a 25 unità di codifica riservate e cinque unità riservate di streaming on demand. È possibile richiedere l'applicazione di un limite superiore mediante l'apertura di un ticket di supporto.

Per aprire un ticket di supporto, effettuare le seguenti operazioni: 

1. Accedere all'account Azure nel [portale di gestione](http://manage.windowsazure.com).
2. Passare a [Supporto](http://azure.microsoft.com/support/contact/).
3. Fare clic su "Ottieni supporto".
4. Selezionare la propria sottoscrizione.
5. Per il tipo di supporto, selezionare "Tecnico".
6. Fare clic su "Crea ticket".
7. Selezionare "Servizi multimediali di Azure" dall'elenco dei prodotti visualizzato nella pagina successiva.
8. Selezionare "Elaborazione multimediale" come tipo di problema, quindi selezionare "Unità riservate" come categoria.
9. Fare clic su Continua.
10. Attenersi alle istruzioni visualizzate nella pagina successiva, quindi specificare il numero di unità riservate di codifica o di streaming on demand necessarie.
11. Fare clic su Invia per aprire il ticket.





<!--HONumber=45--> 
