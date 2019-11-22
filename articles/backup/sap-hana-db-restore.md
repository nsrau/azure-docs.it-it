---
title: Ripristinare i database di SAP HANA nelle macchine virtuali di Azure
description: Questo articolo illustra come ripristinare SAP HANA database in esecuzione in macchine virtuali di Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287918"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Ripristinare i database di SAP HANA nelle macchine virtuali di Azure

Questo articolo descrive come ripristinare SAP HANA database in esecuzione in una macchina virtuale (VM) di Azure, in cui è stato eseguito il backup del servizio backup di Azure in un insieme di credenziali di servizi di ripristino di backup di Azure. I ripristini possono essere usati per creare copie dei dati per scenari di sviluppo/test o per tornare a uno stato precedente.

Per altre informazioni su come eseguire il backup dei database di SAP HANA, vedere [eseguire il backup di database SAP Hana in macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Ripristinare fino a un punto nel tempo o a un punto di ripristino

Backup di Azure è in grado di ripristinare SAP HANA database in esecuzione in macchine virtuali di Azure, come indicato di seguito:

* Ripristinare una data o un'ora specifica (al secondo) utilizzando i backup del log. Backup di Azure determina automaticamente i backup completi e differenziali appropriati e la catena di backup del log necessari per il ripristino in base all'ora selezionata.

* Ripristinare un backup completo o differenziale specifico per eseguire il ripristino in un punto di ripristino specifico.

## <a name="prerequisites"></a>prerequisiti

Prima di ripristinare un database, tenere presente quanto segue:

* È possibile ripristinare il database solo in un'istanza SAP HANA che si trova nella stessa area

* L'istanza di destinazione deve essere registrata con lo stesso insieme di credenziali dell'origine

* Backup di Azure non è in grado di identificare due istanze di SAP HANA diverse nella stessa VM. Pertanto, non è possibile ripristinare i dati da un'istanza a un'altra nella stessa VM

* Per assicurarsi che l'istanza di SAP HANA di destinazione sia pronta per il ripristino, verificare lo stato di **conformità del backup** :

  * Aprire l'insieme di credenziali in cui è registrata l'istanza di SAP HANA di destinazione

  * Nel dashboard dell'insieme di credenziali, in **Guida introduttiva**, scegliere **backup** .

![Backup nel dashboard dell'insieme di credenziali](media/sap-hana-db-restore/getting-started-backup.png)

* In **backup**, in **che cosa si vuole eseguire il backup?** scegliere **SAP Hana nella macchina virtuale di Azure**

![Scegliere SAP HANA nella macchina virtuale di Azure](media/sap-hana-db-restore/sap-hana-backup.png)

* In **individuare i database nelle macchine virtuali** fare clic su **Visualizza dettagli**

![Visualizzare i dettagli](media/sap-hana-db-restore/view-details.png)

* Verificare la **conformità del backup** della macchina virtuale di destinazione

![Server protetti](media/sap-hana-db-restore/protected-servers.png)

* Per altre informazioni sui tipi di ripristino supportati da SAP HANA, vedere la nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Per ripristinare un database

* Aprire l'insieme di credenziali in cui è registrato il database SAP HANA da ripristinare

* Nel dashboard dell'insieme di credenziali, in **elementi protetti**, scegliere **elementi di backup**

![Elementi di backup](media/sap-hana-db-restore/backup-items.png)

* In **elementi di backup**in **tipo di gestione di backup** Selezionare **SAP Hana nella macchina virtuale di Azure**

![Tipo di gestione di backup](media/sap-hana-db-restore/backup-management-type.png)

* Selezionare il database da ripristinare

 ![Database da ripristinare](media/sap-hana-db-restore/database-to-restore.png)

* Verificare il menu database. Fornisce informazioni sul backup del database, tra cui:

  * I punti di ripristino meno recenti e più recenti

  * Stato di backup del log per le ultime 24 ore e 72 ore per il database

![Menu database](media/sap-hana-db-restore/database-menu.png)

* Selezionare **Ripristina database**

* In **configurazione ripristino**specificare dove (o come) ripristinare i dati:

  * **Percorso alternativo**: ripristinare il database in un percorso alternativo e salvare il database di origine originale.

  * **Sovrascrivi database**: consente di ripristinare i dati nella stessa istanza di SAP Hana dell'origine originale. Questa opzione sovrascrive il database originale.

![Configurazione ripristino](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Ripristino in un percorso alternativo

* Nel menu **configurazione ripristino** , in **where to Restore**, selezionare **percorso alternativo**.

![Ripristino in un percorso alternativo](media/sap-hana-db-restore/restore-alternate-location.png)

* Selezionare il nome host SAP HANA e il nome dell'istanza in cui si desidera ripristinare il database.
* Controllare se l'istanza di SAP HANA di destinazione è pronta per il ripristino garantendo la **conformità del backup.** Per ulteriori informazioni, vedere la [sezione Prerequisiti](#prerequisites) .
* Nella finestra di dialogo **Nome del database ripristinato** inserire il nome del database di destinazione.

> [!NOTE]
> I ripristini di Database singolo container (DSC) devono seguire questi [controlli](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore).

* Se applicabile, selezionare **Sovrascrivi se il database con lo stesso nome esiste già nell'istanza di Hana selezionata**.
* Selezionare **OK**.

![Configurazione ripristino-schermata finale](media/sap-hana-db-restore/restore-configuration-last.png)

* In **Seleziona punto di ripristino**selezionare **registri (temporizzato)** per eseguire [il ripristino a un momento specifico](#restore-to-a-specific-point-in-time). In alternativa, selezionare **completo & differenziale** per [ripristinare un punto di ripristino specifico](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Ripristinare e sovrascrivere

* Nel menu **configurazione ripristino** , in percorso **da ripristinare**selezionare **Sovrascrivi database** > **OK**.

![Sovrascrivere il database](media/sap-hana-db-restore/overwrite-db.png)

* In **Seleziona punto di ripristino**selezionare **registri (temporizzato)** per eseguire [il ripristino a un momento specifico](#restore-to-a-specific-point-in-time). In alternativa, selezionare **completo & differenziale** per [ripristinare un punto di ripristino specifico](#restore-to-a-specific-recovery-point).

### <a name="restore-to-a-specific-point-in-time"></a>Ripristinare un punto temporizzato specifico

Se si è scelto **log (punto temporizzato)** come tipo di ripristino, eseguire le operazioni seguenti:

* Selezionare un punto di ripristino dal grafico di log e selezionare **OK** per scegliere il punto di ripristino.

![Punto di ripristino](media/sap-hana-db-restore/restore-point.png)

* Nel menu **Ripristina** selezionare **Ripristina** per avviare il processo di ripristino.

![Selezionare Ripristina](media/sap-hana-db-restore/restore-restore.png)

* Tenere traccia dello stato di avanzamento dell'operazione di ripristino nell'area **notifiche** o monitorarlo selezionando **Ripristina processi** dal menu database.

![Il ripristino è stato attivato](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Ripristinare un punto di ripristino specifico

Se si è scelto **Completo e differenziale** come tipo di ripristino, eseguire le operazioni seguenti:

* Selezionare un punto di ripristino dall'elenco e selezionare **OK** per scegliere il punto di ripristino.

![Ripristinare un punto di ripristino specifico](media/sap-hana-db-restore/specific-recovery-point.png)

* Nel menu **Ripristina** selezionare **Ripristina** per avviare il processo di ripristino.

![Avviare il processo di ripristino](media/sap-hana-db-restore/restore-specific.png)

* Tenere traccia dello stato di avanzamento dell'operazione di ripristino nell'area **notifiche** o monitorarlo selezionando **Ripristina processi** dal menu database.

![Stato del ripristino](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> Quando il database di sistema viene ripristinato in un'istanza di destinazione in più database del contenitore di database (MDC), è necessario eseguire di nuovo lo script di pre-registrazione. Solo i ripristini di database tenant successivi riusciranno. Per altre informazioni, vedere [risoluzione dei problemi: ripristino di MDC](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come](sap-hana-db-manage.md) gestire i database di SAP Hana backup con backup di Azure
