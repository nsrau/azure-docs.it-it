---
title: Considerazioni di Xamarin. Android (Microsoft Authentication Library per .NET) | Azure
description: Informazioni sulle considerazioni specifiche quando si usa Xamarin. Android con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 357c83cfd0ae3fed8b13419e72f50fcb90c04186
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550648"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Considerazioni specifiche di Xamarin Android con MSAL.NET
Questo articolo tratta alcuni aspetti specifici quando si usa Xamarin. Android con Microsoft Authentication Library per .NET (MSAL.NET).

Questo articolo riguarda MSAL.NET 3.x. Se è interessati a MSAL.NET 2.x, vedere [specifiche di Xamarin. Android in MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics-2x).

## <a name="set-the-parent-activity"></a>Impostare l'attività padre

In xamarin. Android, è necessario impostare l'attività padre in modo che il token ottiene nuovamente una volta completato l'interazione è accaduto.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>Garantire controllo torna al MSAL una volta la parte interattiva delle entità finali del flusso di autenticazione
In Android, è necessario eseguire l'override di `OnActivityResult` metodo del `Activity` e chiamare il metodo SetAuthenticationContinuationEventArgs della classe AuthenticationContinuationHelper MSAL.

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```
Tale riga garantisce che il controllo torna al MSAL una volta terminata la parte interattiva del flusso di autenticazione.

## <a name="update-the-android-manifest"></a>Aggiornare il manifesto Android
Il `AndroidManifest.xml` deve contenere i valori seguenti:
```csharp
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msal{client_id}" android:host="auth" />
         </intent-filter>
</activity>
```

## <a name="use-the-embedded-web-view-optional"></a>Usare la visualizzazione web incorporati (facoltativa)

Per impostazione predefinita MSAL.NET usa il browser web del sistema, che consente di ottenere l'accesso SSO con le applicazioni Web e altre app. In alcuni casi rari, è possibile specificare che si desidera utilizzare la visualizzazione web incorporati. Per altre informazioni, vedere [MSAL.NET usa un Web browser](msal-net-web-browsers.md) e [browser del sistema Android](msal-net-system-browser-android-considerations.md).

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>risoluzione dei problemi
Se si crea una nuova applicazione xamarin. Forms e aggiungere un riferimento al pacchetto MSAL.Net NuGet, semplicemente funzionerà.
Tuttavia, se si desidera aggiornare un'applicazione xamarin. Forms esistente a MSAL.NET anteprima 1.1.2 o in un secondo momento si potrebbero riscontrare problemi di compilazione.

Per risolvere questi problemi, è necessario:
- Aggiornare il pacchetto NuGet MSAL.NET esistente all'anteprima MSAL.NET 1.1.2 o versione successiva
- Verificare che xamarin. Forms viene aggiornata automaticamente alla versione 2.5.0.122203 (in caso contrario, l'aggiornamento alla versione corrente)
- Verificare che Xamarin.Android.Support.v4 aggiornata automaticamente alla versione 25.4.0.2 (in caso contrario, l'aggiornamento alla versione corrente)
- Tutti i pacchetti deselezionati devono essere destinato alla versione 25.4.0.2
- Pulire o ricompilazione
- Provare a impostare il progetto parallele massimo si basa su 1 in Visual Studio (Opzioni -> progetti e soluzioni -> Build e Run -> numero massimo di compilazioni di progetti paralleli)
- In alternativa, se si compila dalla riga di comando, provare a rimuovere /m dal comando se si sta utilizzando.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Errore: Il nome 'AuthenticationContinuationHelper' non esiste nel contesto corrente

Si tratta probabilmente perché Visual Studio non non è stato aggiornato correttamente il file Android.csproj*. In alcuni casi il  **\<HintPath >** filepath contiene erroneamente netstandard13 anziché **monoandroid90**.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni dettagliate ed esempi vengono forniti nel [considerazioni specifiche di Android](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) paragrafo del file readme.md dell'esempio seguente:

| Esempio | Piattaforma | Descrizione |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, piattaforma UWP | Una semplice app Xamarin. Forms che illustra come usare MSAL per autenticare l'account del servizio gestito e Azure AD tramite l'endpoint v2.0 di aggiungere e accedere a Microsoft Graph con il token risultante. <br>![Topologia](media/msal-net-xamarin-android-considerations/topology.png) |