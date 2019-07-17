---
title: Risoluzione dei problemi di soluzione acustica del progetto
titlesuffix: Azure Cognitive Services
description: Questa panoramica concettuale descrive la differenza tra risoluzioni grossolane e fini durante la cottura di acustica.
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: kylsto
ms.openlocfilehash: ba7c71c666ae8f90d499bfe52303537aeb112a77
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296706"
---
# <a name="project-acoustics-bake-resolution"></a>Risoluzione dei problemi di soluzione acustica del progetto
Questa panoramica concettuale descrive la differenza tra risoluzioni grossolane e fini durante la cottura di acustica. È possibile scegliere questa impostazione durante il passaggio dei probe del flusso di lavoro di cottura.

## <a name="Coarse-vs-Fine-Resolution"></a>Risoluzione grossolana o fine

L'unica differenza tra le impostazioni di risoluzione grossolana e fine è la frequenza con cui viene eseguita la simulazione. La risoluzione fine usa una frequenza due volte superiore rispetto a quella grossolana. Questa operazione presenta diverse implicazioni sulla simulazione acustica:

* La lunghezza d'onda per la risoluzione grossolana è il doppio rispetto a quella fine, quindi i voxel sono due volte più grandi.
* Il tempo di simulazione è direttamente correlato alle dimensioni dei voxel, di conseguenza un bake grossolano è circa 16 volte più veloce di un bake fine.
* Non è possibile simulare i portali, ad esempio le porte o le finestre, di dimensioni inferiori a voxel. L'impostazione grossolana può causare la mancata simulazione di alcuni dei portali più piccoli; quindi, non passano il suono attraverso in fase di esecuzione. Per verificare se ciò accade, visualizzare i voxel.
* La frequenza di simulazione più bassa comporta meno diffrazione sui bordi e agli angoli.
* Le origini audio non possono trovarsi all'interno di voxel "riempiti", ad esempio voxel che contengono la geometria. Questa operazione non genera alcun suono. È più difficile inserire le origini audio, in modo che non si trovino all'interno di un voxel di dimensioni maggiori rispetto a quando si usa l'impostazione fine.
* I voxel più grandi si estenderanno maggiormente nei portali, come illustrato di seguito. La prima immagine è stata creata con la risoluzione grossolana, mentre la seconda rappresenta la stessa entrata con la risoluzione fine. Come indicato dai contrassegni di colore rosso, l'intrusione è considerevolmente minore nell'entrata con l'impostazione Fine (Fine). La linea blu è l'entrata definita dalla geometria, mentre la linea rossa è il portale acustico effettivo definito dalle dimensioni dei voxel. Le conseguenze di questa intrusione in una determinata situazione dipendono interamente da come i voxel sono allineati alla geometria del portale, il che è determinato dalle dimensioni e dalle posizioni degli oggetti nella scena.

![Screenshot dei voxel con risoluzione grossolana che riempiono un'entrata in Unreal](media/unreal-coarse-bake.png)

![Screenshot dei voxel con risoluzione fine in un'entrata in Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Passaggi successivi

Provare le impostazioni di risoluzione grossolana e fine con i plug-in [Unreal](unreal-baking.md) o [Unity](unity-baking.md) .
