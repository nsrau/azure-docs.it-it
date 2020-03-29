---
title: Risoluzione del forno acustica del progetto
titlesuffix: Azure Cognitive Services
description: Questa panoramica concettuale descrive la differenza tra risoluzioni grossolane e fini durante la cottura dell'acustica.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d8eb3b2cbaf7b4e842d8338eefde756f6d381111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854357"
---
# <a name="project-acoustics-bake-resolution"></a>Risoluzione del forno acustica del progetto
Questa panoramica concettuale descrive la differenza tra risoluzioni grossolane e fini durante la cottura dell'acustica. È possibile scegliere questa impostazione durante il passaggio Sonde del flusso di lavoro di cottura.

## <a name="coarse-vs-fine-resolution"></a><a name="Coarse-vs-Fine-Resolution"></a>Risoluzione grossolana e fine

L'unica differenza tra le impostazioni di risoluzione grossolana e fine è la frequenza con cui viene eseguita la simulazione. La risoluzione fine usa una frequenza due volte superiore rispetto a quella grossolana. Questo ha una serie di implicazioni sulla simulazione acustica:

* La lunghezza d'onda per la risoluzione grossolana è il doppio rispetto a quella fine, quindi i voxel sono due volte più grandi.
* Il tempo di simulazione è direttamente correlato alle dimensioni dei voxel, di conseguenza un bake grossolano è circa 16 volte più veloce di un bake fine.
* I portali (ad esempio porte o finestre) più piccoli delle dimensioni del voxel non possono essere simulati. L'impostazione grossolana può causare la simulazione di alcuni di questi portali più piccoli; pertanto, non passeranno il suono in fase di esecuzione. Per verificare se ciò accade, visualizzare i voxel.
* La frequenza di simulazione più bassa comporta meno diffrazione sui bordi e agli angoli.
* Le sorgenti sonore non possono essere posizionate all'interno di voxel "riempiti" (cioè voxel che contengono geometria). Ciò non comporta alcun suono. E 'più difficile da posizionare sorgenti sonore in modo che non sono all'interno dei voxel più grandi di grossolana di quanto non sia quando si utilizza l'impostazione fine.
* I voxel più grandi si estenderanno maggiormente nei portali, come illustrato di seguito. La prima immagine è stata creata con la risoluzione grossolana, mentre la seconda rappresenta la stessa entrata con la risoluzione fine. Come indicato dai contrassegni di colore rosso, l'intrusione è considerevolmente minore nell'entrata con l'impostazione Fine (Fine). La linea blu è l'entrata definita dalla geometria, mentre la linea rossa è il portale acustico effettivo definito dalle dimensioni dei voxel. Le conseguenze di questa intrusione in una determinata situazione dipendono interamente da come i voxel sono allineati alla geometria del portale, il che è determinato dalle dimensioni e dalle posizioni degli oggetti nella scena.

![Screenshot dei voxel con risoluzione grossolana che riempiono un'entrata in Unreal](media/unreal-coarse-bake.png)

![Screenshot dei voxel con risoluzione fine in un'entrata in Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Passaggi successivi

Prova le impostazioni di risoluzione grossolane e fini utilizzando i nostri plugin [Unreal](unreal-baking.md) o [Unity.](unity-baking.md)
