---
title: Registrazione nelle applicazioni MSAL | Azure
description: Informazioni sulla registrazione nelle applicazioni Microsoft Authentication Library (MSAL).
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
ms.date: 04/22/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 475b692a29edf5cdd05552e7b5c3dc5fde210275
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512518"
---
# <a name="logging"></a>Registrazione
Le app Microsoft Authentication Library (MSAL) consentono di generare messaggi di log che possono rivelarsi utili per diagnosticare i problemi e fornire dettagli. Un'app può configurare la registrazione con alcune righe di codice e avere un controllo personalizzato sul livello di dettaglio, oltre che sulla necessità di registrare o meno i dati personali o dell'organizzazione. È consigliabile impostare un callback di registrazione MSAL e fornire agli utenti un modo per inviare i log in caso di problemi di autenticazione.

## <a name="logging-levels"></a>Livelli di registrazione

Il logger di MSAL consente l'acquisizione di diversi livelli di dettaglio:

- Errore: indica che si è verificato un errore e che è stato generato un errore. Usare questo livello per il debug e l'identificazione dei problemi.
- Avviso: indica eventi dubbi per cui l'app necessita di altre informazioni. Non si è verificato necessariamente un errore o un problema, ma questo livello è specifico per la diagnostica e l'individuazione dei problemi.
- Info: MSAL registrerà gli eventi a scopo informativo e non necessariamente ai fini del debug.
- Verbose: Valore predefinito. MSAL registrerà una grande quantità di informazioni, fornendo i dettagli completi sul comportamento della libreria.

## <a name="personal-and-organizational-data"></a>Dati personali e dell'organizzazione
Per impostazione predefinita, il logger di MSAL non acquisisce dati personali o dell'organizzazione altamente sensibili. La libreria offre la possibilità di abilitare la registrazione di questi due tipi di dati se si decide di procedere in tal senso.

## <a name="logging-in-msalnet"></a>Registrazione in MSAL.NET

 > [!NOTE]
 > Per altre informazioni su MSAL.NET, vedere il [wiki di MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki). Ottenere esempi di registrazione MSAL.NET e altro ancora. 
 
In MSAL 3.x la registrazione viene impostata per le singole applicazioni durante la creazione dell'app usando il modificatore del generatore `.WithLogging`. Questo metodo accetta parametri facoltativi:

- *Level* consente di decidere il livello di registrazione da applicare. Se lo si imposta su Error, verranno restituiti solo gli errori.
- *PiiLoggingEnabled* consente di registrare i dati personali e dell'organizzazione se viene impostato su true. Per impostazione predefinita, questo parametro è impostato su false, in modo che l'applicazione non registri dati personali.
- *LogCallback* è impostato su un delegato che effettua la registrazione. Se *PiiLoggingEnabled* è impostato su true, questo metodo riceverà i messaggi due volte: una volta con il parametro *containsPii* impostato su false e il messaggio senza dati personali e una seconda volta con il parametro *containsPii* impostato su true e il messaggio probabilmente con dati personali. In alcuni casi, quando il messaggio non contiene dati personali, il messaggio ricevuto è identico.
- *DefaultLoggingEnabled* consente di abilitare la registrazione predefinita per la piattaforma. Per impostazione predefinita, questo parametro è impostato su false. Se viene impostato su true, usa Event Tracing nelle applicazioni desktop/UWP, NSLog in iOS e logcat in Android.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>Registrazione in MSAL.js

 È possibile abilitare la registrazione in MSAL.js passando un oggetto logger durante la configurazione per creare un'istanza di `UserAgentApplication`. Questo oggetto logger ha le proprietà seguenti:

- *localCallback*: un'istanza di callback che può essere fornita dallo sviluppatore per utilizzare e pubblicare i log in modo personalizzato. Implementare il metodo localCallback a seconda del modo in cui si intende reindirizzare i log.

- *level* (facoltativa): il livello di log configurabile. I livelli supportati sono i seguenti: Error, Warning, Info e Verbose. Il valore predefinito è Info.

- *piiLoggingEnabled* (facoltativa): consente di registrare i dati personali e dell'organizzazione se viene impostata su true. Per impostazione predefinita, questa proprietà è impostata su false, in modo che l'applicazione non registri dati personali. I log dei dati personali non vengono mai scritti negli output predefiniti come Console, Logcat o NSLog. Il valore predefinito è false.

- *correlationId* (facoltativa): un identificatore univoco usato per eseguire il mapping della richiesta con la risposta a scopo di debug. Per impostazione predefinita, viene usato il GUID RFC 4122 versione 4 (128 bit).

```javascript

function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
    system: {
        logger: {
            localCallback: loggerCallback,
            level: Msal.LogLevel.Verbose,
            piiLoggingEnabled: false,
            correlationId: '1234'
        }
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```
