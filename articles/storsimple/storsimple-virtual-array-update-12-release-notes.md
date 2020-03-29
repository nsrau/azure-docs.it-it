---
title: Note sulla versione di Microsoft Azure StorSimple Virtual Array Update 1.2 Documenti Microsoft
description: Vengono descritti i problemi di apertura critici e le relative soluzioni per l'array virtuale StorSimple che esegue l'aggiornamento 1.2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66420604"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>Note sulla versione di StorSimple Virtual Array Update 1.2

Le seguenti note sulla versione identificano i problemi critici aperti e risolti per lgi aggiornamenti dell'array virtuale di Microsoft Azure StorSimple.

Le note sulla versione vengono continuamente aggiornate. Nel caso venissero rilevati problemi critici che richiedono soluzioni alternative, le informazioni relative verranno immediatamente aggiunte. Prima di distribuire l'array virtuale StorSimple, esaminare attentamente le informazioni contenute nelle note sulla versione.

L'aggiornamento 1.2 corrisponde alla versione software **10.0.10311.0**.

> [!IMPORTANT]
> - Gli aggiornamenti comportano il riavvio del dispositivo. Se sono in corso operazioni di I/O, il dispositivo va incontro a tempi di inattività. Per istruzioni dettagliate sui pacchetti utilizzati per applicare questo aggiornamento, visitare [download dell'aggiornamento 1.2](#download-update-12).
> - L'aggiornamento 1.2 è disponibile tramite il portale di Azure solo se nel dispositivo è in esecuzione l'aggiornamento 1.0 o 1.1.Update 1.2 is available to you via the Azure portal only if your device is running Update 1.0 or 1.1.

## <a name="whats-new-in-update-12"></a>Novità dell'aggiornamento 1.2

Questo aggiornamento contiene le seguenti correzioni di bug:

- Maggiore resilienza durante l'elaborazione dei file eliminati.
- Migliorata la gestione delle eccezioni nel percorso di avvio del codice che porta a errori ridotti nei backup, nel ripristino, nelle letture cloud e nel recupero automatico dello spazio.

## <a name="download-update-12"></a>Scarica l'aggiornamento 1.2

Per scaricare questo aggiornamento, visitare il server [di catalogo di Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx) e scaricare il pacchetto KB4502035. Questo pacchetto contiene i seguenti pacchetti:

 - **KB4493446** che contiene gli aggiornamenti cumulativi di Windows per 2012 R2 fino ad aprile 2019. Per ulteriori informazioni sugli elementi inclusi in questo rollup, vedere [Aggiornamento cumulativo mensile](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)della protezione .
 - **KB3011067** che è un file di pacchetto autonomo di Microsoft Update WindowsTH-KB3011067-x64. Questo file viene usato per aggiornare il software del dispositivo.

Scaricare KB4502035 e seguire queste istruzioni per [applicare l'aggiornamento tramite l'interfaccia utente Web locale.](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)

## <a name="issues-fixed-in-update-12"></a>Problemi risolti nell'aggiornamento 1.2

La tabella seguente fornisce un riepilogo dei problemi risolti in questa versione.

| No. | Funzionalità | Problema |
| --- | --- | --- |
| 1 |Eliminazione| Nelle versioni precedenti del software, si è verificato un problema quando l'utilizzo del dispositivo non è cambiato anche quando i file sono stati eliminati. Questo problema è stato risolto in questa versione. Il percorso del codice di suddivisione in livelli è stato reso più resiliente durante l'elaborazione dei file eliminati.|
| 2 |Gestione delle eccezioni| Nelle versioni precedenti del software, si è verificato un problema dopo il riavvio del sistema che potrebbe potenzialmente portare a errori nei backup, ripristino, lettura dal cloud e recupero automatico dello spazio. Questa versione contiene modifiche su come le eccezioni sono state gestite nel percorso di avvio.|

## <a name="known-issues-in-update-12"></a>Problemi noti nell'aggiornamento 1.2

Nell'aggiornamento 1.2 non sono stati rilevati nuovi problemi di rilascio. Tutti i problemi di versione indicata vengono trasferiti dalle versioni precedenti. Per visualizzare il riepilogo dei problemi noti inclusi nelle versioni precedenti, vedere [Problemi noti nell'aggiornamento 1.1.](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11)

## <a name="next-steps"></a>Passaggi successivi

Scaricare KB4502035 e [applicare l'aggiornamento tramite l'interfaccia utente Web locale](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Riferimenti

Si desidera consultare le note su una versione precedente? Passare a:
* [Note sulla versione di StorSimple Virtual Array Update 1.1](storsimple-virtual-array-update-11-release-notes.md)
* [Note sulla versione dell'aggiornamento 1.0 per l'array virtuale StorSimple](storsimple-virtual-array-update-1-release-notes.md)
* [Note sulla versione dell'aggiornamento 0.6 per l'array virtuale StorSimple](storsimple-virtual-array-update-06-release-notes.md)
* [Note sulla versione dell'aggiornamento 0.5 per l'array virtuale StorSimple](storsimple-virtual-array-update-05-release-notes.md)
* [Note sulla versione dell'aggiornamento 0.4 per l'array virtuale StorSimple](storsimple-virtual-array-update-04-release-notes.md)
* [Note sulla versione dell'aggiornamento 0.3 per l'array virtuale StorSimple](storsimple-ova-update-03-release-notes.md)
* [Note sulla versione dell'Aggiornamento 0.1 e 0.2 per l'array virtuale StorSimple](storsimple-ova-update-01-release-notes.md)
* [Note sulla versione con disponibilità generale dell'array virtuale StorSimple](storsimple-ova-pp-release-notes.md)
