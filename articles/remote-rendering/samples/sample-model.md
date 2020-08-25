---
title: Modelli di esempio
description: Elenca le origini per i modelli di esempio.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 8e5dcb6c9dfa08efc0889fcab779d6cb333d2330
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507536"
---
# <a name="sample-models"></a>Modelli di esempio

Questo articolo elenca alcune risorse per i dati di esempio utilizzabili per testare il servizio Rendering remoto di Azure.

## <a name="built-in-sample-model"></a>Modello di esempio predefinito

È disponibile un modello di esempio predefinito che può essere sempre caricato usando l'URL **builtin://Engine**

![Modello di esempio](./media/sample-model.png "Modello di esempio")

Statistiche modello:

| Nome | valore |
|-----------|:-----------|
| [Dimensioni server necessarie](../how-tos/session-rest-api.md#create-a-session) | standard |
| Numero di triangoli | 18,7 milioni |
| Numero di parti mobili | 2073 |
| Numero di materiali | 94 |

## <a name="third-party-data"></a>Dati di terze parti

Il gruppo Khronos gestisce un set di modelli di esempio glTF per i test. Rendering remoto di Azure supporta il formato glTF in formato testo (*con estensione glTF*) e in formato binario (*con estensione glb*). È consigliabile usare i modelli PBR per ottenere risultati visivi ottimali:

* [Modelli di esempio glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Eseguire il rendering di un modello con Unity](../quickstarts/render-model.md)
* [Avvio rapido: Convertire un modello per il rendering](../quickstarts/convert-model.md)
