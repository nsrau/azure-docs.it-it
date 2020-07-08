---
title: Durata di oggetti e risorse
description: Illustra la gestione della durata per diversi tipi
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: d031ff4a6ee86da2843f0f18ac428c50f7cfc121
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681870"
---
# <a name="object-and-resource-lifetime"></a>Durata di oggetti e risorse

Il rendering remoto di Azure distingue due tipi: **oggetti** e **risorse**.

## <a name="object-lifetime"></a>Durata degli oggetti

*Gli oggetti* sono considerati elementi che l'utente può creare, modificare ed eliminare a propria discrezione. Gli oggetti possono essere duplicati liberamente e ogni istanza può essere mutata nel tempo. Di conseguenza, le [entità](entities.md) e i [componenti](components.md) sono oggetti.

La durata degli oggetti è completamente sotto il controllo utente. Tuttavia, non è correlato alla durata della rappresentazione sul lato client. Classi come `Entity` e `Component` hanno una `Destroy` funzione che deve essere chiamata per deallocare l'oggetto nell'host di rendering remoto. Inoltre, `Entity.Destroy()` eliminerà l'entità, i relativi elementi figlio e tutti i componenti di tale gerarchia.

## <a name="resource-lifetime"></a>Durata delle risorse

Le *risorse* sono elementi la cui durata è interamente gestita dall'host di rendering remoto. Le risorse sono conteggiate a livello interno. Vengono deallocati quando nessuno vi fa più riferimento.

La maggior parte delle risorse può essere creata indirettamente, in genere caricando tali risorse da un file. Quando lo stesso file viene caricato più volte, il rendering remoto di Azure restituirà lo stesso riferimento e non caricherà nuovamente i dati.

Molte risorse non sono modificabili, ad esempio [mesh](meshes.md) e [trame](textures.md). Alcune risorse sono tuttavia modificabili, ad esempio [materiali](materials.md). Poiché le risorse vengono spesso condivise, la modifica di una risorsa può influire su più oggetti. Se ad esempio si modifica il colore di un materiale, viene modificato il colore di tutti gli oggetti che utilizzano mesh, che a loro volta fanno riferimento a tale materiale.

### <a name="built-in-resources"></a>Risorse predefinite

Il rendering remoto di Azure contiene alcune risorse predefinite, che possono essere caricate anteponendo il rispettivo identificatore con `builtin://` durante la chiamata a `AzureSession.Actions.LoadXYZAsync()` . Le risorse predefinite disponibili sono elencate nella documentazione per ogni rispettiva funzionalità. Il [capitolo Sky](../overview/features/sky.md) elenca ad esempio le trame Sky predefinite.

## <a name="general-lifetime"></a>Durata generale

Il ciclo di vita di tutti gli oggetti e le risorse è associato alla connessione. In disconnessione tutti gli elementi vengono eliminati. Quando si riconnette alla stessa sessione, il grafico della scena sarà vuoto e tutte le risorse verranno eliminate.

In pratica, il caricamento della stessa risorsa in una sessione, dopo una disconnessione, è in genere più veloce della prima volta. Questo è il caso, perché la maggior parte delle risorse deve essere scaricata da archiviazione di Azure la prima volta, che non è necessaria la seconda volta, risparmiando una notevole quantità di tempo.

## <a name="next-steps"></a>Passaggi successivi

* [Entità](entities.md)
* [Componenti](components.md)
* [Modelli](models.md)
