---
title: Domande frequenti - Backup di database SAP HANA in VM di Azure
description: In questo articolo è possibile trovare le risposte ad alcune domande comuni sul backup di database SAP HANA tramite il servizio Backup di Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 56f98dddb00eb3ffc87eb27da73066de807a1ee1
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701020"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Domande frequenti - Backup di database SAP HANA in VM di Azure

Questo articolo presenta le risposte ad alcune domande comuni sul backup di database SAP HANA tramite il servizio Backup di Azure.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Quanti backup completi sono supportati al giorno?

È supportato un solo backup completo al giorno. Non è possibile attivare un backup differenziale e un backup completo nello stesso giorno.

### <a name="do-successful-backup-jobs-create-alerts"></a>I processi di backup con esito positivo determinano la creazione di avvisi?

No. I processi di backup con esito positivo non generano avvisi. Gli avvisi vengono inviati solo per i processi di backup con esito negativo. Il comportamento dettagliato degli avvisi del portale è documentato [qui](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). Se tuttavia si ha interesse a ricevere avvisi anche per i processi riusciti, è possibile usare [Monitoraggio di Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>È possibile vedere i processi di backup pianificati nel menu Processi di backup?

Il menu Processi di backup visualizza solo processi di backup ad hoc. Per i processi pianificati, usare [Monitoraggio di Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>I database futuri vengono aggiunti automaticamente per il backup?

No, questa funzionalità non è attualmente supportata.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Se si elimina un database da un'istanza, cosa accade ai backup?

Se un database viene eliminato da un'istanza SAP HANA, l'esecuzione dei backup del database viene comunque tentata. Ciò implica che il database eliminato inizia a essere visualizzato come non integro in **Elementi di backup** e viene ancora protetto.
Il modo corretto per interrompere la protezione di questo database consiste nell'eseguire **Stop Backup with delete data** (Interrompi backup con eliminazione dati) per il database.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Se si modifica il nome del database dopo che è stato protetto, quale sarà il comportamento?

Un database rinominato viene considerato come nuovo database. Il servizio gestirà pertanto questa situazione come se non riuscisse a trovare il database e i backup avranno esito negativo. Il database rinominato verrà visualizzato come nuovo database e dovrà essere configurato per la protezione.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Quali sono i prerequisiti per il backup dei database SAP HANA in una VM di Azure?

Fare riferimento alle sezioni [Prerequisiti](tutorial-backup-sap-hana-db.md#prerequisites) e [Funzionalità dello script di pre-registrazione](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Quali autorizzazioni devono essere impostate per consentire ad Azure di eseguire il backup di database SAP HANA?

L'esecuzione dello script di pre-registrazione consente di impostare le autorizzazioni necessarie per consentire ad Azure di eseguire il backup dei database SAP HANA. È possibile trovare altre informazioni sulle funzionalità dello script di pre-registrazione [qui](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>I backup funzionano dopo la migrazione di SAP HANA da SDC a MDC?

Vedere [questa sezione](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#sdc-to-mdc-upgrade-with-a-change-in-sid) della Guida alla risoluzione dei problemi.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>È possibile configurare il backup di HANA in Azure per un IP virtuale (bilanciamento del carico) e non per una macchina virtuale?

Non è attualmente possibile configurare la soluzione solo per un indirizzo IP virtuale. Per eseguire la soluzione è necessaria una macchina virtuale.

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>Se si dispone di una replica di sistema SAP HANA (HSR, HANA System Replication), come si deve configurare il backup per questa configurazione?

I nodi primario e secondario della replica HSR vengono considerati come due macchine virtuali singole e non correlate. È necessario configurare il backup nel nodo primario e, quando si verifica il failover, configurare il backup nel nodo secondario, che a questo punto diventa nodo primario. Non avviene alcun 'failover' automatico del backup all'altro nodo.

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>Come si può spostare un backup su richiesta nel file system locale anziché nell'insieme di credenziali di Azure?

1. Attendere il completamento del backup attualmente in esecuzione per il database desiderato (controllare in Studio l'avvenuto completamento)
1. Disabilitare i backup del log e impostare il backup del catalogo su **File system** per il database desiderato attenendosi alla procedura seguente:
1. Fare doppio clic su **SYSTEMDB** -> **Configurazione** -> **Seleziona database** -> **Filter (log)** (Filtro (log))
    1. Impostare enable_auto_log_backup su **No**
    1. Impostare log_backup_using_backint su **false**
1. Eseguire un backup su richiesta per il database desiderato e attendere il completamento del backup e del backup del catalogo.
1. Ripristinare le impostazioni precedenti per consentire il flusso dei backup all'insieme di credenziali di Azure:
    1. Impostare enable_auto_log_backup su **sì**
    1. Impostare log_backup_using_backint su **true**

## <a name="restore"></a>Restore

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Per quale motivo il sistema HANA in cui si vuole ripristinare il database non è visualizzato?

Verificare che siano soddisfatti tutti i prerequisiti per il ripristino nell'istanza SAP HANA di destinazione. Per altre informazioni, vedere [Prerequisiti - Ripristinare database SAP HANA in VM di Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Perché il ripristino del database con sovrascrittura non riesce?

Assicurarsi che l'opzione **Forza sovrascrittura** sia selezionata durante il ripristino.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Perché viene visualizzato l'errore "I sistemi di origine e destinazione per il ripristino sono incompatibili"?

Per informazioni sui tipi di ripristino attualmente supportati, vedere la nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [eseguire il backup di database SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) in esecuzione in VM di Azure.
