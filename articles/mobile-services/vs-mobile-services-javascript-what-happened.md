<properties 
	pageTitle="" 
	description="Viene descritto cosa è successo al progetto di Servizi mobili di Azure in Visual Studio .NET" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>


<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="patshea"/>


# Che cosa è successo al progetto?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-javascript-getting-started.md)
> - [What Happened](vs-mobile-services-javascript-what-happened.md)

###Che cosa è successo al progetto?

#####Aggiunta del pacchetto NuGet

È stato installato il pacchetto NuGet **WindowsAzure.MobileServices.WinJS**, inclusa la libreria di Servizi mobili di Azure nel file `js\MobileServices.js`.
  
#####Valori della stringa di connessione per Servizi mobili 

Nella cartella `services\mobileServices\settings` è stato generato un nuovo file JavaScript (.js) con un **MobileServiceClient**, contenente l'URL e la chiave applicazione del servizio mobile selezionato.


#####Aggiunta di riferimenti a default.html

Alcuni riferimenti al file `MobileServices.js` e al file delle impostazioni sono stati aggiunti a `default.html`.


#####Aggiunta di file dei servizi connessi

Nella cartella dei servizi sono stati aggiunti file di configurazione dei servizi connessi.



 

<!---HONumber=August15_HO6-->