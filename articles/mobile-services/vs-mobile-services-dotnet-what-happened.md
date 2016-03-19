<properties
	pageTitle="Cosa è successo al progetto .NET dopo l'aggiunta di servizi mobili tramite i servizi relativi a Visual Studio | Microsoft Azure"
	description="Descrive cosa è successo al progetto Visual Studio .NET dopo l'aggiunta di servizi mobili di Azure utilizzando i servizi connessi."
	services="mobile-services"
	documentationCenter=""
	authors="mlhoop"
	manager="douge"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="mlearned"/>

# Cosa è successo al progetto Visual Studio -NET dopo l'aggiunta di servizi mobili di Azure utilizzando i servizi connessi?

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

## Aggiunta di riferimenti

Il pacchetto NuGet per Servizi mobili di Azure è stato aggiunto al progetto. Sono stati quindi aggiunti al progetto i riferimenti a .NET seguenti:

- **Microsoft.WindowsAzure.Mobile**
- **Microsoft.WindowsAzure.Mobile.Ext**
- **Newtonsoft.Json**
- **System.Net.Http.Extensions**
- **System.Net.Http.Primitives**

## Valori della stringa di connessione per Servizi mobili

Nel file App.xaml.cs è stato creato un oggetto **MobileServiceClient** con l'URL e la chiave applicazione del servizio mobile selezionato.

## Aggiunta di un progetto di Servizi mobili

Se viene creato un servizio mobile .NET nel provider di servizi connessi, verrà creato un progetto di Servizi mobili, che verrà aggiunto alla soluzione.


[Altre informazioni sui servizi mobili](https://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0128_2016-->