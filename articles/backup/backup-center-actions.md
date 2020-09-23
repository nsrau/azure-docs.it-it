---
title: Eseguire azioni con backup Center
description: Questo articolo illustra come eseguire azioni con backup Center
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 81c81f0e9d955e0a5243485baaedff4e6f0fc10d
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997718"
---
# <a name="perform-actions-using-backup-center"></a>Eseguire azioni con backup Center

Il centro di backup consente di eseguire azioni relative al backup delle chiavi da un'interfaccia centrale senza dover passare a un singolo insieme di credenziali. Di seguito sono riportate alcune azioni che è possibile eseguire da backup Center:

* Configurare il backup per le origini dati
* Ripristinare un'istanza di backup
* Creare un nuovo insieme di credenziali
* Creare un nuovo criterio di backup
* Attivare un backup su richiesta per un'istanza di backup
* Arrestare il backup per un'istanza di backup

## <a name="supported-scenarios"></a>Scenari supportati

* Backup Center è attualmente supportato per il backup di macchine virtuali di Azure e per il backup del server database di Azure per PostgreSQL.
* Per un elenco dettagliato degli scenari supportati e non supportati, vedere la [matrice di supporto](backup-center-support-matrix.md) .

## <a name="configure-backup"></a>Configurare il backup

A seconda del tipo di origine dati di cui si vuole eseguire il backup, seguire le istruzioni appropriate come descritto di seguito.

### <a name="configure-backup-for-azure-virtual-machines"></a>Configurare il backup per le macchine virtuali di Azure

