---
title: Durata di oggetti e risorse
description: Illustra la gestione della durata per i diversi tipi
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: d031ff4a6ee86da2843f0f18ac428c50f7cfc121
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681870"
---
# <a name="object-and-resource-lifetime"></a>Durata di oggetti e risorse

Il rendering remoto di Azure distingue tra due tipi: **oggetti** e **risorse**.

## <a name="object-lifetime"></a>Durata dell'oggetto

*Gli oggetti* sono considerati elementi che l'utente può creare, modificare ed eliminare a propria discrezione. Gli oggetti possono essere duplicati liberamente e ogni istanza può mutare nel tempo. Di [conseguenza, entità](entities.md) e [componenti](components.md) sono oggetti.

La durata degli oggetti è completamente sotto il controllo dell'utente. Tuttavia, non è correlato alla durata della rappresentazione lato client. Le `Entity` classi `Component` piace `Destroy` e hanno una funzione che deve essere chiamata per deallocare l'oggetto nell'host di rendering remoto. Inoltre, `Entity.Destroy()` distruggerà l'entità, i relativi elementi figlio e tutti i componenti in tale gerarchia.

## <a name="resource-lifetime"></a>Durata delle risorse

*Le risorse* sono elementi la cui durata è interamente gestita dall'host di rendering remoto. Le risorse sono conteggiate internamente. Vengono deallocati quando nessuno li fa più riferimento.

La maggior parte delle risorse può essere creata solo indirettamente, in genere caricandole da un file. Quando lo stesso file viene caricato più volte, il rendering remoto di Azure restituirà lo stesso riferimento e non caricherà nuovamente i dati.

Molte risorse non sono [modificabili,](textures.md)ad esempio [le maglie](meshes.md) e le trame . Alcune risorse sono modificabili, tuttavia, ad esempio [materiali](materials.md). Poiché le risorse sono spesso condivise, la modifica di una risorsa può influire su più oggetti. Ad esempio, la modifica del colore di un materiale cambierà il colore di tutti gli oggetti che utilizzano le maglie, che a loro volta fanno riferimento a tale materiale.

### <a name="built-in-resources"></a>Risorse predefinite

Il rendering remoto di Azure contiene alcune risorse predefinite, che `builtin://` possono essere `AzureSession.Actions.LoadXYZAsync()`caricate anteponendo il rispettivo identificatore durante la chiamata a . Le risorse predefinite disponibili sono elencate nella documentazione per ogni funzionalità. Ad esempio, il [capitolo cielo](../overview/features/sky.md) elenca le texture del cielo incorporate.

## <a name="general-lifetime"></a>Durata generale

La durata di tutti gli oggetti e le risorse è associata alla connessione. Alla disconnessione tutto viene scartato. Quando ci si riconnette alla stessa sessione, il grafico della scena sarà vuoto e tutte le risorse vengono eliminate.

In pratica, il caricamento della stessa risorsa in una sessione, dopo una disconnessione, è in genere più veloce rispetto alla prima volta. Questo è il caso perché la maggior parte delle risorse deve essere scaricata da Archiviazione di Azure la prima volta, che non è necessaria la seconda volta, risparmiando una notevole quantità di tempo.

## <a name="next-steps"></a>Passaggi successivi

* [Entities](entities.md)
* [Componenti](components.md)
* [Modelli](models.md)
