---
title: Registrazione nelle app MSAL | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulla registrazione nelle applicazioni Microsoft Authentication Library (MSAL).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a71093ee05e032a5df89bb0560b6daa82e2fbf2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916146"
---
# <a name="logging-in-msal-applications"></a>Registrazione nelle applicazioni MSAL

Le app Microsoft Authentication Library (MSAL) generano messaggi di log che consentono di diagnosticare i problemi. Un'app può configurare la registrazione con alcune righe di codice e avere un controllo personalizzato sul livello di dettaglio, oltre che sulla necessità di registrare o meno i dati personali o dell'organizzazione. È consigliabile creare un callback di registrazione MSAL e fornire agli utenti un modo per inviare i log quando si verificano problemi di autenticazione.

## <a name="logging-levels"></a>Livelli di registrazione

MSAL offre diversi livelli di dettaglio per la registrazione:

- Errore: indica che si è verificato un errore e che è stato generato un errore. Usare questo livello per il debug e l'identificazione dei problemi.
- Avviso: non è stato necessariamente generato un errore o un errore, ma sono destinati alla diagnostica e alla individuazione dei problemi.
- Info: MSAL registrerà gli eventi destinati a scopi informativi non necessariamente destinati al debug.
- Verbose: valore predefinito. MSAL registra i dettagli completi del comportamento della libreria.

## <a name="personal-and-organizational-data"></a>Dati personali e dell'organizzazione

Per impostazione predefinita, il logger MSAL non acquisisce dati personali o aziendali altamente sensibili. La libreria fornisce la possibilità di abilitare la registrazione dei dati personali e aziendali se si decide di eseguire questa operazione.

Per informazioni dettagliate sulla registrazione di MSAL in una lingua specifica, scegliere la scheda corrispondente alla lingua:

## <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>Registrazione in MSAL.NET

 > [!NOTE]
 > Vedere il [wiki di MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) per esempi di registrazione di MSAL.NET e altro ancora.

In MSAL 3.x la registrazione viene impostata per le singole applicazioni durante la creazione dell'app usando il modificatore del generatore `.WithLogging`. Questo metodo accetta parametri facoltativi:

- `Level` consente di scegliere il livello di registrazione desiderato. Se lo si imposta su Error, verranno restituiti solo gli errori.
- `PiiLoggingEnabled` consente di registrare dati personali e aziendali se impostati su true. Per impostazione predefinita, questo parametro è impostato su false, in modo che l'applicazione non registri dati personali.
- `LogCallback` viene impostato su un delegato che esegue la registrazione. Se `PiiLoggingEnabled` è true, questo metodo riceverà i messaggi due volte: una volta con il parametro `containsPii` uguale a false e il messaggio senza dati personali e una seconda volta con il parametro `containsPii` uguale a true e il messaggio potrebbe contenere dati personali. In alcuni casi, quando il messaggio non contiene dati personali, il messaggio ricevuto è identico.
- `DefaultLoggingEnabled` Abilita la registrazione predefinita per la piattaforma. Per impostazione predefinita, questo parametro è impostato su false. Se viene impostato su true, usa Event Tracing nelle applicazioni desktop/UWP, NSLog in iOS e logcat in Android.

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

## <a name="androidtabandroid"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>Registrazione in MSAL per Android con Java

Attivare la registrazione durante la creazione dell'app creando un callback di registrazione. Il callback accetta i parametri seguenti:

- `tag` è una stringa passata al callback dalla libreria. È associato alla voce di log e può essere usato per ordinare i messaggi di registrazione.
- `logLevel` consente di scegliere il livello di registrazione desiderato. I livelli di registrazione supportati sono: `Error`, `Warning`, `Info`e `Verbose`.
- `message` è il contenuto della voce di log.
- `containsPII` specifica se i messaggi contenenti dati personali o i dati aziendali vengono registrati. Per impostazione predefinita, questo valore è impostato su false, in modo che l'applicazione non registri i dati personali. Se `containsPII` è `true`, questo metodo riceverà i messaggi due volte: una volta con il parametro `containsPII` impostato su `false` e il `message` senza dati personali e una seconda volta con il `containsPii` parametro impostato su `true` e il messaggio potrebbe contenere dati personali. In alcuni casi, quando il messaggio non contiene dati personali, il messaggio ricevuto è identico.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

