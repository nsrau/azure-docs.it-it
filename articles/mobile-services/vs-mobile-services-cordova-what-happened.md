<properties 
	pageTitle="" 
	description="Viene descritto cosa è successo al progetto Servizi mobili di Azure in Cordova" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# Che cosa è successo al progetto?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-cordova-getting-started.md)
> - [What Happened](vs-mobile-services-cordova-what-happened.md)

###<span id="whathappened">Che cosa è successo al progetto?</span>

#####Aggiunta di riferimenti

È stato abilitato il plug-in client di Servizi mobili di Azure incluso in tutte le app ibride per più dispositivi.
  
#####Valori della stringa di connessione per Servizi mobili

In `services\mobileServices\settings` è stato generato un nuovo file JavaScript (.js) con un **MobileServiceClient** contenente l'URL e la chiave applicazione del servizio mobile selezionato. Il file include l'inizializzazione dell'oggetto client di Servizi mobili, che usa codice analogo al seguente.

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[Altre informazioni sui servizi mobili](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=July15_HO4-->