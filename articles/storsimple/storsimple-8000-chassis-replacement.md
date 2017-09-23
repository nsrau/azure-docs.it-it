---
title: Sostituire lo chassis in un dispositivo StorSimple serie 8000| Microsoft Docs
description: Descrive come rimuovere e sostituire lo chassis per l'alloggiamento principale o EBOD di StorSimple.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 073fcf0064f1d1482f4683d733f00cf918ff2f38
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017

---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Sostituire lo chassis sul dispositivo StorSimple
## <a name="overview"></a>Panoramica
Questa esercitazione illustra come rimuovere e sostituire uno chassis in un dispositivo StorSimple serie 8000. Il modello 8100 di StorSimple è un dispositivo a chassis singolo (uno chassis), mentre il modello 8600 è un dispositivo a chassis doppio (due chassis). Per un modello 8600, esistono potenzialmente due chassis che potrebbero avere esito negativo nel dispositivo: lo chassis per lo chassis principale o lo chassis per lo chassis EBOD.

In entrambi i casi, lo chassis sostitutivo che viene fornito da Microsoft è vuoto. Non verranno inclusi PCM, moduli controller, unità disco a stato solido (SSD), unità disco rigido (HDD) o moduli EBOD.

> [!IMPORTANT]
> Prima di rimuovere e sostituire lo chassis, esaminare le informazioni di sicurezza descritte in [Sostituzione dei componenti hardware di StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Rimuovere lo chassis
Eseguire i passaggi seguenti per rimuovere lo chassis sul dispositivo StorSimple.

#### <a name="to-remove-a-chassis"></a>Per rimuovere uno chassis
1. Assicurarsi che il dispositivo StorSimple sia arrestato e disconnesso da tutte le origini di alimentazione.
2. Rimuovere tutti i cavi di rete e SAS, se applicabile.
3. Rimuovere l'unità dal rack.
4. Rimuovere ciascuna unità e annotare gli slot da cui vengono rimossi. Per ulteriori informazioni, vedere [Rimuovere l'unità disco](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. Nell'enclosure EBOD (se si tratta di chassis non riuscito), rimuovere i moduli controller EBOD. Per ulteriori informazioni, vedere [Rimuovere un controller EBOD](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    Nell’enclosure principale (se si tratta di chassis non riuscito), rimuovere i controller e annotare gli slot da cui vengono rimossi. Per ulteriori informazioni, vedere [Rimuovere un controller](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Installare lo chassis
Eseguire i passaggi seguenti per installare lo chassis sul dispositivo StorSimple.

#### <a name="to-install-a-chassis"></a>Per installare uno chassis
1. Montare lo chassis nel rack. Per altre informazioni, vedere [Montare su rack il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) o [Montare su rack il dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Dopo il montaggio dello chassis nel rack, installare i moduli controller nelle stesse posizioni in cui erano precedentemente installati.
3. Installare le unità nelle stesse posizioni e slot in cui erano precedentemente installati.
   
   > [!NOTE]
   > È consigliabile installare prima le unità SSD negli slot e quindi installare i dischi rigidi.
  
4. Con il dispositivo montato nel rack e i componenti installati, connettere il dispositivo alle appropriate prese di corrente e accendere il dispositivo. Per i dettagli, vedere [Cablare il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) o [Cablare il dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Passaggi successivi
Leggere ulteriori informazioni sulla [Sostituzione dei componenti hardware di StorSimple](storsimple-8000-hardware-component-replacement.md).


