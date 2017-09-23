---
title: Note sulla versione dell'aggiornamento 0.5 per l'array virtuale StorSimple | Microsoft Docs
description: Vengono illustrati i principali problemi in sospeso e le soluzioni per l'array virtuale StorSimple che esegue l'aggiornamento 0.5.
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
ms.workload: NA
ms.date: 05/08/2017
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 4d020ff2b998da4cb52fe91e4d7d4b93544965a8
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>Note sulla versione dell'aggiornamento 0.5 per l'array virtuale StorSimple

## <a name="overview"></a>Panoramica

Le seguenti note sulla versione identificano i problemi critici aperti e risolti per lgi aggiornamenti dell'array virtuale di Microsoft Azure StorSimple.

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire l'array virtuale StorSimple, esaminare attentamente le informazioni contenute nelle note sulla versione.

L'aggiornamento 0.5 corrisponde alla versione del software **10.0.10290.0**.

> [!NOTE]
> Gli aggiornamenti comportano il riavvio del dispositivo. Se sono in corso operazioni di I/O, il dispositivo va incontro a tempi di inattività. Per istruzioni dettagliate su come applicare l'aggiornamento, vedere [Install Update 0.5](storsimple-virtual-array-install-update-05.md) (Installare l'aggiornamento 0.5).


## <a name="whats-new-in-the-update-05"></a>Novità dell'aggiornamento 0.5
L'aggiornamento 0.5 è principalmente una build per la correzione di bug. I miglioramenti e le correzioni di bug principali sono i seguenti:

- **Miglioramenti della resilienza dei backup**: questa versione include correzioni che consentono di migliorare la resilienza dei backup. Nelle versioni precedenti i backup vengono ritentati solo per determinate eccezioni. In questa versione vengono ritentate tutte le eccezioni di backup, con conseguente aumento della resilienza.

- **Aggiornamenti relativi al monitoraggio dell'utilizzo della memoria**: a partire dal 30 giugno 2017, la funzionalità di monitoraggio dell'utilizzo della memoria per Serie di dispositivi virtuali StorSimple verrà ritirata. Questo vale per i grafici di monitoraggio su tutti gli array virtuali che eseguono l'aggiornamento 0.4 o versione precedente. Questo aggiornamento contiene le modifiche necessarie per continuare a usare la funzionalità di monitoraggio dell'utilizzo della memoria nel portale di Azure. Installare questo aggiornamento critico prima del 30 giugno 2017 per continuare a usare la funzionalità di monitoraggio.


## <a name="issues-fixed-in-the-update-05"></a>Problemi risolti nell'aggiornamento 0.5

La tabella seguente fornisce un riepilogo dei problemi risolti in questa versione.

| No. | Funzionalità | Problema |
| --- | --- | --- |
| 1 |Resilienza dei backup| Nelle versioni precedenti i backup vengono ritentati solo per determinate eccezioni. Questa versione include una correzione per aumentare la resilienza dei backup eseguendo nuovamente tutte le eccezioni di backup.|
| 2 |Monitoraggio| La funzionalità di monitoraggio dell'utilizzo della memoria per Serie di dispositivi virtuali StorSimple verrà deprecata a partire dal 30 giugno 2017. Questa azione influisce sui grafici di monitoraggio del servizio Gestione dispositivi StorSimple in esecuzione negli array virtuali StorSimple (modello 1200). Questa versione include aggiornamenti che consentono all'utente di continuare a usare la funzionalità di monitoraggio dell'utilizzo della memoria negli array virtuali anche dopo il 30 giugno 2017.|
| 3 |File server| Nelle versioni precedenti un utente può copiare per errore file crittografati nell'array virtuale. Questa versione include una correzione che impedisce la copia di file crittografati nell'array virtuale. Se, prima dell'aggiornamento, nel dispositivo sono presenti file crittografati, i backup continueranno ad avere esito negativo fino a quando tutti i file crittografati non verranno eliminati dal sistema. |


## <a name="known-issues-in-the-update-05"></a>Problemi noti nell'aggiornamento 0.5

La tabella seguente fornisce un riepilogo dei problemi noti per l'array virtuale StorSimple e include i problemi delle versioni precedenti.

