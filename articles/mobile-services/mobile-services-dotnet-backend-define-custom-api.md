<properties
	pageTitle="Come definire un'API personalizzata in un servizio mobile back-end di .NET | Servizi mobili di Azure"
	description="Informazioni su come definire un endpoint API personalizzato in un servizio mobile back-end di .NET."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="glenga"/>


# Procedura: definire un endpoint API personalizzato in un servizio mobile back-end di .NET

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


> [AZURE.SELECTOR]
- [JavaScript backend](./mobile-services-javascript-backend-define-custom-api.md)
- [.NET backend](./mobile-services-dotnet-backend-define-custom-api.md)

In questo argomento viene illustrato come definire un endpoint API personalizzato in un servizio mobile back-end di .NET. Un'API personalizzata consente di definire endpoint personalizzati con la funzionalità del server, ma non consente di eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura del database. Con un'API personalizzata, è possibile avere maggiore controllo sulla messaggistica, incluse le intestazioni HTTP e il formato del corpo.

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

Per informazioni su come richiamare un'API personalizzata nell'applicazione utilizzando una libreria client di servizi mobili, vedere [Chiamare un'API personalizzata](mobile-services-windows-dotnet-how-to-use-client-library.md#custom-api) nel riferimento client SDK.


<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=AcomDC_0211_2016-->