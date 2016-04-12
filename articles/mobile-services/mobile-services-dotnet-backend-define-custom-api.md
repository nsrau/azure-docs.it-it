<properties
	pageTitle="Come definire un'API personalizzata in un servizio mobile back-end di .NET | Servizi mobili di Azure"
	description="Informazioni su come definire un endpoint API personalizzato in un servizio mobile back-end di .NET."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/06/2016"
	ms.author="glenga"/>


# Procedura: definire un endpoint API personalizzato in un servizio mobile back-end di .NET
> [AZURE.SELECTOR]
- [Back-end JavaScript](./mobile-services-javascript-backend-define-custom-api.md)
- [Back-end .NET](./mobile-services-dotnet-backend-define-custom-api.md)

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Per la versione equivalente di questo argomento per le app per dispositivi mobili, vedere [Procedura: Definire un controller API personalizzato](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-custom-api-controller).

In questo argomento viene illustrato come definire un endpoint API personalizzato in un servizio mobile back-end di .NET. Un'API personalizzata consente di definire endpoint personalizzati con la funzionalità del server, ma non consente di eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura del database. Con un'API personalizzata, è possibile avere maggiore controllo sulla messaggistica, incluse le intestazioni HTTP e il formato del corpo.

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

Per informazioni su come richiamare un'API personalizzata nell'applicazione utilizzando una libreria client di servizi mobili, vedere [Chiamare un'API personalizzata](mobile-services-windows-dotnet-how-to-use-client-library.md#custom-api) nel riferimento client SDK.


<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=AcomDC_0309_2016-->