| No. | Funzionalità | Problema | Soluzione alternativa/commenti |
| --- | --- | --- | --- |
| **1.** |Aggiornamenti |È impossibile aggiornare i dispositivi virtuali creati nella versione di anteprima in una versione supportata di disponibilità generale. |Questi dispositivi virtuali devono essere sottoposti a failover per la versione di disponibilità generale tramite un flusso di lavoro di ripristino di emergenza. |
| **2.** |Disco dati sottoposto a provisioning |Dopo il provisioning di un disco dati di una determinata dimensione specificata e la creazione di un dispositivo virtuale StorSimple corrispondente, non si deve espandere o compattare il disco dati. Il tentativo di eseguire questa operazione comporta la perdita di tutti i dati nei livelli locali del dispositivo. | |
| **3.** |Criteri di gruppo |Nei dispositivi aggiunti a un dominio, l'applicazione di criteri di gruppo può influire negativamente sul funzionamento dei dispositivi. |Assicurarsi che l'array virtuale sia nella propria unità organizzativa (OU) per Active Directory e che ad esso non siano applicati oggetti Criteri di gruppo (GPO). |
| **4.** |Interfaccia utente Web locale |Se sono abilitate le funzionalità di sicurezza avanzate di Internet Explorer, alcune pagine dell'interfaccia utente Web locale come Risoluzione dei problemi o Manutenzione potrebbero non funzionare correttamente. Anche i pulsanti di queste pagine potrebbero non funzionare. |Disattivare le funzionalità di protezione avanzata di Internet Explorer. |
| **5.** |Interfaccia utente Web locale |In una macchina virtuale Hyper-V, le interfacce di rete nell'interfaccia utente Web vengono visualizzate come interfacce da 10 Gbps. |Questo comportamento è una reflection di Hyper-V. Hyper-V visualizza sempre 10 Gbps per le schede di rete virtuale. |
| **6.** |Volumi o condivisioni a livelli |Il blocco dell'intervallo di byte per le applicazioni che operano con i volumi a livelli di StorSimple non è supportato. Se il blocco dell'intervallo di byte è abilitato, la suddivisione in livelli di StorSimple non funziona. |Tra le misure consigliate:  <br></br>Disattivare il blocco dell'intervallo di byte nella logica dell'applicazione.<br></br>Scegliere di inserire dati per questa applicazione in volumi aggiunti in locale invece di volumi a più livelli.<br></br>*Avvertenza*: quando si usano volumi aggiunti in locale e il blocco dell'intervallo di byte è abilitato, il volume aggiunto in locale può risultare online ancora prima del completamento del ripristino. In questi casi, se è in corso un ripristino, è necessario attenderne il completamento. |
| **7.** |Condivisioni a livelli |Lavorare con file di grandi dimensioni può comportare una suddivisione in livelli lenta. |Durante l'uso di file di grandi dimensioni, è consigliabile che il file più grande sia più piccolo del 3% rispetto alle dimensioni della condivisione. |
| **8.** |Capacità usata per le condivisioni |Anche in assenza di dati nelle condivisioni, è possibile riscontrarne il consumo. Questo consumo si verifica perché la capacità usata per le condivisioni include i metadati. | |
| **9.** |Ripristino di emergenza |È possibile eseguire solo il ripristino di emergenza di un file server nello stesso dominio del dispositivo di origine. Il ripristino di emergenza su un dispositivo di destinazione in un dominio diverso non è supportato in questa versione. |L'implementazione è prevista per una versione futura. Per altre informazioni, vedere [Ripristino di emergenza e failover del dispositivo per l'array virtuale StorSimple tramite il portale di Azure](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |I dispositivi virtuali StorSimple non possono essere gestiti tramite Azure PowerShell in questa versione. |La gestione dei dispositivi virtuali deve essere eseguita tramite il portale di Azure e l'interfaccia utente Web locale. |
| **11.** |Modifica della password |La console del dispositivo array virtuale accetta solo input nel formato della tastiera inglese (Stati Uniti). | |
| **12.** |CHAP |Le credenziali di autenticazione CHAP create non possono essere rimosse. Se si modificano le credenziali di autenticazione CHAP, per rendere effettive le modifiche è necessario portare i volumi offline e quindi riportarli online. |La risoluzione del problema è prevista per una versione futura. |
| **13.** |Server iSCSI |Le risorse di archiviazione usate visualizzate per un volume iSCSI possono essere diverse nel servizio Gestione dispositivi StorSimple e nell'host iSCSI. |L'host iSCSI ha la visualizzazione del file system.<br></br>Il dispositivo vede i blocchi allocati quando il volume aveva raggiunto le dimensioni massime. |
| **14.** |File server |Se un file contenuto in una cartella è associato a un flusso di dati alternativi (ADS), sul flusso non viene eseguito il backup o il ripristino tramite ripristino di emergenza, clonazione e ripristino a livello di elemento. | |
| **15.** |File server |I collegamenti simbolici non sono supportati. | |
| **16.** |File server |I file protetti da Windows Encrypting File System (EFS), quando vengono copiati o archiviati in un file server StorSimple Virtual Array, restituiscono una configurazione non supportata.  | |

## <a name="next-step"></a>Passaggio successivo
[Installare l'aggiornamento 0.5](storsimple-virtual-array-install-update-05.md) nell'array virtuale StorSimple.

## <a name="references"></a>Riferimenti
Si desidera consultare le note su una versione precedente? Passare a:

* [Note sulla versione dell'aggiornamento 0.4 per l'array virtuale StorSimple](storsimple-virtual-array-update-04-release-notes.md)
* [Note sulla versione dell'aggiornamento 0.3 per l'array virtuale StorSimple](storsimple-ova-update-03-release-notes.md)
* [Note sulla versione dell'Aggiornamento 0.1 e 0.2 per l'array virtuale StorSimple](storsimple-ova-update-01-release-notes.md)
* [Note sulla versione con disponibilità generale dell'array virtuale StorSimple](storsimple-ova-pp-release-notes.md)


