---
title: Modelli di esempio
description: Elenca le origini per i modelli di esempio.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 0c28d36c7934fbbac0ddd11562c8dc237b0360ee
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678492"
---
# <a name="sample-models"></a>Modelli di esempio

Questo articolo elenca alcune risorse per i dati di esempio utilizzabili per testare il servizio Rendering remoto di Azure.

## <a name="built-in-sample-model"></a>Modello di esempio predefinito

È disponibile un modello di esempio predefinito che può essere sempre caricato usando l'URL **builtin://Engine**

![Modello di esempio](./media/sample-model.png "Modello di esempio")

Statistiche modello:

| Nome | valore |
|-----------|:-----------|
| [Dimensioni VM richieste](../how-tos/session-rest-api.md#create-a-session) | standard |
| Numero di triangoli | 18,7 milioni |
| Numero di parti mobili | 2073 |
| Numero di materiali | 94 |

## <a name="third-party-data"></a>Dati di terze parti

Il gruppo Khronos gestisce un set di modelli di esempio glTF per i test. Rendering remoto di Azure supporta il formato glTF in formato testo (*con estensione glTF*) e in formato binario (*con estensione glb*). È consigliabile usare i modelli PBR per ottenere risultati visivi ottimali:

* [Modelli di esempio glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Eseguire il rendering di un modello con Unity](../quickstarts/render-model.md)
* [Avvio rapido: Convertire un modello per il rendering](../quickstarts/convert-model.md)