Per impostazione predefinita, il logger MSAL non acquisisce informazioni personali o informazioni identificabili dall'organizzazione.
Per abilitare la registrazione di informazioni personali o informazioni identificabili dall'organizzazione:

```java
Logger.getInstance().setEnablePII(true);
```

Per disabilitare la registrazione dei dati personali e delle organizzazioni:

```java
Logger.getInstance().setEnablePII(false);
```

Per impostazione predefinita, la registrazione in logcat è disabilitata. Per abilitare:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

 Abilitare la registrazione in MSAL. js (JavaScript) passando un oggetto logger durante la configurazione per la creazione di un'istanza di `UserAgentApplication`. Questo oggetto logger ha le proprietà seguenti:

- `localCallback`: istanza di callback che può essere fornita dallo sviluppatore per utilizzare e pubblicare i log in modo personalizzato. Implementare il metodo localCallback a seconda del modo in cui si intende reindirizzare i log.
- `level` (facoltativo): il livello di registrazione configurabile. I livelli di registrazione supportati sono: `Error`, `Warning`, `Info`e `Verbose`. Il valore predefinito è `Info`.
- `piiLoggingEnabled` (facoltativo): se impostato su true, registra i dati personali e aziendali. Per impostazione predefinita, questo valore è false in modo che l'applicazione non registri i dati personali. I log dei dati personali non vengono mai scritti negli output predefiniti come Console, Logcat o NSLog.
- `correlationId` (facoltativo): identificatore univoco, usato per eseguire il mapping della richiesta con la risposta a scopo di debug. Per impostazione predefinita, viene usato il GUID RFC 4122 versione 4 (128 bit).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
    },
     system: {
             logger: new Msal.Logger(
                                loggerCallback ,{
                                     level: Msal.LogLevel.Verbose,
                                     piiLoggingEnabled: false,
                                     correlationId: '1234'
                                }
                        )
     }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL per la registrazione di iOS e macOS-ObjC

Impostare un callback per acquisire la registrazione MSAL e incorporarla nella registrazione dell'applicazione. La firma per il callback ha un aspetto simile al seguente:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

ad esempio:

```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

### <a name="personal-data"></a>Dati personali

Per impostazione predefinita, MSAL non acquisisce né registra i dati personali (PII). La libreria consente agli sviluppatori di app di attivare questa operazione tramite una proprietà nella classe MSALLogger. Attivando `pii.Enabled`, l'app assume la responsabilità di gestire in modo sicuro i dati altamente sensibili e i requisiti normativi.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Livelli di registrazione

Per impostare il livello di registrazione quando si registra usando MSAL per iOS e macOS, usare uno dei valori seguenti:

|Level  |Description |
|---------|---------|
| `MSALLogLevelNothing`| Disabilitare tutte le registrazioni |
| `MSALLogLevelError` | Livello predefinito, stampa le informazioni solo quando si verificano errori |
| `MSALLogLevelWarning` | Avvisi |
| `MSALLogLevelInfo` |  Punti di ingresso della libreria, con parametri e varie operazioni Keychain |
|`MSALLogLevelVerbose`     |  Tenere traccia delle API |

ad esempio:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Formato messaggi registro

La parte del messaggio dei messaggi di log di MSAL è nel formato `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

ad esempio:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Fornire ID di correlazione e timestamp è utile per tenere traccia dei problemi. Le informazioni sul timestamp e sull'ID di correlazione sono disponibili nel messaggio del log. L'unica posizione affidabile per recuperarli è MSAL i messaggi di registrazione.

## <a name="swifttabswift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL per la registrazione di iOS e macOS-Swift

Impostare un callback per acquisire la registrazione MSAL e incorporarla nella registrazione dell'applicazione. La firma (rappresentata in Objective-C) per il callback ha un aspetto simile al seguente:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

ad esempio:

