---
title: Registrazione e diagnostica
description: Spiegazione approfondita di come generare e recuperare la registrazione e la diagnostica negli ancoraggi spaziali di Azure.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f4359db1deda2295a66bcb97cf374d0fe9bc3ef7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74270123"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Registrazione e diagnostica negli ancoraggi spaziali di Azure

Gli ancoraggi spaziali di Azure offrono un meccanismo di registrazione standard utile per lo sviluppo di app. La modalità di registrazione diagnostica degli ancoraggi spaziali è utile quando sono necessarie altre informazioni per il debug. La registrazione diagnostica archivia le immagini dell'ambiente.

## <a name="standard-logging"></a>Registrazione standard
Nell'API ancoraggi spaziali è possibile sottoscrivere il meccanismo di registrazione per ottenere log utili per lo sviluppo e il debug di applicazioni. Le API di registrazione standard non archiviano le immagini dell'ambiente nel disco del dispositivo. L'SDK fornisce questi log come callback di evento. È necessario integrare questi log nel meccanismo di registrazione dell'applicazione.

### <a name="configuration-of-log-messages"></a>Configurazione dei messaggi di log
Sono disponibili due callback di interesse per l'utente. Nell'esempio seguente viene illustrato come configurare la sessione.

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

### <a name="events-and-properties"></a>Eventi e proprietà

Questi callback di eventi vengono forniti per elaborare log ed errori dalla sessione:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): specifica il livello di dettaglio per gli eventi da ricevere dal runtime.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): fornisce eventi di log di debug standard.
- [Errore](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): fornisce gli eventi di log considerati come errori dal runtime.

## <a name="diagnostics-logging"></a>Registrazione diagnostica

Oltre alla modalità di funzionamento standard per la registrazione, gli ancoraggi spaziali hanno anche una modalità di diagnostica. La modalità di diagnostica acquisisce immagini dell'ambiente e le registra sul disco. È possibile utilizzare questa modalità per eseguire il debug di determinati tipi di problemi, ad esempio un errore di individuazione prevedibile di un ancoraggio. Abilitare la registrazione diagnostica solo per riprodurre un problema specifico. Quindi disabilitarlo. Non abilitare la diagnostica quando le app vengono eseguite normalmente.

Durante un'interazione del supporto con Microsoft, un rappresentante Microsoft potrebbe chiedere se si è disposti a inviare un bundle di diagnostica per un'ulteriore analisi. In questo caso, si potrebbe decidere di abilitare la diagnostica e riprodurre il problema in modo che sia possibile inviare il bundle di diagnostica.

Se si invia un log di diagnostica a Microsoft senza riconoscimenti precedenti da parte di un rappresentante Microsoft, l'invio non risponderà.

Le sezioni seguenti illustrano come abilitare la modalità di diagnostica e anche come inviare i log di diagnostica a Microsoft.

### <a name="enable-diagnostics-logging"></a>Abilitare la registrazione diagnostica

Quando si abilita una sessione per la registrazione diagnostica, tutte le operazioni nella sessione hanno la registrazione diagnostica corrispondente nella file system locale. Durante la registrazione, le immagini dell'ambiente vengono salvate sul disco.

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

### <a name="submit-the-diagnostics-bundle"></a>Inviare il bundle di diagnostica

Il frammento di codice seguente illustra come inviare un bundle di diagnostica a Microsoft. Questo bundle includerà le immagini dell'ambiente acquisito dalla sessione dopo l'abilitazione della diagnostica.

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
Il bundle di diagnostica potrebbe contenere le informazioni seguenti:

- **Immagini del fotogramma chiave**: immagini dell'ambiente acquisite durante la sessione durante l'abilitazione della diagnostica.
- **Logs**: registra gli eventi registrati dal runtime.
- **Metadati della sessione**: metadati che identificano la sessione.
