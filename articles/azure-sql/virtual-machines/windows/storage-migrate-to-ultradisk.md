---
title: Esegui la migrazione del disco del log al disco Ultra
description: Informazioni su come eseguire la migrazione del SQL Server nel disco di log della macchina virtuale (VM) di Azure a una Ultradisk di Azure per sfruttare i vantaggi offerti dalle prestazioni elevate e dalla bassa latenza.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 12ba0900f2499965f7843672183310dfecfbab2b
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146672"
---
# <a name="migrate-log-disk-to-ultra-disk"></a>Esegui la migrazione del disco del log al disco Ultra
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

I dischi di Azure ultra offrono velocità effettiva elevata, IOPS elevate e archiviazione su disco a bassa latenza per SQL Server in una macchina virtuale (VM) di Azure. 

Questo articolo illustra come eseguire la migrazione del disco di log a un'unità SSD ultra per sfruttare i vantaggi offerti dai dischi Ultra. 

## <a name="back-up-database"></a>Eseguire il backup del database

Completare un [backup completo](backup-restore.md) del database. 

## <a name="attach-disk"></a>Collegamento di un disco

Aggiungere la Ultra SSD alla macchina virtuale dopo aver abilitato la compatibilità con ultradisk nella VM. 

Il disco Ultra è supportato in un subset di dimensioni e aree di macchine virtuali. Prima di procedere, verificare che la macchina virtuale si trovi in un'area, una zona e una dimensione che supportino il disco Ultra. È possibile [determinare e convalidare le dimensioni e l'area della macchina virtuale](../../../virtual-machines/disks-enable-ultra-ssd.md#determine-vm-size-and-region-availability) usando l'interfaccia della riga di comando di Azure 

### <a name="enable-compatibility"></a>Abilita compatibilità

Per abilitare la compatibilità, attenersi alla seguente procedura:

1. Passare alla macchina virtuale nell' [portale di Azure](https://portal.azure.com/). 
1. Arrestare/deallocare la macchina virtuale. 
1. Selezionare **dischi** in **Impostazioni** e quindi selezionare **Impostazioni aggiuntive** . 

   :::image type="content" source="media/storage-migrate-to-ultradisk/additional-disks-settings-azure-portal.png" alt-text="Selezionare impostazioni aggiuntive per i dischi in impostazioni nel portale di Azure":::

1. Selezionare **Sì** per **abilitare la compatibilità del disco Ultra** . 

   :::image type="content" source="../../../virtual-machines/media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png" alt-text="Selezionare impostazioni aggiuntive per i dischi in impostazioni nel portale di Azure":::

1. Selezionare **Salva** . 



### <a name="attach-disk"></a>Collegamento di un disco

Usare il portale di Azure per alleghi un disco ultra alla macchina virtuale. Per informazioni dettagliate, vedere la pagina relativa alla [connessione di un disco Ultra](../../../virtual-machines/disks-enable-ultra-ssd.md#attach-an-ultra-disk).

Una volta collegato il disco, avviare la VM ancora una volta usando il portale di Azure. 



## <a name="format-disk"></a>Formatta disco

Connettersi alla macchina virtuale e formattare il disco Ultra.  

Per formattare il disco Ultra, attenersi alla procedura seguente:

1. Connettersi alla macchina virtuale usando Remote Desktop Protocol (RDP).
1. Usare [Gestione disco](/windows-server/storage/disk-management/overview-of-disk-management) per formattare e partizionare il disco Ultra collegato appena collegato. 


## <a name="use-disk-for-log"></a>Usa disco per log

Configurare SQL Server per l'uso della nuova unità di log. Questa operazione può essere eseguita usando Transact-SQL (T-SQL) o SQL Server Management Studio (SSMS). L'account utilizzato per l'account del servizio SQL Server deve avere il controllo completo sul nuovo percorso del file di log. 

### <a name="configure-permissions"></a>Configurare le autorizzazioni

1. Verificare l'account di servizio utilizzato da SQL Server. A tale scopo, è possibile usare Gestione configurazione SQL Server o Services. msc.
1. Passare al nuovo disco. 
1. Creare una cartella o più cartelle da usare per il file di log. 
1. Fare clic con il pulsante destro del mouse sulla cartella e scegliere **Proprietà** .
1. Nella scheda **sicurezza** concedere l'accesso con controllo completo all'account del servizio SQL Server. 
1. Selezionare **OK**  per salvare le impostazioni. 
1. Ripetere questa operazione per ogni cartella a livello radice in cui si prevede di avere dati SQL. 

### <a name="use-new-log-drive"></a>Usa nuova unità di log 

Una volta concessa l'autorizzazione, utilizzare Transact-SQL (T-SQL) o SQL Server Management Studio (SSMS) per scollegare il database e spostare i file di log esistenti nella nuova posizione.

   > [!CAUTION]
   > Scollegando il database si disconnetterà, chiudendo le connessioni ed eseguendo il rollback di tutte le transazioni in corso. Procedere con cautela e durante una finestra di manutenzione di inattività. 



# <a name="transact-sql-t-sql"></a>[Transact-SQL (T-SQL)](#tab/tsql)

Usare T-SQL per spostare i file esistenti in una nuova posizione:

1. Connettersi al database in SQL Server Management Studio e aprire una **nuova finestra query** . 
1. Ottenere i file e i percorsi esistenti:

   ```sql
   USE AdventureWorks
   GO

   sp_helpfile
   GO
   ```

1. Scollegare il database: 

   ```sql
   USE master
   GO

   sp_detach_db 'AdventureWorks'
   GO
   ```

1. Utilizzare Esplora file per spostare il file di log nel nuovo percorso del disco Ultra. 

1. Alleghi il database, specificando i nuovi percorsi dei file: 

   ```sql
    sp_attach_db 'AdventureWorks'
   'E:\Fixed_FG\AdventureWorks.mdf',
   'E:\Fixed_FG\AdventureWorks_2.ndf',
   'F:\New_Log\AdventureWorks_log.ldf'
   GO
   ```

A questo punto, il database viene portato online con il log nella nuova posizione. 



# <a name="sql-server-management-studio-ssms"></a>[SQL Server Management Studio (SSMS)](#tab/ssms)

Usare SSMS per spostare i file esistenti in una nuova posizione:

1. Connettersi al database in SQL Server Management Studio (SSMS). 
1. Fare clic con il pulsante destro del mouse sul database, scegliere **Proprietà** e quindi selezionare **file** . 
1. Annotare il percorso dei file esistenti. 
1. Selezionare **OK** per chiudere la finestra di dialogo. 
1. Fare clic con il pulsante destro del mouse sul database e selezionare **attività**  >  **Disconnetti** . 
1. Eseguire la procedura guidata per scollegare il database. 
1. Utilizzare Esplora file per spostare manualmente il file di log nel nuovo percorso.
1. Connessione del database in SQL Server Management Studio
   1. Fare clic con il pulsante destro del mouse su **database** in **Esplora oggetti** e scegliere **Connetti database** . 
   1. Utilizzando la finestra di dialogo, aggiungere ogni file, incluso il file di log, nella nuova posizione. 
   1. Selezionare **OK** per alleghi il database. 

A questo punto, il database viene portato online con il log nella nuova posizione.

---


## <a name="next-steps"></a>Passaggi successivi

Per migliorare le prestazioni, esaminare le procedure consigliate per le [prestazioni](performance-guidelines-best-practices.md) aggiuntive. 

Per una panoramica delle SQL Server in macchine virtuali di Azure, vedere gli articoli seguenti:

- [Panoramica di SQL Server nelle macchine virtuali Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Panoramica di SQL Server nelle macchine virtuali Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
