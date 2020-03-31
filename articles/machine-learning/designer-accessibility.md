---
title: Usare le funzionalità di accessibilità nella finestra di progettazione (anteprima)Use accessibility features in the designer (preview)
titleSuffix: Azure Machine Learning
description: Informazioni sui tasti di scelta rapida e sulle funzionalità di accessibilità delle utilità per la lettura dello schermo disponibili nella finestra di progettazione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 59199291589a81d0a0d96b7867078b8196be086f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77366190"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>Usare una tastiera per usare la finestra di progettazione di Azure Machine Learning (anteprima)Use a keyboard to use Azure Machine Learning designer (preview)

Informazioni su come usare una tastiera e un'utilità per la lettura dello schermo per usare la finestra di progettazione di Azure Machine Learning.Learn how to use a keyboard and screen reader to use Azure Machine Learning designer. Per un elenco dei tasti di scelta rapida che funzionano ovunque nel portale di Azure, vedere Scelte rapide da tastiera nel portale di [AzureFor](../azure-portal/azure-portal-keyboard-shortcuts.md) a list of keyboard shortcuts that work everywhere in the Azure portal, see Keyboard shortcuts in the Azure portal

Questo flusso di lavoro è stato testato con [Narrator](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) e [JAWS](https://www.freedomscientific.com/products/software/jaws/), ma dovrebbe funzionare con altre utilità per la lettura dello schermo standard.

## <a name="navigate-the-pipeline-graph"></a>Esplorare il grafico della pipeline

Il grafico della pipeline è organizzato come elenco nidificato. L'elenco esterno è un elenco di moduli, che descrive tutti i moduli nel grafico della pipeline. L'elenco interno è un elenco di connessione, che descrive tutte le connessioni di un modulo specifico.  

1. Nell'elenco dei moduli, utilizzare il tasto freccia per passare da un modulo all'altro.
1. Utilizzare la scheda per aprire l'elenco di connessione per il modulo di destinazione.
1. Utilizzare il tasto freccia per passare da una porta di connessione all'altra per il modulo.
1. Utilizzare "G" per passare al modulo di destinazione.

## <a name="edit-the-pipeline-graph"></a>Modificare il grafico della pipeline

### <a name="add-a-module-to-the-graph"></a>Aggiungere un modulo al grafico

1. Utilizzare CTRL s F6 per spostare lo stato attivo dall'area di disegno all'albero del modulo.
1. Individuare il modulo desiderato nell'albero del modulo utilizzando il controllo treeview standard.

### <a name="edit-a-module"></a>Modificare un modulo

Per collegare un modulo a un altro modulo:

1. Per aprire l'helper di connessione, utilizzare Ctrl , Maiusc e H.
1. Modificare le porte di connessione per il modulo.

Per regolare le proprietà del modulo:

1. Per aprire le proprietà del modulo, utilizzare Ctrl , Maiusc e E.
1. Modificare le proprietà del modulo.

## <a name="navigation-shortcuts"></a>Tasti di scelta rapida per la navigazione

| Tasto o combinazione di tasti | Descrizione |
|-|-|
| CTRL + F6 | Attiva/disattiva lo stato attivo tra l'area di disegno e l'albero del modulo |
| Premere CTRL e F1   | Aprire la scheda informazioni quando ci si concentra su un nodo nell'albero dei moduli |
| Premere CTRL e MAIUSC e quindi Fare Clic su H | Aprire l'helper di connessione quando lo stato attivo si trova su un nodoOpen the connection helper when focus is on a node |
| Premere CTRL e MAIUSC e quindi E | Aprire le proprietà del modulo quando lo stato attivo si trova su un nodoOpen module properties when focus is on a node |
| CTRL+G | Spostare lo stato attivo sul primo nodo non riuscito se l'esecuzione della pipeline non è riuscita |

## <a name="action-shortcuts"></a>Tasti di scelta rapida per le azioni

Utilizzare i seguenti tasti di scelta rapida con il tasto di scelta. Per ulteriori informazioni sui https://en.wikipedia.org/wiki/Access_keytasti di scelta, vedere .

| Tasto o combinazione di tasti | Azione |
|-|-|
| Tasto di scelta: R | Esegui |
| Tasto di scelta: P | Pubblica |
| Tasto di scelta: C | Clone |
| Tasto di scelta : D | Distribuire |
| Tasto di scelta: I | Creare/aggiornare la pipeline di inferenzaCreate/update inference pipeline |
| Tasto di scelta: B | Creare/aggiornare la pipeline di inferenza batchCreate/update batch inference pipeline |
| Tasto di scelta: K | Aprire l'elenco a discesa "Crea pipeline di inferenza" |
| Tasto di scelta : U | Aprire l'elenco a discesa "Aggiorna pipeline di inferenza" |
| Tasto di scelta : M | Apri il menu a discesa Apri altro(...) |

## <a name="next-steps"></a>Passaggi successivi

- [Attivare un contrasto elevato o cambiare tema](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Strumenti correlati all'accessibilità in Microsoft](https://www.microsoft.com/accessibility)