```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-data"></a>Dati personali

Per impostazione predefinita, MSAL non acquisisce né registra i dati personali (PII). La libreria consente agli sviluppatori di app di attivare questa operazione tramite una proprietà nella classe MSALLogger. Attivando `pii.Enabled`, l'app assume la responsabilità di gestire in modo sicuro i dati altamente sensibili e i requisiti normativi.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Livelli di registrazione

Per impostare il livello di registrazione quando si registra usando MSAL per iOS e macOS, usare uno dei valori seguenti:

|Level  |Description |
|---------|---------|
| `MSALLogLevelNothing`| Disabilitare tutte le registrazioni |
| `MSALLogLevelError` | Livello predefinito, stampa le informazioni solo quando si verificano errori |
| `MSALLogLevelWarning` | Avvisi |
| `MSALLogLevelInfo` |  Punti di ingresso della libreria, con parametri e varie operazioni Keychain |
|`MSALLogLevelVerbose`     |  Tenere traccia delle API |

ad esempio:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Formato messaggi registro

La parte del messaggio dei messaggi di log di MSAL è nel formato `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

ad esempio:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Fornire ID di correlazione e timestamp è utile per tenere traccia dei problemi. Le informazioni sul timestamp e sull'ID di correlazione sono disponibili nel messaggio del log. L'unica posizione affidabile per recuperarli è MSAL i messaggi di registrazione.

## <a name="javatabjava"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL per la registrazione Java

MSAL per Java consente di usare la libreria di registrazione già in uso con l'app, purché sia compatibile con SLF4J. MSAL per Java usa la [semplice registrazione Facade per Java](http://www.slf4j.org/) (SLF4J) come facciata o astrazione semplice per vari Framework di registrazione, ad esempio [java. util. Logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [Logback](http://logback.qos.ch/) e [log4j](https://logging.apache.org/log4j/2.x/). SLF4J consente all'utente di inserire il Framework di registrazione desiderato in fase di distribuzione.

Ad esempio, per usare Logback come Framework di registrazione nell'applicazione, aggiungere la dipendenza Logback al file POM di Maven per l'applicazione:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Aggiungere quindi il file di configurazione Logback:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J viene associato automaticamente a Logback al momento della distribuzione. I log MSAL verranno scritti nella console.

Per istruzioni su come eseguire l'associazione ad altri Framework di registrazione, vedere il [Manuale di SLF4J](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Informazioni personali e aziendali

Per impostazione predefinita, la registrazione di MSAL non acquisisce né registra dati personali o aziendali. Nell'esempio seguente la registrazione di dati personali o aziendali è disattivata per impostazione predefinita:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Attivare la registrazione dei dati personali e aziendali impostando `logPii()` nel generatore di applicazioni client. Se si attiva la registrazione dei dati personali o aziendali, l'app deve avere la responsabilità di gestire in modo sicuro i dati altamente sensibili e rispettare eventuali requisiti normativi.

Nell'esempio seguente viene abilitata la registrazione dei dati personali o aziendali:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="pythontabpython"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL per la registrazione di Python

La registrazione in MSAL Python usa il meccanismo di registrazione standard di Python, ad esempio `logging.info("msg")` è possibile configurare la registrazione MSAL come indicato di seguito (e visualizzarla in azione nel [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Abilitare la registrazione di debug per tutti i moduli

Per impostazione predefinita, la registrazione in qualsiasi script Python è disattivata. Se si vuole abilitare la registrazione del debug per tutti i moduli nell'intero script Python, usare:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Tacita solo registrazione MSAL

Per tacitare solo la registrazione della libreria MSAL, abilitando la registrazione del debug in tutti gli altri moduli nello script Python, disattivare il logger usato da MSAL Python:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Dati personali e aziendali in Python

MSAL per Python non registra i dati personali o i dati dell'organizzazione. Non è disponibile alcuna proprietà per attivare o disattivare la registrazione dei dati personali o aziendali.

È possibile usare la registrazione standard di Python per registrare il contenuto desiderato, ma si è responsabili della gestione sicura dei dati sensibili e dei requisiti normativi.

Per ulteriori informazioni sulla registrazione in Python, fare riferimento all'HOWTO sulla [registrazione](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)di Python.

---
