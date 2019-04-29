---
title: Autenticazione e autorizzazione nel servizio app di Azure per app per dispositivi mobili | Microsoft Docs
description: Riferimento concettuale e panoramica della funzionalità di Autenticazione/Autorizzazione per il servizio app di Azure, soprattutto per le app per dispositivi mobili
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 87bdfcc827155e5dd0a02ffb1640bf7e9cd4e479
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859125"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Autenticazione e autorizzazione nel servizio app di Azure per app per dispositivi mobili

Questo articolo descrive le procedure di autenticazione e autorizzazione durante lo sviluppo di app per dispositivi mobili native con un back-end del servizio app. Il servizio app offre funzionalità integrate di autenticazione e autorizzazione, in modo che le app per dispositivi mobili possano consentire l'accesso agli utenti senza modificare il codice del servizio app. Fornisce un modo semplice per proteggere l'applicazione e utilizzare dati per-utente. 

Questo articolo è incentrato sullo sviluppo di app per dispositivi mobili. Per iniziare rapidamente con le procedure di autenticazione e autorizzazione del servizio app per app per dispositivi mobili, vedere una delle esercitazioni seguenti: [Aggiungere l'autenticazione all'app iOS][iOS] (o [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms] o [Cordova]). 

Per informazioni sul funzionamento delle procedure di autenticazione e autorizzazione nel servizio app, vedere [Autenticazione e autorizzazione nel servizio app di Azure](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Autenticazione con SDK del provider

Dopo aver completato tutte le configurazioni nel servizio app, è possibile modificare i client per dispositivi mobili per l'accesso con il servizio app. Di seguito, sono disponibili due approcci:

* Uso di un SDK pubblicato da un determinato provider di identità per stabilire l'identità e quindi accedere al servizio app.
* Uso di una singola riga di codice per consentire all'SDK client delle app per dispositivi mobili di eseguire la procedura di accesso degli utenti.

> [!TIP]
> La maggior parte delle applicazioni deve usare un SDK del provider per offrire agli utenti un'esperienza di accesso più coerente e per usufruire del supporto dell'aggiornamento del token e di altri vantaggi specificati dal provider.
> 
> 

Quando si usa un SDK del provider, gli utenti possono accedere a un'esperienza più integrata con il sistema operativo in cui viene eseguita l'app. Questo metodo mette anche a disposizione un token del provider e alcune informazioni utente sul client, rendendo molto più semplice usare le API Graph e personalizzare l'esperienza utente. In blog e forum si trova talvolta la definizione "flusso client" o "flusso verso il client", perché il codice sul client esegue la procedura di accesso degli utenti e il codice client ha accesso a un token del provider.

Dopo che è stato ottenuto un token del provider, questo deve essere inviato al servizio app per la convalida. Dopo che il servizio app ha convalidato il token, crea un nuovo token del servizio app che viene restituito al client. L'SDK client delle app per dispositivi mobili include metodi helper per la gestione di questo scambio e l'associazione automatica del token a tutte le richieste per il back-end dell'applicazione. Gli sviluppatori possono anche mantenere un riferimento al token del provider.

Per altre informazioni sul flusso di autenticazione, vedere la sezione [Flusso di autenticazione del servizio app](../app-service/overview-authentication-authorization.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Autenticazione senza SDK del provider

Se non si vuole configurare un SDK del provider, è possibile consentire alla funzionalità App per dispositivi mobili del servizio app di Azure di eseguire la procedura di accesso per conto dell'utente. L'SDK client delle app per dispositivi mobili aprirà una visualizzazione Web per il provider scelto e completerà l'accesso dell'utente. In blog e forum si trova talvolta la definizione "flusso server" o "flusso verso il server", perché il server gestisce il processo di accesso degli utenti e l'SDK client non riceve mai il token del provider.

Il codice necessario per avviare questo flusso è incluso nell'esercitazione sull'autenticazione per ogni piattaforma. Alla fine del flusso, l'SDK del client ha un token del servizio app che viene associato automaticamente a tutte le richieste per il back-end dell'applicazione.

Per altre informazioni sul flusso di autenticazione, vedere la sezione [Flusso di autenticazione del servizio app](../app-service/overview-authentication-authorization.md#authentication-flow). 
## <a name="more-resources"></a>Altre risorse

Le esercitazioni seguenti illustrano come aggiungere l'autenticazione ai client per dispositivi mobili usando il [flusso verso il server](../app-service/overview-authentication-authorization.md#authentication-flow):

* [Aggiungere l'autenticazione all'app iOS][iOS]
* [Aggiungere l'autenticazione all'app Android][Android]
* [Aggiungere l'autenticazione all'app Windows][Windows]
* [Aggiungere l'autenticazione all'app Xamarin.iOS][Xamarin.iOS]
* [Aggiungere l'autenticazione all'app Xamarin.Android][Xamarin.Android]
* [Aggiungere l'autenticazione all'app Xamarin.Forms][Xamarin.Forms]
* [Aggiungere l'autenticazione all'app Cordova][Cordova]

Se si vuole usare il [flusso verso il client](../app-service/overview-authentication-authorization.md#authentication-flow) per Azure Active Directory, vedere le risorse seguenti:

* [Usare Active Directory Authentication Library per iOS][ADAL-iOS]
* [Usare Active Directory Authentication Library per Android][ADAL-Android]
* [Usare Active Directory Authentication Library per Windows e Xamarin][ADAL-dotnet]

Se si vuole usare il [flusso verso il client](../app-service/overview-authentication-authorization.md#authentication-flow) per Facebook, vedere le risorse seguenti:

* [Usare Facebook SDK for iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Se si vuole usare il [flusso verso il client](../app-service/overview-authentication-authorization.md#authentication-flow) per Twitter, vedere le risorse seguenti:

* [Usare Twitter Fabric for iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Se si vuole usare il [flusso verso il client](../app-service/overview-authentication-authorization.md#authentication-flow) per Google, vedere le risorse seguenti:

* [Usare Google Sign-In SDK for iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service/configure-authentication-provider-aad.md
[Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter]: ../app-service/configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
