---
title: Domande frequenti - Backup dei database di SQL Server nelle macchine virtuali di AzureFAQ - Backing up SQL Server databases on Azure VMs
description: Risposte alle domande comuni sul backup dei database di SQL Server nelle macchine virtuali di Azure con Backup di Azure.Find answers to common questions about backing up SQL Server databases on Azure VMs with Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: a973761bf16e2d271d718e4a8b29e08624276987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247709"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Domande frequenti sui database di SQL Server in esecuzione in un backup della macchina virtuale di AzureFAQ about SQL Server databases that are running on an Azure VM backup

Questo articolo risponde alle domande comuni sul backup dei database di SQL Server eseguiti nelle macchine virtuali di Azure (VM) e sull'uso del servizio Backup di Azure.This article answers common questions about backing up SQL Server databases that run on Azure virtual machines (VMs) and use the [Azure Backup](backup-overview.md) service.

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>È possibile usare il backup di Azure per la macchina virtuale IaaS e SQL Server nello stesso computer?

Sì, è possibile avere sia il backup della macchina virtuale che il backup SQL nella stessa macchina virtuale. In questo caso, viene attivato internamente il backup completo solo copia nella macchina virtuale per non troncare i log.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>La soluzione riprova o ripristina automaticamente i backup?

In alcune circostanze, il servizio Backup di Azure attiva i backup corretti. La correzione automatica può avvenire per una qualsiasi delle sei condizioni indicate di seguito:

- Se il log o il backup differenziale non riesce a causa di un errore di convalida LSN, il log successivo o il backup differenziale vengono invece convertiti in un backup completo.
- Se non è stato eseguito alcun backup completo prima di un log o di un backup differenziale, tale backup o backup differenziale viene invece convertito in un backup completo.
- Se il tempo di backup completo più recente è precedente a 15 giorni, il log successivo o il backup differenziale viene invece convertito in un backup completo.
- Tutti i processi di backup che vengono annullati a causa di un aggiornamento dell'estensione vengono nuovamente attivati dopo il completamento dell'aggiornamento e l'avvio dell'estensione.
- Se si sceglie di sovrascrivere il database durante il ripristino, il backup di registro/differenziale successivo ha esito negativo e viene invece attivato un backup completo.
- Nei casi in cui è necessario un backup completo per reimpostare le catene di log a causa di modifiche nel modello di recupero del database, viene attivato automaticamente nella pianificazione successiva.

La correzione automatica come funzionalità è abilitata per tutti gli utenti per impostazione predefinita; Tuttavia, nel caso in cui si sceglie di opt-out di esso, quindi eseguire il seguente:

- Nell'istanza di SQL Server, nella cartella *C:, Programmi, Backup del carico di lavoro di Azure,* creare o modificare il file **ExtensionSettingsOverrides.json.**
- In **ExtensionSettingsOverrides.json**impostare *"EnableAutoHealer": false*.
- Salvare le modifiche e chiudere il file.
- Nell'istanza di SQL Server aprire **Gestione attività** e riavviare il servizio **AzureWLBackupCoordinatorSvc.**

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>È possibile controllare il numero di backup simultanei eseguiti nel server SQL?

Sì. È possibile imitare la velocità di esecuzione del criterio di backup per ridurre al minimo l'impatto su un'istanza di SQL Server. Per modificare l'impostazione:

1. Nell'istanza di SQL Server, nella cartella *C:, Programmi, Backup del carico di lavoro di Azure,* creare il file *ExtensionSettingsOverrides.json.*
2. Nel file *ExtensionSettingsOverrides.json* modificare l'impostazione **DefaultBackupTasksThreshold** su un valore inferiore, ad esempio 5. <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
Il valore predefinito di DefaultBackupTasksThreshold è **20**.

3. Salvare le modifiche e chiudere il file.
4. Nell'istanza di SQL Server, aprire **Task Manager**. Riavviare il servizio **AzureWLBackupCoordinatorSvc**.<br/> <br/>
 Mentre questo metodo è utile se l'applicazione di backup utilizza una grande quantità di risorse, SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) è un modo più generico per specificare limiti sulla quantità di CPU, I/O fisico e memoria che le richieste di applicazione in ingresso possono utilizzare.

> [!NOTE]
> Nell'esperienza utente è ancora possibile andare avanti e pianificare il maggior numero di backup in un dato momento, tuttavia verranno elaborati in una finestra scorrevole di esempio, 5, come da esempio.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>È possibile eseguire un backup completo da una replica secondaria?

In base alle limitazioni di SQL, è possibile eseguire Copia solo il backup completo nella replica secondaria. tuttavia il backup completo non è consentito.

## <a name="can-i-protect-availability-groups-on-premises"></a>È possibile proteggere i gruppi di disponibilità in locale?

