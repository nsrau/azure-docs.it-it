---
title: Associazione grafica
description: Impostazione di associazioni grafiche e casi d'uso
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 8b5db0532f3dcc8b6dfb024238d0cacff2e6d2a1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681883"
---
# <a name="graphics-binding"></a>Associazione grafica

Per poter usare il rendering remoto di Azure in un'applicazione personalizzata, è necessario integrarlo nella pipeline di rendering dell'applicazione. Questa integrazione è responsabilità dell'associazione grafica.

Una volta impostata, l'associazione grafica consente di accedere a varie funzioni che influiscono sull'immagine sottoposta a rendering. Queste funzioni possono essere suddivise in due categorie: funzioni generali sempre disponibili `Microsoft.Azure.RemoteRendering.GraphicsApiType`e funzioni specifiche che sono rilevanti solo per l'oggetto selezionato.

## <a name="graphics-binding-in-unity"></a>Associazione grafica in Unity

In Unity, l'intera associazione `RemoteUnityClientInit` viene `RemoteManagerUnity.InitializeManager`gestita dallo struct passato in . Per impostare la `GraphicsApiType` modalità grafica, il campo deve essere impostato sull'associazione scelta. Il campo verrà compilato automaticamente a seconda che sia presente o meno un XRDevice. Il comportamento può essere sottoposto a override manuale con i seguenti comportamenti:

