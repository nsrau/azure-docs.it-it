---
title: Usare Modern Backup Storage con il server di Backup di Azure
description: Informazioni sulle nuove funzionalità del server di Backup di Azure. In questo articolo viene descritto come aggiornare l'installazione del server di backup.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dacurwin
ms.openlocfilehash: 48d58ac303a843c627067c9a0287628c35b65f66
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019077"
---
# <a name="add-storage-to-azure-backup-server"></a>Aggiungere risorse di archiviazione al server di Backup di Azure

Il server di Backup di Azure V2 e versioni successive supporta Modern Backup Storage, che garantisce un risparmio del 50% sullo spazio di archiviazione, backup tre volte più veloci e un'archiviazione più efficiente. Offre anche l'archiviazione con riconoscimento del carico di lavoro.

> [!NOTE]
> Per usare Modern Backup Storage, è necessario eseguire il server di Backup V2 o V3 Windows Server 2016 oppure V3 in Windows Server 2019.
> Se si esegue il server di Backup V2 in una versione precedente di Windows Server, il server di Backup di Azure non può avvalersi di Modern Backup Storage. Invece, i carichi di lavoro vengono protetti come avviene nel server di Backup V1. Per ulteriori informazioni, vedere la [matrice di protezione](backup-mabs-protection-matrix.md) della versione del server di backup.

## <a name="volumes-in-backup-server"></a>Volumi nel server di Backup

Il server di Backup V2 o versione successiva accetta volumi di archiviazione. Quando si aggiunge un volume, il server di backup formatta il volume in Resilient File System (ReFS), richiesto da Modern Backup Storage. Per aggiungere un volume ed espanderlo in un secondo momento se necessario, è consigliabile utilizzare questo flusso di lavoro:

1.  Configurare il server di Backup in una macchina virtuale.
2.  Creare un volume su un disco virtuale in un pool di archiviazione:
    1.  Aggiungere un disco a un pool di archiviazione e creare un disco virtuale con layout semplice.
    2.  Aggiungere altri dischi ed estendere il disco virtuale.
    3.  Creare volumi nel disco virtuale.
3.  Aggiungere i volumi al server di backup.
4.  Configurare l'archiviazione con riconoscimento del carico di lavoro.

## <a name="create-a-volume-for-modern-backup-storage"></a>Creare un volume per Modern Backup Storage

L'uso del server di Backup V2 o versione successiva con volumi come archiviazione su disco consente di mantenere il controllo sull'archiviazione. Un volume può essere un singolo disco. Tuttavia, se si desidera estendere l'archiviazione in futuro, creare un volume da un disco creato utilizzando spazi di archiviazione. Ciò può essere utile se si desidera espandere il volume per l'archiviazione di backup. In questa sezione sono presentate le procedure consigliate per la creazione di un volume con questa configurazione.

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

È possibile configurare l'archiviazione in grado di riconoscere il carico di lavoro usando il cmdlet di PowerShell Update-DPMDiskStorage, che aggiorna le proprietà di un volume nel pool di archiviazione in un server di Backup di Azure. 

Sintassi:

`Parameter Set: Volume`

```powershell
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
Nella schermata seguente viene illustrato il cmdlet Update-DPMDiskStorage nella finestra di PowerShell.

![Comando Update-DPMDiskStorage nella finestra di PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

Le modifiche apportate tramite PowerShell vengono riflesse nella Console di amministrazione del server di backup.

![Dischi e volumi nella Console di amministrazione](./media/backup-mabs-add-storage/mabs-add-storage-9.png)


## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrare lo spazio di archiviazione legacy a Modern Backup Storage
Dopo l'aggiornamento o l'istallazione del server di Backup V2 e l'aggiornamento del sistema operativo a Windows Server 2016, aggiornare i gruppi protezione dati per l'uso di Modern Backup Storage. Per impostazione predefinita, i gruppi protezione dati non vengono modificati. Continuano a funzionare nel modo in cui erano stati configurati inizialmente.

L'aggiornamento dei gruppi protezione dati per l'uso di Modern Backup Storage è facoltativo. Per aggiornare il gruppo protezione dati, arrestare la protezione di tutte le origini dati usando l'opzione di conservazione dei dati. Quindi aggiungere le origini dati a un nuovo gruppo protezione dati.

1. Nella Console di amministrazione selezionare la funzione **Protezione**. Nell'elenco **Membro del gruppo protezione dati** fare clic con il pulsante destro del mouse sul membro e quindi scegliere **Arresta protezione dati del membro**.

   ![Arresto della protezione del membro](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. Nella finestra di dialogo **Rimuovi da gruppo** esaminare lo spazio su disco utilizzato e lo spazio disponibile per il pool di archiviazione. L'impostazione predefinita è lasciare i punti di recupero sul disco e consentire che scadano in base ai criteri di conservazione associati. Fare clic su **OK**.

   Se si desidera restituire immediatamente lo spazio su disco utilizzato al pool di archiviazione disponibile, selezionare la casella di controllo **Elimina replica su disco** per eliminare i dati di backup (e i punti di recupero) associati a tale membro.

   ![Rimozione dalla finestra di dialogo del gruppo](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Creare un gruppo protezione dati che usa Modern Backup Storage. Includere le origini dati non protette.

## <a name="add-disks-to-increase-legacy-storage"></a>Aggiungere dischi per aumentare lo spazio di archiviazione legacy

Se si desidera usare lo spazio di archiviazione legacy con il server di backup, potrebbe essere necessario aggiungere dischi per aumentare lo spazio di archiviazione legacy.

Per aggiungere spazio di archiviazione su disco:

1. Nella Console di amministrazione selezionare **Gestione** > **Spazio di archiviazione su disco** > **Aggiungi**.

    ![Finestra di dialogo Aggiungi spazio di archiviazione su disco](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. Nella finestra di dialogo **Aggiungi spazio di archiviazione su disco** selezionare **Aggiungi dischi**.

5. Nell'elenco dei dischi disponibili selezionare i dischi che si desidera aggiungere, selezionare **Aggiungi** e poi **OK**.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver installato il server di backup, leggere le informazioni su come preparare il server o iniziare a proteggere un carico di lavoro.

- [Preparare i carichi di lavoro del server di backup](backup-azure-microsoft-azure-backup.md)
- [Usare il server di backup per eseguire il backup di un server VMware](backup-azure-backup-server-vmware.md)
- [Usare il server di backup per eseguire il backup di SQL Server](backup-azure-sql-mabs.md)
