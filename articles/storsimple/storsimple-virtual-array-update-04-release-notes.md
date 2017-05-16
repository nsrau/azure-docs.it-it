---
title: Note sulla versione dell&quot;aggiornamento 0.4 per l&quot;array virtuale StorSimple | Documentazione Microsoft
description: Vengono illustrati i principali problemi in sospeso e le soluzioni per l&quot;array virtuale StorSimple che esegue l&quot;aggiornamento 0.4.
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
ms.date: 04/05/2017
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: cc2b025b7f3e28954c7f95409ffab03e5cbcf13d
ms.lasthandoff: 04/06/2017


---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>Note sulla versione dell'aggiornamento 0.4 per l'array virtuale StorSimple

## <a name="overview"></a>Panoramica

Le seguenti note sulla versione identificano i problemi critici aperti e risolti per lgi aggiornamenti dell'array virtuale di Microsoft Azure StorSimple.

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire l'array virtuale StorSimple, esaminare attentamente le informazioni contenute nelle note sulla versione.

L'Aggiornamento 0.4 corrisponde alla versione del software **10.0.10289.0**.

> [!NOTE]
> Gli aggiornamenti comportano il riavvio del dispositivo. Se sono in corso operazioni di I/O, il dispositivo va incontro a tempi di inattività.


## <a name="whats-new-in-the-update-04"></a>Novità dell'aggiornamento 0.4
L'aggiornamento 0.4 è principalmente una build di correzioni di bug a cui sono stati aggiunti alcuni miglioramenti. In questa versione sono stati risolti diversi bug che nella versione precedente causavano errori di backup. I miglioramenti e le correzioni di bug principali sono i seguenti:

- **Miglioramenti alle prestazioni del backup**: questa versione ha apportato diversi miglioramenti chiave per aumentare le prestazioni del backup. Di conseguenza, i backup che coinvolgono un numero elevato di file vengono completati in molto meno tempo, ad esempio i backup completi e incrementali.

- **Prestazioni del ripristino migliorate**: questa versione contiene miglioramenti che aumentano considerevolmente le prestazioni del ripristino quando si usa un numero elevato di file. Se si usano da 2 a 4 milioni di file, è consigliabile effettuare il provisioning di un array virtuale con 16 GB di RAM per osservare i miglioramenti. Quando si usano meno di 2 milioni di file, il requisito minimo per la macchina virtuale continua a essere una RAM da 8 GB.

- **Miglioramenti al pacchetto per il supporto**: i miglioramenti includono la registrazione delle statistiche per disco, CPU, memoria, rete e cloud nel pacchetto di supporto, ottimizzando quindi il processo di diagnosi/debug dei problemi dei dispositivi.

- **Limitazione dei volumi iSCSI aggiunti in locale a 200 GB**: per i volumi aggiunti in locale, è consigliabile limitarsi a un volume iSCSI da 200 GB nell'array virtuale StorSimple. La prenotazione locale per i volumi a livelli continua a essere il 10 % delle dimensioni del volume di cui è stato effettuato il provisioning, ma prevede un limite di 200 GB. 

- **Correzioni di bug correlate al backup**: nelle versioni precedenti del software, sono presenti problemi relativi ai backup che causano errori di backup. I bug sono stati risolti in questa versione.


## <a name="issues-fixed-in-the-update-04"></a>Problemi risolti nell'aggiornamento 0.4

La tabella seguente fornisce un riepilogo dei problemi risolti in questa versione.

| No. | Funzionalità | Problema |
| --- | --- | --- |
| 1 |Prestazioni del backup|Nelle versioni precedenti il completamento dei backup che coinvolgono un numero elevato di file richiede molto tempo (nell'ordine di giorni). In questa versione il tempo necessario per completare sia i backup completi che quelli incrementali è considerevolmente ridotto. |
| 2 |Pacchetto di supporto|Le statistiche relative a disco, CPU, memoria, rete e cloud vengono ora registrate nei log di supporto, facendo i pacchetti per il supporto uno strumento molto efficace per la risoluzione dei problemi dei dispositivi.|
| 3 |Backup |Nelle versioni precedenti i backup a esecuzione prolungata possono ridurre lo spazio sul dispositivo causando errori di backup. Il bug è stato risolto in questa versione consentendo l'accodamento di non più di 5 backup contemporaneamente.|
| 4 |iSCSI | Nelle versioni precedenti la prenotazione locale per i volumi a livelli o aggiunti in locale è il 10% delle dimensioni del volume di cui è stato effettuato il provisioning. In questa versione la prenotazione locale per tutti i volumi iSCSI (aggiunti in locale o a livelli) è limitata al 10 % con un massimo di 200 GB (per i volumi a livelli superiori a 2 TB), liberando quindi più spazio sul dico locale. È consigliabile limitare i volumi aggiunti in locale a 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Problemi noti nell'aggiornamento 0.4

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
| **8.** |Capacità usata per le condivisioni |Anche in assenza di dati nelle condivisioni, è possibile riscontrarne il consumo. Questo accade perché la capacità usata per le condivisioni include i metadati. | |
| **9.** |Ripristino di emergenza |È possibile eseguire solo il ripristino di emergenza di un file server nello stesso dominio del dispositivo di origine. Il ripristino di emergenza su un dispositivo di destinazione in un dominio diverso non è supportato in questa versione. |L'implementazione è prevista per una versione futura. |
| **10.** |Azure PowerShell |I dispositivi virtuali StorSimple non possono essere gestiti tramite Azure PowerShell in questa versione. |La gestione dei dispositivi virtuali deve essere eseguita tramite il portale di Azure classico e l'interfaccia utente Web locale. |
| **11.** |Modifica della password |La console del dispositivo array virtuale accetta solo input nel formato della tastiera inglese (Stati Uniti). | |
| **12.** |CHAP |Le credenziali di autenticazione CHAP create non possono essere rimosse. Se si modificano le credenziali di autenticazione CHAP, per rendere effettive le modifiche è necessario portare i volumi offline e quindi riportarli online. |La risoluzione del problema è prevista per una versione futura. |
| **13.** |Server iSCSI |Le risorse di archiviazione usate visualizzate per un volume iSCSI possono essere diverse nel servizio StorSimple Manager e nell'host iSCSI. |L'host iSCSI ha la visualizzazione del file system.<br></br>Il dispositivo vede i blocchi allocati quando il volume aveva raggiunto le dimensioni massime. |
| **14.** |File server |Se un file contenuto in una cartella è associato a un flusso di dati alternativi (ADS), sul flusso non viene eseguito il backup o il ripristino tramite ripristino di emergenza, clonazione e ripristino a livello di elemento. | |
| **15.** |File server |I collegamenti simbolici non sono supportati. | |
| **16.** |File server |I file protetti da Windows Encrypting File System (EFS), quando vengono copiati o archiviati in un file server StorSimple Virtual Array, restituiscono una configurazione non supportata.  | |

## <a name="next-step"></a>Passaggio successivo
[Installare l'aggiornamento 0.4](storsimple-virtual-array-install-update-04.md) nell'array virtuale StorSimple.

## <a name="references"></a>Riferimenti
Si desidera consultare le note su una versione precedente? Passare a: 

* [Note sulla versione dell'aggiornamento 0.3 per l'array virtuale StorSimple](storsimple-ova-update-03-release-notes.md)
* [Note sulla versione dell'Aggiornamento 0.1 e 0.2 per l'array virtuale StorSimple](storsimple-ova-update-01-release-notes.md)
* [Note sulla versione con disponibilità generale dell'array virtuale StorSimple](storsimple-ova-pp-release-notes.md)


