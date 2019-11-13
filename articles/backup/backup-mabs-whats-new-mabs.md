---
title: Novità del server di Backup di Microsoft Azure
description: Il server di Backup di Microsoft Azure offre funzionalità avanzate di backup per la protezione di macchine virtuali, file e cartelle, carichi di lavoro e altro ancora. Informazioni su come installare o aggiornare il server di Backup di Azure V3.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dacurwin
ms.openlocfilehash: 403585a198b2569b700ef304e83b45b0a1138db5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012809"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Novità del server di Backup di Microsoft Azure

Backup di Microsoft Azure Server versione 3 (MAB V3) è l'aggiornamento più recente e include correzioni di bug critiche, supporto di Windows Server 2019, supporto di SQL 2017 e altre funzionalità e miglioramenti. Per visualizzare l'elenco dei bug corretti e le istruzioni di installazione di MABS V3, vedere l'articolo della knowledge base [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3).

In MABS V3 sono incluse le funzionalità seguenti:

## <a name="volume-to-volume-migration"></a>Migrazione da volume a volume

Con Modern Backup Storage (MBS) in MABS V2, è stata annunciata l'archiviazione con riconoscimento del carico di lavoro, in cui è possibile configurare alcuni carichi di lavoro per il backup in specifiche risorse di archiviazione, in base alle proprietà di archiviazione. Dopo la configurazione, tuttavia, può risultare necessario spostare i backup di alcune origini dati in altre risorse di archiviazione per un utilizzo ottimizzato delle risorse. MAB V3 offre la possibilità di eseguire la migrazione dei backup e configurarli in modo che vengano archiviati in un volume diverso in [tre passaggi](https://blogs.technet.microsoft.com/dpm/2017/10/24/storage-migration-with-dpm-2016-mbs/).

## <a name="prevent-unexpected-data-loss"></a>Evitare perdite di dati impreviste

Nelle aziende, MABS viene gestito da un team di amministratori. Sebbene esistano linee guida sull'archiviazione che devono essere seguite per l'esecuzione dei backup, specificare un volume non corretto come archivio di backup di MABS può causare la perdita di dati di importanza critica. Con MABS V3, è possibile evitare tali scenari configurando questi volumi come non disponibili per l'archiviazione usando [questi cmdlet di PowerShell](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

## <a name="custom-size-allocation"></a>Allocazione personalizzata delle dimensioni

Modern Backup Storage (MBS) utilizza l'archiviazione in modo mirato, in base a specifiche esigenze. A tale scopo, MABS calcola le dimensioni dei dati sottoposti a backup quando viene configurato per la protezione. Se, tuttavia, molti file e cartelle vengono sottoposti a backup contemporaneamente, come nel caso di un file server, il calcolo delle dimensioni può richiedere molto tempo. Con MABS V3, è possibile configurare MABS in modo che accetti le dimensioni del volume predefinite, anziché calcolare le dimensioni di ogni file, con un conseguente risparmio di tempo.

## <a name="optimized-cc-for-rct-vms"></a>CC ottimizzati per le macchine virtuali RCT

MABS usa RCT (la funzionalità di rilevamento delle modifiche nativa in Hyper-V), che riduce la necessità di verifiche della coerenza molto dispendiose in termini di tempo in scenari come arresti anomali della macchina virtuale. RCT offre una migliore resilienza rispetto al rilevamento delle modifiche offerto dai backup basati su snapshot di VSS. MABS V3 ottimizza ulteriormente il consumo di archiviazione e rete trasferendo solo i dati modificati durante le verifiche della coerenza.

## <a name="support-to-tls-12"></a>Support per TLS 1.2

TLS 1.2 è la modalità di comunicazione più sicura suggerita da Microsoft con il miglior livello di crittografia. MABS ora supporta la comunicazione TLS 1.2 tra MABS e i server protetti, per l'autenticazione basata su certificati e per i backup nel cloud.

## <a name="vmware-vm-protection-support"></a>Supporto della protezione di VM VMware

Il backup di VM VMware è ora supportato per le distribuzioni di produzione. MABS V3 offre quanto segue per la protezione di VM VMware:

- Supporto per vCenter ed ESXi 6.5, insieme al supporto per 5.5 e 6.0.
- Protezione automatica delle macchine virtuali VMware nel cloud. Se vengono aggiunte nuove macchine virtuali VMware a una cartella protetta, sono automaticamente protette su disco e cloud.
- Miglioramenti dell'efficienza per il ripristino del percorso alternativo di VMware.

## <a name="sql-2017-support"></a>Supporto per SQL 2017

MABS V3 può essere installato con SQL 2017 come database di MABS. È possibile eseguire l'aggiornamento di SQL Server da SQL 2016 a SQL 2017 o una nuova installazione. Con MABS V3 è anche possibile eseguire il backup dei carichi di lavoro di SQL 2017 in ambienti cluster e non cluster.

## <a name="windows-server-2019-support"></a>Supporto per Windows Server 2019

MABS V3 può essere installato in Windows Server 2019. Per usare MABS V3 con WS2019, è possibile aggiornare il sistema operativo a WS2019 prima di installare/aggiornare MABS V3 oppure è possibile eseguire l'aggiornamento del sistema operativo successivamente all'installazione/aggiornamento di V3 su WS2016.

MABS V3 è una versione completa e può essere installato direttamente in Windows Server 2016, Windows Server 2019 o può essere aggiornato da MABS V2. Prima di eseguire l'aggiornamento o di installare il server di Backup V3, leggere i prerequisiti di installazione.
In [questo articolo](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package) sono disponibili altre informazioni sulla procedura di installazione/aggiornamento per MABS.

> [!NOTE]
>
> MABS ha la stessa base di codice di System Center Data Protection Manager. MABS V3 corrisponde a Data Protection Manager 1807.

## <a name="next-steps"></a>Passaggi successivi

Leggere le informazioni su come preparare il server o iniziare a proteggere un carico di lavoro:

- [Problemi noti in MABS V3](backup-mabs-release-notes-v3.md)
- [Preparare i carichi di lavoro del server di backup](backup-azure-microsoft-azure-backup.md)
- [Usare il server di backup per eseguire il backup di un server VMware](backup-azure-backup-server-vmware.md)
- [Usare il server di backup per eseguire il backup di SQL Server](backup-azure-sql-mabs.md)
- [Usare Modern Backup Storage con il server di backup](backup-mabs-add-storage.md)
