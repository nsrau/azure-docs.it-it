---
title: Giunzioni di rilevamento del corpo di Azure Kinect
description: Comprendere il frame del corpo, le giunzioni, le coordinate congiunte e la gerarchia congiunta in Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: cedmonds, abalan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, porting, body, tracking, joint, Hierarchy, Bone, Connection
ms.openlocfilehash: 4cf6ac13a93d0674f9fa144abcc3153a2d7c3350
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276737"
---
# <a name="azure-kinect-body-tracking-joints"></a>Giunzioni di rilevamento del corpo di Azure Kinect

Il rilevamento del corpo Kinect di Azure consente di tenere traccia di più corpi umani allo stesso tempo. Ogni corpo include un ID per la correlazione temporale tra i frame e la Skeleton cinematica. Il numero di corpi rilevati in ogni frame può essere acquisito usando `k4abt_frame_get_num_bodies()` .

## <a name="joints"></a>Giunti

La posizione e l'orientamento comuni sono stime relative al frame del sensore di profondità globale di riferimento. La posizione è specificata in millimetri. L'orientamento è espresso come un quaternione normalizzato.

## <a name="joint-coordinates"></a>Coordinate congiunte

La posizione e l'orientamento di ogni giunzione formano il proprio sistema di coordinate congiunte. Tutti i sistemi di coordinate congiunte sono sistemi di coordinate assoluti rispetto al sistema di coordinate 3D di profondità della fotocamera.

> [!NOTE]
> Le coordinate congiunte sono in orientamento asse. L'orientamento dell'asse è ampiamente usato con avatar commerciali, motori di gioco e software di rendering. L'uso dell'orientamento dell'asse semplifica i movimenti con mirroring, ad esempio, aumentano entrambe le armi di 20 gradi.

![Coordinate congiunte](./media/concepts/joint-coordinates.png)

## <a name="joint-hierarchy"></a>Gerarchia congiunta

Una Skeleton include 32 giunzioni con la gerarchia congiunta che scorre dal centro del corpo alle estremità. Ogni connessione (Bone) collega il giunto padre a una giunzione figlio. Nella figura vengono illustrati i percorsi comuni e la connessione rispetto al corpo umano.

![Gerarchia congiunta](./media/concepts/joint-hierarchy.png)

Nella tabella seguente vengono elencate le connessioni congiunte standard.

|Indice |Nome comune     | Unione padre   |
|------|---------------|----------------|
| 0    |BACINO         | -              |
| 1    |SPINE_NAVAL    | BACINO         |
| 2    |SPINE_CHEST    | SPINE_NAVAL    |
| 3    |COLLO           | SPINE_CHEST    |
| 4    |CLAVICLE_LEFT  | SPINE_CHEST    |
| 5    |SHOULDER_LEFT  | CLAVICLE_LEFT  |
| 6    |ELBOW_LEFT     | SHOULDER_LEFT  |
| 7    |WRIST_LEFT     | ELBOW_LEFT     |
| 8    |HAND_LEFT      | WRIST_LEFT     |
| 9    |HANDTIP_LEFT   | HAND_LEFT      |
| 10   |THUMB_LEFT     | WRIST_LEFT     |
| 11   |CLAVICLE_RIGHT | SPINE_CHEST    |
| 12   |SHOULDER_RIGHT | CLAVICLE_RIGHT |
| 13   |ELBOW_RIGHT    | SHOULDER_RIGHT |
| 14   |WRIST_RIGHT    | ELBOW_RIGHT    |
| 15   |HAND_RIGHT     | WRIST_RIGHT    |
| 16   |HANDTIP_RIGHT  | HAND_RIGHT     |
| 17   |THUMB_RIGHT    | WRIST_RIGHT    |
| 18   |HIP_LEFT       | BACINO         |
| 19   |KNEE_LEFT      | HIP_LEFT       |
| 20   |ANKLE_LEFT     | KNEE_LEFT      |
| 21   |FOOT_LEFT      | ANKLE_LEFT     |
| 22   |HIP_RIGHT      | BACINO         |
| 23   |KNEE_RIGHT     | HIP_RIGHT      |
| 24   |ANKLE_RIGHT    | KNEE_RIGHT     |
| 25   |FOOT_RIGHT     | ANKLE_RIGHT    |
| 26   |HEAD           | COLLO           |
| 27   |MUSO           | HEAD           |
| 28   |EYE_LEFT       | HEAD           |
| 29   |EAR_LEFT       | HEAD           |
| 30   |EYE_RIGHT      | HEAD           |
| 31   |EAR_RIGHT      | HEAD           |

## <a name="next-steps"></a>Passaggi successivi

[Mappa di indice di rilevamento corpo](body-index-map.md)
