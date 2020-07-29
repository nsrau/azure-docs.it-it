---
title: Eseguire la migrazione di un database di SQL Server a SQL Server in una macchina virtuale | Microsoft Docs
description: Informazioni su come eseguire la migrazione di un database utente locale a SQL Server in una macchina virtuale di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.topic: article
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7da9b83001d50c8e9a03188ed8dbf1245189bc30
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668850"
---
# <a name="migrate-a-sql-server-database-to-sql-server-on-an-azure-virtual-machine"></a>Eseguire la migrazione di un database di SQL Server a SQL Server in una macchina virtuale di Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Esistono diversi modi di eseguire la migrazione di un database utente locale di SQL Server a SQL Server in una macchina virtuale di Azure. Questo articolo illustra brevemente vari metodi e consiglia quello più adatto ai diversi scenari.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 e SQL Server 2008 R2 si avvicinano alla [fine del ciclo di vita del supporto](https://www.microsoft.com/sql-server/sql-server-2008) per le istanze locali. Per estendere il supporto, è possibile eseguire la migrazione dell'istanza di SQL Server in una macchina virtuale di Azure oppure acquistare gli aggiornamenti della sicurezza estesi per mantenerla in locale. Per altre informazioni, vedere [Estendere il supporto per SQL Server 2008 e 2008 R2 con Azure](sql-server-2008-extend-end-of-support.md)

## <a name="what-are-the-primary-migration-methods"></a>Quali sono i metodi di migrazione principali?

I metodi di migrazione principali sono:

* Eseguire un backup locale usando la compressione e poi copiare manualmente il file di backup nella macchina virtuale di Azure.
* Eseguire un backup nell'URL e quindi ripristinare la macchina virtuale di Azure dall'URL.
* Scollegare i dati e i file di log, copiarli nell'archivio BLOB di Azure, quindi collegarli a SQL Server nella macchina virtuale di Azure dall'URL.
* Convertire la macchina fisica locale in un disco rigido virtuale Hyper-V, caricarla nell'archivio BLOB di Azure e poi distribuirla come nuova macchina virtuale usando il disco rigido virtuale caricato.
* Spedire il disco rigido usando il servizio di Importazione/Esportazione di Windows.
* Se si ha una distribuzione locale di Gruppi di disponibilità AlwaysOn, usare la [procedura guidata per l'aggiunta della replica di Azure](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) per creare una replica in Azure, eseguire il failover e indirizzare gli utenti all'istanza del database di Azure.
* Usare la [replica transazionale](https://msdn.microsoft.com/library/ms151176.aspx) di SQL Server per configurare l'istanza del server SQL di Azure come server di sottoscrizione, disabilitare la replica e indirizzare gli utenti all'istanza del database di Azure.

> [!TIP]
> È possibile anche usare queste tecniche per spostare database tra diverse macchine virtuali di SQL Server in Azure. Ad esempio, non esiste un modo supportato per aggiornare una macchina virtuale immagine della raccolta di SQL Server da una versione/edizione a un'altra. In questo caso si deve creare una nuova macchina virtuale di SQL Server con la nuova versione/edizione e usare una delle tecniche di migrazione descritte in questo articolo per spostare i database. 

## <a name="choose-a-migration-method"></a>Scegliere un metodo di migrazione

Per ottimizzare le prestazioni di trasferimento dei dati, eseguire la migrazione dei file di database nella macchina virtuale di Azure usando un file di backup compresso.

Per ridurre al minimo il tempo di inattività durante il processo di migrazione del database, usare l'opzione AlwaysOn o la replica transazionale.

Se non è possibile usare i metodi sopra indicati, eseguire manualmente la migrazione del database. In genere, si inizia con un backup del database, lo si segue con una copia del backup del database in Azure e quindi si ripristina il database. In alternativa, è possibile copiare i file del database in Azure e collegarli. Esistono diversi metodi che consentono di eseguire questo processo di migrazione manuale di un database in una macchina virtuale di Azure.

> [!NOTE]
> Durante l'aggiornamento a SQL Server 2014 o SQL Server 2016 da versioni precedenti di SQL Server, è necessario considerare se sono necessarie modifiche. È consigliabile prendere in considerazione tutte le dipendenze sulle funzionalità non supportate dalla nuova versione di SQL Server come parte del progetto di migrazione. Per altre informazioni sulle edizioni e sugli scenari supportati, vedere [Aggiornamento a SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

Nella tabella seguente sono elencati tutti i principali metodi di migrazione e viene illustrato quando l'utilizzo di ciascun metodo è più appropriato.

| Metodo | Versione del database di origine | Versione del database di destinazione | Vincolo di dimensioni del backup del database di origine | Note |
| --- | --- | --- | --- | --- |
| [Eseguire un backup locale usando la compressione e copiare manualmente il file di backup nella macchina virtuale di Azure](#back-up-and-restore) |SQL Server 2005 o versione successiva |SQL Server 2005 o versione successiva |[Limite di archiviazione della macchina virtuale di Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Questa tecnica è semplice e ben collaudata per lo stato di trasferimento dei database tra computer. |
| [Esecuzione di un backup nell’URL e ripristino nella macchina virtuale di Azure dall'URL](#backup-to-url-and-restore-from-url) |SQL Server 2012 SP1 CU2 o versione successiva | SQL Server 2012 SP1 CU2 o versione successiva | < 12.8 TB per SQL Server 2016, in caso contrario < 1 TB | Questo è un altro metodo per spostare il file di backup nella macchina virtuale usando l'archiviazione di Azure. |
| [Scollegare e successivamente copiare i dati e i file di log nell'archivio BLOB di Azure, quindi collegare a SQL Server nella macchina virtuale di Azure dall'URL](#detach-and-attach-from-a-url) | SQL Server 2005 o versione successiva |SQL Server 2014 o versione successiva | [Limite di archiviazione della macchina virtuale di Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Usare questo metodo quando si prevede di [archiviare i file tramite il servizio di archiviazione BLOB di Azure](https://msdn.microsoft.com/library/dn385720.aspx) e di collegarli a SQL Server in esecuzione su una macchina virtuale di Azure, in particolare con database di grandi dimensioni. |
| [Conversione della macchina locale a dischi rigidi virtuali Hyper-V, caricamento sull'archivio BLOB di Azure e successiva distribuzione di una nuova macchina virtuale tramite il disco rigido virtuale caricato](#convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm) |SQL Server 2005 o versione successiva |SQL Server 2005 o versione successiva |[Limite di archiviazione della macchina virtuale di Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Usare quando si usa [la licenza di SQL Server](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md), quando si esegue la migrazione di un database che verrà eseguito su una versione precedente di SQL Server o quando si esegue la migrazione dei database di sistema e utente insieme come parte della migrazione del database dipendente da altri database utente e/o database di sistema. |
| [Spedizione del disco rigido tramite il servizio di Importazione/Esportazione di Windows](#ship-a-hard-drive) |SQL Server 2005 o versione successiva |SQL Server 2005 o versione successiva |[Limite di archiviazione della macchina virtuale di Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Usare il [servizio di importazione/esportazione di Windows](../../../storage/common/storage-import-export-service.md) quando il metodo della copia manuale è troppo lento, ad esempio con database di grandi dimensioni |
| [Usare la procedura guidata Aggiungi replica Azure](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 o versione successiva |SQL Server 2012 o versione successiva |[Limite di archiviazione della macchina virtuale di Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Riduce al minimo il tempo di inattività; da usare quando si ha una distribuzione locale di Always On |
| [Uso della replica transazionale di SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 o versione successiva |SQL Server 2005 o versione successiva |[Limite di archiviazione della macchina virtuale di Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Usare quando è necessario ridurre al minimo i tempi di inattività e non si dispone di una distribuzione Always On locale |

## <a name="back-up-and-restore"></a>Backup e ripristino

Eseguire il backup del database con la compressione, copiare il backup nella macchina virtuale e ripristinare il database. Se il file di backup è superiore a 1 TB, è necessario creare un set con striping perché la dimensione massima del disco di una macchina virtuale è 1 TB. Per eseguire la migrazione di un database utente tramite il metodo manuale, attenersi ai passaggi generali seguenti:

1. Eseguire un backup completo del database su una posizione locale.
2. Creare o caricare una macchina virtuale con la versione desiderata di SQL Server.
3. Configurare la connettività in base ai requisiti specifici. Vedere [Connettersi a una macchina virtuale di SQL Server in Azure (Resource Manager)](ways-to-connect-to-sql.md).
4. Copiare i file di backup nella macchina virtuale usando desktop remoto, Esplora risorse o il comando Copy da un prompt dei comandi.

## <a name="backup-to-url-and-restore-from-url"></a>Backup su URL e ripristino da URL

Anziché eseguire il backup in un file locale, è possibile usare il [backup nell'URL](https://msdn.microsoft.com/library/dn435916.aspx) e quindi eseguire il ripristino dall'URL alla macchina virtuale. SQL Server 2016 supporta i set di backup con striping. Sono consigliati per le prestazioni e obbligatori se si superano i limiti di dimensione per i BLOB. Per i database di dimensioni molto grandi è consigliabile usare il [servizio di importazione/esportazione di Windows](../../../storage/common/storage-import-export-service.md) .

## <a name="detach-and-attach-from-a-url"></a>Rimuovere e allegare da un URL

Rimuovere il database e i file di log e trasferirli nell'[archivio BLOB di Azure](https://msdn.microsoft.com/library/dn385720.aspx). Allegare il database dall'URL nella macchina virtuale di Azure. Utilizzare questo metodo se si desidera che i file di database fisici si trovino nell'archivio BLOB, che può essere utile per database di dimensioni molto grandi. Per eseguire la migrazione di un database utente tramite il metodo manuale, attenersi ai passaggi generali seguenti:

1. Scollegare i file del database dall'istanza del database locale.
2. Copiare i file del database scollegati nell'archivio BLOB di Azure usando l'[utilità della riga di comando AZCopy](../../../storage/common/storage-use-azcopy.md).
3. Collegare i file del database dall'URL di Azure all'istanza di SQL Server nella macchina virtuale di Azure.

## <a name="convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm"></a>Convertire in una macchina virtuale, caricare in un URL e distribuire come nuova macchina virtuale

Usare questo metodo per eseguire la migrazione di tutti i database di sistema e utente in un'istanza di SQL Server locale a una macchina virtuale di Azure. Per eseguire la migrazione di un'intera istanza di SQL Server utilizzando il metodo manuale, attenersi ai passaggi generali seguenti:

1. Convertire le macchine fisiche o virtuali in dischi rigidi virtuali Hyper-V.
2. Caricare i file dei dischi rigidi virtuali su Archiviazione di Azure usando il [cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Distribuire una nuova macchina virtuale utilizzando il disco rigido virtuale caricato.

> [!NOTE]
> Per eseguire la migrazione di un'intera applicazione, è consigliabile usare [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md).

## <a name="ship-a-hard-drive"></a>Spedire un disco rigido

Usare il [metodo del servizio di importazione/esportazione di Windows](../../../storage/common/storage-import-export-service.md) per trasferire grandi quantità di dati di file sull'archivio BLOB di Azure in situazioni in cui il caricamento in rete è eccessivamente costoso o non è possibile. Con questo servizio, è possibile inviare uno o più dischi rigidi contenenti tali dati a un data center di Azure, dove i dati verranno caricati nell'account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).

> [!TIP]
> In caso di domande sulle macchine virtuali SQL Server, vedere le [domande frequenti](frequently-asked-questions-faq.md).

Per istruzioni sulla creazione di SQL Server in una macchina virtuale di Azure da un'immagine acquisita, vedere [Tips & Tricks on 'cloning' Azure SQL virtual machines from captured images](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) (Suggerimenti per clonare macchine virtuali di SQL Server di Azure da immagini acquisite) nel blog dei tecnici di SQL Server CSS.