1. Passare al centro di backup e selezionare **+ backup** nella parte superiore della scheda **Panoramica** .

    ![Panoramica di Centro backup](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. Selezionare il tipo di origine dati di cui si vuole eseguire il backup (macchina virtuale di Azure in questo caso).

    ![Selezionare l'origine dati per configurare il backup della macchina virtuale](./media/backup-center-actions/backup-select-datasource-vm.png)

3. Scegliere un insieme di credenziali dei servizi di ripristino e selezionare **continua**. In questo modo si arriva all'esperienza di configurazione del backup identica a quella raggiungibile da un insieme di credenziali di servizi di ripristino. [Altre informazioni su come configurare il backup per le macchine virtuali di Azure con un insieme](tutorial-backup-vm-at-scale.md)di credenziali di servizi di ripristino.

### <a name="configure-backup-for-azure-database-for-postgresql-server"></a>Configurare il backup per il server di database di Azure per PostgreSQL

1. Passare al centro di backup e selezionare **+ backup** nella parte superiore della scheda **Panoramica** .
2. Selezionare il tipo di origine dati di cui si vuole eseguire il backup (in questo caso il database di Azure per il server PostgreSQL).

    ![Selezionare DataSource per configurare il backup del server di database di Azure per PostgreSQL](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. Selezionare **Continua**. In questo modo si arriva all'esperienza di configurazione del backup identica a quella raggiungibile da un insieme di credenziali per il backup. [Altre informazioni su come configurare il backup per il database di Azure per il server PostgreSQL con un insieme](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)di credenziali per il backup.

## <a name="restore-a-backup-instance"></a>Ripristinare un'istanza di backup

A seconda del tipo di origine dati che si desidera ripristinare, seguire le istruzioni appropriate come descritto di seguito.

### <a name="if-youre-restoring-an-azure-virtual-machine"></a>Se si sta ripristinando una macchina virtuale di Azure

1. Passare al centro di backup e selezionare **Ripristina** nella parte superiore della scheda **Overview (panoramica** ).

    ![Panoramica del centro di backup per il ripristino della macchina virtuale](./media/backup-center-actions/backup-center-overview-restore.png)

2. Selezionare il tipo di origine dati che si vuole ripristinare (macchina virtuale di Azure in questo caso).

    ![Selezionare l'origine dati per il ripristino della macchina virtuale](./media/backup-center-actions/restore-select-datasource-vm.png)

3. Scegliere un'istanza di backup e selezionare **continua**. In questo modo si arriva all'esperienza di ripristino delle impostazioni che è identica a quella raggiungibile da un insieme di credenziali di servizi di ripristino. [Altre informazioni su come ripristinare una macchina virtuale di Azure con un insieme](backup-azure-arm-restore-vms.md#before-you-start)di credenziali di servizi di ripristino.

### <a name="if-youre-restoring-an-azure-database-for-postgresql-server"></a>Se si sta ripristinando un database di Azure per il server PostgreSQL

1. Passare al centro di backup e selezionare **Ripristina** nella parte superiore della scheda **Overview (panoramica** ).
2. Selezionare il tipo di origine dati di cui si vuole eseguire il backup (in questo caso il database di Azure per il server PostgreSQL).

    ![Selezionare l'origine dati per il ripristino del server database di Azure per PostgreSQL](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. Scegliere un'istanza di backup e selezionare **continua**. In questo modo si arriva all'esperienza di ripristino delle impostazioni che è identica a quella raggiungibile da un insieme di credenziali di servizi di ripristino. [Altre informazioni su come ripristinare un database di Azure per il server PostgreSQL con un insieme di credenziali per il backup](backup-azure-database-postgresql.md#restore).

## <a name="create-a-new-vault"></a>Creare un nuovo insieme di credenziali

È possibile creare un nuovo insieme di credenziali passando a backup Center e selezionando **+ Vault** nella parte superiore della scheda **Panoramica** .

![Create vault](./media/backup-center-actions/backup-center-create-vault.png)

* [Altre informazioni sulla creazione di un insieme di credenziali di servizi di ripristino](backup-create-rs-vault.md)
* [Altre informazioni sulla creazione di un insieme di credenziali per il backup](backup-vault-overview.md)

## <a name="create-a-new-backup-policy"></a>Creare un nuovo criterio di backup

A seconda del tipo di origine dati di cui si vuole eseguire il backup, seguire le istruzioni appropriate descritte di seguito.

### <a name="if-youre-backing-up-an-azure-virtual-machine"></a>Se si esegue il backup di una macchina virtuale di Azure

1. Passare al centro di backup e selezionare **+ policy** nella parte superiore della scheda **Overview (panoramica** ).

    ![Panoramica del Centro backup per i criteri di backup](./media/backup-center-actions/backup-center-overview-policy.png)

2. Selezionare il tipo di origine dati di cui si vuole eseguire il backup (macchina virtuale di Azure in questo caso).

    ![Selezionare l'origine dati per i criteri per il backup delle VM](./media/backup-center-actions/policy-select-datasource-vm.png)

3. Scegliere un insieme di credenziali dei servizi di ripristino e selezionare **continua**. In questo modo si accede all'esperienza di creazione dei criteri identica a quella raggiungibile da un insieme di credenziali di servizi di ripristino. [Altre informazioni su come creare un nuovo criterio di backup per una macchina virtuale di Azure con un insieme](backup-azure-arm-vms-prepare.md#create-a-custom-policy)di credenziali di servizi di ripristino.

### <a name="if-youre-backing-up-an-azure-database-for-postgresql-server"></a>Se si esegue il backup di un database di Azure per il server PostgreSQL

1. Passare al centro di backup e selezionare **+ policy** nella parte superiore della scheda **Overview (panoramica** ).
2. Selezionare il tipo di origine dati di cui si vuole eseguire il backup (in questo caso il database di Azure per il server PostgreSQL).

    ![Selezionare l'origine dati per i criteri per il backup del server database di Azure per PostgreSQL](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. Selezionare **Continua**. In questo modo si passa all'esperienza di creazione dei criteri che è identica a quella raggiungibile da un insieme di credenziali per il backup. [Altre informazioni su come creare un nuovo criterio di backup con un insieme di credenziali per il backup](backup-azure-database-postgresql.md#create-backup-policy).

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>Eseguire un backup su richiesta per un'istanza di backup

Backup Center consente di cercare le istanze di backup nell'area di backup e di eseguire le operazioni di backup su richiesta.

Per attivare un backup su richiesta, passare a backup Center e selezionare la voce di menu **istanze di backup** . Selezionando questa finestra è possibile visualizzare i dettagli di tutte le istanze di backup a cui si ha accesso. È possibile cercare l'istanza di backup di cui si vuole eseguire il backup. Facendo clic con il pulsante destro del mouse su un elemento della griglia viene aperto un elenco di azioni disponibili. Selezionare l'opzione Esegui **backup ora** per eseguire un backup su richiesta.

![Backup su richiesta](./media/backup-center-actions/backup-center-on-demand-backup.png)

[Altre informazioni sull'esecuzione di backup su richiesta per macchine virtuali di Azure](backup-azure-manage-vms.md#run-an-on-demand-backup).

[Altre informazioni sull'esecuzione di backup su richiesta per il server di database di Azure per PostgreSQL](backup-azure-database-postgresql.md#on-demand-backup).

## <a name="stop-backup-for-a-backup-instance"></a>Arrestare il backup per un'istanza di backup

Esistono scenari in cui potrebbe essere necessario arrestare il backup per un'istanza di backup, ad esempio quando la risorsa sottostante di cui viene eseguito il backup non esiste più.

Per attivare un backup su richiesta, passare a backup Center e selezionare la voce di menu **istanze di backup** . Selezionare questa finestra consente di visualizzare i dettagli di tutte le istanze di backup a cui si ha accesso. È possibile cercare l'istanza di backup di cui si vuole eseguire il backup. Facendo clic con il pulsante destro del mouse su un elemento della griglia viene aperto un elenco di azioni disponibili. Selezionare l'opzione **Interrompi backup** per arrestare il backup per l'istanza di backup.

![Arresta protezione](./media/backup-center-actions/backup-center-stop-protection.png)

[Altre informazioni sull'arresto del backup per le macchine virtuali di Azure](backup-azure-manage-vms.md#stop-protecting-a-vm).

[Altre informazioni sull'arresto del backup per il database di Azure per il server PostgreSQL](backup-azure-database-postgresql.md#stop-protection)

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare e gestire i backup](backup-center-monitor-operate.md)
* [Governare la proprietà di backup](backup-center-govern-environment.md)
* [Ottenere informazioni dettagliate sui backup](backup-center-obtain-insights.md)
