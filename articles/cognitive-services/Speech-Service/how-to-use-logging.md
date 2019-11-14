---
title: Registrazione dell'SDK vocale-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come abilitare la registrazione nell'SDK di riconoscimento vocaleC++( C#,, Python, Objective-C, Java).
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 58f6c97ac819947f84735bc0bc4c125b43db58dc
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075793"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Abilitare la registrazione nell'SDK di riconoscimento vocale

La registrazione nel file è una funzionalità facoltativa per l'SDK di riconoscimento vocale. Durante la registrazione dello sviluppo fornisce informazioni e diagnostica aggiuntive dai componenti di base di Speech SDK. Può essere abilitata impostando la proprietà `Speech_LogFilename` in un oggetto di configurazione vocale sul percorso e il nome del file di log. La registrazione verrà attivata globalmente una volta che un riconoscimento viene creato da tale configurazione e non può essere disabilitato in seguito. Non è possibile modificare il nome di un file di log durante una sessione di registrazione in esecuzione.

> [!NOTE]
> La registrazione è disponibile dalla versione dell'SDK vocale 1.4.0 in tutti i linguaggi di programmazione dell'SDK vocale supportati, ad eccezione di JavaScript.

## <a name="sample"></a>Esempio

Il nome del file di log viene specificato in un oggetto di configurazione. Prendendo il `SpeechConfig` come esempio e supponendo che sia stata creata un'istanza denominata `config`:

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

È possibile creare un riconoscimento dall'oggetto config. In questo modo verrà abilitata la registrazione per tutti i riconoscitori.

> [!NOTE]
> Se si crea un `SpeechSynthesizer` dall'oggetto config, non verrà abilitata la registrazione. Se la registrazione è abilitata, tuttavia, si riceverà anche la diagnostica dal `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Creare un file di log su piattaforme diverse

Per Windows o Linux, il file di log può trovarsi in qualsiasi percorso per cui l'utente disponga dell'autorizzazione di scrittura. Le autorizzazioni di scrittura per file system percorsi in altri sistemi operativi possono essere limitate o limitate per impostazione predefinita.

### <a name="universal-windows-platform-uwp"></a>Piattaforma UWP (Universal Windows Platform)

Le applicazioni UWP devono essere posizionate nei file di log in uno dei percorsi dati applicazione (locale, roaming o temporaneo). È possibile creare un file di log nella cartella dell'applicazione locale:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Altre informazioni sulle autorizzazioni di accesso ai file per le applicazioni UWP sono disponibili [qui](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

È possibile salvare un file di log in una risorsa di archiviazione interna, in uno spazio di archiviazione esterno o nella directory della cache. I file creati nella risorsa di archiviazione interna o nella directory della cache sono privati dell'applicazione. È preferibile creare un file di log in un archivio esterno.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Il codice precedente consente di salvare un file di log nella risorsa di archiviazione esterna nella radice di una directory specifica dell'applicazione. Un utente può accedere al file con gestione file (in genere in `Android/data/ApplicationName/logfile.txt`). Il file verrà eliminato quando l'applicazione viene disinstallata.

È anche necessario richiedere l'autorizzazione `WRITE_EXTERNAL_STORAGE` nel file manifesto:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Altre informazioni sull'archiviazione di file e dati per le applicazioni Android sono disponibili [qui](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Sono accessibili solo le directory all'interno dell'applicazione sandbox. I file possono essere creati in documenti, raccolte e directory temporanee. I file nella directory Documents possono essere resi disponibili per un utente. Il frammento di codice seguente mostra la creazione di un file di log nella directory del documento dell'applicazione:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Per accedere a un file creato, aggiungere le proprietà seguenti all'elenco di proprietà `Info.plist` dell'applicazione:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Altre informazioni sul file System iOS sono disponibili [qui](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esplorare gli esempi su GitHub](https://aka.ms/csspeech/samples)
