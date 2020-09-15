---
title: Fotocamera
description: Descrive le impostazioni della fotocamera e i casi d'uso
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: 5ad39ad9f5e585029cff5d573a026702f259607e
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564849"
---
# <a name="camera"></a>Fotocamera

Per assicurarsi che il contenuto sottoposto a rendering in locale e in remoto possa essere composto senza interruzioni, diverse proprietà della fotocamera devono rimanere sincronizzate tra server e client. In particolare la trasformazione e la proiezione della fotocamera. Ad esempio, il contenuto sottoposto a rendering in locale deve usare la stessa trasformazione della fotocamera e la stessa proiezione con cui è stato eseguito il rendering dell'ultimo frame remoto.

![Fotocamera locale e remota](./media/camera.png)

Nell'immagine precedente la fotocamera locale è stata spostata rispetto al frame remoto inviato dal server. Di conseguenza, è necessario eseguire il rendering del contenuto locale dalla stessa prospettiva del frame remoto e infine l'immagine risultante viene riproiettata nello spazio locale della fotocamera, descritta in dettaglio nella [riproiezione della fase finale](late-stage-reprojection.md).

Il ritardo tra il rendering remoto e quello locale significa che qualsiasi modifica di queste impostazioni viene applicata con un ritardo. Pertanto, qualsiasi nuovo valore non può essere utilizzato immediatamente nello stesso frame.

Nelle impostazioni della fotocamera è possibile impostare le distanze del piano vicino e lontano. In HoloLens 2, i dati di trasformazione e proiezione rimanenti vengono definiti dall'hardware. Quando si usa la [simulazione desktop](../../concepts/graphics-bindings.md), questi vengono impostati tramite l'input della relativa `Update` funzione.

I valori modificati possono essere usati localmente non appena viene eseguito il primo frame remoto di cui è stato eseguito il rendering. In HoloLens 2 i dati da usare per il rendering vengono forniti da *HolographicFrame* proprio come in qualsiasi altra applicazione olografica. Nella [simulazione del desktop](../../concepts/graphics-bindings.md)vengono recuperate tramite l'output della `Update` funzione.

## <a name="camera-settings"></a>Impostazioni della fotocamera

Le proprietà seguenti possono essere modificate nelle impostazioni della fotocamera:

**Piano vicino e lontano:**

Per assicurarsi che non sia possibile impostare intervalli non validi, le proprietà **NearPlane** e **FarPlane** sono di sola lettura e per modificare l'intervallo esiste una funzione separata **SetNearAndFarPlane** . Questi dati verranno inviati al server al termine del frame.

> [!IMPORTANT]
> In Unity, questo viene gestito automaticamente quando si modifica la videocamera principale vicino a un piano e un altro.

**EnableDepth**:

A volte è utile disabilitare la scrittura del buffer di profondità dell'immagine remota a scopo di debug. La disabilitazione della profondità impedirà inoltre al server di inviare dati di profondità, riducendo così la larghezza di banda.

> [!TIP]
> In Unity è disponibile un componente di debug denominato **EnableDepthComponent** che può essere usato per abilitare o disabilitare questa funzionalità nell'interfaccia utente dell'editor.

La modifica delle impostazioni della fotocamera può essere eseguita come indicato di seguito:

```cs
void ChangeCameraSetting(AzureSession session)
{
    CameraSettings settings = session.Actions.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<CameraSettings> settings = session->Actions()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
}
```

## <a name="api-documentation"></a>Documentazione dell'API

* [CameraSettings C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [CameraSettings C++](https://docs.microsoft.com/cpp/api/remote-rendering/camerasettings)
* [Funzione C# GraphicsBindingSimD3d11. Update](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [Funzione C++ GraphicsBindingSimD3d11:: Update](https://docs.microsoft.com/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Passaggi successivi

* [Binding di grafica](../../concepts/graphics-bindings.md)
* [Riproiezione con ritardo della fase](late-stage-reprojection.md)
