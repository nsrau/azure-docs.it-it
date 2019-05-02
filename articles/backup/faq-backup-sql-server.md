---
title: Domande frequenti relative al backup di database di SQL Server in macchine virtuali di Azure con Backup di Azure
description: Trova le risposte alle domande frequenti su backup di database di SQL Server in macchine virtuali di Azure con Backup di Azure.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: sachdevaswati
ms.openlocfilehash: 649e50634d901ab48f1cb36c39d7331401c0cc51
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64700177"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Domande frequenti sul database di SQL Server che eseguono un backup delle macchine Virtuali di Azure

Questo articolo risponde alle domande frequenti sul backup di database di SQL Server in esecuzione su macchine virtuali di Azure (VM) e che utilizzano le [Backup di Azure](backup-overview.md) servizio.

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>È possibile usare backup di Azure per VM IaaS, nonché SQL Server nello stesso computer?
Sì, è possibile avere sia backup delle macchine Virtuali e backup di SQL nella stessa VM. In questo caso, è attiva internamente la copia solo backup completo sulla VM per troncare il log.


## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>La soluzione ripetere o correzione i backup?

In alcune circostanze, il servizio Backup di Azure attiva backup correttive. Correzione automatica possono verificarsi per uno qualsiasi dei sei le condizioni indicate di seguito:

  - Se log o backup differenziale non riesce a causa dell'errore di convalida numero LSN, log successiva o backup differenziale invece viene convertito in un backup completo.
  - Se si è verificato alcun backup completo prima di un log o backup differenziale, tale log o backup differenziale invece viene convertito in un backup completo.
  - Se punto nel tempo dell'ultimo backup completo risale a più di 15 giorni, il log successiva o backup differenziale invece viene convertito in un backup completo.
  - Tutti i processi di backup che ottengano annullati a causa di un aggiornamento di estensione vengono attivati nuovamente dopo aver completato l'aggiornamento e l'estensione viene avviata.
  - Se si sceglie di sovrascrivere il database durante il ripristino, il backup del log/backup differenziale successivo ha esito negativo e viene generato invece un backup completo.
  - Nei casi in cui un backup completo è richiesto per reimpostare le catene di log a causa di modificare il modello di recupero di database, una procedura completa viene attivata automaticamente in base alla pianificazione successiva.

Auto-correzione come una funzionalità è abilitata per tutti gli utenti per impostazione predefinita. Tuttavia nel caso in cui si sceglie di rifiutare esplicitamente di esso, quindi eseguire il seguente:

  * Nell'istanza di SQL Server, nelle *C:\Program Files\Azure del carico di lavoro Backup\bin* cartella, creare o modificare le **ExtensionSettingsOverrides.json** file.
  * Nel **ExtensionSettingsOverrides.json**, impostare *{"EnableAutoHealer": false}*.
  * Salvare le modifiche e chiudere il file.
  * Nell'istanza di SQL Server, aprire **attività di gestione** e quindi riavviare il **AzureWLBackupCoordinatorSvc** servizio.  

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>È possibile controllare il numero di backup simultaneo eseguire in SQL server?

