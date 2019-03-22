---
title: Registrazione e diagnostica di Azure gli ancoraggi spaziali | Microsoft Docs
description: Spiegazione approfondita di come generare e recuperare la registrazione e diagnostica in Azure gli ancoraggi spaziale.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 134023c0884ce3a402b99806f1bf19dcb59ecc32
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57882829"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Registrazione e diagnostica di Azure gli ancoraggi spaziali

Gli ancoraggi spaziali Azure fornisce un meccanismo di registrazione standard che è utile per lo sviluppo di app. La modalità di registrazione diagnostica Anchor spaziale è utile quando sono necessarie altre informazioni per il debug. La registrazione diagnostica archivia le immagini dell'ambiente.

## <a name="standard-logging"></a>Registrazione standard
Nell'API di ancoraggi spaziale, è possibile sottoscrivere il meccanismo di registrazione per ottenere log utili per lo sviluppo di applicazioni e il debug. Le API di registrazione standard non archiviare le immagini dell'ambiente del disco di dispositivo. il SDK fornisce questi log come callback degli eventi. Spetta all'utente per integrare i log nel meccanismo di registrazione dell'applicazione.

### <a name="configuration-of-log-messages"></a>Configurazione dei messaggi di log
Esistono due callback di interesse per l'utente. L'esempio seguente viene illustrato come configurare la sessione.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>Proprietà ed eventi

Questi callback degli eventi vengono forniti per elaborare i log ed errori dalla sessione:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Specifica il livello di dettaglio per gli eventi da ricevere dal runtime.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Fornisce gli eventi di log di debug standard.
- [Errore](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Fornisce gli eventi del log che il runtime considera come errori.

## <a name="diagnostics-logging"></a>Registrazione diagnostica

Oltre alla modalità standard di operazione per la registrazione, Anchor spaziali include anche una modalità di diagnostica. Modalità di diagnostica consente di acquisire le immagini dell'ambiente e li registra nel disco. È possibile utilizzare questa modalità per eseguire il debug di determinati tipi di problemi, ad esempio l'impossibilità di individuare in modo prevedibile un ancoraggio. Abilitare la registrazione diagnostica solo per riprodurre un problema specifico. Quindi disabilitarlo. Non abilitare la diagnostica quando si esegue normalmente l'app.

Durante un'interazione di supporto con Microsoft, un rappresentante Microsoft potrebbe chiedere se si è disposti a presentare un bundle di diagnostica per un'analisi più approfondita. In questo caso, è possibile decidere di abilitare la diagnostica e riprodurre il problema ed è possibile inviare il pacchetto di diagnostica. 

Se si invia un log di diagnostica a Microsoft senza precedenti acknowledgement da un rappresentante Microsoft, l'invio passerà senza risposta.

Le sezioni seguenti illustrano come abilitare la modalità di diagnostica e anche come inviare i log di diagnostica a Microsoft.

### <a name="enable-diagnostics-logging"></a>Abilitare la registrazione diagnostica

Quando si attiva una sessione per la registrazione diagnostica, tutte le operazioni nella sessione hanno corrispondente la registrazione diagnostica nel file system locale. Durante la registrazione, le immagini dell'ambiente vengono salvate su disco.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>Inviare il pacchetto di diagnostica

Il frammento di codice seguente illustra come inviare un bundle di diagnostica a Microsoft. Questo bundle include le immagini dell'ambiente di acquisiti dalla sessione dopo aver abilitato la diagnostica. 

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>Parti di un bundle di diagnostica
Il pacchetto di diagnostica potrebbe contenere le informazioni seguenti:

- **Le immagini di fotogramma chiave**: Immagini dell'ambiente di acquisiti durante la sessione mentre diagnostica sono stata abilitata.
- **Log**: Registrare gli eventi registrati dal runtime.
- **Metadati della sessione**: Metadati che identifica la sessione.
