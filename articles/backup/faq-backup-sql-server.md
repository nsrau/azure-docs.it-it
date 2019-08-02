---
title: Domande frequenti relative al backup di database di SQL Server in macchine virtuali di Azure con Backup di Azure
description: Risposte alle domande frequenti sul backup di SQL Server database in macchine virtuali di Azure con backup di Azure.
ms.reviewer: vijayts
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dacurwin
ms.openlocfilehash: 23dda22a8350591d5cf87cefc1a2f15fd90f17c7
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688842"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Domande frequenti sui database SQL Server in esecuzione in un backup di macchine virtuali di Azure

Questo articolo risponde alle domande più comuni sul backup di database SQL Server eseguiti in macchine virtuali (VM) di Azure e che usano il servizio [backup di Azure](backup-overview.md) .

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>È possibile usare backup di Azure per la macchina virtuale IaaS e SQL Server nello stesso computer?
Sì, è possibile avere sia il backup della macchina virtuale che il backup SQL nella stessa VM. In questo caso, viene attivato internamente il backup completo di sola copia nella macchina virtuale per non troncare i log.


## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>La soluzione esegue un nuovo tentativo o la correzione automatica dei backup?

In alcune circostanze, il servizio backup di Azure attiva i backup corretti. La correzione automatica può verificarsi per una delle sei condizioni indicate di seguito:

  - Se il backup del log o differenziale ha esito negativo a causa di un errore di convalida LSN, il backup successivo o differenziale viene invece convertito in un backup completo.
  - Se non si è verificato alcun backup completo prima di un backup del log o differenziale, il backup del log o differenziale viene invece convertito in un backup completo.
  - Se il punto di riferimento del backup completo più recente è precedente a 15 giorni, il successivo backup del log o differenziale viene invece convertito in un backup completo.
  - Tutti i processi di backup che vengono annullati a causa di un aggiornamento dell'estensione vengono riattivati dopo che l'aggiornamento è stato completato e l'estensione è stata avviata.
  - Se si sceglie di sovrascrivere il database durante il ripristino, il successivo backup del log/differenziale ha esito negativo e viene invece attivato un backup completo.
  - Nei casi in cui è necessario un backup completo per reimpostare le catene di log a causa di una modifica nel modello di recupero del database, viene attivata automaticamente una completa per la pianificazione successiva.

Per impostazione predefinita, la funzionalità di correzione automatica è abilitata per tutti gli utenti. Tuttavia, nel caso in cui si scelga di rifiutare esplicitamente, eseguire le operazioni seguenti:

  * Nell'istanza di SQL Server, nella cartella *C:\Program Files\Azure workload Backup\bin* creare o modificare il file **ExtensionSettingsOverrides. JSON** .
  * In **ExtensionSettingsOverrides. JSON**impostare *{"EnableAutoHealer": false}* .
  * Salvare le modifiche e chiudere il file.
  * Nell'istanza SQL Server aprire **attività Gestisci** e riavviare il servizio **AzureWLBackupCoordinatorSvc** .  

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>È possibile controllare il numero di backup simultanei eseguiti in SQL Server?

Sì. È possibile imitare la velocità di esecuzione del criterio di backup per ridurre al minimo l'impatto su un'istanza di SQL Server. Per modificare l'impostazione:
1. Nell'istanza di SQL Server, nella cartella *C:\Program Files\Azure workload Backup\bin* creare il file *ExtensionSettingsOverrides. JSON* .
2. Nel file *ExtensionSettingsOverrides. JSON* , modificare l'impostazione **DefaultBackupTasksThreshold** impostando un valore inferiore (ad esempio, 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`

3. Salvare le modifiche e chiudere il file.
4. Nell'istanza di SQL Server, aprire **Task Manager**. Riavviare il servizio **AzureWLBackupCoordinatorSvc**.<br/> <br/>
 Sebbene questo metodo consenta di usare una grande quantità di risorse per l'applicazione di backup, SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) è un modo più generico per specificare i limiti sulla quantità di CPU, i/o fisico e memoria che le richieste dell'applicazione in ingresso possono usare.

> [!NOTE]
> Nell'esperienza utente è comunque possibile pianificare tutti i backup in qualsiasi momento, ma verranno elaborati in una finestra temporale scorrevole, ad esempio 5, in base all'esempio precedente.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>È possibile eseguire un backup completo da una replica secondaria?
Per quanto riguarda le limitazioni di SQL, è possibile eseguire solo backup completo di copia nella replica secondaria; Tuttavia, il backup completo non è consentito.

