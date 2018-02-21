---
title: Note sulla versione dell'aggiornamento 1.0 per l'array virtuale StorSimple | Microsoft Docs
description: Vengono illustrati i principali problemi in sospeso e le soluzioni per l'array virtuale StorSimple che esegue l'aggiornamento 1.0.
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 777718c4893f1edab3613be5dc941e2716d4c972
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>Note sulla versione dell'aggiornamento 1.0 per l'array virtuale StorSimple

## <a name="overview"></a>Panoramica

Le seguenti note sulla versione identificano i problemi critici aperti e risolti per lgi aggiornamenti dell'array virtuale di Microsoft Azure StorSimple.

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire l'array virtuale StorSimple, esaminare attentamente le informazioni contenute nelle note sulla versione.

L'aggiornamento 1.0 corrisponde alla versione del software **10.0.10296.0**.

> [!IMPORTANT]
> - Gli aggiornamenti comportano il riavvio del dispositivo. Se sono in corso operazioni di I/O, il dispositivo va incontro a tempi di inattività. Per istruzioni dettagliate su come applicare l'aggiornamento, vedere [Installare l'aggiornamento 1.0](storsimple-virtual-array-install-update-1.md).
>
> - L'aggiornamento 1 è disponibile tramite il portale di Azure solo se il dispositivo esegue l'aggiornamento 0.6.

## <a name="whats-new-in-update-10"></a>Novità dell'aggiornamento 1.0

**L'aggiornamento 1.0 contiene delle modifiche relative all'autenticazione del servizio Gestione dispositivi StorSimple e deve essere implementato appena possibile.** Questo aggiornamento contiene i miglioramenti e le correzioni di bug seguenti:

 - **Utilizzo di Azure Active Directory (AAD) per l'autenticazione con il servizio Gestione dispositivi StorSimple** - A partire dall'aggiornamento 1.0, viene usato Azure Active Directory per l'autenticazione con il servizio Gestione dispositivi StorSimple. Il meccanismo di autenticazione precedente verrà deprecato entro dicembre 2017. Tutti gli utenti devono includere i nuovi URL di autenticazione nelle rispettive regole del firewall. Per altre informazioni, vedere gli [URL di autenticazione elencati nei requisiti di rete per l'array virtuale StorSimple](storsimple-ova-system-requirements.md).
 
    Se l'URL di autenticazione non viene incluso nelle regole del firewall, verrà visualizzato un avviso critico per informare l'utente che non è stato possibile autenticare il dispositivo StorSimple con il servizio. Se viene visualizzato questo avviso, è necessario includere il nuovo URL di autenticazione. Per altre informazioni, vedere [StorSimple networking alerts](storsimple-virtual-array-manage-alerts.md) (Avvisi di rete di StorSimple).

 - **Miglioramento delle prestazioni** - Sono stati corretti numerosi bug per migliorare la velocità di lettura nel cloud e di suddivisione in livelli. Di conseguenza, le prestazioni di backup e ripristino sono migliorate per i dispositivi iSCSI e file server.

 - **Miglioramento del processo di Garbage Collection** - Le correzioni di bug di questa versione migliorano le prestazioni del ciclo di Garbage Collection quando l'account del dispositivo e l'account di archiviazione sono in due aree distanti.

 - **Miglioramento della registrazione** - Questa versione contiene dei miglioramenti alla registrazione relativa al processo di Garbage Collection e al percorso I/O.


## <a name="issues-fixed-in-update-10"></a>Problemi risolti nell'aggiornamento 1.0

La tabella seguente fornisce un riepilogo dei problemi risolti in questa versione.

