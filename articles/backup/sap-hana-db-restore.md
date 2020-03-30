---
title: Ripristinare i database SAP HANA nelle macchine virtuali di AzureRestore SAP HANA databases on Azure VMs
description: In this article, discover how to restore SAP HANA databases that are running on Azure Virtual Machines.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287918"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Ripristinare i database SAP HANA nelle macchine virtuali di AzureRestore SAP HANA databases on Azure VMs

In questo articolo viene descritto come ripristinare i database SAP HANA in esecuzione nella macchina virtuale (VM) di Azure di cui è stato eseguito il backup del servizio Backup di Azure in un insieme di credenziali di Azure Backup.This article describes how to restore SAP HANA databases that are running on Azure Virtual Machine (VM), that the Azure Backup service has backed up to an Azure Backup Recovery Services vault. I ripristini possono essere utilizzati per creare copie dei dati per scenari di sviluppo/test o per tornare a uno stato precedente.

Per altre informazioni, su come eseguire il backup dei database SAP HANA, vedere Eseguire il backup dei database SAP HANA nelle macchine virtuali di Azure.For more information, on how to back up SAP HANA databases, see [Back up SAP HANA databases on Azure VMs](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Ripristinare fino a un punto nel tempo o a un punto di ripristinoRestore to a point in time or to a recovery point

Backup di Azure può ripristinare database SAP HANA in esecuzione nelle macchine virtuali di Azure seguendo questa procedura:

* Ripristinare una data specifica o un'ora (al secondo), usando i backup del log. In base ai tempi di ripristino specificati, Backup di Azure determina automaticamente i backup completi, differenziali e la catena di backup del log necessari per ripristinare in base all'ora selezionata.

* Ripristinare un backup completo o differenziale specifico per il ripristino a un punto di ripristino specifico.

## <a name="prerequisites"></a>Prerequisiti

Prima di ripristinare un database, tenere presente quanto segue:

* È possibile ripristinare il database solo in un'istanza SAP HANA che risiede nella stessa area

* L'istanza di destinazione deve essere registrata con lo stesso insieme di credenziali dell'origine

* Backup di Azure non è in grado di identificare due istanze SAP HANA diverse nella stessa macchina virtuale. Pertanto, il ripristino dei dati da un'istanza a un'altra nella stessa macchina virtuale non è possibile

* Per assicurarsi che l'istanza SAP HANA di destinazione sia pronta per il ripristino, controllare lo stato **Preparazione backup:**

  * Aprire l'insieme di credenziali in cui è registrata l'istanza SAP HANA di destinazione

  * Nel dashboard dell'insieme di credenziali, in **Guida introduttiva,** scegliere **Backup**

![Backup nel dashboard dell'insieme di credenziali](media/sap-hana-db-restore/getting-started-backup.png)

* In **Backup**, in **Che cosa si vuole eseguire il backup?** scegliere SAP **HANA in Azure VM**

![Scegliere SAP HANA nella macchina virtuale di AzureChoose SAP HANA in Azure VM](media/sap-hana-db-restore/sap-hana-backup.png)

* In **Individua database nelle macchine virtuali** fare clic su Visualizza **dettagli**

![Visualizzare i dettagli](media/sap-hana-db-restore/view-details.png)

* Esaminare lo stato di preparazione del backup della macchina virtuale di destinazioneReview the **Backup Readiness** of the target VM

![Server protetti](media/sap-hana-db-restore/protected-servers.png)

* Per ulteriori informazioni sui tipi di ripristino supportati da SAP HANA, fare riferimento alla nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Ripristinare un database

* Aprire l'insieme di credenziali in cui è registrato il database SAP HANA da ripristinare

* Nel dashboard dell'insieme di credenziali, in **Elementi protetti**, scegliere **Elementi di backup**

![Elementi di backup](media/sap-hana-db-restore/backup-items.png)

* In **Elementi di backup**, in Tipo di gestione **backup** selezionare **SAP HANA nella macchina virtuale** di Azure

![Tipo di gestione del backup](media/sap-hana-db-restore/backup-management-type.png)

* Selezionare il database da ripristinare

 ![Database da ripristinare](media/sap-hana-db-restore/database-to-restore.png)

* Verificare il menu database. Fornisce informazioni sul backup del database, tra cui:

  * I punti di ripristino più vecchi e più recenti

  * Lo stato di backup del log per le ultime 24 e 72 ore per il database

![Menu Database](media/sap-hana-db-restore/database-menu.png)

* Selezionare **Ripristina database**

* In **Ripristina configurazione**specificare dove (o come) ripristinare i dati:

  * **Percorso alternativo**: ripristinare il database in un percorso alternativo e mantenere il database di origine originale.

  * **Sovrascrivi database**: Ripristina i dati nella stessa istanza SAP HANA dell'origine originale. Questa opzione sovrascrive il database originale.

![Ripristinare la configurazione](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Ripristinare in un percorso alternativo

* Nel menu **Ripristina configurazione,** in **Dove ripristinare,** selezionare **Percorso alternativo**.

![Ripristinare in un percorso alternativo](media/sap-hana-db-restore/restore-alternate-location.png)

* Selezionare il nome host SAP HANA e il nome dell'istanza in cui si desidera ripristinare il database.
* Verificare se l'istanza SAP HANA di destinazione è pronta per il ripristino verificando ne viene eseguita la preparazione del **backup.** Per ulteriori dettagli, fare riferimento alla [sezione Prerequisiti.](#prerequisites)
* Nella finestra di dialogo **Nome del database ripristinato** inserire il nome del database di destinazione.

> [!NOTE]
> I ripristini del contenitore di database singolo (SDC, Single Database Container) devono seguire questi [controlli.](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)

* Se applicabile, selezionare **Sovrascrivi se il database con lo stesso nome esiste già nell'istanza HANA selezionata.**
* Selezionare **OK**.

![Ripristina configurazione - schermata finale](media/sap-hana-db-restore/restore-configuration-last.png)

* In **Seleziona punto di ripristino**selezionare Registri **(punto nel tempo)** per [eseguire il ripristino in un momento specifico.](#restore-to-a-specific-point-in-time) In alternativa, selezionare **Differenziale & completo** per eseguire il ripristino in un punto di [ripristino specifico.](#restore-to-a-specific-recovery-point)

### <a name="restore-and-overwrite"></a>Ripristinare e sovrascrivere

* Nel menu **Ripristina configurazione,** in **Dove ripristinare,** selezionare **Sovrascrivi db** > **OK**.

![Sovrascrivere il database](media/sap-hana-db-restore/overwrite-db.png)

* In **Seleziona punto di ripristino**selezionare Registri **(punto nel tempo)** per [eseguire il ripristino in un momento specifico.](#restore-to-a-specific-point-in-time) In alternativa, selezionare **Differenziale & completo** per eseguire il ripristino in un punto di [ripristino specifico.](#restore-to-a-specific-recovery-point)

### <a name="restore-to-a-specific-point-in-time"></a>Ripristinare un punto temporizzato specifico

Se si è scelto **log (punto temporizzato)** come tipo di ripristino, eseguire le operazioni seguenti:

* Selezionare un punto di ripristino dal grafico del log e scegliere **OK** per scegliere il punto di ripristino.

![Punto di ripristino](media/sap-hana-db-restore/restore-point.png)

* Nel menu **Ripristina** selezionare **Ripristina** per avviare il processo di ripristino.

![Selezionare ripristina](media/sap-hana-db-restore/restore-restore.png)

* Tenere traccia dello stato di avanzamento del ripristino nell'area **Notifiche** o tenere traccia selezionando Processi di **ripristino** dal menu del database.

![Ripristino attivato correttamente](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Eseguire il ripristino in un punto di ripristino specificoRestore to a specific recovery point

Se si è scelto **Completo e differenziale** come tipo di ripristino, eseguire le operazioni seguenti:

* Selezionare un punto di ripristino dall'elenco e scegliere **OK** per scegliere il punto di ripristino.

![Ripristinare un punto di ripristino specificoRestore specific recovery point](media/sap-hana-db-restore/specific-recovery-point.png)

* Nel menu **Ripristina** selezionare **Ripristina** per avviare il processo di ripristino.

![Avvia processo di ripristino](media/sap-hana-db-restore/restore-specific.png)

* Tenere traccia dello stato di avanzamento del ripristino nell'area **Notifiche** o tenere traccia selezionando Processi di **ripristino** dal menu del database.

![Stato del ripristino](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> In più contenitori di database (MDC) viene ripristinato dopo il ripristino del database di sistema in un'istanza di destinazione, è necessario eseguire nuovamente lo script di pre-registrazione. Solo allora i successivi ripristini del database tenant avranno esito positivo. Per ulteriori informazioni, vedere [Risoluzione dei problemi – Ripristino MDC](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come](sap-hana-db-manage.md) gestire i database SAP HANA di cui è stato eseguito il backup con Backup di Azure
