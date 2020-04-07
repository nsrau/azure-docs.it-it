---
title: Lights
description: Descrizione e proprietà della sorgente luminosa
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 0a4a226af1347b5302b0c3964889fc072f89e7f8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680947"
---
# <a name="lights"></a>Lights

Per impostazione predefinita, gli oggetti sottoposti a rendering remoto vengono illuminati utilizzando una [luce del cielo.](sky.md) Per la maggior parte delle applicazioni questo è già sufficiente, ma è possibile aggiungere ulteriori sorgenti di luce alla scena.

> [!IMPORTANT]
> Solo i [materiali PBR](pbr-materials.md) sono interessati da sorgenti luminose. [I materiali a](color-materials.md) colori appaiono sempre completamente luminosi.

> [!NOTE]
> La fusione di ombre non è attualmente supportata. Il rendering remoto di Azure è ottimizzato per eseguire il rendering di enormi quantità di geometria, utilizzando più GPU, se necessario. Gli approcci tradizionali per la fusione delle ombre non funzionano bene in tali scenari.

## <a name="common-light-component-properties"></a>Proprietà comuni dei componenti di luce

Tutti i tipi di luce `LightComponent` derivano dalla classe base astratta e condividono queste proprietà:All light types derive from the abstract base class and share these properties:

* **Colore:** Il colore della luce nello [spazio Gamma](https://en.wikipedia.org/wiki/SRGB). Alpha viene ignorato.

* **Intensità:** La luminosità della luce. Per le luci puntie e spot, l'intensità definisce anche la distanza di luce.

## <a name="point-light"></a>Luce punto

Nel rendering remoto `PointLightComponent` di Azure il può non solo emettere luce da un singolo punto, ma anche da una piccola sfera o un piccolo tubo, per simulare sorgenti di luce più morbide.

### <a name="pointlightcomponent-properties"></a>PointLightComponent proprietà

* **Raggio:** Il raggio di default è zero, nel qual caso la luce agisce come una luce punto. Se il raggio è maggiore di zero, agisce come sorgente di luce sferica, che cambia l'aspetto delle luci speculari.

* **Lunghezza:** Se `Length` entrambi `Radius` e sono diversi da zero, la luce agisce come una luce tubo. Questo può essere utilizzato per simulare tubi al neon.

* **AttenuazioneCutoff:** Se lasciato a (0,0) l'attenuazione della `Intensity`luce dipende solo dalla sua . Tuttavia, è possibile fornire distanze min/max personalizzate sulle quali l'intensità della luce viene scalata linearmente fino a 0. Questa funzione può essere utilizzata per applicare una gamma più piccola di influenza di una luce specifica.

* **Mappa cubo proiettata:** Se impostata su una [mappa cubo](../../concepts/textures.md)valida, la trama viene proiettata sulla geometria circostante della luce. Il colore della mappa cubo è modulato con il colore della luce.

## <a name="spot-light"></a>Luce spot

Il `SpotLightComponent` è simile `PointLightComponent` al ma la luce è vincolata alla forma di un cono. L'orientamento del cono è definito *dall'asse z negativo dell'entità proprietario.*

### <a name="spotlightcomponent-properties"></a>SpotLightComponent proprietà

* **Raggio:** Come per `PointLightComponent`il file .

* **SpotAngleDeg:** Questo intervallo definisce l'angolo interno ed esterno del cono, misurato in gradi. Tutto all'interno dell'angolo interno è illuminato con piena luminosità. Una decadimento viene applicata all'angolo esterno che genera un effetto simile a una penombra.

* **FalloffExponent:** Definisce la nitidezza delle transizioni di decadimento tra l'angolo del cono interno ed esterno. Un valore più alto determina una transizione più nitida. Il valore predefinito 1.0 determina una transizione lineare.

* **AttenuazioneCutoff:** Come per `PointLightComponent`il file .

* **Proiettato2dTexture:** Se impostata su una [trama 2D](../../concepts/textures.md)valida, l'immagine viene proiettata sulla geometria a cui la luce risplende. Il colore della texture è modulato con il colore della luce.

## <a name="directional-light"></a>Luce direzionale

Il `DirectionalLightComponent` simula una fonte di luce che è infinitamente lontano. La luce brilla nella direzione *dell'asse z negativo dell'entità proprietaria.* La posizione dell'entità viene ignorata.

Non ci sono proprietà aggiuntive.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Le sorgenti luminose hanno un impatto significativo sulle prestazioni di rendering. Utilizzarli con attenzione e solo se richiesto dall'applicazione. Qualsiasi condizione di illuminazione globale statica, incluso un componente direzionale statico, può essere ottenuta con una [trama del cielo personalizzata,](sky.md)senza costi di rendering aggiuntivi.

## <a name="next-steps"></a>Passaggi successivi

* [Materiali](../../concepts/materials.md)
* [Cielo](sky.md)
