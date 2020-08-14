---
title: Usare le funzionalità di accessibilità nella finestra di progettazione (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni sulle scelte rapide da tastiera e sulle funzionalità di accessibilità per la lettura dello schermo disponibili nella finestra di progettazione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.custom: designer
ms.openlocfilehash: 5d91dc212eb2f856f0252261f2ddfc8a5a4d70ee
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210374"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>Usare una tastiera per la finestra di progettazione di Azure Machine Learning (anteprima)

Vengono fornite informazioni su come usare una tastiera e un'utilità per la lettura dello schermo per usare la finestra di progettazione di Azure Machine Learning. Per un elenco di scelte rapide da tastiera che funzionano ovunque nel portale di Azure, vedere [Tasti di scelta rapida nel portale di Azure](../azure-portal/azure-portal-keyboard-shortcuts.md).

Questo flusso di lavoro è stato testato con [Assistente vocale](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) e [JAWS](https://www.freedomscientific.com/products/software/jaws/), ma dovrebbe funzionare con altre utilità standard per la lettura dello schermo.

## <a name="navigate-the-pipeline-graph"></a>Esplorare il grafo della pipeline

Il grafo della pipeline è organizzato come elenco annidato. L'elenco esterno è un elenco di moduli che descrive tutti i moduli presenti nel grafo della pipeline. L'elenco interno è un elenco di connessioni che descrive tutte le connessioni di un modulo specifico.  

1. Nell'elenco di moduli usare il tasto di direzione per passare da un modulo a un altro.
1. Usare TAB per aprire l'elenco di connessioni per il modulo di destinazione.
1. Usare il tasto di direzione per alternarsi tra le porte di connessione del modulo.
1. Usare "G" per passare al modulo di destinazione.

## <a name="edit-the-pipeline-graph"></a>Modificare il grafo della pipeline

### <a name="add-a-module-to-the-graph"></a>Aggiungere un modulo al grafo

1. Usare CTRL+F6 per spostare lo stato attivo dall'area di disegno all'albero dei moduli.
1. Trovare il modulo desiderato nell'albero dei moduli usando il controllo TreeView standard.

### <a name="edit-a-module"></a>Modificare un modulo

Per connettere un modulo a un altro modulo:

1. Usare CTRL+MAIUSC+H quando la destinazione è un modulo dell'elenco di moduli per aprire l'helper di connessione.
1. Modificare le porte di connessione del modulo.

Per modificare le proprietà del modulo:

1. Usare CTRL+MAIUSC+ E quando la destinazione è un modulo per aprire le proprietà del modulo.
1. Modificare le proprietà del modulo.

## <a name="navigation-shortcuts"></a>Scelte rapide di navigazione

| Combinazioni di tasti | Descrizione |
|-|-|
| CTRL+F6 | Attiva/disattiva lo stato attivo tra l'area di disegno e l'albero dei moduli |
| CTRL+F1   | Apre la scheda informazioni quando lo stato attivo passa su un nodo nell'albero dei moduli |
| CTRL+MAIUSC+H | Apre l'helper di connessione quando lo stato attivo si trova su un nodo |
| CTRL+MAIUSC+E | Apre le proprietà del modulo quando lo stato attivo si trova su un nodo |
| CTRL+G | Sposta lo stato attivo sul primo nodo con errore se l'esecuzione della pipeline ha avuto esito negativo |

## <a name="action-shortcuts"></a>Scelte rapide di azione

Usare le scelte rapide seguenti con il tasto di scelta. Per altre informazioni sui tasti di scelta, vedere https://en.wikipedia.org/wiki/Access_key.

| Combinazioni di tasti | Azione |
|-|-|
| Tasto di scelta+R | Esegui |
| Tasto di scelta+P | Pubblica |
| Tasto di scelta+C | Clone |
| Tasto di scelta+D | Distribuire |
| Tasto di scelta+I | Crea/aggiorna una pipeline di inferenza |
| Tasto di scelta+B | Crea/aggiorna una pipeline di inferenza batch |
| Tasto di scelta+K | Apre l'elenco a discesa "Create inference pipeline" (Crea pipeline inferenza) |
| Tasto di scelta+U | Apre l'elenco a discesa "Update inference pipeline" (Aggiorna pipeline inferenza) |
| Tasto di scelta+M | Apre l'elenco a discesa per altre opzioni (...) |

## <a name="next-steps"></a>Passaggi successivi

- [Attivare un contrasto elevato o cambiare tema](../azure-portal/set-preferences.md#choose-a-theme-or-enable-high-contrast)
- [Strumenti correlati all'accessibilità in Microsoft](https://www.microsoft.com/accessibility)
