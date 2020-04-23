---
title: Eseguire il debug del rendering
description: Panoramica degli effetti di rendering del debug lato server
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: f10c736cad9322752d5d552d29ef0c63635628a5
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868167"
---
# <a name="debug-rendering"></a>Eseguire il debug del rendering

L'API di rendering del debug fornisce una gamma di opzioni globali per modificare il rendering lato server con diversi effetti di debug.

## <a name="available-debug-rendering-effects"></a>Effetti di rendering di debug disponibili

|Impostazione                          | Effetto                               |
|---------------------------------|:-------------------------------------|
|Contatore telaio                    | Esegue il rendering di una sovrapposizione di testo nell'angolo superiore sinistro della cornice. Il testo mostra l'ID fotogramma sul lato server corrente, che viene continuamente incrementato man mano che il rendering procede. |
|Conteggio poligoni                    | Esegue il rendering di una sovrapposizione di testo nell'angolo superiore sinistro della cornice. Il testo mostra la quantità di poligoni attualmente sottoposta a rendering, lo stesso valore di [query sul lato server](performance-queries.md)| 
|Wireframe                        | Se abilitata, tutte le geometrie degli oggetti caricate sul server verranno visualizzate in modalità wireframe. Solo i bordi dei poligoni verranno rasterizzati in questa modalità. |

Il codice seguente abilita questi effetti di debug:The following code enables these debugging effects:

``` cs
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

![Rendering di debug](./media/debug-rendering.png)

> [!NOTE]
> Tutti gli effetti di rendering di debug sono impostazioni globali che influiscono sull'intero fotogramma.

## <a name="use-cases"></a>Casi d'uso

L'API di rendering del debug è destinata a semplici attività di debug, ad esempio la verifica della corretta esecuzione della connessione al servizio. Le opzioni di rendering del testo influiscono direttamente sui fotogrammi video in downstreaming. L'abilitazione di essi verifica se vengono ricevuti nuovi fotogrammi e video decodificati correttamente.

Tuttavia, gli effetti forniti non danno alcuna introspezione dettagliata in integrità del servizio. Le [query sulle prestazioni lato server](performance-queries.md) sono consigliate per questo caso di utilizzo.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

* L'abilitazione delle sovrapposizioni di testo comporta un overhead delle prestazioni minimo o nessuno.
* L'abilitazione della modalità wireframe comporta un sovraccarico delle prestazioni non banale, anche se può variare a seconda della scena. Per le scene complesse, questa modalità può causare un calo della frequenza fotogrammi al di sotto della destinazione a 60 Hz.

## <a name="next-steps"></a>Passaggi successivi

* [Modalità di rendering](../../concepts/rendering-modes.md)
* [Query sulle prestazioni lato server](performance-queries.md)
