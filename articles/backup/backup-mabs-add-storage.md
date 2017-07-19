---
title: Utilizzare Modern Backup Storage con il server di Backup di Azure v2 | Microsoft Docs
description: "Informazioni sulle nuove funzionalità del server di Backup di Azure v2. In questo articolo viene descritto come aggiornare l'installazione del server di backup."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: masaran;markgal
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 751b9b495fd368dff1f72429707f5f33a0ccb569
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---

# <a name="add-storage-to-azure-backup-server-v2"></a>Aggiungere spazio di archiviazione al server di Backup di Azure v2

Il server di Backup di Azure v2 include Modern Backup Storage di System Center 2016 Data Protection Manager. Modern Backup Storage garantisce un risparmio del 50% sullo spazio di archiviazione, backup tre volte più veloci e un'archiviazione più efficiente. Offre anche l'archiviazione con riconoscimento del carico di lavoro. 

> [!NOTE]
> Per utilizzare Modern Backup Storage, è necessario eseguire il server di Backup v2 in Windows Server 2016. Se si esegue il server di Backup v2 in una versione precedente di Windows Server, il server di Backup di Azure non può avvalersi di Modern Backup Storage. Invece, i carichi di lavoro vengono protetti come avviene nel server di Backup v1. Per ulteriori informazioni, vedere la [matrice di protezione](backup-mabs-protection-matrix.md) della versione del server di backup.

## <a name="volumes-in-backup-server-v2"></a>Volumi nel server di Backup v2

Il server di Backup v2 accetta volumi di archiviazione. Quando si aggiunge un volume, il server di backup formatta il volume in Resilient File System (ReFS), richiesto da Modern Backup Storage. Per aggiungere un volume ed espanderlo in un secondo momento se necessario, è consigliabile utilizzare questo flusso di lavoro:

1.  Configurare il server di Backup v2 in una macchina virtuale.
2.  Creare un volume su un disco virtuale in un pool di archiviazione:
    1.  Aggiungere un disco a un pool di archiviazione e creare un disco virtuale con layout semplice.
    2.  Aggiungere altri dischi ed estendere il disco virtuale.
    3.  Creare volumi nel disco virtuale.
3.  Aggiungere i volumi al server di backup.
4.  Configurare l'archiviazione con riconoscimento del carico di lavoro.

## <a name="create-a-volume-for-modern-backup-storage"></a>Creare un volume per Modern Backup Storage

L'utilizzo del server di Backup v2 con volumi come archiviazione su disco consente di mantenere il controllo sull'archiviazione. Un volume può essere un singolo disco. Tuttavia, se si desidera estendere l'archiviazione in futuro, creare un volume da un disco creato utilizzando spazi di archiviazione. Ciò può essere utile se si desidera espandere il volume per l'archiviazione di backup. In questa sezione sono presentate le procedure consigliate per la creazione di un volume con questa configurazione.

1. In Server Manager selezionare **Servizi file e archiviazione** > **Volumi** > **Pool di archiviazione**. In **DISCHI FISICI**, selezionare **Nuovo pool di archiviazione**. 

    ![Creare un nuovo pool di archiviazione](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. Nella casella di riepilogo a discesa **ATTIVITÀ** selezionare **Nuovo disco virtuale**.

    ![Aggiungere un disco virtuale](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Selezionare il pool di archiviazione e quindi selezionare **Aggiungi disco fisico**.

    ![Aggiungere un disco fisico](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Selezionare il disco fisico e quindi **Estendi disco virtuale**.

    ![Estendere il disco virtuale](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Selezionare il disco virtuale e quindi **Nuovo volume**.

    ![Creare un nuovo volume](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. Nella finestra di dialogo **Selezionare il server e il disco** selezionare il server e il nuovo disco. Quindi selezionare **Avanti**.

    ![Selezionare il server e il disco](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Aggiungere i volumi all'archiviazione su disco del server di backup

Per aggiungere un volume al server di backup, nel riquadro **Gestione** ripetere l'analisi dell'archiviazione e quindi selezionare **Aggiungi**. Viene visualizzato un elenco di tutti i volumi disponibili per l'aggiunta per l'archiviazione del server di backup. Dopo l'aggiunta dei volumi disponibili all'elenco dei volumi selezionati, è possibile assegnare loro un nome descrittivo per agevolarne la gestione. Per formattare questi volumi in ReFS e consentire al server di backup di sfruttare i vantaggi di Modern Backup Storage, selezionare **OK**.

![Aggiungere i volumi disponibili](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Configurare l'archiviazione con riconoscimento del carico di lavoro

Con l'archiviazione del carico di lavoro, è possibile selezionare i volumi in cui archiviare preferibilmente determinati tipi di carichi di lavoro. Ad esempio, è possibile configurare volumi costosi che supportano un numero elevato di operazioni di input/output al secondo (IOPS) per archiviare solo i carichi di lavoro che richiedono backup frequenti di volumi elevati. Un esempio è SQL Server con i log delle transazioni. Per il backup di altri carichi di lavoro, ad esempio le macchine virtuali, che viene eseguito meno frequentemente, utilizzare volumi a basso costo.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

È possibile configurare l'archiviazione con riconoscimento del carico di lavoro utilizzando il cmdlet di PowerShell Update-DPMDiskStorage che aggiorna le proprietà di un volume nel pool di archiviazione di un server Data Protection Manager.

Sintassi:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
Nella schermata seguente viene illustrato il cmdlet Update-DPMDiskStorage nella finestra di PowerShell.

![Comando Update-DPMDiskStorage nella finestra di PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

Le modifiche apportate tramite PowerShell vengono riflesse nella Console di amministrazione del server di backup.

![Dischi e volumi nella Console di amministrazione](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="next-steps"></a>Passaggi successivi
Dopo aver installato il server di backup, leggere le informazioni su come preparare il server o iniziare a proteggere un carico di lavoro.

- [Preparare i carichi di lavoro del server di backup](backup-azure-microsoft-azure-backup.md)
- [Usare il server di backup per eseguire il backup di un server VMware](backup-azure-backup-server-vmware.md)
- [Usare il server di backup per eseguire il backup di SQL Server](backup-azure-sql-mabs.md)


