---
title: Progetto acustica Bake risoluzione
titlesuffix: Azure Cognitive Services
description: Questa panoramica concettuale descrive la differenza tra le soluzioni grossolane e fine durante creare con bake acustica.
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: kylesto
ms.openlocfilehash: c4f4581beb26eb63392644b40b1e5f16dae0481d
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849949"
---
# <a name="project-acoustics-bake-resolution"></a>Progetto acustica Bake risoluzione
Questa panoramica concettuale descrive la differenza tra le soluzioni grossolane e fine durante creare con bake acustica. Si sceglie questa impostazione durante il passaggio di probe del salvataggio dei flusso di lavoro.

## <a name="Coarse-vs-Fine-Resolution"></a>Risoluzione grossolana o fine

L'unica differenza tra le impostazioni di risoluzione grossolana e fine è la frequenza con cui viene eseguita la simulazione. La risoluzione fine usa una frequenza due volte superiore rispetto a quella grossolana. Ciò comporta diverse implicazioni sul modello acustica simulazione:

* La lunghezza d'onda per la risoluzione grossolana è il doppio rispetto a quella fine, quindi i voxel sono due volte più grandi.
* Il tempo di simulazione è direttamente correlato alle dimensioni dei voxel, di conseguenza un bake grossolano è circa 16 volte più veloce di un bake fine.
* I portali (ad esempio, le porte o windows) inferiori alle dimensioni voxel non possono essere simulati. L'impostazione grossolana può causare alcuni di questi portali più piccoli per non eseguire la simulazione; Pertanto, non passano suoni tramite in fase di esecuzione. Per verificare se ciò accade, visualizzare i voxel.
* La frequenza di simulazione più bassa comporta meno diffrazione sui bordi e agli angoli.
* Origini audio non possono trovarsi all'interno di "colorata" voxels (vale a dire voxels che contengono la geometria). Di conseguenza alcun suono. È più difficile posizionare origini audio in modo che non siano presenti all'interno di voxels più grande di grossolani rispetto a quando si usa l'impostazione di fine.
* I voxel più grandi si estenderanno maggiormente nei portali, come illustrato di seguito. La prima immagine è stata creata con la risoluzione grossolana, mentre la seconda rappresenta la stessa entrata con la risoluzione fine. Come indicato dai contrassegni di colore rosso, l'intrusione è considerevolmente minore nell'entrata con l'impostazione Fine (Fine). La linea blu è l'entrata definita dalla geometria, mentre la linea rossa è il portale acustico effettivo definito dalle dimensioni dei voxel. Le conseguenze di questa intrusione in una determinata situazione dipendono interamente da come i voxel sono allineati alla geometria del portale, il che è determinato dalle dimensioni e dalle posizioni degli oggetti nella scena.

![Screenshot dei voxel con risoluzione grossolana che riempiono un'entrata in Unreal](media/unreal-coarse-bake.png)

![Screenshot dei voxel con risoluzione fine in un'entrata in Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Passaggi successivi

Provare le impostazioni di risoluzione grossolano e accurato manualmente usando nostri [Unreal](unreal-baking.md) oppure [Unity](unity-baking.md) plug-in.