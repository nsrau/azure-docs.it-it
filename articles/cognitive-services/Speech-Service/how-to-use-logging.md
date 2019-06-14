---
title: Registrazione vocale SDK - servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Abilitare la registrazione nel SDK di riconoscimento vocale.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 4573ff14c5326a47cf7b15ae2660614661d45d80
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073077"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Abilitare la registrazione in Speech SDK

La registrazione al file è una funzionalità facoltativa per il SDK di riconoscimento vocale. Durante lo sviluppo la registrazione fornisce informazioni aggiuntive e la diagnostica di componenti di base del SDK Speech. Può essere abilitato impostando la proprietà `Speech_LogFilename` su un oggetto di configurazione di riconoscimento vocale per il percorso e nome del file di log. La registrazione verrà attivata a livello globale dopo aver creato un sistema di riconoscimento da tale configurazione e non può essere disabilitata in un secondo momento. È possibile modificare il nome di un file di log durante un'esecuzione della sessione di registrazione.

> [!NOTE]
> La registrazione è disponibile poiché Speech SDK versione 1.4.0 tutte supportate Speech SDK, linguaggi di programmazione, fatta eccezione per JavaScript.

## <a name="sample"></a>Esempio

Il nome del file di registro è specificato in un oggetto di configurazione. Richiede la `SpeechConfig` come esempio e presupponendo di avere creato un'istanza denominata `config`:

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

È possibile creare un riconoscimento dall'oggetto di configurazione. Abilita la registrazione per tutti i tipi di riconoscimento.

> [!NOTE]
> Se si crea un `SpeechSynthesizer` dell'oggetto di configurazione, non sarà possibile abilitare la registrazione. Se tuttavia viene abilitata la registrazione, si riceverà anche la diagnostica del `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Creare un file di registro su piattaforme diverse

Per Windows o Linux, il file di log può essere in qualsiasi percorso in cui per che l'utente dispone dell'autorizzazione di scrittura. Autorizzazioni di scrittura per i percorsi del file system in altri sistemi operativi potrebbero essere limitate o limitate per impostazione predefinita.

### <a name="universal-windows-platform-uwp"></a>Piattaforma UWP (Universal Windows Platform)

Applicazioni della piattaforma UWP devono essere percorsi file di log in uno dei percorsi dei dati dell'applicazione (locali, mobili o temporanei). Un file di log può essere creato nella cartella dell'applicazione locale:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Più informazioni sull'accesso ai file per le applicazioni UWP è disponibile l'autorizzazione [qui](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

È possibile salvare un file di log per l'archiviazione interna, risorsa di archiviazione esterna o la directory della cache. I file creati nell'archiviazione interna o la directory della cache sono privati per l'applicazione. È preferibile per creare un file di log in un archivio esterno.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Il codice sopra riportato salverà un file di log nell'archiviazione esterna nella radice di una directory specifiche dell'applicazione. Un utente può accedere al file con il gestore di file (in genere in `Android/data/ApplicationName/logfile.txt`). Il file verrà eliminato quando viene disinstallata l'applicazione.

È anche necessario richiedere `WRITE_EXTERNAL_STORAGE` autorizzazione nel file manifesto:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Più informazioni su dati e file di archiviazione per le applicazioni Android è disponibile [qui](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Solo le directory all'interno di sandbox dell'applicazione sono accessibili. File possono essere creati i documenti, libreria e delle directory temp. I file nella directory dei documenti possono essere resi disponibili a un utente. Il frammento di codice seguente illustra la creazione di un file di log nella directory del documento dell'applicazione:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Per accedere a un file creato, aggiungere il sotto le proprietà per il `Info.plist` elenco di proprietà dell'applicazione:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Più su iOS File System è disponibile [qui](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esplorare gli esempi su GitHub](https://aka.ms/csspeech/samples)

