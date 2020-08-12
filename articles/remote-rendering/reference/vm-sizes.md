---
title: Dimensioni delle macchine virtuali
description: Descrive le dimensioni di VM distinte che è possibile allocare
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.openlocfilehash: e8e439a055b71ed291573965c561ee31610e3ed4
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121613"
---
# <a name="vm-sizes"></a>Dimensioni delle macchine virtuali

Il servizio di rendering può funzionare su due diversi tipi di computer in Azure, denominati `Standard` e `Premium` .

## <a name="polygon-limits"></a>Limiti del poligono

È previsto un limite rigido di **20 milioni poligoni** per le `Standard` dimensioni della macchina virtuale. Non esiste alcuna limitazione di questo tipo per le `Premium` dimensioni.

Quando il renderer in una macchina virtuale standard raggiunge questa limitazione, passa il rendering a uno sfondo a scacchi:

![Scacchi](media/checkerboard.png)

## <a name="allocate-the-vm"></a>Allocare la macchina virtuale

Il tipo desiderato di macchina virtuale deve essere specificato al momento dell'inizializzazione della sessione di rendering. Non può essere modificato all'interno di una sessione in esecuzione. Gli esempi di codice seguenti illustrano la posizione in cui è necessario specificare le dimensioni della macchina virtuale:

```cs
async void CreateRenderingSession(AzureFrontend frontend)
{
    RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
    sessionCreationParams.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend)
{
    RenderingSessionCreationParams sessionCreationParams;
    sessionCreationParams.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    if (auto createSessionAsync = frontend->CreateNewRenderingSessionAsync(sessionCreationParams))
    {
        // ...
    }
}
```

Per gli [script di PowerShell di esempio](../samples/powershell-example-scripts.md), è necessario specificare le dimensioni della macchina virtuale all'interno del `arrconfig.json` file:

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>Come il renderer valuta il numero di poligoni

Il numero di poligoni considerati per il test di limitazione è il numero di poligoni effettivamente passati al renderer. Questa geometria è in genere la somma di tutti i modelli di cui è stata creata un'istanza, ma sono presenti anche eccezioni. La geometria seguente **non è inclusa**:
* Istanze del modello caricate che sono completamente esterne alla visualizzazione tronco.
* Modelli o parti del modello passati a invisibile, usando il componente di [override dello stato gerarchico](../overview/features/override-hierarchical-state.md).

Di conseguenza, è possibile scrivere un'applicazione destinata `standard` a dimensioni che caricano più modelli con un conteggio di poligoni vicino al limite per ogni singolo modello. Quando l'applicazione mostra un solo modello alla volta, la scacchiera non viene attivata.

### <a name="how-to-determine-the-number-of-polygons"></a>Come determinare il numero di poligoni

Esistono due modi per determinare il numero di poligoni di un modello o di una scena che contribuiscono al limite di budget della `standard` VM di dimensioni:
* Sul lato conversione del modello recuperare il [file JSON di output della conversione](../how-tos/conversion/get-information.md)e controllare la `numFaces` voce nella sezione [ *inputStatistics* ](../how-tos/conversion/get-information.md#the-inputstatistics-section)
* Se l'applicazione sta occupando contenuto dinamico, il numero di poligoni sottoposti a rendering può essere sottoposto a query in modo dinamico durante il Runtime. Usare una [query di valutazione delle prestazioni](../overview/features/performance-queries.md#performance-assessment-queries) e verificare la presenza del `polygonsRendered` membro nello `FrameStatistics` struct. Il `polygonsRendered` campo verrà impostato su `bad` quando il renderer raggiunge la limitazione del poligono. Lo sfondo della scacchiera viene sempre sbiadito con un certo ritardo per garantire che l'azione dell'utente possa essere eseguita dopo questa query asincrona. L'azione dell'utente può, ad esempio, nascondere o eliminare le istanze del modello.

## <a name="pricing"></a>Prezzi

Per una suddivisione dettagliata dei prezzi per ogni tipo di macchina virtuale, vedere la pagina relativa ai prezzi per il [rendering remoto](https://azure.microsoft.com/pricing/details/remote-rendering) .

## <a name="next-steps"></a>Passaggi successivi
* [Script di Azure PowerShell di esempio](../samples/powershell-example-scripts.md)
* [Conversione di modelli](../how-tos/conversion/model-conversion.md)

