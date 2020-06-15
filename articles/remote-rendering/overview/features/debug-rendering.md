---
title: Eseguire il debug del rendering
description: Panoramica degli effetti di rendering del debug sul lato server
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: dc07b20340b852eadeb7c93e5cef2ed2092b3641
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758657"
---
# <a name="debug-rendering"></a>Eseguire il debug del rendering

L'API di rendering del debug offre una gamma di opzioni globali per modificare il rendering sul lato server con diversi effetti di debug.

## <a name="available-debug-rendering-effects"></a>Effetti di rendering del debug disponibili

|Impostazione                          | Effetto                               |
|---------------------------------|:-------------------------------------|
|Contatore fotogrammi                    | Esegue il rendering di una sovrimpressione di testo nell'angolo superiore sinistro del fotogramma. Il testo mostra l'ID del frame sul lato server corrente, che viene incrementato continuamente durante il processo di rendering. |
|Conteggio poligoni                    | Esegue il rendering di una sovrimpressione di testo nell'angolo superiore sinistro del fotogramma. Il testo mostra la quantità di poligoni attualmente sottoposti a rendering, lo stesso valore restituito dalle [query sulle prestazioni lato server](performance-queries.md)| 
|Wireframe                        | Se abilitata, il rendering di tutta la geometria degli oggetti caricati nel server verrà eseguito in modalità wireframe. In questa modalità verranno rasterizzati solo i bordi dei poligoni. |

Il codice seguente abilita questi effetti di debug:

```cs
void EnableDebugRenderingEffects(AzureSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Actions.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

```cpp
void EnableDebugRenderingEffects(ApiHandle<AzureSession> session, bool highlight)
{
    ApiHandle<DebugRenderingSettings> settings = *session->Actions()->DebugRenderingSettings();

    // Enable frame counter text overlay on the server side rendering
    settings->RenderFrameCount(true);

    // Enable polygon count text overlay on the server side rendering
    settings->RenderPolygonCount(true);

    // Enable wireframe rendering of object geometry on the server
    settings->RenderWireframe(true);
}
```

![Rendering del debug](./media/debug-rendering.png)

> [!NOTE]
> Tutti gli effetti di rendering del debug sono impostazioni globali che hanno effetto sull'intero fotogramma.

## <a name="use-cases"></a>Casi d'uso

L'API di rendering del debug è concepita per attività di debug semplici, come verificare che la connessione del servizio funzioni correttamente. Le opzioni di rendering del testo influiscono direttamente sui fotogrammi video trasmessi a valle. Abilitandole, si verifica se vengono ricevuti nuovi fotogrammi e se la decodifica video viene eseguita correttamente.

Tuttavia, gli effetti forniti non offrono un'introspezione dettagliata sull'integrità del servizio. Per questo caso d'uso sono consigliate le [query sulle prestazioni lato server](performance-queries.md).

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

* L'abilitazione delle sovrimpressioni di testo comporta un overhead delle prestazioni minimo o inesistente.
* L'abilitazione della modalità wireframe comporta un overhead delle prestazioni non indifferente, anche se variabile a seconda della scena. Per le scene complesse, questa modalità può causare il calo della frequenza dei fotogrammi sotto il target dei 60 Hz.

## <a name="next-steps"></a>Passaggi successivi

* [Modalità di rendering](../../concepts/rendering-modes.md)
* [Query sulle prestazioni lato server](performance-queries.md)