* **HoloLens 2**: viene sempre utilizzata l'associazione grafica [Windows Mixed Reality.](#windows-mixed-reality)
* **Flat UWP app desktop**: [La simulazione](#simulation) viene sempre utilizzata. Per utilizzare questa modalità, assicurarsi di seguire i passaggi descritti in [Esercitazione: Impostazione](../tutorials/unity/project-setup.md)di un progetto Unity da zero.
* **Editor unity**: [La simulazione](#simulation) viene sempre utilizzata a meno che non sia collegato un auricolare WMR VR, nel qual caso ARR verrà disabilitato per consentire il debug delle parti non correlate all'ARR dell'applicazione. Vedere anche [servizi remoti olografici](../how-tos/unity/holographic-remoting.md).

L'unica altra parte rilevante per Unity è l'accesso [all'associazione di base,](#access)tutte le altre sezioni seguenti possono essere ignorate.

## <a name="graphics-binding-setup-in-custom-applications"></a>Configurazione dell'associazione grafica nelle applicazioni personalizzate

Per selezionare un'associazione grafica, eseguire i due passaggi seguenti: In primo luogo, l'associazione grafica deve essere inizializzata in modo statico quando il programma viene inizializzato:

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

La chiamata precedente è necessaria per inizializzare il rendering remoto di Azure nelle API olografiche. Questa funzione deve essere chiamata prima di qualsiasi API olografica viene chiamata e prima di qualsiasi altra API di rendering remoto. Analogamente, la funzione `RemoteManagerStatic.ShutdownRemoteRendering();` de-init corrispondente deve essere chiamata dopo che non vengono più chiamate API olografiche.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Accesso all'associazione grafica

Una volta configurato un client, è possibile accedere `AzureSession.GraphicsBinding` all'associazione grafica di base con il getter. Ad esempio, le statistiche dell'ultimo fotogramma possono essere recuperate in questo modo:As an example, the last frame statistics can be retrieved like this:

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

Attualmente è possibile selezionare due API `WmrD3D11` grafiche e `SimD3D11`. Una terza `Headless` esiste ma non è ancora supportata sul lato client.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`è l'associazione predefinita da eseguire in HoloLens 2. Verrà creata `GraphicsBindingWmrD3d11` l'associazione. In questa modalità, il rendering remoto di Azure esegue l'hook direttamente nelle API olografiche.

Per accedere alle associazioni grafiche `GraphicsBinding` derivate, è necessario eseguire il cast della base.
Ci sono due cose che devono essere fatte per utilizzare l'associazione WMR:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Informare il rendering remoto del sistema di coordinate utilizzato

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

Dove quanto `ptr` sopra deve essere `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` un puntatore a un oggetto nativo che definisce il sistema di coordinate dello spazio globale in cui vengono espresse le coordinate nell'API.

#### <a name="render-remote-image"></a>Rendering di immagine remota

All'inizio di ogni fotogramma il frame remoto deve essere sottoposto a rendering nel buffer nascosto. Questa operazione viene `BlitRemoteFrame`eseguita chiamando , che riempirà le informazioni sul colore e sulla profondità nella destinazione di rendering attualmente associata. Pertanto è importante che questa operazione venga eseguita dopo l'associazione del buffer nascosto come destinazione di rendering.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>Simulazione

`GraphicsApiType.SimD3D11`è l'associazione di simulazione e, se selezionata, crea l'associazione `GraphicsBindingSimD3d11` grafica. Questa interfaccia viene utilizzata per simulare il movimento della testa, ad esempio in un'applicazione desktop ed esegue il rendering di un'immagine monoscopica.
L'installazione è un po 'più coinvolto e funziona come segue:

#### <a name="create-proxy-render-target"></a>Crea destinazione di rendering proxy

Il contenuto remoto e locale deve essere sottoposto a rendering su un destinazione di `GraphicsBindingSimD3d11.Update` rendering a colori/profondità fuori schermo chiamato 'proxy' utilizzando i dati della fotocamera proxy forniti dalla funzione. Il proxy deve corrispondere alla risoluzione del buffer nascosto. Una volta che `GraphicsBindingSimD3d11.InitSimulation` una sessione è pronta, deve essere chiamata prima di connettersi ad essa:

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

La funzione init deve essere fornita con puntatori al dispositivo d3d nativo, nonché alla trama di colore e profondità della destinazione di rendering proxy. Una volta `AzureSession.ConnectToRuntime` inizializzato e `DisconnectFromRuntime` può essere chiamato più volte, `GraphicsBindingSimD3d11.DeinitSimulation` ma quando si passa a `GraphicsBindingSimD3d11.InitSimulation` una sessione diversa, deve essere chiamato prima nella sessione precedente può essere chiamato su un'altra sessione.

#### <a name="render-loop-update"></a>Aggiornamento del ciclo di rendering

L'aggiornamento del ciclo di rendering è costituito da più passaggi:The render loop update consists of multiple steps:

1. Ogni fotogramma, prima di `GraphicsBindingSimD3d11.Update` qualsiasi rendering, viene chiamato con la trasformazione della fotocamera corrente che viene inviata al server per il rendering. Allo stesso tempo, la trasformazione proxy restituita deve essere applicata alla videocamera proxy per eseguire il rendering nella destinazione di rendering proxy.
Se l'aggiornamento `SimulationUpdate.frameId` proxy restituito è null, non sono ancora presenti dati remoti. In questo caso, invece di eseguire il rendering nella destinazione di rendering del proxy, qualsiasi contenuto locale deve essere sottoposto a rendering direttamente nel buffer nascosto utilizzando i dati della fotocamera correnti e i due passaggi successivi vengono ignorati.
1. L'applicazione deve ora associare `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`la destinazione di rendering proxy e chiamare . In questo modo le informazioni remote sul colore e sulla profondità verranno inserite nella destinazione di rendering del proxy. È ora possibile eseguire il rendering di qualsiasi contenuto locale sul proxy utilizzando la trasformazione della fotocamera proxy.
1. Successivamente, il buffer nascosto deve essere associato `GraphicsBindingSimD3d11.ReprojectProxy` come destinazione di rendering e chiamato a quel punto il buffer nascosto può essere presentato.

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

* [Esercitazione: Impostazione di un progetto Unity da zeroTutorial: Setting up a Unity project from scratch](../tutorials/unity/project-setup.md)
