---
title: Binding di grafica
description: Configurazione di binding di grafica e casi d'uso
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 4854d5ff9d697a2bf082a788c0e761a2152b0294
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758708"
---
# <a name="graphics-binding"></a>Binding di grafica

Per poter usare Rendering remoto di Azure in un'applicazione personalizzata, è necessario integrare questo servizio nella pipeline di rendering dell'applicazione. Questa integrazione è compito del binding di grafica.

Dopo che è stato configurato, il binding di grafica consente l'accesso a varie funzioni che interessano l'immagine di cui è stato eseguito il rendering. È possibile dividere queste funzioni in due categorie: funzioni generali sempre disponibili e funzioni specifiche pertinenti solo per l'API `Microsoft.Azure.RemoteRendering.GraphicsApiType` selezionata.

## <a name="graphics-binding-in-unity"></a>Binding di grafica in Unity

In Unity il binding viene interamente gestito dallo struct `RemoteUnityClientInit` passato in `RemoteManagerUnity.InitializeManager`. Per impostare la modalità di grafica, è necessario impostare il campo `GraphicsApiType` sul binding scelto. Il campo verrà popolato automaticamente a seconda della presenza di un XRDevice. È possibile eseguire l'override del comportamento manualmente con i comportamenti seguenti:

* **HoloLens 2**: viene sempre usato il binding di grafica [Windows Mixed Reality](#windows-mixed-reality).
* **App desktop per la piattaforma UWP flat**: [viene sempre usata la simulazione](#simulation). Per usare questa modalità, assicurarsi di seguire la procedura descritta in [Esercitazione: Configurazione di un progetto Unity da zero](../tutorials/unity/project-setup.md).
* **Editor di Unity**: viene sempre usata la[simulazione](#simulation), a meno che non sia connessa una cuffia per la realtà virtuale Windows Mixed Reality, nel qual caso ARR (Application Request Routing) viene disabilitato per consentire il debug delle parti correlate non ARR dell'applicazione. Vedere anche la [comunicazione remota olografica](../how-tos/unity/holographic-remoting.md).

L'unica altra parte pertinente per Unity è l'accesso al [binding di base](#access). Tutte le altre sezioni riportate di seguito possono essere ignorate.

## <a name="graphics-binding-setup-in-custom-applications"></a>Configurazione del binding di grafica nelle applicazioni personalizzate

Per selezionare un binding di grafica, eseguire i due passaggi seguenti: Prima di tutto è necessario che il binding di grafica venga inizializzato in modo statico quando viene inizializzato il programma:

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.graphicsApi = GraphicsApiType::WmrD3D11;
managerInit.connectionType = ConnectionType::General;
managerInit.right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering
```

La chiamata precedente è necessaria per inizializzare Rendering remoto di Azure nelle API olografiche. Questa funzione deve essere chiamata prima di una qualsiasi API olografica e prima dell'accesso a qualsiasi altra API di Rendering remoto. Analogamente, la funzione di deinizializzazione corrispondente `RemoteManagerStatic.ShutdownRemoteRendering();` deve essere chiamata quando non vengono più eseguite chiamate alle API olografiche.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Accesso al binding di grafica

Dopo la configurazione di un client, è possibile accedere al binding di grafica di base con il getter `AzureSession.GraphicsBinding`. È ad esempio possibile recuperare le statistiche dell'ultimo fotogramma come segue:

```cs
AzureSession currentSession = ...;
if (currentSession.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (*binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>API di grafica

Sono attualmente disponibili per la selezione due API di grafica, `WmrD3D11` e `SimD3D11`. Ne esiste una terza `Headless`, che tuttavia non è ancora supportata sul lato client.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11` è il binding predefinito da eseguire in HoloLens 2 e crea il binding `GraphicsBindingWmrD3d11`. In questa modalità, Rendering remoto di Azure si collega direttamente alle API olografiche.

Per accedere ai binding di grafica derivati, è necessario eseguire il cast di `GraphicsBinding` di base.
Per usare il binding Windows Mixed Reality è necessario eseguire due operazioni:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Informare Rendering remoto del sistema di coordinate usato

```cs
AzureSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (*wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```


Dove `ptr` qui sopra deve essere un puntatore a un oggetto `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` nativo che definisce il sistema di coordinate dello spazio globale in cui sono espresse le coordinate nell'API.

#### <a name="render-remote-image"></a>Eseguire il rendering dell'immagine remota

All'inizio di ogni fotogramma è necessario eseguire il rendering del fotogramma remoto nel buffer nascosto. Questa operazione viene eseguita chiamando `BlitRemoteFrame`, che inserisce le informazioni relative a colore e profondità nella destinazione di rendering del binding attuale. È quindi importante che questa operazione venga eseguita dopo il binding del buffer nascosto come destinazione di rendering.

```cs
AzureSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>Simulazione

`GraphicsApiType.SimD3D11` è il binding della simulazione e se selezionato crea il binding di grafica `GraphicsBindingSimD3d11`. Questa interfaccia viene usata per simulare il movimento della testa, ad esempio in un'applicazione desktop, ed esegue il rendering di un'immagine monoscopica.
La configurazione è un po' più complessa e funziona nel modo seguente:

#### <a name="create-proxy-render-target"></a>Creare la destinazione di rendering proxy

È necessario eseguire il rendering del contenuto remoto e locale in una destinazione di rendering di colore/profondità fuori schermo denominata 'proxy' usando i dati della camera proxy forniti dalla funzione `GraphicsBindingSimD3d11.Update`. Il proxy deve avere la stessa risoluzione del buffer nascosto. Quando la sessione è pronta, è necessario chiamare `GraphicsBindingSimD3d11.InitSimulation` prima della connessione:

```cs
AzureSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

La funzione init deve essere dotata di puntatori al dispositivo D3D nativo, nonché alla texture di colore e profondità della destinazione di rendering proxy. Dopo l'inizializzazione, `AzureSession.ConnectToRuntime` e `DisconnectFromRuntime` possono essere chiamate più volte, ma quando si passa a una sessione diversa è necessario chiamare prima `GraphicsBindingSimD3d11.DeinitSimulation` nella sessione precedente per poter chiamare `GraphicsBindingSimD3d11.InitSimulation` in un'altra sessione.

#### <a name="render-loop-update"></a>Aggiornamento del ciclo di rendering

L'aggiornamento del ciclo di rendering è costituito da più passaggi:

1. Per ogni fotogramma, prima che venga eseguito il rendering, viene chiamata la funzione `GraphicsBindingSimD3d11.Update` con la trasformazione della camera corrente inviata al server di cui eseguire il rendering. Allo stesso tempo, la trasformazione proxy restituita deve essere applicata alla camera proxy per eseguire il rendering nella destinazione di rendering proxy.
Se `SimulationUpdate.frameId` dell'aggiornamento proxy restituito è Null, non sono ancora presenti dati remoti. In questo caso, anziché eseguire il rendering nella destinazione di rendering proxy, è necessario eseguire il rendering del contenuto locale nel buffer nascosto usando direttamente i dati della camera correnti. I due passaggi successivi verranno ignorati.
1. L'applicazione dovrebbe ora eseguire il binding della destinazione di rendering proxy e chiamare `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`. In questo modo le informazioni remote relative a colore e profondità verranno inserite nella destinazione di rendering proxy. È ora possibile eseguire il rendering di qualsiasi contenuto locale nel proxy usando la trasformazione della camera proxy.
1. È quindi necessario eseguire il binding del buffer nascosto come destinazione di rendering e chiamare `GraphicsBindingSimD3d11.ReprojectProxy`. A questo punto è possibile presentare il buffer nascosto.

```cs
AzureSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdate update;
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate;
simBinding->Update(update, &proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Configurazione di un progetto Unity da zero](../tutorials/unity/project-setup.md)
