---
title: Note sulla versione degli aggiornamenti per l'array virtuale StorSimple | Microsoft Docs
description: Vengono illustrati i principali problemi in sospeso e le soluzioni per l'array virtuale StorSimple che esegue l'aggiornamento 0.3.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2016
ms.author: alkohli

---
# Note sulla versione dell'aggiornamento 0.3 per l'array virtuale StorSimple
## Overview
Le seguenti note sulla versione identificano i problemi critici aperti e risolti per lgi aggiornamenti dell'array virtuale di Microsoft Azure StorSimple.

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire l'array virtuale StorSimple, esaminare attentamente le informazioni contenute nelle note sulla versione.

L'Aggiornamento 0.3 corrisponde alla versione del software **10.0.10288.0**.

> [!NOTE]
> Gli aggiornamenti comportano il riavvio del dispositivo. Se sono in corso operazioni di I/O, il dispositivo va incontro a tempi di inattività.
> 
> 

## Novità dell'aggiornamento 0.3
L'aggiornamento 0.3 è principalmente una build per la correzione di bug. In questa versione sono stati risolti diversi bug che nella versione precedente causavano errori di backup.

## Problemi risolti nell'Aggiornamento 0.3
La tabella seguente fornisce un riepilogo dei problemi risolti in questa versione.

| No. | Funzionalità | Problema |
| --- | --- | --- |
| 1 |Backup |Nella versione precedente è stato riscontrato un problema che impediva il completamento dei backup per una condivisone file. Se si verifica questo problema, il processo di backup ha esito negativo e viene generato un avviso critico nel servizio StorSimple Manager per notificare l'evento all'utente. Questo problema non influisce sui dati delle condivisioni o sull'accesso ai dati. In questa versione il problema è stato risolto una volta individuata la causa radice. <br></br> La correzione non viene applicata in modo retroattivo alle condivisioni in cui il problema era già presente. I clienti che riscontrano questo problema devono innanzitutto applicare l'aggiornamento 0.3, quindi contattare il supporto Microsoft per eseguire un backup completo del sistema al fine di risolvere il problema. In alternativa, anziché rivolgersi al supporto Microsoft, i clienti possono anche ripristinare le condivisioni interessate dal problema usando il backup di una versione integra. |
| 2 |iSCSI |Nella versione precedente è stato individuato un problema in cui i volumi scomparivano al momento di copiare dati in un volume della matrice virtuale StorSimple. In questa versione il problema è stato corretto. <br></br> Le correzioni hanno effetto solo sui nuovi volumi. Le correzioni non vengono applicate in modo retroattivo ai volumi in cui il problema era già presente. I clienti sono invitati a portare online i volumi interessati tramite il portale di Azure classico, a eseguirne il backup e quindi a ripristinarli in volumi nuovi. |

## Problemi noti nell'aggiornamento 0.3
La tabella seguente fornisce un riepilogo dei problemi noti per l'array virtuale StorSimple e include i problemi delle versioni precedenti.

