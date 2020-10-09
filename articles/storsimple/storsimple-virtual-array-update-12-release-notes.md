---
title: Note sulla versione dell'aggiornamento 1,2 dell'array virtuale Microsoft Azure StorSimple | Microsoft Docs
description: Descrive i problemi e le risoluzioni critici aperti per l'array virtuale StorSimple che esegue l'aggiornamento 1,2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "66420604"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>Note sulla versione dell'aggiornamento 1,2 di StorSimple Virtual Array

Le seguenti note sulla versione identificano i problemi critici aperti e risolti per lgi aggiornamenti dell'array virtuale di Microsoft Azure StorSimple.

Le note sulla versione vengono aggiornate continuamente. Nel caso venissero rilevati problemi critici che richiedono soluzioni alternative, le informazioni relative verranno immediatamente aggiunte. Prima di distribuire l'array virtuale StorSimple, esaminare attentamente le informazioni contenute nelle note sulla versione.

L'aggiornamento 1,2 corrisponde alla versione del software **10.0.10311.0**.

> [!IMPORTANT]
> - Gli aggiornamenti comportano il riavvio del dispositivo. Se sono in corso operazioni di I/O, il dispositivo va incontro a tempi di inattività. Per istruzioni dettagliate sui pacchetti usati per applicare questo aggiornamento, vedere [scaricare l'aggiornamento 1,2](#download-update-12).
> - L'aggiornamento 1,2 è disponibile tramite il portale di Azure solo se il dispositivo esegue l'aggiornamento 1,0 o 1,1.

## <a name="whats-new-in-update-12"></a>Novità dell'aggiornamento 1.2

Questo aggiornamento contiene le correzioni di bug seguenti:

- Miglioramento della resilienza durante l'elaborazione dei file eliminati.
- Sono state migliorate le eccezioni di gestione nel percorso di avvio del codice, con conseguente riduzione degli errori nei backup, ripristino, letture del cloud e recupero automatizzato dello spazio.

## <a name="download-update-12"></a>Scaricare l'aggiornamento 1,2

Per scaricare l'aggiornamento, passare al server di [catalogo Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx) e scaricare il pacchetto KB4502035. Questo pacchetto contiene i pacchetti seguenti:

 - **KB4493446** che contiene gli aggiornamenti cumulativi di Windows per 2012 R2 fino al 2019 aprile. Per ulteriori informazioni su ciò che è incluso in questo rollup, vedere [rollup della sicurezza mensile di aprile](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067** , ovvero un file di pacchetto autonomo Microsoft Update WINDOWSTH-KB3011067-x64. Questo file viene usato per aggiornare il software del dispositivo.

Scaricare KB4502035 e seguire queste istruzioni per [applicare l'aggiornamento tramite l'interfaccia utente Web locale](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Problemi risolti nell'aggiornamento 1.2

La tabella seguente fornisce un riepilogo dei problemi risolti in questa versione.

| No. | Funzionalità | Problema |
| --- | --- | --- |
| 1 |Eliminazione| Nelle versioni precedenti del software si verificava un problema quando l'utilizzo del dispositivo non cambiava anche quando i file sono stati eliminati. Questo problema è stato risolto in questa versione. Il percorso del codice di suddivisione in livelli è stato reso più resiliente durante l'elaborazione dei file eliminati.|
| 2 |Gestione delle eccezioni| Nelle versioni precedenti del software si verificava un problema dopo il riavvio del sistema che poteva potenzialmente causare errori nei backup, nel ripristino, nella lettura dal cloud e nel recupero automatizzato dello spazio. Questa versione contiene le modifiche apportate al modo in cui le eccezioni sono state gestite nel percorso di avvio.|

## <a name="known-issues-in-update-12"></a>Problemi noti nell'aggiornamento 1.2

Non sono stati rilasciati nuovi problemi, indicati nell'aggiornamento 1,2. Tutti i problemi indicati dalla versione vengono rilasciati dalle versioni precedenti. Per visualizzare il riepilogo dei problemi noti inclusi nelle versioni precedenti, passare a [problemi noti nell'aggiornamento 1,1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Passaggi successivi

Scaricare KB4502035 e [applicare l'aggiornamento tramite l'interfaccia utente Web locale](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Riferimenti

Si desidera consultare le note su una versione precedente? Passare a:
* [Note sulla versione dell'aggiornamento 1,1 di StorSimple Virtual Array](storsimple-virtual-array-update-11-release-notes.md)
* [Note sulla versione dell'aggiornamento 1.0 per l'array virtuale StorSimple](storsimple-virtual-array-update-1-release-notes.md)
* [Note sulla versione dell'aggiornamento 0.6 per l'array virtuale StorSimple](storsimple-virtual-array-update-06-release-notes.md)
* [Note sulla versione dell'aggiornamento 0.5 per l'array virtuale StorSimple](storsimple-virtual-array-update-05-release-notes.md)
* [Note sulla versione dell'aggiornamento 0.4 per l'array virtuale StorSimple](storsimple-virtual-array-update-04-release-notes.md)
* [Note sulla versione dell'aggiornamento 0.3 per l'array virtuale StorSimple](storsimple-ova-update-03-release-notes.md)
* [Note sulla versione dell'Aggiornamento 0.1 e 0.2 per l'array virtuale StorSimple](storsimple-ova-update-01-release-notes.md)
* [Note sulla versione con disponibilità generale dell'array virtuale StorSimple](storsimple-ova-pp-release-notes.md)
