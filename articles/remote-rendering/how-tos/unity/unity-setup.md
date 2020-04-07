---
title: Configurare il rendering remoto per Unity
description: Come inizializzare il rendering remoto di Azure in un progetto UnityHow to initialize Azure Remote Rendering in a Unity project
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: 0415c0e7ee1432521c3cc2026feff5fc2a41d77e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681142"
---
# <a name="set-up-remote-rendering-for-unity"></a>Configurare il rendering remoto per Unity

Per abilitare il rendering remoto di Azure (ARR) in Unity, vengono forniti metodi dedicati che si occupano di alcuni aspetti specifici di Unity.To enable Azure Remote Rendering (ARR) in Unity, we provide dedicated methods that take care of some Unity-specific aspects.

## <a name="startup-and-shutdown"></a>Avvio e arresto

Per inizializzare il `RemoteManagerUnity`rendering remoto, utilizzare . Questa classe chiama `RemoteManager` il generico, ma implementa già i dettagli specifici di Unity per l'utente. Ad esempio, Unity utilizza un sistema di coordinate specifico. Quando `RemoteManagerUnity.Initialize`si chiama , verrà impostata la convenzione corretta. La chiamata richiede anche di fornire la fotocamera Unity che deve essere utilizzata per la visualizzazione del contenuto sottoposto a rendering remoto.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Per arrestare il rendering `RemoteManagerStatic.ShutdownRemoteRendering()`remoto, chiamare .

Dopo `AzureSession` che un oggetto è stato creato e scelto `RemoteManagerUnity`come sessione di rendering principale, deve essere registrato con :

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Codice di esempio completo

The code below demonstrates all the steps needed to initialize Azure Remote Rendering in Unity:

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

### <a name="session-state-events"></a>Eventi dello stato sessione

`RemoteManagerUnity.OnSessionUpdate`genera eventi per quando lo stato della sessione cambia, vedere la documentazione del codice per i dettagli.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity`è un componente opzionale per semplificare l'installazione e la gestione delle sessioni. Contiene opzioni per interrompere automaticamente la sessione quando l'applicazione è in uscita o la modalità di riproduzione viene chiusa nell'editor, nonché rinnovare automaticamente il lease di sessione quando necessario. Memorizza nella cache dati quali le `LastProperties` proprietà della sessione (vedere la variabile) ed espone eventi per le modifiche dello stato della sessione e gli errori di sessione.

Non può essere presente più `ARRServiceUnity` di un'istanza alla volta. Ha lo scopo di iniziare più rapidamente implementando alcune funzionalità comuni. Per un'applicazione più grande può essere preferibile fare queste cose da soli, però.

Per un esempio di come `ARRServiceUnity` impostare e utilizzare, vedere [Esercitazione: Impostazione](../../tutorials/unity/project-setup.md)di un progetto Unity da zero.

## <a name="next-steps"></a>Passaggi successivi

* [Installare il pacchetto di rendering remoto per UnityInstall the Remote Rendering package for Unity](install-remote-rendering-unity-package.md)
* [Esercitazione: Impostazione di un progetto Unity da zeroTutorial: Setting up a Unity project from scratch](../../tutorials/unity/project-setup.md)
