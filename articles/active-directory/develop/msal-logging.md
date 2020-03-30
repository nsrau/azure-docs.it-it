---
title: Accesso alle app MSAL Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulla registrazione nelle applicazioni Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 58697cc535357710c6889f05060b5e04e129ae7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084886"
---
# <a name="logging-in-msal-applications"></a>Accesso alle applicazioni MSAL

Le app Microsoft Authentication Library (MSAL) generano messaggi di log che consentono di diagnosticare i problemi. Un'app può configurare la registrazione con alcune righe di codice e avere un controllo personalizzato sul livello di dettaglio, oltre che sulla necessità di registrare o meno i dati personali o dell'organizzazione. È consigliabile creare un callback di registrazione MSAL e fornire agli utenti un modo per inviare i log in caso di problemi di autenticazione.

## <a name="logging-levels"></a>Livelli di registrazione

MSAL fornisce diversi livelli di dettaglio di registrazione:

- Errore: indica che si è verificato un errore e viene generato un errore. Usare questo livello per il debug e l'identificazione dei problemi.
- Avviso: non si è verificato un errore o un errore, ma sono destinati alla diagnostica e all'individuazione dei problemi.
- Info: MSAL registrerà gli eventi destinati a scopi informativi non necessariamente destinati al debug.
- Dettagliato: predefinito. MSAL registra i dettagli completi del comportamento della libreria.

## <a name="personal-and-organizational-data"></a>Dati personali e dell'organizzazione

Per impostazione predefinita, il logger MSAL non acquisisce dati personali o organizzativi altamente sensibili. La libreria offre la possibilità di abilitare la registrazione dei dati personali e dell'organizzazione se si decide di farlo.

Per informazioni dettagliate sulla registrazione MSAL in una determinata lingua, scegliere la scheda corrispondente alla lingua:

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>Registrazione in MSAL.NET

 > [!NOTE]
 > Vedere il [wiki MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) per esempi di registrazione MSAL.NET e altro ancora.

In MSAL 3.x la registrazione viene impostata per le singole applicazioni durante la creazione dell'app usando il modificatore del generatore `.WithLogging`. Questo metodo accetta parametri facoltativi:

- `Level`consente di decidere il livello di registrazione desiderato. Se lo si imposta su Error, verranno restituiti solo gli errori.
- `PiiLoggingEnabled`consente di registrare i dati personali e dell'organizzazione se impostato su true. Per impostazione predefinita, questo parametro è impostato su false, in modo che l'applicazione non registri dati personali.
- `LogCallback`è impostato su un delegato che esegue la registrazione. Se `PiiLoggingEnabled` è vero, questo metodo riceverà i `containsPii` messaggi due volte: una volta con il parametro è false e il messaggio senza dati personali e una seconda volta con il `containsPii` parametro è uguale a true e il messaggio potrebbe contenere dati personali. In alcuni casi, quando il messaggio non contiene dati personali, il messaggio ricevuto è identico.
- `DefaultLoggingEnabled`abilita la registrazione predefinita per la piattaforma. Per impostazione predefinita, questo parametro è impostato su false. Se viene impostato su true, usa Event Tracing nelle applicazioni desktop/UWP, NSLog in iOS e logcat in Android.

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

## <a name="android"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>Accesso a MSAL per Android tramite Java

Attivare la registrazione al momento della creazione dell'app creando un callback di registrazione. Il callback accetta questi parametri:The callback takes these parameters:

- `tag`è una stringa passata al callback dalla libreria. È associato alla voce di registro e può essere utilizzato per ordinare i messaggi di registrazione.
- `logLevel`consente di decidere il livello di registrazione desiderato. I livelli di `Error`log `Warning` `Info`supportati `Verbose`sono: , , , e .
- `message`è il contenuto della voce di registro.
- `containsPII`specifica se i messaggi contenenti dati personali o i dati dell'organizzazione vengono registrati. Per impostazione predefinita, questa opzione è impostata su false, in modo che l'applicazione non registri i dati personali. Se `containsPII` `true`è , questo metodo riceverà i `containsPII` messaggi `false` due `message` volte: una volta con il `containsPii` parametro `true` impostato su e senza dati personali e una seconda volta con il parametro impostato su e il messaggio potrebbe contenere dati personali. In alcuni casi, quando il messaggio non contiene dati personali, il messaggio ricevuto è identico.

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

