<properties 
   pageTitle="Sostituire lo chassis sul dispositivo StorSimple | Microsoft Azure"
   description="Descrive come rimuovere e sostituire lo chassis per l'alloggiamento principale o EBOD di StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# Sostituire lo chassis sul dispositivo StorSimple

## Overview

Questa esercitazione illustra come rimuovere e sostituire uno chassis in un dispositivo StorSimple serie 8000. Il modello 8100 di StorSimple è un dispositivo a chassis singolo (uno chassis), mentre il modello 8600 è un dispositivo a chassis doppio (due chassis). Per un modello 8600, esistono potenzialmente due chassis che potrebbero avere esito negativo nel dispositivo: lo chassis per lo chassis principale o lo chassis per lo chassis EBOD.

In entrambi i casi, lo chassis sostitutivo che viene fornito da Microsoft è vuoto. Non verranno inclusi PCM, moduli controller, unità disco a stato solido (SSD), unità disco rigido (HDD) o moduli EBOD.

>[AZURE.IMPORTANT] Prima di rimuovere e sostituire lo chassis, esaminare le informazioni di sicurezza descritte in [Sostituzione dei componenti hardware di StorSimple](storsimple-hardware-component-replacement.md).

## Rimuovere lo chassis

Eseguire i passaggi seguenti per rimuovere lo chassis sul dispositivo StorSimple.

#### Per rimuovere uno chassis

1. Assicurarsi che il dispositivo StorSimple sia arrestato e disconnesso da tutte le origini di alimentazione.

2. Rimuovere tutti i cavi di rete e SAS, se applicabile.

3. Rimuovere l'unità dal rack.

4. Rimuovere ciascuna unità e annotare gli slot da cui vengono rimossi. Per ulteriori informazioni, vedere[Rimuovere l'unità disco](storsimple-disk-drive-replacement.md#remove-the-disk-drive).

5. Nell'enclosure EBOD (se si tratta di chassis non riuscito), rimuovere i moduli controller EBOD. Per ulteriori informazioni, vedere[Rimuovere un controller EBOD](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller).

    Nell’enclosure principale (se si tratta di chassis non riuscito), rimuovere i controller e annotare gli slot da cui vengono rimossi. Per ulteriori informazioni, vedere[Rimuovere un controller](storsimple-controller-replacement.md#remove-a-controller).

## Installare lo chassis

Eseguire i passaggi seguenti per installare lo chassis sul dispositivo StorSimple.

#### Per installare uno chassis

1. Montare lo chassis nel rack. Per ulteriori informazioni, vedere[Montare su rack il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device)o[Montare su rack il dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).

2. Dopo il montaggio dello chassis nel rack, installare i moduli controller nelle stesse posizioni in cui erano precedentemente installati.

3. Installare le unità nelle stesse posizioni e slot in cui erano precedentemente installati.

    >[AZURE.NOTE] È consigliabile installare prima le unità SSD negli slot e quindi installare i dischi rigidi.

2. Con il dispositivo montato nel rack e i componenti installati, connettere il dispositivo alle appropriate prese di corrente e accendere il dispositivo. Per ulteriori informazioni, vedere[Cablare il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device)o[Cablare il dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## Passaggi successivi

Leggere ulteriori informazioni sulla [Sostituzione dei componenti hardware di StorSimple](storsimple-hardware-component-replacement.md).

<!---HONumber=AcomDC_0817_2016-->