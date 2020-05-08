---
title: Considerazioni su Novell Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle considerazioni sull'uso di Novell Android con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: bb5950360734bc46923ef18424e3ad1ce275ad7a
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652679"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>Considerazioni sull'uso di Novell Android con MSAL.NET
Questo articolo illustra le considerazioni da tenere presenti quando si usa Novell Android con Microsoft Authentication Library per .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Impostare l'attività padre

In Novell Android impostare l'attività padre in modo che il token venga restituito dopo l'interazione. Ecco un esempio di codice:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

In MSAL 4,2 e versioni successive è anche possibile impostare questa funzionalità a livello di `PublicClientApplication`. A tale scopo, usare un callback:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Se si usa [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin), il `PublicClientApplication` codice del compilatore sarà simile all'esempio seguente.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Verificare che il controllo torni a MSAL 
Quando viene terminata la parte interattiva del flusso di autenticazione, assicurarsi che il controllo torni a MSAL. In Android eseguire l'override `OnActivityResult` del metodo `Activity`di. Chiamare quindi il `SetAuthenticationContinuationEventArgs` metodo della classe `AuthenticationContinuationHelper` MSAL. 

Ad esempio:

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

Questa riga garantisce che il controllo torni a MSAL alla fine della parte interattiva del flusso di autenticazione.

## <a name="update-the-android-manifest"></a>Aggiornare il manifesto Android
Il file *file AndroidManifest. XML* deve contenere i valori seguenti:

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
```
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
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

Sostituire il nome del pacchetto registrato nell'portale di Azure per il `android:host=` valore. Sostituire l'hash della chiave registrato nell'portale di Azure per il `android:path=` valore. L'hash della firma *non* deve essere codificato in URL. Assicurarsi che una barra iniziale (`/`) venga visualizzata all'inizio dell'hash della firma.

In alternativa, [creare l'attività nel codice](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) anziché modificare manualmente *file AndroidManifest. XML*. Per creare l'attività nel codice, creare prima una classe che includa `Activity` l'attributo e `IntentFilter` l'attributo. 

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

### <a name="xamarinforms-43x-manifest"></a>Manifesto Novell. Forms 4.3. X

Novell. Forms 4.3. x genera il codice che `package` imposta l' `com.companyname.{appName}` attributo su in *file AndroidManifest. XML*. Se si usa `DataScheme` As `msal{client_id}`, potrebbe essere necessario modificare il valore in modo che corrisponda al valore dello `MainActivity.cs` spazio dei nomi.

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


## <a name="troubleshoot"></a>Risolvere problemi
È possibile creare una nuova applicazione Novell. Forms e aggiungere un riferimento al pacchetto NuGet MSAL.NET.
Tuttavia, se si aggiorna un'applicazione Novell. Forms esistente a MSAL.NET Preview 1.1.2 o successiva, potrebbero verificarsi problemi di compilazione.

Per risolvere i problemi di compilazione:

- Aggiornare il pacchetto NuGet MSAL.NET esistente a MSAL.NET Preview 1.1.2 o versione successiva.
- Verificare che Novell. Forms venga aggiornato automaticamente alla versione 2.5.0.122203. Se necessario, aggiornare Novell. Forms a questa versione.
- Verificare che Novell. Android. support. v4 venga aggiornato automaticamente alla versione 25.4.0.2. Se necessario, eseguire l'aggiornamento alla versione 25.4.0.2.
- Verificare che tutti i pacchetti Novell. Android. support siano della versione di destinazione 25.4.0.2.
- Pulire o ricompilare l'applicazione.
- In Visual Studio provare a impostare il numero massimo di compilazioni di progetto parallele su 1. A tale scopo, selezionare **Opzioni** > **progetti e soluzioni** > **Compila ed Esegui** > **numero massimo di compilazioni di progetti paralleli**.
- Se si esegue la compilazione dalla riga di comando e il comando `/m`USA, provare a rimuovere questo elemento dal comando.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Errore: il nome AuthenticationContinuationHelper non esiste nel contesto corrente

Se un errore indica che `AuthenticationContinuationHelper` non esiste nel contesto corrente, è possibile che in Visual Studio il file Android. csproj * sia stato aggiornato in modo errato. In alcuni casi il percorso del *netstandard13* *monoandroid90* * \<file di>HintPath* contiene erroneamente netstandard13 anziché monoandroid90.

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
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Novell. iOS, Android, UWP | Semplice app Novell. Forms che illustra come usare MSAL per autenticare gli account personali Microsoft e Azure AD tramite l'endpoint Azure AD 2,0. L'app mostra anche come accedere a Microsoft Graph e visualizzare il token risultante. <br>![Topologia](media/msal-net-xamarin-android-considerations/topology.png) |
