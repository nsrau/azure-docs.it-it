<properties 
	pageTitle="" 
	description="Viene descritto cosa è successo al progetto in Visual Studio .NET di Servizi mobili di Azure" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# Che cosa è successo al progetto?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-dotnet-getting-started.md)
> - [What Happened](vs-mobile-services-dotnet-what-happened.md)

###<span id="whathappened">Che cosa è successo al progetto?</span>

#####Aggiunta di riferimenti

Il pacchetto NuGet per Servizi mobili di Azure è stato aggiunto al progetto. Sono stati quindi aggiunti al progetto i riferimenti a .NET seguenti:

- `Microsoft.WindowsAzure.Mobile`
- `Microsoft.WindowsAzure.Mobile.Ext`
- `Newtonsoft.Json`
- `System.Net.Http.Extensions`
- `System.Net.Http.Primitives` 

#####Valori della stringa di connessione per Servizi mobili

Nel file App.xaml.cs è stato creato un oggetto **MobileServiceClient** con l'URL e la chiave applicazione del servizio mobile selezionato.

[Altre informazioni sui servizi mobili](http://azure.microsoft.com/documentation/services/mobile-services/)
<!--HONumber=54--> 