---
title: Configurare Rendering remoto per Unity
description: Come inizializzare il rendering remoto di Azure in un progetto Unity
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a0be44d8709726e159e17e703566c6c576bc18f
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018978"
---
# <a name="set-up-remote-rendering-for-unity"></a>Configurare Rendering remoto per Unity

Per abilitare il rendering remoto di Azure (ARR) in Unity, vengono forniti metodi dedicati che interessano alcuni aspetti specifici di Unity.

## <a name="startup-and-shutdown"></a>Avvio e arresto

Per inizializzare il rendering remoto, usare `RemoteManagerUnity` . Questa classe chiama l'oggetto generico, `RemoteManager` ma implementa già i dettagli specifici di Unity. Unity USA ad esempio un sistema di coordinate specifico. Quando `RemoteManagerUnity.Initialize` si chiama, viene configurata la convenzione corretta. La chiamata richiede anche di fornire la fotocamera Unity da usare per la visualizzazione del contenuto sottoposto a rendering remoto.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Per arrestare il rendering remoto, chiamare `RemoteManagerStatic.ShutdownRemoteRendering()` .

Dopo `AzureSession` la creazione e la scelta di una sessione di rendering primaria, è necessario registrarla con `RemoteManagerUnity` :

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Codice di esempio completo

Il codice seguente illustra tutti i passaggi necessari per inizializzare il rendering remoto di Azure in Unity:

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// ... fill out accountInfo ...
AzureFrontend frontend = new AzureFrontend(accountInfo);

// start a session
AzureSession session = await frontend.CreateNewRenderingSessionAsync(new RenderingSessionCreationParams(RenderingSessionVmSize.Standard, 0, 30)).AsTask();

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

session.ConnectToRuntime(new ConnectToRuntimeParams());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Funzioni di convenienza

### <a name="session-state-events"></a>Eventi dello stato della sessione

`RemoteManagerUnity.OnSessionUpdate` genera eventi per quando cambia lo stato della sessione. per informazioni dettagliate, vedere la documentazione del codice.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity` è un componente facoltativo per semplificare la configurazione e la gestione delle sessioni. Contiene opzioni per arrestare automaticamente la sessione quando l'applicazione è in uscita o la modalità di riproduzione viene chiusa nell'editor, oltre a rinnovare automaticamente il lease della sessione quando necessario. Memorizza nella cache i dati, ad esempio le proprietà della sessione (vedere la relativa `LastProperties` variabile) ed espone gli eventi per le modifiche dello stato della sessione e gli errori di sessione.

Non può essere presente più di un'istanza di `ARRServiceUnity` alla volta. È concepito per iniziare più rapidamente implementando alcune funzionalità comuni. Per un'applicazione di dimensioni maggiori, può essere preferibile eseguire tali operazioni, tuttavia.

Per un esempio di come configurare e usare, `ARRServiceUnity` vedere [esercitazione: visualizzazione di modelli con rendering remoto](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="next-steps"></a>Passaggi successivi

* [Installare il pacchetto Rendering remoto per Unity](install-remote-rendering-unity-package.md)
* [Esercitazione: Visualizzazione di un modello di cui è stato eseguito il rendering in remoto](../../tutorials/unity/view-remote-models/view-remote-models.md)
