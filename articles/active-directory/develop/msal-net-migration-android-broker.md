---
title: Eseguire la migrazione di app Novell Android usando i broker in MSAL.NET
titleSuffix: Microsoft identity platform
description: Informazioni su come eseguire la migrazione di app Novell Android che usano il Microsoft Authenticator o Portale aziendale Intune da ADAL.NET a MSAL.NET.
author: aiwang
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2020
ms.author: aiwang
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 47902b29da1dfe20fb54d633a0559e6a337fd771
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89183633"
---
# <a name="migrate-android-applications-that-use-a-broker-from-adalnet-to-msalnet"></a>Eseguire la migrazione di applicazioni Android che usano un broker da ADAL.NET a MSAL.NET

Se si dispone di un'app Novell per Android che usa attualmente la libreria di autenticazione Azure Active Directory per .NET (ADAL.NET) e un [broker di autenticazione](brokered-auth.md), è possibile eseguire la migrazione a [Microsoft Authentication Library per .NET ](msal-overview.md) (MSAL.NET).

## <a name="prerequisites"></a>Prerequisiti

* Un'app Novell Android è già integrata con un broker ([Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) o [Portale aziendale Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)) e adal.NET di cui è necessario eseguire la migrazione a MSAL.NET.

## <a name="step-1-enable-the-broker"></a>Passaggio 1: abilitare Service Broker

<table>
<tr><td>Codice ADAL corrente:</td><td>Controparte MSAL:</td></tr>
<tr><td>
In ADAL.NET il supporto del broker è abilitato in base al contesto di autenticazione.

Per chiamare il broker, è necessario impostare `useBroker` su *true* nel `PlatformParameters` costruttore:

```CSharp
public PlatformParameters(
        Activity callerActivity,
        bool useBroker)
```

Nel codice renderer della pagina specifica della piattaforma per Android, impostare il `useBroker` flag su true:

```CSharp
page.BrokerParameters = new PlatformParameters(
        this,
        true,
        PromptBehavior.SelectAccount);
```

Quindi, includere i parametri nella chiamata al token di acquisizione:

```CSharp
AuthenticationResult result =
        await
            AuthContext.AcquireTokenAsync(
                Resource,
                ClientId,
                new Uri(RedirectURI),
                platformParameters)
                .ConfigureAwait(false);
```

</td><td>
In MSAL.NET il supporto del broker è abilitato per ogni PublicClientApplication.

`WithBroker()`Per chiamare broker, utilizzare il parametro (che è impostato su true per impostazione predefinita):

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid)
                .Build();
```

Quindi, nella chiamata a AcquireToken:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="step-2-set-an-activity"></a>Passaggio 2: impostare un'attività

In ADAL.NET è stata passata un'attività (in genere MainActivity) come parte di PlatformParameters, come illustrato in [passaggio 1: abilitare il broker](#step-1-enable-the-broker).

MSAL.NET usa anche un'attività, ma non è necessaria in un normale utilizzo di Android senza Broker. Per utilizzare Service Broker, impostare l'attività per l'invio e la ricezione di risposte da Service Broker.

<table>
<tr><td>Codice ADAL corrente:</td><td>Controparte MSAL:</td></tr>
<tr><td>
L'attività viene passata in PlatformParameters nella piattaforma specifica per Android.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>

In MSAL.NET, eseguire due operazioni per impostare l'attività per Android:

1. In `MainActivity.cs` impostare `App.RootViewController`  su `MainActivity` per assicurarsi che sia presente un'attività con la chiamata al broker.

    Se non è impostato correttamente, è possibile che venga ricevuto questo errore: `"Activity_required_for_android_broker":"Activity is null, so MSAL.NET cannot invoke the Android broker. See https://aka.ms/Brokered-Authentication-for-Android"`

1. Nella chiamata AcquireTokenInteractive usare `.WithParentActivityOrWindow(App.RootViewController)` e passare il riferimento all'attività che si userà. In questo esempio verrà usato MainActivity.

**Ad esempio:**

In *app.cs*:

```CSharp
   public static object RootViewController { get; set; }
```

In *MainActivity.cs*:

```CSharp
   LoadApplication(new App());
   App.RootViewController = this;
```

Nella chiamata a AcquireToken:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle considerazioni specifiche di Android quando si usa MSAL.NET con Novell, vedere [requisiti di configurazione e suggerimenti per la risoluzione dei problemi per Novell Android con MSAL.NET](msal-net-xamarin-android-considerations.md).