| No. | Funzionalità | Problema | Soluzione alternativa/commenti |
| --- | --- | --- | --- |
| **1.** |Aggiornamenti |È impossibile aggiornare i dispositivi virtuali creati nella versione di anteprima in una versione supportata di disponibilità generale. |Questi dispositivi virtuali devono essere sottoposti a failover per la versione di disponibilità generale tramite un flusso di lavoro di ripristino di emergenza. |
| **2.** |Disco dati sottoposto a provisioning |Dopo il provisioning di un disco dati di una determinata dimensione specificata e la creazione di un dispositivo virtuale StorSimple corrispondente, non si deve espandere o compattare il disco dati. Il tentativo di eseguire questa operazione comporta la perdita di tutti i dati nei livelli locali del dispositivo. | |
| **3.** |Criteri di gruppo |Nei dispositivi aggiunti a un dominio, l'applicazione di criteri di gruppo può influire negativamente sul funzionamento dei dispositivi. |Assicurarsi che l'array virtuale sia nella propria unità organizzativa (OU) per Active Directory e che ad esso non siano applicati oggetti Criteri di gruppo (GPO). |
| **4.** |Interfaccia utente Web locale |Se sono abilitate le funzionalità di sicurezza avanzate di Internet Explorer, alcune pagine dell'interfaccia utente Web locale come Risoluzione dei problemi o Manutenzione potrebbero non funzionare correttamente. Anche i pulsanti di queste pagine potrebbero non funzionare. |Disattivare le funzionalità di protezione avanzata di Internet Explorer. |
| **5.** |Interfaccia utente Web locale |In una macchina virtuale Hyper-V, le interfacce di rete nell'interfaccia utente Web vengono visualizzate come interfacce da 10 Gbps. |Questo comportamento è una reflection di Hyper-V. Hyper-V visualizza sempre 10 Gbps per le schede di rete virtuale. |
| **6.** |Volumi o condivisioni a livelli |Il blocco dell'intervallo di byte per le applicazioni che operano con i volumi a livelli di StorSimple non è supportato. Se il blocco dell'intervallo di byte è abilitato, la suddivisione in livelli di StorSimple non funziona. |Tra le misure consigliate: <br></br>Disabilitare il blocco dell'intervallo di byte nella logica dell'applicazione.<br></br>Scegliere di inserire i dati per l'applicazione in volumi aggiunti in locale anziché in volumi a livelli.<br></br>*Avvertenza*: quando si usano volumi aggiunti in locale e il blocco dell'intervallo di byte è abilitato, il volume aggiunto in locale può risultare online ancora prima del completamento del ripristino. In questi casi, se è in corso un ripristino, è necessario attenderne il completamento. |
| **7.** |Condivisioni a livelli |Lavorare con file di grandi dimensioni può comportare una suddivisione in livelli lenta. |Durante l'uso di file di grandi dimensioni, è consigliabile che il file più grande sia più piccolo del 3% rispetto alle dimensioni della condivisione. |
| **8.** |Capacità usata per le condivisioni |Anche in assenza di dati nelle condivisioni, è possibile riscontrarne il consumo. Questo accade perché la capacità usata per le condivisioni include i metadati. | |
| **9.** |Ripristino di emergenza |È possibile eseguire solo il ripristino di emergenza di un file server nello stesso dominio del dispositivo di origine. Il ripristino di emergenza su un dispositivo di destinazione in un dominio diverso non è supportato in questa versione. |L'implementazione è prevista per una versione futura. |
| **10.** |Azure PowerShell |I dispositivi virtuali StorSimple non possono essere gestiti tramite Azure PowerShell in questa versione. |La gestione dei dispositivi virtuali deve essere eseguita tramite il portale di Azure classico e l'interfaccia utente Web locale. |
| **11.** |Modifica della password |La console del dispositivo array virtuale accetta solo input nel formato della tastiera inglese (Stati Uniti). | |
| **12.** |CHAP |Le credenziali di autenticazione CHAP create non possono essere rimosse. Se si modificano le credenziali di autenticazione CHAP, per rendere effettive le modifiche è necessario portare i volumi offline e quindi riportarli online. |La risoluzione del problema è prevista per una versione futura. |
| **13.** |Server iSCSI |Le risorse di archiviazione usate visualizzate per un volume iSCSI possono essere diverse nel servizio StorSimple Manager e nell'host iSCSI. |L'host iSCSI ha la visualizzazione del file system.<br></br>Il dispositivo vede i blocchi allocati quando il volume aveva raggiunto le dimensioni massime. |
| **14.** |File server |Se un file contenuto in una cartella è associato a un flusso di dati alternativi (ADS), sul flusso non viene eseguito il backup o il ripristino tramite ripristino di emergenza, clonazione e ripristino a livello di elemento. | |

## Passaggio successivo
[Installare l'aggiornamento 0.3](storsimple-ova-install-update-01.md) sull'array virtuale StorSimple.

## Riferimenti
Si desidera consultare le note su una versione precedente? Passare a:

* [Note sulla versione dell'Aggiornamento 0.1 e 0.2 per l'array virtuale StorSimple](storsimple-ova-update-01-release-notes.md)
* [Note sulla versione con disponibilità generale dell'array virtuale StorSimple](storsimple-ova-pp-release-notes.md)

<!---HONumber=AcomDC_0921_2016-->