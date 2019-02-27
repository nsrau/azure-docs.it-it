---
title: Domande frequenti relative al backup di database di SQL Server in macchine virtuali di Azure con Backup di Azure
description: Fornisce le risposte alle domande frequenti relative al backup di database di SQL Server in macchine virtuali di Azure con Backup di Azure.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: a14406733ff60d53d4bf7792ff0c9a015c57d9b3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430925"
---
# <a name="faq-on-sql-server-running-on-azure-vm-backup"></a>Domande frequenti sul backup di SQL server in esecuzione su macchine virtuali di Azure

Questo articolo fornisce le risposte alle domande frequenti relative al backup di database di SQL Server in esecuzione su macchine virtuali di Azure con il servizio [Backup di Azure](backup-overview.md).

> [!NOTE]
> Questa funzionalità è attualmente in anteprima pubblica.



## <a name="can-i-throttle-the-backup-speed"></a>È possibile limitare la velocità di backup?

Sì. È possibile imitare la velocità di esecuzione del criterio di backup per ridurre al minimo l'impatto su un'istanza di SQL Server. Per modificare l'impostazione:
1. Nell'istanza di SQL Server, nella *cartella C:\Programmi\Azure Workload Backup\bin*, creare il file **ExtensionSettingsOverrides.json**.
2. Nel file **ExtensionSettingsOverrides.json** impostare **DefaultBackupTasksThreshold** su un valore inferiore, ad esempio 5. <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Salvare le modifiche. Chiudere il file.
4. Nell'istanza di SQL Server, aprire **Task Manager**. Riavviare il servizio **AzureWLBackupCoordinatorSvc**.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>È possibile eseguire un backup completo da una replica secondaria?
 No. Questa funzionalità non è supportata.

## <a name="do-successful-backup-jobs-create-alerts"></a>I processi di backup con esito positivo determinano la creazione di avvisi?

 No. I processi di backup con esito positivo non generano avvisi. Gli avvisi vengono inviati solo per i processi di backup con esito negativo.

## <a name="can-i-see-scheduled-backup-jobs-in-the-jobs-menu"></a>È possibile vedere i processi di backup pianificati nel menu Processi?

 No. Il menu **Processi di backup** mostra i dettagli dei processi su richiesta, ma non i processi di backup pianificati. Se un processo di backup pianificato ha esito positivo, i dettagli sono disponibili negli avvisi relativi. Per monitorare tutti i processo di backup ad hoc e pianificati, [usare SQL Server Management Studio](manage-monitor-sql-database-backup.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>I database futuri vengono aggiunti automaticamente per il backup?

 No. Quando si configura la protezione per un'istanza di SQL Server, se si seleziona l'opzione a livello di server, vengono aggiunti tutti i database. Se si aggiungono database a un’istanza server SQL dopo la configurazione della protezione, è necessario aggiungere manualmente i nuovi database per proteggerli. I database non sono inclusi automaticamente nella protezione configurata.

##  <a name="how-do-i-restart-protection-after-changing-recovery-type"></a>Come è possibile riavviare la protezione dopo aver modificato il tipo di ripristino?

Attivare un backup completo. I backup del log iniziano come previsto.

## <a name="can-i-protect-availability-groups-on-premises"></a>È possibile proteggere i gruppi di disponibilità in locale?

 No. Backup di Azure protegge gli SQL Server in esecuzione in Azure. Se un gruppo di disponibilità (AG) è sparso tra le macchine Azure e in locale, l'AG può essere protetto solo se la replica primaria è in esecuzione in Azure. Inoltre, Backup di Azure protegge solo i nodi in esecuzione nella stessa area di Azure come l'insieme di credenziali di Servizi di ripristino.

## <a name="can-i-protect-availability-groups-across-regions"></a>È possibile proteggere i gruppi di disponibilità tra aree?

I servizi di ripristino di Backup di Azure possono rilevare e proteggere tutti i nodi che sono nella stessa area dell'insieme di credenziali di Servizi di ripristino. Se si dispone di un gruppo di disponibilità Always On SQL che si estende su più aree di Azure, è necessario configurare il backup dall'area che contiene il nodo primario. Backup di Azure sarà in grado di rilevare e proteggere tutti i database nel gruppo di disponibilità in base alle preferenze di backup. Se le preferenze di backup non vengono soddisfatte, i backup avranno esito negativo e verrà visualizzato l'avviso di errore.

## <a name="can-i-exclude-databases-with-auto-protection-enabled"></a>È possibile escludere i database in cui è abilitata la protezione automatica?

No, la [protezione automatica](backup-azure-sql-database.md#enable-auto-protection) si applica all'intera istanza. Non è possibile proteggere in modo selettivo i database in un'istanza usando la protezione automatica.

## <a name="can-i-have-different-policies-in-an-auto-protected-instance"></a>È possibile definire criteri diversi in un'istanza protetta automaticamente?

Se un'istanza include già alcuni database protetti, questi continueranno a essere protetti tramite i rispettivi criteri anche dopo che l'opzione di [protezione automatica](backup-azure-sql-database.md#enable-auto-protection) è stata **attivata**. Tuttavia, a tutti i database non protetti e a quelli che verranno aggiunti in futuro verranno applicati solo i criteri definiti in **Configura backup** dopo aver selezionato i database. Infatti, diversamente dagli altri database protetti, non è possibile modificare i criteri per un database in un'istanza protetta automaticamente.
Se necessario, l'unico modo consiste nel disabilitare temporaneamente la protezione automatica per l'istanza e quindi modificare i criteri per il database, È a questo punto possibile riabilitare la protezione automatica per questa istanza.

## <a name="if-i-delete-a-database-from-auto-protection-will-backups-stop"></a>Se si elimina un database dalla protezione automatica, verranno interrotti anche i backup?

No, se un database viene eliminato da un'istanza protetta automaticamente, i backup per tale database vengono comunque tentati. Ciò implica che il database eliminato inizia a essere visualizzato come non integro in **Elementi di backup** e viene ancora considerato come protetto.

L'unico modo per arrestare la protezione di questo database consiste nel disabilitare temporaneamente la [protezione automatica](backup-azure-sql-database.md#enable-auto-protection) nell'istanza e quindi scegliere l'opzione **Interrompi backup** in **Elementi di backup** per tale database. È a questo punto possibile riabilitare la protezione automatica per questa istanza.

##  <a name="why-cant-i-see-an-added-database-for-an-auto-protected-instance"></a>Perché non viene visualizzato un database aggiunto per un'istanza protetta automaticamente?

È possibile che un database appena aggiunto a un'istanza [protetta automaticamente](backup-azure-sql-database.md#enable-auto-protection) non venga visualizzato immediatamente tra gli elementi protetti. Il motivo è che l'individuazione viene eseguita in genere ogni 8 ore. Tuttavia, è possibile eseguire un'individuazione manuale tramite l'opzione **Individua di nuovo i database** per individuare e proteggere immediatamente i nuovi database, come illustrato nell'immagine seguente:

  ![Visualizzare il database appena aggiunto](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come](backup-azure-sql-database.md) eseguire il backup di un database SQL Server in esecuzione in una macchina virtuale di Azure.
