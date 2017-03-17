---
title: StorSimple 8000 Series Update 4 release notes | Microsoft Docs (Note sulla versione dell&quot;aggiornamento 4 di StorSimple serie 8000 | Microsoft Docs)
description: "Vengono descritte le nuove funzionalità, i problemi e le soluzioni alternative dell&quot;aggiornamento 4 per StorSimple serie 8000."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/03/2017
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: c682f9eac1a6ff5e3771aa9e4dfa5b5ffefc1223
ms.lasthandoff: 03/04/2017


---
# <a name="storsimple-8000-series-update-4-release-notes"></a>Note sulla versione dell'aggiornamento 4 di StorSimple serie 8000

## <a name="overview"></a>Panoramica

Nelle note sulla versione seguenti vengono descritte le nuove funzionalità e sono indicati i problemi critici non risolti relativi all'aggiornamento 4 di StorSimple serie 8000. Contengono inoltre un elenco degli aggiornamenti software di StorSimple inclusi in questa versione. 

L'aggiornamento 4 può essere applicato a qualsiasi dispositivo StorSimple che esegue la versione in disponibilità generale del software o una versione di aggiornamento compresa tra 0.1 e 3.1. La versione del dispositivo associata all'aggiornamento 4 è 6.3.9600.17820.

Esaminare le informazioni contenute nelle note sulla versione prima di distribuire l'aggiornamento della soluzione StorSimple.

> [!IMPORTANT]
> * L'aggiornamento 4 include software per dispositivi, firmware USM, firmware e driver LSI, firmware del disco, Storport e Spaceport, aggiornamenti di sicurezza e una serie di altri aggiornamenti del sistema operativo. L'installazione dell'aggiornamento richiede circa 4 ore. È consigliabile applicare questo aggiornamento per mantenere aggiornato il dispositivo. 
> * Per le nuove versioni è possibile che gli aggiornamenti non siano immediatamente visibili perché viene effettuata un'implementazione graduale degli aggiornamenti. Attendere alcuni giorni e quindi provare a cercare nuovamente gli aggiornamenti, perché verranno presto resi disponibili.

## <a name="whats-new-in-update-4"></a>Novità dell'aggiornamento 4

L'aggiornamento 4 include gli importanti aggiornamenti e correzioni di bug descritti di seguito.

* **Algoritmi di recupero dello spazio automatizzati e più intelligenti**: nell'aggiornamento 4 sono stati migliorati gli algoritmi di recupero dello spazio automatizzati per regolare i cicli di recupero dello spazio in base allo spazio recuperato previsto disponibile nel cloud. 

* **Miglioramenti delle prestazioni per i volumi aggiunti in locale**: l'aggiornamento 4 ha migliorato le prestazioni dei volumi aggiunti in locale negli scenari con inserimenti elevati di dati (dati paragonabili alle dimensioni del volume).

