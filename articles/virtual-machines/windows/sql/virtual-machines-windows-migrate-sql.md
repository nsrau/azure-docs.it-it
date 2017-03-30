---
title: Eseguire la migrazione di un database di SQL Server a SQL Server in una macchina virtuale di Azure | Microsoft Docs
description: Leggere le informazioni su come eseguire la migrazione di un database utente locale a SQL Server in una macchina virtuale di Azure.
services: virtual-machines-windows
documentationcenter: 
author: sabotta
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: carlasab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 789e1eabcd284c17c5728156cf185d2ca168f0eb
ms.lasthandoff: 03/25/2017


---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Eseguire la migrazione di un database di SQL Server a SQL Server in una macchina virtuale di Azure

Esistono diversi metodi per eseguire la migrazione di un database utente di SQL Server locale a SQL Server in una macchina virtuale di Azure. Questo articolo illustra brevemente vari metodi e consiglia quello più adatto ai diversi scenari.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="what-are-the-primary-migration-methods"></a>Quali sono i metodi di migrazione principali?
I metodi di migrazione principali sono:

* Esecuzione del backup locale tramite la compressione e copia manuale del file di backup nella macchina virtuale di Azure
* Esecuzione di un backup nell’URL e ripristino nella macchina virtuale di Azure dall'URL
* Scollegamento e successiva copia dei dati e dei file di log sull’archivio BLOB di Azure, quindi collegamento a SQL Server nella macchina virtuale di Azure dall'URL
* Conversione della macchina fisica locale a disco rigido virtuale Hyper-V, caricamento sull'archivio BLOB di Azure e successiva distribuzione come nuova macchina virtuale tramite il disco rigido virtuale caricato
* Spedizione del disco rigido tramite il servizio di Importazione/Esportazione di Windows
* Se si ha una distribuzione locale di AlwaysOn, uso dell' [aggiunta guidata di una replica di Azure](../classic/sql-onprem-availability.md) per creare una replica in Azure e quindi eseguire il failover, indirizzando gli utenti all'istanza del database di Azure
* Uso della [replica transazionale](https://msdn.microsoft.com/library/ms151176.aspx) di SQL Server per configurare l'istanza del server SQL di Azure come server di sottoscrizione e quindi disabilitare la replica, indirizzando gli utenti all'istanza del database di Azure

> [!TIP]
> È possibile anche usare queste tecniche per spostare database tra diverse macchine virtuali di SQL Server in Azure. Ad esempio, non esiste un modo supportato per aggiornare una macchina virtuale immagine della raccolta di SQL Server da una versione/edizione a un'altra. In questo caso si deve creare una nuova macchina virtuale di SQL Server con la nuova versione/edizione e usare una delle tecniche di migrazione descritte in questo articolo per spostare i database. 

## <a name="choosing-your-migration-method"></a>Scelta del metodo di migrazione
Per prestazioni ottimali di trasferimento dei dati, eseguire la migrazione dei file del database alla macchina virtuale di Azure usando un file di backup compresso.

Per ridurre al minimo il tempo di inattività durante il processo di migrazione del database, usare l'opzione AlwaysOn o la replica transazionale.

Se non è possibile usare i metodi sopra indicati, eseguire manualmente la migrazione del database. Se si adotta questo metodo, per prima cosa si esegue il backup del database, quindi si copia il backup del database in Azure e infine si esegue un ripristino del database. In alternativa, è possibile copiare i file del database in Azure e collegarli. Esistono diversi metodi che consentono di eseguire questo processo di migrazione manuale di un database in una macchina virtuale di Azure.

> [!NOTE]
> Durante l'aggiornamento a SQL Server 2014 o SQL Server 2016 da versioni precedenti di SQL Server, è necessario considerare se sono necessarie modifiche. È consigliabile prendere in considerazione tutte le dipendenze sulle funzionalità non supportate dalla nuova versione di SQL Server come parte del progetto di migrazione. Per altre informazioni sulle edizioni e sugli scenari supportati, vedere [Aggiornamento a SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

Nella tabella seguente sono elencati tutti i principali metodi di migrazione e viene illustrato quando l'utilizzo di ciascun metodo è più appropriato.

| Metodo | Versione del database di origine | Versione del database di destinazione | Vincolo di dimensioni del backup del database di origine | Note |
| --- | --- | --- | --- | --- |
| [Esecuzione del backup locale tramite la compressione e copia manuale del file di backup nella macchina virtuale di Azure](#backup-to-file-and-copy-to-vm-and-restore) |SQL Server 2005 o versione successiva |SQL Server 2005 o versione successiva |[Limite di archiviazione della macchina virtuale di Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | È una tecnica molto semplice e ben collaudata per spostare i database tra più computer. |
| [Esecuzione di un backup nell’URL e ripristino nella macchina virtuale di Azure dall'URL](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 o versione successiva |SQL Server 2012 SP1 CU2 o versione successiva |< 12.8 TB per SQL Server 2016, in caso contrario < 1 TB | Questo è un altro metodo per spostare il file di backup nella macchina virtuale usando l'archiviazione di Azure. |
| [Scollegamento e successiva copia dei dati e dei file di log sull’archivio BLOB di Azure, quindi collegamento a SQL Server nella macchina virtuale di Azure dall'URL](#detach-and-copy-to-url-and-attach-from-url) |SQL Server 2005 o versione successiva |SQL Server 2014 o versione successiva |[Limite di archiviazione della macchina virtuale di Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Usare questo metodo quando si prevede di [archiviare i file tramite il servizio di archiviazione BLOB di Azure](https://msdn.microsoft.com/library/dn385720.aspx) e di collegarli a SQL Server in esecuzione su una macchina virtuale di Azure, in particolare con database di grandi dimensioni. |
| [Conversione della macchina locale a dischi rigidi virtuali Hyper-V, caricamento sull'archivio BLOB di Azure e successiva distribuzione di una nuova macchina virtuale tramite il disco rigido virtuale caricato](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 o versione successiva |SQL Server 2005 o versione successiva |[Limite di archiviazione della macchina virtuale di Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Usare per la [propria licenza di SQL Server](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md), per la migrazione di un database che verrà eseguito su una versione precedente di SQL Server o per la migrazione combinata dei database di sistema e utente nell'ambito della migrazione di database dipendenti da altri database utente e/o di sistema. |
| [Spedizione del disco rigido tramite il servizio di Importazione/Esportazione di Windows](#ship-hard-drive) |SQL Server 2005 o versione successiva |SQL Server 2005 o versione successiva |[Limite di archiviazione della macchina virtuale di Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Usare il [servizio di importazione/esportazione di Windows](../../../storage/storage-import-export-service.md) quando il metodo della copia manuale è troppo lento, ad esempio con database di grandi dimensioni |
| [Uso della procedura guidata per l'aggiunta della replica di Azure](../classic/sql-onprem-availability.md) |SQL Server 2012 o versione successiva |SQL Server 2012 o versione successiva |[Limite di archiviazione della macchina virtuale di Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Riduce al minimo il tempo di inattività; da usare quando si ha una distribuzione locale di AlwaysOn |
| [Uso della replica transazionale di SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 o versione successiva |SQL Server 2005 o versione successiva |[Limite di archiviazione della macchina virtuale di Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Da usare quando è necessario ridurre al minimo il tempo di inattività e si ha una distribuzione locale di AlwaysOn |

## <a name="backup-and-restore"></a>Backup e ripristino
Eseguire il backup del database con la compressione, copiare il backup nella macchina virtuale e ripristinare il database. Se il file di backup è superiore a 1 TB, è necessario eseguirne lo striping perché le dimensioni massime del disco di una macchina virtuale sono pari a 1 TB. Per eseguire la migrazione di un database utente tramite il metodo manuale, attenersi ai passaggi generali seguenti:

1. Eseguire un backup completo del database su una posizione locale.
2. Creare o caricare una macchina virtuale con la versione di SQL Server desiderata.
3. Configurare la connettività in base ai requisiti specifici. Vedere [Connettersi a una macchina virtuale di SQL Server in Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
4. Copiare i file di backup sulla macchina virtuale utilizzando il desktop remoto, Esplora risorse o il comando di copia da un prompt dei comandi.

## <a name="backup-to-url-and-restore"></a>Backup nell’URL e ripristino
Invece di eseguire il backup in un file locale è possibile usare il [backup nell'URL](https://msdn.microsoft.com/library/dn435916.aspx) e quindi eseguire il ripristino dall'URL alla macchina virtuale. Con SQL Server 2016, i set di backup con striping sono supportati, sono consigliati per le prestazioni e necessari per superare i limiti di dimensione per BLOB. Per i database di dimensioni molto grandi è consigliabile usare il [servizio di importazione/esportazione di Windows](../../../storage/storage-import-export-service.md) .

## <a name="detach-and-attach-from-url"></a>Rimuovere e allegare dall'URL
Rimuovere il database e i file di log e trasferirli nell'[archivio BLOB di Azure](https://msdn.microsoft.com/library/dn385720.aspx). Allegare il database dall'URL nella macchina virtuale di Azure. Usare questa opzione se si desidera che i file del database fisico risiedano nell'archivio BLOB. Ciò può risultare utile per i database di dimensioni molto grandi. Per eseguire la migrazione di un database utente tramite il metodo manuale, attenersi ai passaggi generali seguenti:

1. Scollegare i file del database dall'istanza del database locale.
2. Copiare i file del database scollegati nell'archivio BLOB di Azure usando l' [utilità della riga di comando AZCopy](../../../storage/storage-use-azcopy.md).
3. Collegare i file del database dall'URL di Azure all'istanza di SQL Server nella macchina virtuale di Azure.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Conversione alla macchina virtuale, caricamento nell’URL e distribuzione come nuova macchina virtuale
Utilizzare questo metodo per eseguire la migrazione di tutti i database di sistema e utente in un'istanza di SQL Server locale alla macchina virtuale Azure. Per eseguire la migrazione di un'intera istanza di SQL Server utilizzando il metodo manuale, attenersi ai passaggi generali seguenti:

1. Convertire le macchine fisiche o virtuali in dischi rigidi virtuali Hyper-V usando [Microsoft Virtual Machine Converter](http://technet.microsoft.com/library/dn873998.aspx).
2. Caricare i file dei dischi rigidi virtuali su Archiviazione di Azure usando il [cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Distribuire una nuova macchina virtuale utilizzando il disco rigido virtuale caricato.

> [!NOTE]
> Per eseguire la migrazione di un'intera applicazione, è consigliabile usare [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md).

## <a name="ship-hard-drive"></a>Spedizione del disco rigido
Usare il [metodo del servizio di importazione/esportazione di Windows](../../../storage/storage-import-export-service.md) per trasferire grandi quantità di dati di file sull'archivio BLOB di Azure in situazioni in cui il caricamento in rete è eccessivamente costoso o non è possibile. Con questo servizio, è possibile inviare uno o più dischi rigidi contenenti tali dati a un datacenter di Azure, dove i dati verranno caricati sull'account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'esecuzione di SQL Server in Macchine virtuali di Azure, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

Per istruzioni sulla creazione di una macchina virtuale di SQL Server di Azure da un'immagine acquisita, vedere [Tips & Tricks on 'cloning' Azure SQL virtual machines from captured images](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) (Suggerimenti per clonare macchine virtuali di SQL Server di Azure da immagini acquisite) nel blog dei tecnici di SQL Server CSS.


