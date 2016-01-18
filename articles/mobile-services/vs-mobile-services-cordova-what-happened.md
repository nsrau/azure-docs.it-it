<properties 
	pageTitle="Cosa è successo a un progetto Cordova (Servizi relativi a Visual Studio)| Microsoft Azure" 
	description="Descrive cosa è successo al progetto Azure Cordova dopo l'aggiunta di servizi mobili di Azure utilizzando i servizi relativi a Visual Studio." 
	services="mobile-services" 
	documentationCenter="na" 
	authors="TomArcher" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="01/05/2016" 
	ms.author="tarcher"/>

# Cosa è successo al progetto Azure Cordova dopo l'aggiunta di servizi mobili di Azure utilizzando i servizi relativi a Visual Studio?

##Aggiunta di riferimenti

È stato abilitato il plug-in client di Servizi mobili di Azure incluso in tutte le app ibride per più dispositivi.
  
##Valori della stringa di connessione per Servizi mobili

In `services\mobileServices\settings` è stato generato un nuovo file JavaScript (.js) con un **MobileServiceClient** contenente l'URL e la chiave applicazione del servizio mobile selezionato. Il file include l'inizializzazione dell'oggetto client di Servizi mobili, che usa codice analogo al seguente.

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[Altre informazioni sui servizi mobili](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0107_2016-->