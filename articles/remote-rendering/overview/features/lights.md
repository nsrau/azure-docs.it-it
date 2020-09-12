---
title: Illuminazione della scena
description: Descrizione e proprietà della sorgente di luce
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 2ddce7e2f073914e9393eabf12e8b0f4ef347828
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613645"
---
# <a name="scene-lighting"></a>Illuminazione della scena

Per impostazione predefinita, gli oggetti di cui è stato eseguito il rendering in remoto vengono illuminati con una [luce celeste](sky.md) Per la maggior parte delle applicazioni questo è già sufficiente, ma è possibile aggiungere altre fonti di luce alla scena.

> [!IMPORTANT]
> Solo i [materiali PBR](pbr-materials.md) sono interessati dalle fonti di luce. I [materiali colori](color-materials.md) sono sempre completamente luminosi.

> [!NOTE]
> Il cast delle ombreggiature non è al momento supportato. Il rendering remoto di Azure è ottimizzato per eseguire il rendering di grandi quantità di geometria, usando più GPU, se necessario. Gli approcci tradizionali per il cast shadow non funzionano bene in questi scenari.

## <a name="common-light-component-properties"></a>Proprietà comuni del componente chiaro

Tutti i tipi leggeri derivano dalla classe di base astratta `LightComponent` e condividono queste proprietà:

* **Colore:** Colore della luce nello [spazio gamma](https://en.wikipedia.org/wiki/SRGB). Alfa viene ignorato.

* **Intensità:** Luminosità della luce. Per le luci punto e spot, l'intensità definisce anche la distanza di luminosità.

## <a name="point-light"></a>Luce puntiforme

Nel rendering remoto di Azure `PointLightComponent` non solo può emettere luce da un singolo punto, ma anche da una piccola sfera o da un piccolo tubicino, per simulare sorgenti di luce più flessibili.

### <a name="pointlightcomponent-properties"></a>Proprietà di PointLightComponent

* **Raggio:** Il raggio predefinito è zero, nel qual caso la luce funge da luce puntiforme. Se il raggio è maggiore di zero, funge da sorgente di luce sferica, che modifica l'aspetto delle evidenziazioni speculari.

* **Lunghezza:** Se `Length` e `Radius` sono diversi da zero, la luce funge da luce di tubo. Questa operazione può essere usata per simulare i tubi neon.

* **AttenuationCutoff:** Se lasciato a (0,0), l'attenuazione della luce dipende solo dalla relativa `Intensity` . Tuttavia, è possibile specificare Distanze minime/massime personalizzate per le quali l'intensità della luce viene ridimensionata in modo lineare fino a 0. Questa funzionalità può essere usata per applicare una minore gamma di influenza di una specifica luce.

* **ProjectedCubemap:** Se impostato su un [mappa cubi](../../concepts/textures.md)valido, la trama viene proiettata sulla geometria circostante della luce. Il colore del mappa cubi viene modulato con il colore della luce.

## <a name="spot-light"></a>Luce spot

`SpotLightComponent`È simile a `PointLightComponent` , ma la luce è vincolata alla forma di un cono. L'orientamento del cono viene definito dall' *asse z negativo dell'entità proprietario*.

### <a name="spotlightcomponent-properties"></a>Proprietà di SpotLightComponent

* **Raggio:** Come per `PointLightComponent` .

* **SpotAngleDeg:** Questo intervallo definisce l'angolo interno ed esterno del cono, misurato in gradi. Tutto ciò che si trova all'interno dell'angolo interno è illuminato con luminosità completa. Viene applicata un'interruzione verso l'angolo esterno che genera un effetto di tipo penombra.

* **FalloffExponent:** Definisce il modo in cui il decadimento delle transizioni tra l'angolo interno e quello esterno. Un valore più elevato comporta una transizione più netta. Il valore predefinito di 1,0 comporta una transizione lineare.

* **AttenuationCutoff:** Come per `PointLightComponent` .

* **Projected2dTexture:** Se è impostato su una [trama 2D](../../concepts/textures.md)valida, l'immagine viene proiettata sulla geometria a cui brilla la luce. Il colore della trama viene modulato con il colore della luce.

## <a name="directional-light"></a>Luce direzionale

`DirectionalLightComponent`Simula una sorgente di luce infinitamente distante. La luce si illumina nella direzione dell' *asse z negativo dell'entità Owner*. La posizione dell'entità verrà ignorata.

Non sono presenti proprietà aggiuntive.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Le fonti di luce hanno un impatto significativo sulle prestazioni di rendering. Usarle attentamente e solo se richiesto dall'applicazione. Qualsiasi condizione di illuminazione globale statica, incluso un componente direzionale statico, può essere realizzata con una [trama Sky personalizzata](sky.md), senza costi di rendering aggiuntivi.

## <a name="api-documentation"></a>Documentazione dell'API

* [C# LightComponentBase (classe)](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.lightcomponentbase)
* [C# PointLightComponent (classe)](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.pointlightcomponent)
* [C# SpotLightComponent (classe)](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.spotlightcomponent)
* [C# DirectionalLightComponent (classe)](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.directionallightcomponent)
* [Classe C++ LightComponentBase](https://docs.microsoft.com/cpp/api/remote-rendering/lightcomponentbase)
* [Classe C++ PointLightComponent](https://docs.microsoft.com/cpp/api/remote-rendering/pointlightcomponent)
* [Classe C++ SpotLightComponent](https://docs.microsoft.com/cpp/api/remote-rendering/spotlightcomponent)
* [Classe C++ DirectionalLightComponent](https://docs.microsoft.com/cpp/api/remote-rendering/directionallightcomponent)

## <a name="next-steps"></a>Passaggi successivi

* [Materiali](../../concepts/materials.md)
* [Cielo](sky.md)