| di serie | Funzionalità | Problema |
| --- | --- | --- |
| 1 |Autenticazione basata su Azure Active Directory| Questa versione contiene delle modifiche che consentono ad Azure Active Directory di eseguire l'autenticazione con Gestione dispositivi StorSimple.|
| 2 |Garbage Collection| Questo problema è stato segnalato nel sito di un cliente in cui l'account del dispositivo e l'account di archiviazione sono in due aree diverse. Il cliente ha segnalato errori di rete intermittenti che hanno un impatto sulla fatturazione. In questa versione il problema è stato corretto. |
| 3 |Prestazioni| Questa versione contiene modifiche che migliorano le prestazioni di ripristino, lettura nel cloud e suddivisione in livelli.|
| 4 |Aggiornamento| Nella versione precedente si verificava un problema con l'aggiornamento che causava errori di backup nel sito del cliente. Tale problema è stato corretto in questa versione.|

## <a name="known-issues-in-update-10"></a>Problemi noti nell'aggiornamento 1.0

La tabella seguente fornisce un riepilogo dei problemi noti per l'array virtuale StorSimple e include i problemi delle versioni precedenti.

| di serie | Funzionalità | Problema | Soluzione alternativa/commenti |
| --- | --- | --- | --- |
| **1.** |Aggiornamenti |È impossibile aggiornare gli array virtuali creati nella versione di anteprima in una versione supportata di disponibilità generale. |Questi array virtuali devono essere sottoposti a failover per la versione di disponibilità generale tramite un flusso di lavoro di ripristino di emergenza. |
| **2.** |Disco dati sottoposto a provisioning |Dopo il provisioning di un disco dati di una determinata dimensione specificata e la creazione di un array virtuale StorSimple corrispondente, non si deve espandere o compattare il disco dati. Il tentativo di eseguire questa operazione comporta la perdita di tutti i dati nei livelli locali del dispositivo. | |
| **3.** |Criteri di gruppo |Nei dispositivi aggiunti a un dominio, l'applicazione di criteri di gruppo può influire negativamente sul funzionamento dei dispositivi. |Assicurarsi che l'array virtuale sia nella propria unità organizzativa (OU) per Active Directory e che ad esso non siano applicati oggetti Criteri di gruppo (GPO). |
| **4.** |Interfaccia utente Web locale |Se sono abilitate le funzionalità di sicurezza avanzate di Internet Explorer, alcune pagine dell'interfaccia utente Web locale come Risoluzione dei problemi o Manutenzione potrebbero non funzionare correttamente. Anche i pulsanti di queste pagine potrebbero non funzionare. |Disattivare le funzionalità di protezione avanzata di Internet Explorer. |
| **5.** |Interfaccia utente Web locale |In una macchina virtuale Hyper-V, le interfacce di rete nell'interfaccia utente Web vengono visualizzate come interfacce da 10 Gbps. |Questo comportamento è una reflection di Hyper-V. Hyper-V visualizza sempre 10 Gbps per le schede di rete virtuale. |
| **6.** |Volumi o condivisioni a livelli |Il blocco dell'intervallo di byte per le applicazioni che operano con i volumi a livelli di StorSimple non è supportato. Se il blocco dell'intervallo di byte è abilitato, la suddivisione in livelli di StorSimple non funziona. |Tra le misure consigliate:  <br></br>Disattivare il blocco dell'intervallo di byte nella logica dell'applicazione.<br></br>Scegliere di inserire dati per questa applicazione in volumi aggiunti in locale invece di volumi a più livelli.<br></br>*Avvertenza*: quando si usano volumi aggiunti in locale e il blocco dell'intervallo di byte è abilitato, il volume aggiunto in locale può risultare online ancora prima del completamento del ripristino. In questi casi, se è in corso un ripristino, è necessario attenderne il completamento. |
| **7.** |Condivisioni a livelli |Lavorare con file di grandi dimensioni può comportare una suddivisione in livelli lenta. |Durante l'uso di file di grandi dimensioni, è consigliabile che il file più grande sia più piccolo del 3% rispetto alle dimensioni della condivisione. |
| **8.** |Capacità usata per le condivisioni |Anche in assenza di dati nelle condivisioni, è possibile riscontrarne il consumo. Questo consumo si verifica perché la capacità usata per le condivisioni include i metadati. | |
| **9.** |Ripristino di emergenza |È possibile eseguire solo il ripristino di emergenza di un file server nello stesso dominio del dispositivo di origine. Il ripristino di emergenza su un dispositivo di destinazione in un dominio diverso non è supportato in questa versione. |L'implementazione è prevista per una versione futura. Per altre informazioni, vedere [Ripristino di emergenza e failover del dispositivo per l'array virtuale StorSimple tramite il portale di Azure](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Gli array virtuali StorSimple non possono essere gestiti tramite Azure PowerShell in questa versione. |La gestione dei dispositivi virtuali deve essere eseguita tramite il portale di Azure e l'interfaccia utente Web locale. |
| **11.** |Modifica della password |La console del dispositivo array virtuale accetta solo input nel formato della tastiera inglese (Stati Uniti). | |
| **12.** |CHAP |Le credenziali di autenticazione CHAP create non possono essere rimosse. Se si modificano le credenziali di autenticazione CHAP, per rendere effettive le modifiche è necessario portare i volumi offline e quindi riportarli online. |La risoluzione del problema è prevista per una versione futura. |
| **13.** |Server iSCSI |Le risorse di archiviazione usate visualizzate per un volume iSCSI possono essere diverse nel servizio Gestione dispositivi StorSimple e nell'host iSCSI. |L'host iSCSI ha la visualizzazione del file system.<br></br>Il dispositivo vede i blocchi allocati quando il volume aveva raggiunto le dimensioni massime. |
| **14.** |File server |Se un file contenuto in una cartella è associato a un flusso di dati alternativi (ADS), sul flusso non viene eseguito il backup o il ripristino tramite ripristino di emergenza, clonazione e ripristino a livello di elemento. | |
| **15.** |File server |I collegamenti simbolici non sono supportati. | |
| **16.** |File server |I file protetti da Windows Encrypting File System (EFS), quando vengono copiati o archiviati in un file server StorSimple Virtual Array, restituiscono una configurazione non supportata.  | |
| **17.** |Aggiornamenti |Se viene visualizzato il codice di errore: 2359302 (hex 0x240006) quando si tenta di installare un aggiornamento rapido tramite l'interfaccia utente locale, vuol dire che l'aggiornamento rapido è già installato nel dispositivo.   | |
| **18.** |Aggiornamenti |Se si usa l'interfaccia utente Web locale per installare l'aggiornamento 1 nell'array virtuale, verificare che si stia eseguendo l'aggiornamento 0.6. Se si esegue una versione precedente all'aggiornamento 0.6, installare prima l'aggiornamento 0.6 e quindi applicare l'aggiornamento 1. Se si installa direttamente l'aggiornamento 1.0 da una versione precedente all'aggiornamento 0.6, alcuni aggiornamenti non verranno eseguiti e i grafici di monitoraggio non funzioneranno.   | |


## <a name="next-steps"></a>Passaggi successivi
[Installare l'aggiornamento 1.0](storsimple-virtual-array-install-update-1.md) sull'array virtuale StorSimple.

## <a name="references"></a>Riferimenti
Si desidera consultare le note su una versione precedente? Passare a:
*  [Note sulla versione dell'aggiornamento 0.6 per l'array virtuale StorSimple](storsimple-virtual-array-update-06-release-notes.md)
* [Note sulla versione dell'aggiornamento 0.5 per l'array virtuale StorSimple](storsimple-virtual-array-update-05-release-notes.md)
* [Note sulla versione dell'aggiornamento 0.4 per l'array virtuale StorSimple](storsimple-virtual-array-update-04-release-notes.md)
* [Note sulla versione dell'aggiornamento 0.3 per l'array virtuale StorSimple](storsimple-ova-update-03-release-notes.md)
* [Note sulla versione dell'Aggiornamento 0.1 e 0.2 per l'array virtuale StorSimple](storsimple-ova-update-01-release-notes.md)
* [Note sulla versione con disponibilità generale dell'array virtuale StorSimple](storsimple-ova-pp-release-notes.md)
