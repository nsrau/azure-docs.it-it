---
title: Note sulla versione dell'aggiornamento 5 di StorSimple serie 8000 | Microsoft Docs
description: "Vengono descritte le nuove funzionalità, i problemi e le soluzioni alternative dell'aggiornamento 5 per StorSimple serie 8000."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 672757e82bcf645b705f46a9975e09c9dc5eef92
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2017
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>Note sulla versione dell'aggiornamento 5 di StorSimple serie 8000

## <a name="overview"></a>Panoramica

Le note sulla versione seguenti descrivono le nuove funzionalità e illustrano i problemi critici non risolti relativi all'aggiornamento 5 di StorSimple serie 8000. Contengono inoltre un elenco degli aggiornamenti software di StorSimple inclusi in questa versione.

L'aggiornamento 5 può essere applicato a qualsiasi dispositivo StorSimple che esegue una versione di aggiornamento compresa tra 0.1 e 4. La versione del dispositivo associata all'aggiornamento 5 è 6.3.9600.17845.

Verificare le informazioni contenute nelle note sulla versione prima di distribuire l'aggiornamento nella soluzione StorSimple.

> [!IMPORTANT]
> * L'aggiornamento 5 è un aggiornamento obbligatorio e deve essere installato immediatamente. Per altre informazioni, vedere come [applicare l'aggiornamento 5](storsimple-8000-install-update-5.md).
> * L'aggiornamento 5 include software per dispositivi, firmware del disco, aggiornamenti di sicurezza del sistema operativo e altri aggiornamenti di sicurezza. L'installazione dell'aggiornamento richiede circa 4 ore. L'aggiornamento firmware del disco è problematico e comporta un tempo di inattività per il dispositivo. È consigliabile applicare l'aggiornamento 5 per mantenere aggiornato il dispositivo.
> * Per le nuove versioni è possibile che gli aggiornamenti non siano immediatamente visibili perché viene effettuata un'implementazione graduale degli aggiornamenti. Attendere alcuni giorni e provare a cercare nuovamente gli aggiornamenti, perché verranno presto resi disponibili.

## <a name="whats-new-in-update-5"></a>Novità dell'aggiornamento 5

L'aggiornamento 5 include i miglioramenti essenziali e le correzioni di bug descritti di seguito.

* **Utilizzo di Azure Active Directory (AAD) per l'autenticazione con il servizio Gestione dispositivi StorSimple** - A partire dall'aggiornamento 5, viene usato Azure Active Directory per l'autenticazione con il servizio Gestione dispositivi StorSimple. Il meccanismo di autenticazione precedente verrà deprecato entro dicembre 2017. Tutti gli utenti devono includere i nuovi URL di autenticazione nelle rispettive regole del firewall. Per altre informazioni, vedere gli [URL di autenticazione elencati nei requisiti di rete per il dispositivo StorSimple](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Se l'URL di autenticazione non viene incluso nelle regole del firewall, verrà visualizzato un avviso critico per informare l'utente che non è stato possibile autenticare il dispositivo StorSimple con il servizio. Se viene visualizzato questo avviso, è necessario includere il nuovo URL di autenticazione. Per altre informazioni, vedere [StorSimple networking alerts](storsimple-8000-manage-alerts.md#networking-alerts) (Avvisi di rete di StorSimple).

* **Nuova versione di StorSimple Snapshot Manager** -una nuova versione di StorSimple Snapshot Manager viene rilasciata con aggiornamento 5 ed è compatibile con tutti i dispositivi StorSimple che eseguono l'aggiornamento 4 o versione successiva. È consigliabile eseguire l'aggiornamento a questa versione, La versione precedente di StorSimple Snapshot Manager viene usata per i dispositivi StorSimple che eseguono l'aggiornamento 3 o versioni precedenti. [Scaricare la versione appropriata di StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) e fare riferimento a [distribuire StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problemi risolti nell'aggiornamento 5

La seguente tabella fornisce un riepilogo dei problemi risolti nell'aggiornamento 5.

| No | Funzionalità | Problema | Si applica a un dispositivo fisico | Si applica a un dispositivo virtuale |
| --- | --- | --- | --- | --- |
| 1 |Comunicazione remota di Windows PowerShell |Nella versione precedente, gli utenti ricevevano un errore se provavano a stabilire una connessione remota all'appliance cloud StorSimple tramite Windows PowerShell. In questa versione il problema è stato corretto una volta individuata la causa radice. |No |Sì |
| 2 |Modelli di larghezza di banda |Nella versione precedente, un problema con i modelli di larghezza di banda determinava una larghezza di banda inferiore rispetto a quella per cui era stato configurato il dispositivo. Questo problema è stato risolto in questa versione. |Sì |Sì |
| 3 |Failover |Nella versione precedente, quando veniva eseguito il failover di un dispositivo con un numero elevato di volumi su un altro dispositivo che eseguiva l'aggiornamento 4, il processo si interrompeva mentre provava ad applicare i record di controllo di accesso. Tale problema è stato corretto in questa versione. |Sì |Sì |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Problemi noti nell'aggiornamento 5 rispetto alle versioni precedenti

Nessun nuovo problema noto nell'aggiornamento 5. Per un elenco di problemi ancora presenti nell'aggiornamento 5 rispetto alle versioni precedenti, vedere le [note sulla versione dell'aggiornamento 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Aggiornamenti firmware e controller SAS (Serial-attached SCSI) presenti nell'aggiornamento 5

Questa versione dispone degli aggiornamenti del controller SAS e del firmware e driver LSI. Per altre informazioni su come installare gli aggiornamenti, vedere l'articolo sull'[installazione dell'aggiornamento 5](storsimple-8000-install-update-5.md) nel dispositivo StorSimple.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Aggiornamenti all'appliance cloud StorSimple presenti nell'aggiornamento 5

Questo aggiornamento non può essere applicato all'appliance cloud StorSimple (ovvero il dispositivo virtuale). Le nuove appliance cloud devono essere create usando l'immagine dell'aggiornamento 5. Per informazioni su come creare un'appliance cloud StorSimple, vedere [Distribuire e gestire un'appliance cloud StorSimple in Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Passaggio successivo

Informazioni su come [installare l'aggiornamento 5](storsimple-8000-install-update-5.md) nel dispositivo StorSimple.

