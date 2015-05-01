<properties 
	pageTitle="Come aggiungere unità di codifica" 
	description="Informazioni su come aggiungere unità di codifica mediante .NET"  
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




#Come scalare la codifica con .NET SDK

Questo articolo fa parte della serie [Flusso di lavoro Video on Demand di Servizi multimediali](media-services-video-on-demand-workflow.md).
  
##Informazioni generali

Un account di Servizi multimediali è associato a un tipo di unità riservata che determina la velocità di elaborazione dei processi di codifica. È possibile scegliere uno dei seguenti tipi di unità riservata: Basic, Standard o Premium. Ad esempio, lo stesso processo di codifica viene eseguito più velocemente quando si usa il tipo di unità riservata Standard rispetto a quando si usa il tipo Basic. Per altre informazioni, vedere il blog sui tipi di unità riservata di codifica scritto da [Milan Gada](http://azure.microsoft.com/blog/author/milanga/).

Oltre al tipo di unità riservata, è possibile specificare il provisioning dell'account con unità riservate di codifica. Il numero delle unità riservate di codifica sottoposte a provisioning determina il numero di attività multimediali che possono essere elaborate contemporaneamente in un determinato account. Se, ad esempio, il proprio account dispone di cinque unità riservate, è possibile eseguire simultaneamente cinque attività multimediali, purché siano presenti attività da elaborare. Le attività rimanenti verranno messe in coda e prelevate in sequenza per l'elaborazione non appena un'attività in esecuzione viene completata. Se per un account non sono state fornite unità riservate, le attività verranno prelevate in sequenza. In questo caso, il tempo di attesa tra il completamento di un'attività e l'avvio di quella successiva dipende dalle risorse disponibili nel sistema.

Per cambiare il tipo di unità riservata e il numero di unità riservate di codifica mediante l'SDK per .NET, seguire questa procedura:

	IEncodingReservedUnit encodingBasicReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
	encodingBasicReservedUnit.ReservedUnitType = ReservedUnitType.Basic;
	encodingBasicReservedUnit.Update();
	Console.WriteLine("Reserved Unit Type: {0}", encodingBasicReservedUnit.ReservedUnitType);
	
	encodingBasicReservedUnit.CurrentReservedUnits = 2;
	encodingBasicReservedUnit.Update();
	
	Console.WriteLine("Number of reserved units: {0}", encodingBasicReservedUnit.CurrentReservedUnits);

##Apertura di un ticket di supporto

Per impostazione predefinita, per ogni account di Servizi multimediali è possibile aumentare fino a 25 le unità riservate di codifica e fino a 5 le unità riservate di streaming on demand. È possibile richiedere l'applicazione di un limite superiore mediante l'apertura di un ticket di supporto.

Per aprire un ticket di supporto, seguire questa procedura: 

1. Accedere all'account Azure nel [portale di gestione](http://manage.windowsazure.com).
2. Passare a [Supporto](http://www.windowsazure.com/support/contact/).
3. Fare clic su "Ottieni supporto".
4. Selezionare la propria sottoscrizione.
5. Per il tipo di supporto, selezionare "Tecnico".
6. Fare clic su "Crea ticket".
7. Selezionare "Servizi multimediali di Azure" dall'elenco dei prodotti visualizzato nella pagina successiva.
8. Selezionare "Elaborazione multimediale" come tipo di problema, quindi selezionare "Unità riservate" come categoria.
9. Fare clic su Continua.
10. Seguire le istruzioni nella pagina successiva, quindi immettere i dettagli sul numero di unità riservate di codifica o di streaming on demand necessarie.
11. Fare clic su Invia per aprire il ticket.



<!--HONumber=52-->