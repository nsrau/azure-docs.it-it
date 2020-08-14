---
title: Eseguire il backup di una macchina virtuale SQL Server dal riquadro VM
description: Questo articolo illustra come eseguire il backup di SQL Server database in macchine virtuali di Azure dal riquadro VM.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: dd30ca1fb138d3e3bd44633b9d5e71beae6d96be
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227252"
---
# <a name="back-up-a-sql-server-from-the-vm-pane"></a>Eseguire il backup di un SQL Server dal riquadro VM

Questo articolo illustra come eseguire il backup di SQL Server in esecuzione in macchine virtuali di Azure con il servizio [backup di Azure](backup-overview.md) . È possibile eseguire il backup di SQL Server VM usando due metodi:

- Singola SQL Server VM di Azure: le istruzioni in questo articolo descrivono come eseguire il backup di una macchina virtuale SQL Server direttamente dalla visualizzazione della macchina virtuale.
- Più macchine virtuali SQL Server di Azure: è possibile configurare un insieme di credenziali di servizi di ripristino e configurare il backup per più macchine virtuali. Per questo scenario, seguire le istruzioni riportate in [questo articolo](backup-sql-server-database-azure-vms.md) .

## <a name="before-you-start"></a>Prima di iniziare

1. Verificare l'ambiente con la [matrice di supporto](sql-support-matrix.md).
2. Ottenere una [Panoramica](backup-azure-sql-database.md) di backup di Azure per SQL Server VM.
3. Verificare che la macchina virtuale abbia [connettività di rete](backup-sql-server-database-azure-vms.md#establish-network-connectivity).

## <a name="configure-backup-on-the-sql-server"></a>Configurare il backup nel SQL Server

È possibile abilitare il backup nella macchina virtuale SQL Server dal riquadro **backup** della macchina virtuale. Questo metodo esegue due operazioni:

- Registra la VM SQL con il servizio backup di Azure per consentire l'accesso.
- Protegge completamente tutte le istanze del SQL Server in esecuzione all'interno della macchina virtuale. Ciò significa che il criterio di backup viene applicato a tutti i database esistenti, oltre che ai database che verranno aggiunti a tali istanze in futuro.

1. Selezionare il banner nella parte superiore della pagina per aprire la visualizzazione backup SQL Server.

    ![Visualizzazione backup SQL Server](./media/backup-sql-server-vm-from-vm-pane/sql-server-backup-view.png)

    >[!NOTE]
    >Non viene visualizzato il banner? Il banner viene visualizzato solo per le macchine virtuali SQL Server create con le immagini di Azure Marketplace. Viene inoltre visualizzato per le macchine virtuali protette con il backup delle VM di Azure. Per altre immagini, è possibile configurare il backup come illustrato [qui](backup-sql-server-database-azure-vms.md).

2. Immettere il nome dell'insieme di credenziali di servizi di ripristino. Un insieme di credenziali è un'entità logica per archiviare e gestire tutti i backup. Se si crea un nuovo insieme di credenziali:

    - Verrà creata nella stessa sottoscrizione e nella stessa area della macchina virtuale SQL Server che si sta proteggendo.
    - Verrà creato con l'impostazione di archiviazione con ridondanza geografica (GRS) per tutti i backup. Se si vuole modificare il tipo di ridondanza, è consigliabile eseguire questa operazione prima di proteggere la macchina virtuale. Per altre informazioni, vedi [questo articolo](backup-create-rs-vault.md#set-storage-redundancy).

3. Scegliere un **criterio di backup**. È possibile scegliere tra i criteri predefiniti o tutti gli altri criteri esistenti creati nell'insieme di credenziali. Se si desidera creare un nuovo criterio, è possibile fare riferimento a [questo articolo](backup-sql-server-database-azure-vms.md#create-a-backup-policy) per una guida dettagliata.

    ![Scegliere i criteri di backup](./media/backup-sql-server-vm-from-vm-pane/backup-policy.png)

4. Selezionare **Abilita backup**. Il completamento dell'operazione potrebbe richiedere alcuni minuti.

    ![Selezionare Abilita backup](./media/backup-sql-server-vm-from-vm-pane/enable-backup.png)

5. Al termine dell'operazione, nel banner verrà visualizzato il **nome** dell'insieme di credenziali.

    ![Il nome dell'insieme di credenziali viene visualizzato nel banner](./media/backup-sql-server-vm-from-vm-pane/vault-name.png)

6. Selezionare il banner per passare alla visualizzazione dell'insieme di credenziali in cui è possibile visualizzare tutte le macchine virtuali registrate e il relativo stato di protezione.

    ![Visualizzazione dell'insieme di credenziali con macchine virtuali registrate](./media/backup-sql-server-vm-from-vm-pane/vault-view.png)

7. Per le immagini non del Marketplace, la registrazione potrebbe avere esito positivo, ma la **configurazione del backup** potrebbe non essere attivata finché l'estensione di backup di Azure non dispone dell'autorizzazione per la SQL Server. In questi casi, la colonna **conformità backup** non è **pronta**. È necessario [assegnare manualmente le autorizzazioni appropriate per le](backup-azure-sql-database.md#set-vm-permissions) immagini non del Marketplace, in modo che sia possibile attivare la configurazione del backup.

    ![Preparazione backup non pronta](./media/backup-sql-server-vm-from-vm-pane/backup-readiness-not-ready.png)

8. Per ulteriori operazioni o monitoraggio da eseguire nella macchina virtuale SQL Server di cui è stato eseguito il backup, passare all'insieme di credenziali del servizio di ripristino corrispondente. Passare a **elementi di backup** per visualizzare tutti i database di cui è stato eseguito il backup in questo insieme di credenziali e attivare operazioni come il backup e il ripristino su richiesta. Analogamente, passare a **processi di backup** per [monitorare](manage-monitor-sql-database-backup.md) i processi corrispondenti a operazioni come la configurazione della protezione, il backup e il ripristino.

    ![Vedere database sottoposti a backup in elementi di backup](./media/backup-sql-server-vm-from-vm-pane/backup-items.png)

>[!NOTE]
>Il backup non viene configurato automaticamente in nessuna delle nuove istanze di SQL Server che possono essere aggiunte successivamente alla VM protetta. Per configurare il backup nelle istanze appena aggiunte, è necessario passare all'insieme di credenziali in cui è registrata la VM e seguire i passaggi elencati di [seguito](backup-sql-server-database-azure-vms.md).

## <a name="next-steps"></a>Passaggi successivi

Viene illustrato come:

- [Ripristinare database SQL Server di cui è stato eseguito il backup](restore-sql-database-azure-vm.md)
- [Gestire database SQL Server di cui è stato eseguito il backup](manage-monitor-sql-database-backup.md)