Per impostazione predefinita, il logger MSAL non acquisisce informazioni personali identificabili o identificabili dall'organizzazione.
Per abilitare la registrazione di informazioni personali identificabili o di informazioni identificabili dall'organizzazione:

```java
Logger.getInstance().setEnablePII(true);
```

Per disabilitare la registrazione dei dati personali e dei dati dell'organizzazione:

```java
Logger.getInstance().setEnablePII(false);
```

Per impostazione predefinita, la registrazione in logcat è disabilitata. Per abilitare:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascript"></a>[Javascript](#tab/javascript)

 Abilitare la registrazione in MSAL.js (JavaScript) passando un `UserAgentApplication` oggetto logger durante la configurazione per la creazione di un'istanza. Questo oggetto logger ha le proprietà seguenti:

- `localCallback`: un'istanza di callback che può essere fornita dallo sviluppatore per utilizzare e pubblicare i log in modo personalizzato. Implementare il metodo localCallback a seconda del modo in cui si intende reindirizzare i log.
- `level`(facoltativo): il livello di registrazione configurabile. I livelli di `Error`log `Warning` `Info`supportati `Verbose`sono: , , , e . Il valore predefinito è `Info`.
- `piiLoggingEnabled`(facoltativo): se impostato su true, registra i dati personali e dell'organizzazione. Per impostazione predefinita, questo è false in modo che l'applicazione non registri i dati personali. I log dei dati personali non vengono mai scritti negli output predefiniti come Console, Logcat o NSLog.
- `correlationId`(facoltativo): un identificatore univoco, utilizzato per eseguire il mapping della richiesta con la risposta a scopo di debug. Per impostazione predefinita, viene usato il GUID RFC 4122 versione 4 (128 bit).

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

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL per iOS e macOS logging-ObjC

Impostare un callback per acquisire la registrazione MSAL e incorporarla nella registrazione dell'applicazione. La firma per il callback è simile alla seguente:The signature for the callback looks like this:

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

Ad esempio:

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

Per impostazione predefinita, MSAL non acquisisce o registra dati personali (PII). La libreria consente agli sviluppatori di app di attivare questa funzionalità tramite una proprietà nella classe MSALLogger. Attivando `pii.Enabled`, l'app si assume la responsabilità di gestire in modo sicuro i dati altamente sensibili e i requisiti normativi.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Livelli di registrazione

Per impostare il livello di registrazione quando si accede utilizzando MSAL per iOS e macOS, utilizzare uno dei seguenti valori:

|Level  |Descrizione |
|---------|---------|
| `MSALLogLevelNothing`| Disabilita tutte le registrazioni |
| `MSALLogLevelError` | Livello predefinito, stampa le informazioni solo quando si verificano errori |
| `MSALLogLevelWarning` | Avvisi |
| `MSALLogLevelInfo` |  Punti di ingresso della libreria, con parametri e varie operazioni del portachiavi |
|`MSALLogLevelVerbose`     |  Tenere traccia delle API |

Ad esempio:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Registrare il formato del messaggio

