<properties 
	pageTitle="Come aggiungere unità di codifica" 
	description="Informazioni su come aggiungere unità di codifica mediante .NET"  
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="09/01/2016"
	ms.author="juliako;milangada;gtrifonov"/>  


#Come scalare la codifica con .NET SDK

> [AZURE.SELECTOR]
- [Portale](media-services-portal-scale-media-processing.md)
- [.NET](media-services-dotnet-encoding-units.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##Overview

>[AZURE.IMPORTANT] Per altre informazioni sul ridimensionamento dell'elaborazione multimediale, vedere questo argomento di [panoramica](media-services-scale-media-processing-overview.md).
 
Per cambiare il tipo di unità riservata e il numero di unità riservate di codifica mediante l'SDK per .NET, seguire questa procedura:

	IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
	encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
	encodingS1ReservedUnit.Update();
	Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);
	
	encodingS1ReservedUnit.CurrentReservedUnits = 2;
	encodingS1ReservedUnit.Update();
	
	Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

##Apertura di un ticket di supporto

Per impostazione predefinita, ogni account di Media Services può includere fino a 25 unità di codifica riservate e cinque unità riservate di streaming on demand. È possibile richiedere l'applicazione di un limite superiore mediante l'apertura di un ticket di supporto.

###Aprire un ticket di supporto

Per aprire un ticket di supporto, seguire questa procedura:

1. Fare clic su [Ottieni supporto](https://manage.windowsazure.com/?getsupport=true). Se non si è ancora connessi, verrà richiesto di immettere le credenziali.

1. Selezionare la propria sottoscrizione.

1. Come tipo di supporto, selezionare "Tecnico".

1. Fare clic su "Create Ticket".

1. Selezionare "Servizi multimediali di Azure" dall'elenco dei prodotti visualizzato nella pagina successiva.

1. Selezionare un tipo di problema appropriato per la situazione specifica.

1. Fare clic su Continue.

1. Seguire le istruzioni nella pagina successiva e quindi immettere i dettagli relativi al problema.

1. Fare clic su Invia per aprire il ticket.



##Percorsi di apprendimento di Servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0907_2016-->