## <a name="can-i-protect-availability-groups-on-premises"></a>È possibile proteggere I gruppi di disponibilità in locale?
No. Backup di Azure protegge SQL Server database in esecuzione in Azure. Se un gruppo di disponibilità (AG) viene distribuito tra Azure e i computer locali, il gruppo di disponibilità può essere protetto solo se la replica primaria è in esecuzione in Azure. Backup di Azure protegge anche solo i nodi in esecuzione nella stessa area di Azure dell'insieme di credenziali di servizi di ripristino.

## <a name="can-i-protect-availability-groups-across-regions"></a>È possibile proteggere I gruppi di disponibilità tra aree?
L'insieme di credenziali di servizi di ripristino di backup di Azure può rilevare e proteggere tutti i nodi che si trovano nella stessa area dell'insieme di credenziali. Se il SQL Server Always On gruppo di disponibilità si estende su più aree di Azure, configurare il backup dall'area che contiene il nodo primario. Backup di Azure è in grado di rilevare e proteggere tutti i database nel gruppo di disponibilità in base alle preferenze di backup. Quando la preferenza di backup non viene soddisfatta, i backup hanno esito negativo e viene generato l'avviso di errore.

## <a name="do-successful-backup-jobs-create-alerts"></a>I processi di backup con esito positivo determinano la creazione di avvisi?
No. I processi di backup con esito positivo non generano avvisi. Gli avvisi vengono inviati solo per i processi di backup con esito negativo. Il comportamento dettagliato per gli avvisi del portale è illustrato [qui](backup-azure-monitoring-built-in-monitor.md). Tuttavia, nel caso in cui si sia interessati ad avere avvisi anche per i processi riusciti, è possibile usare [monitoraggio di Azure](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>È possibile visualizzare i processi di backup pianificati nel menu processi di backup?
Il menu **processo di backup** mostrerà solo i processi di backup ad hoc. Per il processo pianificato, usare [monitoraggio di Azure](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>I database futuri vengono aggiunti automaticamente per il backup?
Sì, è possibile ottenere questa funzionalità con la [protezione automatica](backup-sql-server-database-azure-vms.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Se si elimina un database da un'istanza protetta in modo automatico, cosa accade ai backup?
Se un database viene eliminato da un'istanza protetta in modo automatico, i backup del database vengono comunque tentati. Ciò implica che il database eliminato inizia a essere visualizzato come non integro in **elementi di backup** ed è ancora protetto.

Il modo corretto per arrestare la protezione di questo database consiste nell' **arrestare il backup** con **Elimina dati** in questo database.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Se si interrompe l'operazione di backup di un database autoprotetto, quale sarà il comportamento?
Se si **interrompe il backup con Mantieni dati**, non verrà eseguita alcuna copia di backup futura e i punti di ripristino esistenti rimarranno intatti. Il database verrà comunque considerato protetto e visualizzato sotto gli **elementi di backup**.

Se si **interrompe il backup con Elimina dati**, non verrà eseguita alcuna copia di backup futura e verranno eliminati anche i punti di ripristino esistenti. Il database verrà considerato non protetto e verrà visualizzato nell'istanza del backup di configurazione. Tuttavia, a differenza di altri database protetti che possono essere selezionati manualmente o che possono essere protetti in modo autoprotetto, questo database viene visualizzato in grigio e non può essere selezionato. L'unico modo per riproteggere il database è disabilitare la protezione automatica sull'istanza. È ora possibile selezionare il database e configurare la protezione su di esso oppure riabilitare la protezione automatica nell'istanza di.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Se si modifica il nome del database dopo che è stato protetto, quale sarà il comportamento?
Un database rinominato viene considerato come un nuovo database. Il servizio considererà pertanto questa situazione come se il database non venisse trovato e con i backup non superati.

È possibile selezionare il database, che ora è stato rinominato e configurare la protezione su di esso. Se per l'istanza è abilitata la protezione automatica, il database rinominato verrà automaticamente rilevato e protetto.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Perché non è possibile visualizzare un database aggiunto per un'istanza protetta in modo autoprotetto?
Un database che è possibile [aggiungere a un'istanza](backup-sql-server-database-azure-vms.md#enable-auto-protection) protetta in modo autoprotetto potrebbe non essere visualizzato immediatamente in elementi protetti. Il motivo è che l'individuazione viene eseguita in genere ogni 8 ore. Tuttavia, è possibile individuare e proteggere immediatamente i nuovi database se si esegue manualmente un'individuazione selezionando **Ripristina**database, come illustrato nella figura seguente.

  ![Individuare manualmente un database appena aggiunto](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [eseguire il backup di un database di SQL Server](backup-azure-sql-database.md) in esecuzione in una macchina virtuale di Azure.
