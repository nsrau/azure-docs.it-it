---
title: Configurazione del codice e risoluzione dei problemi di Novell Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle considerazioni sull'uso di Novell Android con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 320d48535c4792a4d610888c6a7030568ccf16bc
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89459845"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Requisiti di configurazione e suggerimenti per la risoluzione dei problemi per Novell Android con MSAL.NET

Quando si usa Novell Android con Microsoft Authentication Library per .NET (MSAL.NET), è necessario apportare alcune modifiche alla configurazione nel codice. Le sezioni seguenti descrivono le modifiche necessarie, seguite da una sezione relativa alla [risoluzione dei problemi](#troubleshooting) che consente di evitare alcuni dei problemi più comuni.

## <a name="set-the-parent-activity"></a>Impostare l'attività padre

In Novell Android impostare l'attività padre in modo che il token venga restituito dopo l'interazione:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

In MSAL.NET 4,2 e versioni successive è anche possibile impostare questa funzionalità a livello di [PublicClientApplication][PublicClientApplication]. A tale scopo, usare un callback:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Se si usa [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin), il [`PublicClientApplication`][PublicClientApplication] codice del compilatore dovrebbe essere simile al frammento di codice seguente:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Verificare che il controllo torni a MSAL

Al termine della parte interattiva del flusso di autenticazione, restituire il controllo a MSAL eseguendo l'override di [`Activity`][Activity] .[`OnActivityResult()`][OnActivityResult] ProcessOnStatus.

Nella sostituzione chiamare MSAL. NET `AuthenticationContinuationHelper` .`SetAuthenticationContinuationEventArgs()` Metodo per restituire il controllo a MSAL alla fine della parte interattiva del flusso di autenticazione.

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest"></a>Aggiornare il manifesto Android

Il file di *AndroidManifest.xml* deve contenere i valori seguenti:

```XML
  <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
  <activity
        android:name="microsoft.identity.client.BrowserTabActivity">
     <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
     </intent-filter>
  </activity>
```

Sostituire il nome del pacchetto registrato nell'portale di Azure per il `android:host=` valore. Sostituire l'hash della chiave registrato nell'portale di Azure per il `android:path=` valore. L'hash della firma *non* deve essere codificato in URL. Assicurarsi che una barra iniziale ( `/` ) venga visualizzata all'inizio dell'hash della firma.

In alternativa, [creare l'attività nel codice](/xamarin/android/platform/android-manifest#the-basics) anziché modificare manualmente *AndroidManifest.xml*. Per creare l'attività nel codice, creare prima una classe che includa l' `Activity` attributo e l' `IntentFilter` attributo.

Di seguito è riportato un esempio di una classe che rappresenta i valori del file XML:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>Manifesto Novell. Forms 4.3. x

Novell. Forms 4.3. x genera il codice che imposta l' `package` attributo su `com.companyname.{appName}` in *AndroidManifest.xml*. Se si usa `DataScheme` As `msal{client_id}` , potrebbe essere necessario modificare il valore in modo che corrisponda al valore dello `MainActivity.cs` spazio dei nomi.

## <a name="use-the-embedded-web-view-optional"></a>Usare la visualizzazione Web incorporata (facoltativo)

Per impostazione predefinita, MSAL.NET usa il Web browser di sistema. Questo browser consente di ottenere Single Sign-On (SSO) usando le applicazioni Web e altre app. In alcuni casi rari, potrebbe essere necessario che il sistema utilizzi una visualizzazione Web incorporata.

In questo esempio di codice viene illustrato come configurare una visualizzazione Web incorporata:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Per altre informazioni, vedere [usare i Web browser per](msal-net-web-browsers.md) le [considerazioni sul browser di sistema](msal-net-system-browser-android-considerations.md)di MSAL.NET e Novell Android.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="general-tips"></a>Suggerimenti generali

- Aggiornare il pacchetto NuGet MSAL.NET esistente alla [versione più recente di MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/).
- Verificare che Novell. Forms sia nella versione più recente.
- Verificare che Novell. Android. support. v4 sia nella versione più recente.
- Verificare che tutti i pacchetti Novell. Android. support siano destinati alla versione più recente.
- Pulire o ricompilare l'applicazione.
- In Visual Studio provare a impostare il numero massimo di compilazioni di progetto parallele su **1**. A tale scopo, selezionare **Opzioni**  >  **progetti e soluzioni**  >  **Compila ed Esegui**  >  **numero massimo di compilazioni di progetti paralleli**.
- Se si esegue la compilazione dalla riga di comando e il comando USA `/m` , provare a rimuovere questo elemento dal comando.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Errore: il nome AuthenticationContinuationHelper non esiste nel contesto corrente

Se un errore indica che `AuthenticationContinuationHelper` non esiste nel contesto corrente, è possibile che in Visual Studio il file *Android. csproj \* * sia stato aggiornato in modo errato. Talvolta il percorso del file nell' `<HintPath>` elemento contiene erroneamente `netstandard13` anziché `monoandroid90` .

Questo esempio contiene un percorso file corretto:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere l'esempio di un' [applicazione per dispositivi mobili Novell che usa la piattaforma di identità Microsoft](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). Nella tabella seguente sono riepilogate le informazioni rilevanti nel file Leggimi.

| Esempio | Piattaforma | Descrizione |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Novell. iOS, Android, UWP | Semplice app Novell. Forms che illustra come usare MSAL per autenticare gli account personali Microsoft e Azure AD tramite l'endpoint Azure AD 2,0. L'app mostra anche come accedere a Microsoft Graph e visualizzare il token risultante. <br>![Diagramma del flusso di autenticazione](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
