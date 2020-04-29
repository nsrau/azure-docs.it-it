---
title: Eseguire il debug del rendering
description: Panoramica degli effetti di rendering del debug sul lato server
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: f10c736cad9322752d5d552d29ef0c63635628a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868167"
---
# <a name="debug-rendering"></a>Eseguire il debug del rendering

L'API per il rendering del debug offre una gamma di opzioni globali per modificare il rendering lato server con diversi effetti di debug.

## <a name="available-debug-rendering-effects"></a>Effetti di rendering di debug disponibili

|Impostazione                          | Effetto                               |
|---------------------------------|:-------------------------------------|
|Contatore frame                    | Esegue il rendering di una sovrapposizione di testo nell'angolo superiore sinistro del frame. Il testo Mostra l'ID del frame sul lato server corrente, che viene incrementato continuamente durante il processo di rendering. |
|Conteggio poligoni                    | Esegue il rendering di una sovrapposizione di testo nell'angolo superiore sinistro del frame. Il testo Mostra la quantità di poligoni attualmente sottoposta a rendering, lo stesso valore della [query sulle prestazioni lato server.](performance-queries.md)| 
|Wireframe                        | Se abilitata, verrà eseguito il rendering di tutti i Geometry di oggetti caricati nel server in modalità wireframe. In questa modalità verranno rasterizzati solo i bordi dei poligoni. |

Il codice seguente Abilita questi effetti di debug:

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

![Rendering del debug](./media/debug-rendering.png)

> [!NOTE]
> Tutti gli effetti di rendering del debug sono impostazioni globali che interessano l'intero frame.

## <a name="use-cases"></a>Casi d'uso

L'API per il rendering del debug è concepita per semplici attività di debug, ad esempio la verifica della connessione del servizio in esecuzione correttamente. Le opzioni di rendering del testo influiscono direttamente sui fotogrammi video con flusso inferiore. L'abilitazione di questi elementi verifica se vengono ricevuti nuovi frame e la decodifica video è stata eseguita correttamente.

Tuttavia, gli effetti forniti non forniscono un'introspezione dettagliata sull'integrità del servizio. Per questo caso d'uso sono consigliate le [query sulle prestazioni lato server](performance-queries.md) .

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

* L'abilitazione della sovrimpressione del testo non comporta un sovraccarico delle prestazioni.
* L'abilitazione della modalità wireframe comporta un sovraccarico delle prestazioni non semplice, sebbene possa variare a seconda della scena. Per le scene complesse, questa modalità può causare il calo della frequenza dei fotogrammi sotto la destinazione 60 Hz.

## <a name="next-steps"></a>Passaggi successivi

* [Modalità di rendering](../../concepts/rendering-modes.md)
* [Query sulle prestazioni lato server](performance-queries.md)
