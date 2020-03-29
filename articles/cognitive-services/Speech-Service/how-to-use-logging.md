---
title: Registrazione dell'SDK vocale - Servizio di riconoscimento vocaleSpeech SDK logging - Speech service
titleSuffix: Azure Cognitive Services
description: Informazioni su come abilitare la registrazione nell'SDK di riconoscimento vocale (C, C, Python, Objective-C, Java).
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 707a0f801a739a7a91cee19635e609305cd8f021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74805791"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Abilitare la registrazione nell'SDK di riconoscimento vocaleEnable logging in the Speech SDK

La registrazione nel file è una funzionalità facoltativa per Speech SDK. Durante la registrazione dello sviluppo vengono fornite informazioni e diagnostica aggiuntive dai componenti principali di Speech SDK. Può essere abilitata impostando `Speech_LogFilename` la proprietà di un oggetto di configurazione vocale sul percorso e sul nome del file di log. La registrazione verrà attivata a livello globale una volta creato un sistema di riconoscimento da tale configurazione e non può essere disabilitato in seguito. Non è possibile modificare il nome di un file di registro durante una sessione di registrazione in esecuzione.

> [!NOTE]
> La registrazione è disponibile da Speech SDK versione 1.4.0 in tutti i linguaggi di programmazione Speech SDK supportati, ad eccezione di JavaScript.

## <a name="sample"></a>Esempio

Il nome del file di registro viene specificato in un oggetto di configurazione. Prendendo `SpeechConfig` il come esempio e supponendo che `config`sia stata creata un'istanza denominata :

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

È possibile creare un sistema di riconoscimento dall'oggetto di configurazione. In questo modo verrà abilitata la registrazione per tutti i sistemi di riconoscimento.

> [!NOTE]
> Se si `SpeechSynthesizer` crea un dall'oggetto di configurazione, non abiliterà la registrazione. Se la registrazione è abilitata, tuttavia, si riceverà anche la `SpeechSynthesizer`diagnostica dal file .

## <a name="create-a-log-file-on-different-platforms"></a>Creare un file di log su piattaforme diverseCreate a log file on different platforms

Per Windows o Linux, il file di log può trovarsi in qualsiasi percorso per cui l'utente dispone dell'autorizzazione di scrittura. Le autorizzazioni di scrittura per i percorsi del file system in altri sistemi operativi possono essere limitate o limitate per impostazione predefinita.

### <a name="universal-windows-platform-uwp"></a>Piattaforma UWP (Universal Windows Platform)

Le applicazioni UWP devono essere collocate nei file di log in uno dei percorsi dei dati dell'applicazione (locale, roaming o temporaneo). È possibile creare un file di registro nella cartella dell'applicazione locale:A log file can be created in the local application folder:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Ulteriori informazioni sulle autorizzazioni di accesso ai file per le applicazioni UWP sono disponibili [qui](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

È possibile salvare un file di registro nell'archiviazione interna, nell'archiviazione esterna o nella directory della cache. I file creati nella memoria interna o nella directory della cache sono privati per l'applicazione. È preferibile creare un file di registro in un archivio esterno.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Il codice precedente salverà un file di log nell'archivio esterno nella radice di una directory specifica dell'applicazione. Un utente può accedere al file con `Android/data/ApplicationName/logfile.txt`il gestore di file (in genere in ). Il file verrà eliminato quando l'applicazione viene disinstallata.

È inoltre necessario `WRITE_EXTERNAL_STORAGE` richiedere l'autorizzazione nel file manifesto:You also need to request permission in the manifest file:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Ulteriori informazioni su dati e archiviazione di file per le applicazioni Android sono disponibili [qui](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Sono accessibili solo le directory all'interno della sandbox dell'applicazione. I file possono essere creati nelle directory documenti, libreria e temp. I file nella directory dei documenti possono essere resi disponibili a un utente. Il frammento di codice seguente illustra la creazione di un file di log nella directory dei documenti dell'applicazione:The following code snippet shows creation of a log file in the application document directory:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Per accedere a un file creato, `Info.plist` aggiungere le seguenti proprietà all'elenco delle proprietà dell'applicazione:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Ulteriori informazioni su iOS File System sono disponibili [qui](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esplorare gli esempi su GitHub](https://aka.ms/csspeech/samples)