No. Azure Backup protects SQL Server databases running in Azure. If an availability group (AG) is spread between Azure and on-premises machines, the AG can be protected only if the primary replica is running in Azure. Backup di Azure protegge inoltre solo i nodi eseguiti nella stessa area di Azure dell'insieme di credenziali dei servizi di ripristino.

## <a name="can-i-protect-availability-groups-across-regions"></a>È possibile proteggere i gruppi di disponibilità tra aree?

L'insieme di credenziali di Azure Backup Recovery Services è in grado di rilevare e proteggere tutti i nodi che si trovano nella stessa area dell'insieme di credenziali. Se il gruppo di disponibilità AlwaysOn di SQL Server si estende su più aree di Azure, configurare il backup dall'area con il nodo primario. Backup di Azure è in grado di rilevare e proteggere tutti i database nel gruppo di disponibilità in base alle preferenze di backup. Quando la preferenza di backup non viene soddisfatta, i backup hanno esito negativo e viene visualizzato l'avviso di errore.

## <a name="do-successful-backup-jobs-create-alerts"></a>I processi di backup con esito positivo determinano la creazione di avvisi?

No. I processi di backup con esito positivo non generano avvisi. Gli avvisi vengono inviati solo per i processi di backup con esito negativo. Il comportamento dettagliato per gli avvisi del portale è documentato [qui](backup-azure-monitoring-built-in-monitor.md). Tuttavia, nel caso in cui si sia interessati a disporre di avvisi anche per processi riusciti, è possibile usare [monitoraggio tramite Monitoraggio di](backup-azure-monitoring-use-azuremonitor.md)Azure.However, in case you are interested do have alerts even for successful jobs, you can use Monitoring using Azure Monitor .

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>È possibile visualizzare i processi di backup pianificati nel menu Processi di backup?

Nel menu **Processo di backup** verranno visualizzati solo i processi di backup su richiesta. Per il monitoraggio pianificato dei processi [tramite Monitoraggio di Azure.](backup-azure-monitoring-use-azuremonitor.md)

## <a name="are-future-databases-automatically-added-for-backup"></a>I database futuri vengono aggiunti automaticamente per il backup?

Sì, è possibile ottenere questa funzionalità con [la protezione automatica](backup-sql-server-database-azure-vms.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Se si elimina un database da un'istanza protetta automatica, cosa accadrà ai backup?

Se un database viene eliminato da un'istanza protetta automatica, i backup del database vengono comunque tentati. Ciò implica che il database eliminato inizia a essere visualizzato come non integro in Elementi di **backup** ed è ancora protetto.

Il modo corretto per interrompere la protezione di questo database consiste nell'eseguire **Interrompi backup** con i dati di **eliminazione** in questo database.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Se si interrompe il funzionamento di backup di un database autoprotected quale sarà il suo comportamento?

Se si interrompe il backup con i dati di **conservazione,** non verrà eseguito alcun backup futuro e i punti di ripristino esistenti rimarranno intatti. Il database verrà comunque considerato protetto e visualizzato nella casella Elementi di **backup**.

Se si interrompe il **backup con i dati**di eliminazione , non verranno eseguiti backup futuri e verranno eliminati anche i punti di ripristino esistenti. Il database verrà considerato non protetto e visualizzato nell'istanza in Configura backup. Tuttavia, a differenza di altri database up-protected che possono essere selezionati manualmente o che possono essere protetti automaticamente, questo database appare in grigio e non può essere selezionato. L'unico modo per proteggere nuovamente questo database consiste nel disabilitare la protezione automatica nell'istanza. È ora possibile selezionare il database e configurare la protezione su di esso o riattivare la protezione automatica nell'istanza.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Se si modifica il nome del database dopo che è stato protetto, quale sarà il comportamento?

Un database rinominato viene considerato come un nuovo database. Di conseguenza, il servizio tratterà questa situazione come se il database non fosse stato trovato e con esito negativo i backup.

È possibile selezionare il database, che ora è rinominato e configurare la protezione su di esso. Nel caso in cui la protezione automatica sia abilitata nell'istanza, il database rinominato verrà automaticamente rilevato e protetto.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Perché non è possibile visualizzare un database aggiunto per un'istanza protetta automatica?

Un database [aggiunto a un'istanza protetta automaticamente](backup-sql-server-database-azure-vms.md#enable-auto-protection) potrebbe non essere visualizzato immediatamente sotto gli elementi protetti. Il motivo è che l'individuazione viene eseguita in genere ogni 8 ore. Tuttavia, è possibile individuare e proteggere immediatamente i nuovi database se si esegue manualmente un'individuazione selezionando **Riscopri DB**, come illustrato nell'immagine seguente:

  ![Individuare manualmente un database appena aggiuntoManually discover a newly added database](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come eseguire il backup di un database di SQL Server in esecuzione in una macchina virtuale di Azure.Learn how to back up a [SQL Server database](backup-azure-sql-database.md) that's running on an Azure VM.
