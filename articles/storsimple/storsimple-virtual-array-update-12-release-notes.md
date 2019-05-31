---
title: Note sulla versione di Microsoft Azure StorSimple Virtual Array Update 1.2 | Microsoft Docs
description: Descrive i problemi critici aperti e le risoluzioni per l'Array virtuale StorSimple esegue l'aggiornamento 1.2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420604"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>Note sulla versione di aggiornamento 1.2 di StorSimple Virtual Array

Le seguenti note sulla versione identificano i problemi critici aperti e risolti per lgi aggiornamenti dell'array virtuale di Microsoft Azure StorSimple.

Le note sulla versione vengono aggiornate continuamente. Come vengono individuati problemi critici che richiedono una soluzione alternativa, vengono aggiunti. Prima di distribuire l'array virtuale StorSimple, esaminare attentamente le informazioni contenute nelle note sulla versione.

L'aggiornamento 1.2 corrisponde alla versione del software **10.0.10311.0**.

> [!IMPORTANT]
> - Gli aggiornamenti comportano il riavvio del dispositivo. Se sono in corso operazioni di I/O, il dispositivo va incontro a tempi di inattività. Per istruzioni dettagliate su pacchetti usati per applicare questo aggiornamento, visitare [Scarica aggiornamento 1.2](#download-update-12).
> - L'aggiornamento 1.2 è disponibile tramite il portale di Azure solo se il dispositivo esegue l'aggiornamento 1.0 o 1.1.

## <a name="whats-new-in-update-12"></a>Novità dell'aggiornamento 1.2

Questo aggiornamento contiene correzioni di bug seguenti:

- Miglioramento della resilienza durante l'elaborazione dei file eliminati
- Migliorata la gestione delle eccezioni nel percorso di codice di avvio iniziali per ridurre gli errori nel backup, ripristino, letture dal cloud e recupero dello spazio automatizzati.

## <a name="download-update-12"></a>Scaricare l'aggiornamento 1.2

Per scaricare questo aggiornamento, visitare il [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Home.aspx) server e il pacchetto di download di KB4502035. Questo pacchetto contiene i pacchetti seguenti:

 - **KB4493446** che contiene gli aggiornamenti cumulativi di Windows per 2012 R2 fino a aprile 2019. Per altre informazioni su cosa è incluso in questo rollup, visitare [rollup mensili della sicurezza del mese di aprile](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067** che è un file di pacchetto autonomo Microsoft Update WindowsTH-KB3011067-x64. Questo file viene usato per aggiornare il software del dispositivo.

Scaricare KB4502035 e queste istruzioni [applicare l'aggiornamento tramite interfaccia utente web locale](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Problemi risolti nell'aggiornamento 1.2

La tabella seguente fornisce un riepilogo dei problemi risolti in questa versione.

| No. | Funzionalità | Problema |
| --- | --- | --- |
| 1 |Eliminazione| Nelle versioni precedenti del software, si è verificato un problema durante l'utilizzo del dispositivo non è stato modificato anche quando i file sono stati eliminati. Questo problema viene risolto in questa versione. Suddivisione in livelli nel percorso del codice è stato effettuato più resiliente durante l'elaborazione di file eliminati.|
| 2 |Gestione delle eccezioni| Nelle versioni precedenti del software, si è verificato un problema dopo il riavvio del sistema che può causare errori nei backup, ripristino, la lettura dal cloud e recupero dello spazio automatizzati. Questa versione contiene modifiche per quanto riguarda come le eccezioni erano gestite nel percorso di avvio.|

## <a name="known-issues-in-update-12"></a>Problemi noti nell'aggiornamento 1.2

Nessun nuovo problema sono stati annotati versione nell'aggiornamento 1.2. Tutti i problemi indicati rilascio vengono trasferiti dalle versioni precedenti. Per visualizzare il riepilogo dei problemi noti inclusi rispetto alle versioni precedenti, passare a [problemi noti di aggiornamento 1.1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Passaggi successivi

Scaricare KB4502035 e [applicare l'aggiornamento tramite interfaccia utente web locale](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Riferimenti

Si desidera consultare le note su una versione precedente? Passare a:
* [Note sulla versione 1.1 di StorSimple Virtual Array Update](storsimple-virtual-array-update-11-release-notes.md)
* [Note sulla versione dell'aggiornamento 1.0 per l'array virtuale StorSimple](storsimple-virtual-array-update-1-release-notes.md)
* [Note sulla versione dell'aggiornamento 0.6 per l'array virtuale StorSimple](storsimple-virtual-array-update-06-release-notes.md)
* [Note sulla versione dell'aggiornamento 0.5 per l'array virtuale StorSimple](storsimple-virtual-array-update-05-release-notes.md)
* [Note sulla versione dell'aggiornamento 0.4 per l'array virtuale StorSimple](storsimple-virtual-array-update-04-release-notes.md)
* [Note sulla versione dell'aggiornamento 0.3 per l'array virtuale StorSimple](storsimple-ova-update-03-release-notes.md)
* [Note sulla versione dell'Aggiornamento 0.1 e 0.2 per l'array virtuale StorSimple](storsimple-ova-update-01-release-notes.md)
* [Note sulla versione con disponibilità generale dell'array virtuale StorSimple](storsimple-ova-pp-release-notes.md)