* **Ripristino basato su mappa termica**: nelle versioni precedenti, dopo un ripristino di emergenza i dati venivano ripristinati dal cloud in base agli schemi di accesso, il che causava un rallentamento delle prestazioni. 

    L'aggiornamento 4 include una nuova funzionalità che traccia i dati a cui si accede di frequente per creare una mappa termica quando il dispositivo è in uso prima del ripristino di emergenza. I blocchi dei dati più usati avranno una "temperatura" maggiore rispetto a quelli dei dati meno usati. Dopo un ripristino di emergenza, StorSimple usa la mappa termica per ripristinare e riattivare automaticamente i dati dal cloud. 

    Tutti i ripristini sono basati su mappa termica. Per ulteriori informazioni su come eseguire una query e annullare i processi di ripristino e riattivazione basati su mappa termica, vedere [Informazioni di riferimento sui cmdlet di Windows PowerShell per StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

* **Strumento di diagnostica StorSimple**: nell'aggiornamento 4 è incluso un nuovo strumento di diagnostica StorSimple per facilitare le operazioni di diagnostica e risoluzione dei problemi legati a sistema, rete, prestazioni e integrità dei componenti hardware. Questo strumento viene eseguito tramite Windows PowerShell per StorSimple. 

* **Strumento di migrazione StorSimple basato su interfaccia utente**: prima di questa versione, la migrazione dei dati dalle serie 5000-7000 richiedeva l'esecuzione di parte del flusso di lavoro della migrazione tramite l'interfaccia Azure PowerShell. In questa versione è invece disponibile uno strumento di migrazione StorSimple basato su un'interfaccia utente facile da usare, per facilitare lo stesso flusso di lavoro di migrazione. Questo strumento permette inoltre il consolidamento dei bucket di ripristino. 

* **Supporto MPIO per StorSimple Snapshot Manager**: in questa versione è stato implementato il supporto MPIO per StorSimple Snapshot Manager.

* **Modifiche correlate a FIPS**: da questa versione in poi, FIPS è abilitato per impostazione predefinita su tutti i dispositivi StorSimple serie 8000 per gli account Microsoft Azure per enti pubblici e del cloud Azure pubblico.

* **Modifiche dell'aggiornamento**: da questa versione in poi, FIPS è abilitato per impostazione predefinita su tutti i dispositivi StorSimple serie 8000 per gli account Microsoft Azure per enti pubblici e del cloud Azure pubblico.

* **Avviso per gli errori del disco**: aggiunto un nuovo avviso che informa l'utente di errori imminenti legati al disco.

* **Modifiche alla sostituzione del controller**: aggiunto un cmdlet che consente all'utente di richiedere lo stato del processo di sostituzione del controller. Per altre informazioni, vedere il [cmdlet per richiedere lo stato di sostituzione del controller](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Problemi risolti nell'aggiornamento 4

La seguente tabella fornisce un riepilogo dei problemi risolti nell'aggiornamento 4.    

| No | Funzionalità | Problema | Si applica a un dispositivo fisico | Si applica a un dispositivo virtuale |
| --- | --- | --- | --- | --- |
| 1 |Failover |Nella versione precedente, dopo il failover nel sito del cliente si verificava un problema legato alla pulizia. Tale problema è stato corretto in questa versione. |Sì |Sì |
| 2 |Volumi aggiunti in locale |Nella versione precedente, si verificava un problema per la creazione di volumi correlati per i volumi aggiunti localmente che causava errori nella creazione. In questa versione il problema è stato corretto una volta individuata la causa radice. |Sì |No |
| 3 |Pacchetto di supporto |Nella versione precedente, si verificavano problemi relativi al pacchetto di supporto che causavano un'eccezione System.OutOfMemory o altri errori, risultanti nell'esito negativo della creazione del pacchetto. Tali bug sono stati risolti in questa versione. |Sì |Sì |
| 4 |Monitoraggio |Nella versione precedente, si verificava un problema con i grafici di monitoraggio per i volumi aggiunti localmente, per cui l'utilizzo veniva mostrato in EB. Questo bug è stato risolto in questa versione. |Sì |Sì |
| 5 |Migrazione |Nella versione precedente, si verificavano diversi problemi legati all'affidabilità della migrazione dai dispositivi delle serie 5000-7000 a quelli della serie 8000. I problemi sono stati risolti in questa versione. |Sì |Sì |
| 6 |Aggiornare |Se nelle versioni precedenti si è verificato un errore di aggiornamento, i controller vengono inseriti in modalità di ripristino, pertanto l'utente non può procedere con l'aggiornamento e deve contattare il supporto tecnico Microsoft. <br> In questa versione il comportamento è stato modificato. Se l'utente sperimenta un errore di aggiornamento con entrambi i controller che eseguono la stessa versione (aggiornamento 4), i controller non entrano in modalità di ripristino. Se l'utente sperimenta questo errore, è consigliabile che attenda un po' e poi riprovi a eseguire l'aggiornamento. Il nuovo tentativo potrebbe avere esito positivo. Se il nuovo tentativo non riesce, è necessario contattare il supporto tecnico Microsoft. |Sì |Sì |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Problemi noti nell'aggiornamento 4 rispetto alle versioni precedenti

Nessun nuovo problema noto nell'aggiornamento 4. Per un elenco di problemi ancora presenti nell'aggiornamento 4 rispetto alle versioni precedenti, vedere le [note sulla versione dell'aggiornamento 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Aggiornamenti firmware e controller SAS presenti nell'aggiornamento 4

Questa versione dispone degli aggiornamenti del controller SAS e del firmware e driver LSI. Per altre informazioni su come installare gli aggiornamenti, vedere l'articolo sull' [installazione dell'aggiornamento 4](storsimple-install-update-4.md) nel dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-4"></a>Aggiornamenti del dispositivo virtuale nell'aggiornamento 4

Questo aggiornamento non può essere applicato all'appliance cloud StorSimple (ovvero il dispositivo virtuale). Sarà necessario creare nuovi dispositivi virtuali. 

## <a name="next-step"></a>Passaggio successivo

Informazioni su come [installare l'aggiornamento 4](storsimple-install-update-4.md) nel dispositivo StorSimple.


