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
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# Che cosa è successo al progetto?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-javascript-getting-started.md)
> - [What Happened](vs-mobile-services-javascript-what-happened.md)

###<span id="whathappened">Che cosa è successo al progetto?</id>

#####Aggiunta di riferimenti

La libreria di Servizi mobili di Microsoft Azure è stata aggiunta al progetto sotto forma di un file **MobileServices.js**.
  
#####Valori della stringa di connessione per Servizi mobili 

Nella cartella `services\mobileServices\settings` è stato generato un nuovo file JavaScript (.js) con un **MobileServiceClient**, contenente l'URL e la chiave applicazione del servizio mobile selezionato.

<!--HONumber=54--> 