La parte del messaggio dei messaggi di registro MSAL è nel formato di`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Ad esempio:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Fornire ID di correlazione e timestamp sono utili per tenere traccia dei problemi. Le informazioni sul timestamp e sull'ID di correlazione sono disponibili nel messaggio di log. L'unico posto affidabile per recuperarli è dai messaggi di registrazione MSAL.

## <a name="swift"></a>[Repentino](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL per iOS e macOS logging-Swift

Impostare un callback per acquisire la registrazione MSAL e incorporarla nella registrazione dell'applicazione. La firma (rappresentata in Objective-C) per il callback è simile alla seguente:

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

Ad esempio:

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

Per impostazione predefinita, MSAL non acquisisce o registra dati personali (PII). La libreria consente agli sviluppatori di app di attivare questa funzionalità tramite una proprietà nella classe MSALLogger. Attivando `pii.Enabled`, l'app si assume la responsabilità di gestire in modo sicuro i dati altamente sensibili e i requisiti normativi.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Livelli di registrazione

Per impostare il livello di registrazione quando si accede utilizzando MSAL per iOS e macOS, utilizzare uno dei seguenti valori:

|Level  |Descrizione |
|---------|---------|
| `MSALLogLevelNothing`| Disabilita tutte le registrazioni |
| `MSALLogLevelError` | Livello predefinito, stampa le informazioni solo quando si verificano errori |
| `MSALLogLevelWarning` | Avvisi |
| `MSALLogLevelInfo` |  Punti di ingresso della libreria, con parametri e varie operazioni del portachiavi |
|`MSALLogLevelVerbose`     |  Tenere traccia delle API |

Ad esempio:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Registrare il formato del messaggio

La parte del messaggio dei messaggi di registro MSAL è nel formato di`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Ad esempio:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Fornire ID di correlazione e timestamp sono utili per tenere traccia dei problemi. Le informazioni sul timestamp e sull'ID di correlazione sono disponibili nel messaggio di log. L'unico posto affidabile per recuperarli è dai messaggi di registrazione MSAL.

## <a name="java"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL per la registrazione Java

MSAL per Java consente di utilizzare la libreria di registrazione già in uso con l'app, purché sia compatibile con SLF4J. MSAL per Java utilizza La facciata di registrazione [semplice per Java](http://www.slf4j.org/) (SLF4J) come una semplice facciata o astrazione per vari framework di registrazione, ad esempio [java.util.logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [Logback](http://logback.qos.ch/) e [Log4j](https://logging.apache.org/log4j/2.x/). SLF4J consente all'utente di collegare il framework di registrazione desiderato in fase di distribuzione.

Ad esempio, per usare Logback come framework di registrazione nell'applicazione, aggiungere la dipendenza Logback al file pom di Maven per l'applicazione:

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

SLF4J esegue automaticamente il binding a Logback in fase di distribuzione. I registri MSAL verranno scritti nella console.

Per istruzioni su come eseguire l'associazione ad altri framework di registrazione, vedere il [manuale SLF4J](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Informazioni personali e sull'organizzazione

Per impostazione predefinita, la registrazione MSAL non acquisisce né registra dati personali o organizzativi. Nell'esempio seguente, la registrazione dei dati personali o dell'organizzazione è disattivata per impostazione predefinita:In the following example, logging personal or organizational data is off by default:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Attivare la registrazione dei `logPii()` dati personali e dell'organizzazione impostando il generatore di applicazioni client. Se attivi la registrazione dei dati personali o dell'organizzazione, la tua app deve assumersi la responsabilità di gestire in modo sicuro i dati altamente sensibili e di rispettare eventuali requisiti normativi.

Nell'esempio seguente è abilitata la registrazione dei dati personali o dell'organizzazione:In the following example, logging personal or organizational data is enabled:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL per la registrazione Python

La registrazione in MSAL Python utilizza il `logging.info("msg")` meccanismo di registrazione Python standard, ad esempio È possibile configurare la registrazione MSAL come segue (e vederla in azione nel [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Abilitare la registrazione di debug per tutti i moduliEnable debug logging for all modules

Per impostazione predefinita, la registrazione in qualsiasi script Python è disattivata. Se si desidera abilitare la registrazione di debug per tutti i moduli nell'intero script Python, utilizzare:If you want to enable debug logging for all of the modules in your entire Python script, use:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Silenzio solo registrazione MSAL

Per disattivare solo la registrazione della libreria MSAL, mentre si abilita la registrazione di debug in tutti gli altri moduli nello script Python, disattivare il logger utilizzato da MSAL Python:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Dati personali e organizzativi in Python

MSAL per Python non registra i dati personali o i dati dell'organizzazione. Non esiste alcuna proprietà per attivare o disattivare la registrazione dei dati personali o dell'organizzazione.

È possibile utilizzare la registrazione Standard Python per registrare quello che vuoi, ma sei responsabile della gestione sicura dei dati sensibili e dei seguenti requisiti normativi.

Per ulteriori informazioni sulla registrazione in Python, fare riferimento a [Python's Logging HOWTO](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

---
