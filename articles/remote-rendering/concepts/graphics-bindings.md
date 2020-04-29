---
title: Associazione grafica
description: Configurazione di associazioni grafiche e casi di utilizzo
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 8b5db0532f3dcc8b6dfb024238d0cacff2e6d2a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681883"
---
# <a name="graphics-binding"></a>Associazione grafica

Per poter usare il rendering remoto di Azure in un'applicazione personalizzata, è necessario integrarlo nella pipeline di rendering dell'applicazione. Questa integrazione è responsabilità dell'associazione grafica.

Una volta configurato, l'associazione grafica fornisce l'accesso a varie funzioni che interessano l'immagine di cui è stato eseguito il rendering. Queste funzioni possono essere separate in due categorie, ovvero funzioni generali sempre disponibili e funzioni specifiche rilevanti solo per l'oggetto selezionato `Microsoft.Azure.RemoteRendering.GraphicsApiType`.

## <a name="graphics-binding-in-unity"></a>Associazione grafica in Unity

In Unity, l'intera associazione viene gestita dallo `RemoteUnityClientInit` struct passato a `RemoteManagerUnity.InitializeManager`. Per impostare la modalità grafica, il `GraphicsApiType` campo deve essere impostato sull'associazione scelta. Il campo verrà popolato automaticamente a seconda che sia presente un XRDevice. Il comportamento può essere sostituito manualmente con i comportamenti seguenti:

* **HoloLens 2**: l'associazione grafica della [realtà mista di Windows](#windows-mixed-reality) viene sempre usata.
* **App desktop UWP Flat**: la [simulazione](#simulation) viene sempre usata. Per usare questa modalità, assicurarsi di seguire la procedura descritta in [esercitazione: configurazione di un progetto Unity da zero](../tutorials/unity/project-setup.md).
* **Editor di Unity**: la [simulazione](#simulation) viene sempre usata, a meno che non sia connessa una cuffia WMR VR. in questo caso, arr verrà disabilitato per consentire il debug delle parti correlate non ARR dell'applicazione. Vedere anche [comunicazione remota olografica](../how-tos/unity/holographic-remoting.md).

L'unica altra parte pertinente per Unity è l'accesso al [binding di base](#access). tutte le altre sezioni seguenti possono essere ignorate.

## <a name="graphics-binding-setup-in-custom-applications"></a>Impostazione dell'associazione grafica nelle applicazioni personalizzate

Per selezionare un'associazione grafica, eseguire i due passaggi seguenti: prima, l'associazione grafica deve essere inizializzata in modo statico al momento dell'inizializzazione del programma:

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

La chiamata precedente è necessaria per inizializzare il rendering remoto di Azure nelle API olografiche. Questa funzione deve essere chiamata prima che venga chiamata un'API olografica e prima che venga eseguito l'accesso a qualsiasi altra API di rendering remoto. Analogamente, la funzione `RemoteManagerStatic.ShutdownRemoteRendering();` de-init corrispondente deve essere chiamata dopo che non sono più state chiamate API olografiche.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Accesso al binding grafico

Una volta configurato un client, è possibile accedere al binding grafico di base con il `AzureSession.GraphicsBinding` Getter. Ad esempio, è possibile recuperare le statistiche dell'ultimo frame come segue:

``` cs
AzureSession currentSesson = ...;
if (currentSesson.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>API grafiche

Attualmente sono disponibili due API grafiche che è possibile selezionare, `WmrD3D11` e `SimD3D11`. Ne `Headless` esiste una terza, ma non è ancora supportata sul lato client.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`è l'associazione predefinita da eseguire su HoloLens 2. Verrà creata l' `GraphicsBindingWmrD3d11` associazione. In questa modalità, il rendering remoto di Azure viene associato direttamente alle API olografiche.

Per accedere ai binding grafici derivati, è `GraphicsBinding` necessario eseguire il cast della base.
Per usare l'associazione WMR, è necessario eseguire due operazioni:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Informare il rendering remoto del sistema di coordinate usato

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

Dove il valore `ptr` precedente deve essere un puntatore a un `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` oggetto nativo che definisce il sistema di coordinate dello spazio globale in cui vengono espresse le coordinate nell'API.

#### <a name="render-remote-image"></a>Rendering immagine remota

All'inizio di ogni frame è necessario eseguire il rendering del frame remoto nel buffer nascosto. Questa operazione viene eseguita chiamando `BlitRemoteFrame`, che riempie le informazioni relative a colore e profondità nella destinazione di rendering attualmente associata. È quindi importante che questa operazione venga eseguita dopo avere associato il buffer nascosto come destinazione di rendering.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>Simulazione

`GraphicsApiType.SimD3D11`è l'associazione della simulazione e, se selezionata, `GraphicsBindingSimD3d11` crea l'associazione grafica. Questa interfaccia viene usata per simulare lo spostamento Head, ad esempio in un'applicazione desktop, ed esegue il rendering di un'immagine monoscopiche.
La configurazione è un po' più complessa e funziona nel modo seguente:

#### <a name="create-proxy-render-target"></a>Crea destinazione di rendering proxy

È necessario eseguire il rendering del contenuto remoto e locale in una destinazione di rendering di colore/profondità Offscreen denominata ' proxy ' usando i dati della `GraphicsBindingSimD3d11.Update` fotocamera proxy forniti dalla funzione. Il proxy deve corrispondere alla risoluzione del buffer nascosto. Una volta pronta, `GraphicsBindingSimD3d11.InitSimulation` una sessione deve essere chiamata prima della connessione:

``` cs
AzureSession currentSesson = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

La funzione init deve essere fornita con puntatori al dispositivo D3D nativo, nonché alla trama di colore e profondità della destinazione di rendering del proxy. Una volta `AzureSession.ConnectToRuntime` inizializzate `DisconnectFromRuntime` e possono essere chiamate più volte, ma quando si passa a una `GraphicsBindingSimD3d11.DeinitSimulation` sessione diversa, è necessario chiamare prima la sessione precedente `GraphicsBindingSimD3d11.InitSimulation` per poter essere chiamata in un'altra sessione.

#### <a name="render-loop-update"></a>Aggiornamento del ciclo di rendering

L'aggiornamento del ciclo di rendering è costituito da più passaggi:

1. Ogni frame, prima che venga eseguito il rendering `GraphicsBindingSimD3d11.Update` , viene chiamato con la trasformazione della fotocamera corrente inviata al server di cui eseguire il rendering. Allo stesso tempo, la trasformazione del proxy restituita deve essere applicata alla fotocamera del proxy per eseguire il rendering nella destinazione di rendering del proxy.
Se l'aggiornamento `SimulationUpdate.frameId` del proxy restituito è null, non sono ancora presenti dati remoti. In questo caso, anziché eseguire il rendering nella destinazione di rendering del proxy, è necessario eseguire il rendering del contenuto locale nel buffer nascosto usando direttamente i dati della fotocamera corrente e i due passaggi successivi verranno ignorati.
1. L'applicazione deve ora associare la destinazione di rendering del proxy `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`e chiamare. In questo modo, le informazioni sul colore remoto e sulla profondità vengono inserite nella destinazione di rendering del proxy. È ora possibile eseguire il rendering di qualsiasi contenuto locale nel proxy usando la trasformazione della fotocamera del proxy.
1. Successivamente, è necessario associare il buffer nascosto come destinazione di rendering e `GraphicsBindingSimD3d11.ReprojectProxy` chiamare il buffer nascosto.

``` cs
AzureSession currentSesson = ...;
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

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Configurazione di un progetto Unity da zero](../tutorials/unity/project-setup.md)