Sì. È possibile imitare la velocità di esecuzione del criterio di backup per ridurre al minimo l'impatto su un'istanza di SQL Server. Per modificare l'impostazione:
1. Nell'istanza di SQL Server, nelle *C:\Program Files\Azure del carico di lavoro Backup\bin* cartella, creare il *ExtensionSettingsOverrides.json* file.
2. Nel *ExtensionSettingsOverrides.json* file, modificare il **DefaultBackupTasksThreshold** impostazione su un valore inferiore (ad esempio, 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`

3. Salvare le modifiche e chiudere il file.
4. Nell'istanza di SQL Server, aprire **Task Manager**. Riavviare il servizio **AzureWLBackupCoordinatorSvc**.<br/> <br/>
 Sebbene questo metodo è utile se l'applicazione di backup viene utilizza una quantità elevata di risorse, SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) è un modo più generico per specificare i limiti sulla quantità di CPU, i/o fisico e memoria che può essere richieste in ingresso dell'applicazione usare.

> [!NOTE]
> Nell'esperienza utente è comunque possibile proseguire e pianificare le copie di backup in qualsiasi momento, tuttavia verranno elaborati in una finestra temporale scorrevole, 5, come nell'esempio precedente.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>È possibile eseguire un backup completo da una replica secondaria?
In base alle limitazioni di SQL, è possibile eseguire copia solo backup completo nella Replica secondaria; Tuttavia, non è consentito il backup completo.

## <a name="can-i-protect-availability-groups-on-premises"></a>È possibile proteggere disponibilità gruppi locali?
 No. Backup di Azure protegge i database di SQL Server in esecuzione in Azure. Se un gruppo di disponibilità (AG) viene distribuito tra le macchine locali e Azure, il gruppo di disponibilità può essere protette solo se la replica primaria è in esecuzione in Azure. Inoltre, Backup di Azure protegge solo i nodi che eseguono nella stessa area di Azure come l'insieme di credenziali di servizi di ripristino.

## <a name="can-i-protect-availability-groups-across-regions"></a>È possibile proteggere i gruppi di disponibilità in aree geografiche?
L'insieme di credenziali di servizi di ripristino di Backup di Azure può rilevare e proteggere tutti i nodi che si trovano nella stessa area dell'insieme di credenziali. Se il gruppo di disponibilità SQL Server Always On si estende su più aree di Azure, configurare il backup dall'area che contiene il nodo primario. Backup di Azure è in grado di rilevare e proteggere tutti i database nel gruppo di disponibilità in base alle preferenze di backup. Quando le preferenze di backup non viene soddisfatta, i backup non riescono e viene visualizzato l'avviso di errore.

## <a name="do-successful-backup-jobs-create-alerts"></a>I processi di backup con esito positivo determinano la creazione di avvisi?
 No. I processi di backup con esito positivo non generano avvisi. Gli avvisi vengono inviati solo per i processi di backup con esito negativo. Dettagli del comportamento per gli avvisi del portale è documentato [qui](backup-azure-monitoring-built-in-monitor.md). Tuttavia, nel caso in cui si è interessati sono presenti avvisi relativi anche per i processi completati, è possibile usare [il monitoraggio tramite Monitoraggio di Azure](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>È possibile visualizzare i processi di backup nel menu processi di Backup pianificati?
Il **processo di Backup** menu visualizzerà soltanto ad hoc dei processi di backup. Per il processo pianificato, usare [il monitoraggio tramite Monitoraggio di Azure](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>I database futuri vengono aggiunti automaticamente per il backup?
Sì, è possibile ottenere questa funzionalità con [la protezione automatica](backup-sql-server-database-azure-vms.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Se si elimina un database da un'istanza autoprotected, cosa accade ai backup?
Se viene eliminato un database da un'istanza autoprotected, i backup del database sono comunque tentati. Ciò implica che il database eliminato che inizi venga visualizzata come non integro sotto **elementi di Backup** ed è comunque protetto.

Il modo corretto per arrestare la protezione di questo database è sufficiente **Interrompi Backup** con **eliminare dati** in questo database.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Se si arresta l'operazione di backup di un database autoprotected qual è il comportamento?
Se decidi **interruzione del backup con conservazione dati**, Nessun backup futuri verrà eseguito e i punti di ripristino esistenti rimarranno invariati. Il database sarà considerato come protetto e visualizzato sotto la **elementi di Backup**.

Se decidi **Interrompi backup con cancellazione dei dati**, Nessun backup futuri verrà eseguito e verranno eliminati anche i punti di ripristino esistenti. Il database viene considerato non protetto e visualizzati sotto l'istanza di configurare il Backup. Tuttavia, a differenza di altri protetto da backup database che può essere selezionato manualmente o che possono ottenere autoprotected, questo database risulta disattivato e non può essere selezionato. L'unico modo per proteggere nuovamente il database è per disabilitare la protezione automatica nell'istanza. È ora possibile selezionare questo database e configurare la protezione su di esso o riabilitare la protezione automatica nell'istanza.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Se modifica il nome del database dopo che è stata protetta, qual è il comportamento?
Un database denominato nuovamente viene considerato come un nuovo database. Di conseguenza, il servizio considera questa situazione come se il database non sono stati trovati e con esito negativo i backup.

È possibile selezionare il database, che è stato rinominato e configura la protezione su di esso. Nel caso in cui la protezione automatica è abilitata nell'istanza, database rinominato automaticamente rilevato e protette.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Il motivo per cui non vengono visualizzati un database aggiunto per un'istanza autoprotected?
Oggetto di database che si [aggiunta a un'istanza autoprotected](backup-sql-server-database-azure-vms.md#enable-auto-protection) potrebbe non venga visualizzata immediatamente in elementi protetti. Il motivo è che l'individuazione viene eseguita in genere ogni 8 ore. Tuttavia, è possibile individuare e proteggere immediatamente i nuovi database se si esegue manualmente un'individuazione selezionando **ripristinare i database**, come illustrato nell'immagine seguente.

  ![Individuare manualmente un database appena aggiunto](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [eseguire il backup di un database di SQL Server](backup-azure-sql-database.md) che è in esecuzione in una VM di Azure.
