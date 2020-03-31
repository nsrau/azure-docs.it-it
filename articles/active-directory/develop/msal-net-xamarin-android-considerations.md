---
title: Considerazioni su Xamarin Android (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle considerazioni per l'uso di Xamarin Android con Microsoft Authentication Library for .NET (MSAL.NET).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b55253d757f641979c6f72001803d7d38d9af3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132509"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>Considerazioni sull'utilizzo di Xamarin Android con MSAL.NET
In questo articolo vengono illustrati gli aspetti da considerare quando si usa Xamarin Android con Microsoft Authentication Library per .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Impostare l'attività padre

In Xamarin Android, imposta l'attività padre in modo che il token restituisca dopo l'interazione. Ecco un esempio di codice:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

In MSAL 4.2 e versioni successive, è anche `PublicClientApplication`possibile impostare questa funzionalità al livello di . A tale scopo, utilizzare un callback:To do so, use a callback:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Se si utilizza [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin), il codice del `PublicClientApplication` generatore sarà simile all'esempio seguente.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Assicurarsi che il controllo ritorni a MSAL 
Al termine della parte interattiva del flusso di autenticazione, assicurarsi che il controllo torni a MSAL. In Android, `OnActivityResult` eseguire `Activity`l'override del metodo di . Chiamare quindi `SetAuthenticationContinuationEventArgs` il `AuthenticationContinuationHelper` metodo della classe MSAL. 

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

Questa riga garantisce che il controllo ritorni a MSAL alla fine della parte interattiva del flusso di autenticazione.

## <a name="update-the-android-manifest"></a>Aggiornare il manifesto AndroidUpdate the Android manifest
Il file AndroidManifest.xml deve contenere i valori seguenti:The *AndroidManifest.xml* file should contain the following values:

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

Sostituire il nome del pacchetto registrato nel `android:host=` portale di Azure con il valore. Sostituire l'hash della chiave registrato nel `android:path=` portale di Azure con il valore. L'hash della firma *non* deve essere codificato in URL. Assicurarsi che all'inizio dell'hash della firma venga visualizzata una barra iniziale (`/`).

In alternativa, [creare l'attività nel codice](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) anziché modificare manualmente *AndroidManifest.xml*. Per creare l'attività nel codice, creare `Activity` innanzitutto `IntentFilter` una classe che include l'attributo e l'attributo. 

Di seguito è riportato un esempio di classe che rappresenta i valori del file XML:Here's an example of a class that represents the values of the XML file:

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

### <a name="xamarinforms-43x-manifest"></a>Xamarin.Forms 4.3.X manifesto

Xamarin.Forms 4.3.x genera codice `package` che `com.companyname.{appName}` imposta l'attributo su *AndroidManifest.xml*. Se si `DataScheme` `msal{client_id}`utilizza come , è possibile modificare il valore `MainActivity.cs` in modo che corrisponda al valore dello spazio dei nomi.

## <a name="use-the-embedded-web-view-optional"></a>Utilizzare la visualizzazione Web incorporata (facoltativo)

Per impostazione predefinita, MSAL.NET utilizza il browser Web di sistema. Questo browser consente di ottenere Single Sign-On (SSO) utilizzando applicazioni Web e altre applicazioni. In alcuni rari casi, è possibile che si desideri che il sistema utilizzi una visualizzazione Web incorporata. 

Questo esempio di codice mostra come impostare una visualizzazione Web incorporata:This code example shows how to set up an embedded web view:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Per ulteriori informazioni, consultate [Utilizzare i browser Web per considerazioni sull'MSAL.NET](msal-net-web-browsers.md) e [Xamarin per i browser](msal-net-system-browser-android-considerations.md)di sistema Android .


## <a name="troubleshoot"></a>Risolvere i problemi
È possibile creare una nuova applicazione Xamarin.Forms e aggiungere un riferimento al pacchetto NuGet MSAL.NET.
Tuttavia, se si aggiorna un'applicazione Xamarin.Forms esistente a MSAL.NET'anteprima 1.1.2 o versione successiva, è possibile che si verifichino problemi di compilazione.

Per risolvere i problemi di compilazione:

- Aggiornare il pacchetto NuGet MSAL.NET esistente per MSAL.NET'anteprima 1.1.2 o versione successiva.
- Verificare che Xamarin.Forms sia aggiornato automaticamente alla versione 2.5.0.122203. Se necessario, aggiornare Xamarin.Forms a questa versione.
- Verifica che Xamarin.Android.Support.v4 sia aggiornato automaticamente alla versione 25.4.0.2. Se necessario, eseguire l'aggiornamento alla versione 25.4.0.2.If necessary, update to version 25.4.0.2.
- Assicurarsi che tutti i pacchetti Xamarin.Android.Support destinati alla versione 25.4.0.2.Ensure all the Xamarin.Android.Support packages target version 25.4.0.2.
- Pulire o ricompilare l'applicazione.
- In Visual Studio provare a impostare il numero massimo di compilazioni di progetto parallele su 1.In Visual Studio, try setting the maximum number of parallel project builds to 1. A tale scopo, selezionare **Opzioni** > **progetti e soluzioni** > **compilazione ed eseguire** > **Il numero massimo di compilazioni di progetti paralleli**.
- Se si sta compilando dalla riga `/m`di comando e il comando utilizza , provare a rimuovere questo elemento dal comando.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Errore: il nome AuthenticationContinuationHelper non esiste nel contesto corrente

Se un errore `AuthenticationContinuationHelper` indica che non esiste nel contesto corrente, Visual Studio potrebbe aver aggiornato in modo non corretto il file Android.csproj. In * \<* alcuni caso il percorso del file di>HintPath contiene in modo non corretto *netstandard13* anziché *monoandroid90*.

Questo esempio contiene un percorso di file corretto:This example contains a correct file path:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere l'esempio di [un'applicazione mobile Xamarin che utilizza Microsoft Identity Platform](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). Nella tabella seguente sono riepilogate le informazioni rilevanti contenute nel file README.

| Esempio | Piattaforma | Descrizione |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, UWP | Una semplice app Xamarin.Forms che mostra come usare MSAL per autenticare gli account personali Microsoft e Azure AD tramite l'endpoint di Azure AD 2.0. L'app mostra anche come accedere a Microsoft Graph e mostra il token risultante. <br>![Topologia](media/msal-net-xamarin-android-considerations/topology.png) |
