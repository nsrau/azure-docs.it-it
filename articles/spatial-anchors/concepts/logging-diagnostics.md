---
title: Registrazione e diagnostica
description: Spiegazione approfondita di come generare e recuperare la registrazione e la diagnostica in Azure Spatial Anchors.In-depth explanation of how to generate and retrieve logging and diagnostics in Azure Spatial Anchors.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f4359db1deda2295a66bcb97cf374d0fe9bc3ef7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270123"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Registrazione e diagnostica in Azure Spatial AnchorsLogging and diagnostics in Azure Spatial Anchors

Azure Spatial Anchors offre un meccanismo di registrazione standard utile per lo sviluppo di app. La modalità di registrazione diagnostica di Gli ancoraggi spaziali è utile quando sono necessarie ulteriori informazioni per il debug. La registrazione diagnostica archivia le immagini dell'ambiente.

## <a name="standard-logging"></a>Registrazione standard
Nell'API Degli ancoraggi spaziali è possibile sottoscrivere il meccanismo di registrazione per ottenere log utili per lo sviluppo e il debug di applicazioni. Le API di registrazione standard non archiviano immagini dell'ambiente sul disco del dispositivo. L'SDK fornisce questi registri come callback di eventi. Spetta all'utente integrare questi log nel meccanismo di registrazione dell'applicazione.

### <a name="configuration-of-log-messages"></a>Configurazione dei messaggi di registro
Esistono due callback di interesse per l'utente. Nell'esempio seguente viene illustrato come configurare la sessione.

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

Questi callback di eventi vengono forniti per elaborare i registri e gli errori dalla sessione:These event callbacks are provided to process logs and errors from the session:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Specifica il livello di dettaglio per gli eventi da ricevere dal runtime.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Fornisce eventi di log di debug standard.
- [Errore:](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error)fornisce eventi di log che il runtime considera errori.

## <a name="diagnostics-logging"></a>Registrazione diagnostica

Oltre alla modalità di funzionamento standard per la registrazione, Gli ancoraggi spaziali hanno anche una modalità di diagnostica. La modalità di diagnostica acquisisce le immagini dell'ambiente e le registra sul disco. È possibile utilizzare questa modalità per eseguire il debug di determinati tipi di problemi, ad esempio l'impossibilità di individuare in modo prevedibile un ancoraggio. Abilitare la registrazione diagnostica solo per riprodurre un problema specifico. Quindi disattivarlo. Non abilitare la diagnostica quando si eseguono le app normalmente.

Durante un'interazione di supporto con Microsoft, un rappresentante Microsoft potrebbe chiedere se si è disposti a inviare un pacchetto di diagnostica per ulteriori indagini. In questo caso, è possibile decidere di abilitare la diagnostica e riprodurre il problema in modo da poter inviare il pacchetto di diagnostica.

Se si invia un log di diagnostica a Microsoft senza preavviso da parte di un rappresentante Microsoft, l'invio rimarrà senza risposta.

Nelle sezioni seguenti viene illustrato come abilitare la modalità di diagnostica e come inviare i log di diagnostica a Microsoft.The following sections show how to enable diagnostics mode and also how to submit diagnostics logs to Microsoft.

### <a name="enable-diagnostics-logging"></a>Abilitare la registrazione diagnostica

Quando si abilita una sessione per la registrazione diagnostica, tutte le operazioni nella sessione hanno la registrazione diagnostica corrispondente nel file system locale. Durante la registrazione, le immagini dell'ambiente vengono salvate sul disco.

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

### <a name="submit-the-diagnostics-bundle"></a>Inviare il pacchetto di diagnosticaSubmit the diagnostics bundle

Il frammento di codice seguente mostra come inviare un pacchetto di diagnostica a Microsoft.The following code snippet shows how to submit a diagnostics bundle to Microsoft. Questo pacchetto includerà le immagini dell'ambiente acquisite dalla sessione dopo l'abilitazione della diagnostica.

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

### <a name="parts-of-a-diagnostics-bundle"></a>Parti di un pacchetto di diagnostica
Il pacchetto di diagnostica può contenere le seguenti informazioni:

- **Immagini con fotogrammi chiave:** immagini dell'ambiente acquisite durante la sessione mentre la diagnostica è stata abilitata.
- **Log**: Registra gli eventi registrati dal runtime.
- **Metadati della sessione**: Metadati che identificano la sessione.
