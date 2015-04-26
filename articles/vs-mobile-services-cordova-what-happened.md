<properties 
	pageTitle="" 
	description="" 
	services="mobile-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/8/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Introduzione](vs-mobile-services-cordova-getting-started.md)
> - [Risultati](vs-mobile-services-cordova-what-happened.md)

###<span id="whathappened">Che cosa è successo al progetto?</span>

#####Aggiunta di riferimenti

È stato abilitato il plug-in client di Servizi mobili di Azure incluso in tutte le app ibride per più dispositivi.
  
#####Valori della stringa di connessione per Servizi mobili

In `services\mobileServices\settings` è stato generato un nuovo file JavaScript (.js) con un client **MobileServiceClient**, contenente l'URL e la chiave applicazione del servizio mobile selezionato. Il file include l'inizializzazione dell'oggetto client di Servizi mobili, che usa codice analogo al seguente.

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[Altre informazioni sui servizi mobili](http://azure.microsoft.com/documentation/services/mobile-services/)


<!--HONumber=42-->
