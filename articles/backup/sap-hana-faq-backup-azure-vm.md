---
title: Domande frequenti - Backup di database SAP HANA in VM di Azure
description: In questo articolo è possibile trovare le risposte ad alcune domande comuni sul backup di database SAP HANA tramite il servizio Backup di Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: ddc4af9a164de3a822e8aebd6c0a4db769ec62a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262583"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Domande frequenti - Backup di database SAP HANA in VM di Azure

Questo articolo presenta le risposte ad alcune domande comuni sul backup di database SAP HANA tramite il servizio Backup di Azure.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Quanti backup completi sono supportati al giorno?

È supportato un solo backup completo al giorno. Non è possibile eseguire il backup differenziale e il backup completo attivato nello stesso giorno.

### <a name="do-successful-backup-jobs-create-alerts"></a>I processi di backup con esito positivo determinano la creazione di avvisi?

No. I processi di backup con esito positivo non generano avvisi. Gli avvisi vengono inviati solo per i processi di backup con esito negativo. Il comportamento dettagliato degli avvisi del portale è documentato [qui](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). Tuttavia, se si è interessati ad avere avvisi anche per i processi riusciti, è possibile usare [monitoraggio di Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>È possibile vedere i processi di backup pianificati nel menu Processi di backup?

Il menu Processi di backup visualizza solo processi di backup ad hoc. Per i processi pianificati, usare [Monitoraggio di Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>I database futuri vengono aggiunti automaticamente per il backup?

No, questa opzione non è attualmente supportata.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Se si elimina un database da un'istanza, cosa accade ai backup?

Se un database viene eliminato da un'istanza SAP HANA, l'esecuzione dei backup del database viene comunque tentata. Ciò implica che il database eliminato inizia a essere visualizzato come non integro in **Elementi di backup** e viene ancora protetto.
Il modo corretto per interrompere la protezione di questo database consiste nell'eseguire **Stop Backup with delete data** (Interrompi backup con eliminazione dati) per il database.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Se si modifica il nome del database dopo che è stato protetto, quale sarà il comportamento?

Un database rinominato viene considerato come nuovo database. Il servizio considererà pertanto questa situazione come se il database non venisse trovato e con i backup con esito negativo. Il database rinominato verrà visualizzato come nuovo database e dovrà essere configurato per la protezione.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Quali sono i prerequisiti per il backup dei database SAP HANA in una VM di Azure?

Fare riferimento alle sezioni [Prerequisiti](tutorial-backup-sap-hana-db.md#prerequisites) e [Funzionalità dello script di pre-registrazione](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="what-permissions-should-be-set-so-azure-can-back-up-sap-hana-databases"></a>Quali autorizzazioni devono essere impostate per consentire ad Azure di eseguire il backup SAP HANA database?

L'esecuzione dello script di pre-registrazione consente di impostare le autorizzazioni necessarie per consentire ad Azure di eseguire il backup dei database SAP HANA. È possibile trovare altre informazioni sulle funzionalità dello script di pre-registrazione [qui](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>I backup funzionano dopo la migrazione di SAP HANA da SDC a MDC?

Vedere [questa sezione](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#sdc-to-mdc-upgrade-with-a-change-in-sid) della Guida alla risoluzione dei problemi.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>È possibile configurare il backup di HANA in Azure per un IP virtuale (bilanciamento del carico) e non per una macchina virtuale?

Attualmente non è possibile configurare la soluzione in base a un indirizzo IP virtuale da solo. Per eseguire la soluzione è necessaria una macchina virtuale.

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

### <a name="how-can-i-use-sap-hana-backup-with-my-hana-replication-set-up"></a>Come è possibile usare SAP HANA backup con la configurazione della replica HANA?

Attualmente, backup di Azure non è in grado di comprendere la configurazione di un HSR. Ciò significa che i nodi primari e secondari di HSR verranno considerati come due macchine virtuali singole e non correlate. Per prima cosa è necessario configurare il backup nel nodo primario. Quando si verifica un failover, il backup deve essere configurato nel nodo secondario, che ora diventa il nodo primario. Non esiste un failover automatico del backup nell'altro nodo.

Per eseguire il backup dei dati dal nodo attivo (primario) in un determinato momento, è possibile **passare la protezione** al nodo secondario, che ora diventa primario dopo il failover.

Per eseguire questa **opzione**, attenersi alla procedura seguente:

- [Arrestare la protezione](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) (con conservazione dei dati) nel database primario
- Eseguire lo [script di pre-registrazione](https://aka.ms/scriptforpermsonhana) nel nodo secondario
- [Individuare i database](tutorial-backup-sap-hana-db.md#discover-the-databases) nel nodo secondario e [configurare i backup](tutorial-backup-sap-hana-db.md#configure-backup) su di essi

Questa procedura deve essere eseguita manualmente dopo ogni failover. È possibile eseguire questi passaggi tramite la riga di comando/REST HTTP oltre al portale di Azure. Per automatizzare questi passaggi, è possibile usare una Runbook di Azure.

Di seguito è riportato un esempio dettagliato del modo in cui deve essere eseguita la **protezione del cambio** :

In questo esempio sono presenti due nodi node 1 (primario) e node 2 (secondario) nella configurazione HSR.  I backup sono configurati nel nodo 1. Come indicato in precedenza, non tentare ancora di configurare i backup nel nodo 2.

Quando si verifica il primo failover, node 2 diventa il database primario. Con questi presupposti,

1. Arrestare la protezione del nodo 1 (primario precedente) con l'opzione Mantieni dati.
1. Eseguire lo script di pre-registrazione sul nodo 2 (che ora è primario).
1. Individuare i database nel nodo 2, assegnare i criteri di backup e configurare i backup.

Viene quindi attivato un primo backup completo sul nodo 2 e, al termine di tale operazione, vengono avviati i backup del log.

Quando si verifica il successivo failover, node 1 diventa di nuovo primario e node 2 diventa secondario. A questo punto, ripetere il processo:

1. Arrestare la protezione dati del nodo 2 con l'opzione Mantieni dati.
1. Eseguire lo script di pre-registrazione sul nodo 1 (che è diventato di nuovo il database primario)
1. Quindi [riprendere il backup](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) sul nodo 1 con i criteri richiesti (poiché i backup sono stati interrotti in precedenza nel nodo 1).

Il backup completo verrà nuovamente attivato sul nodo 1 e, al termine dell'operazione, i backup del log verranno avviati.

## <a name="restore"></a>Restore

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Per quale motivo il sistema HANA in cui si vuole ripristinare il database non è visualizzato?

Verificare che siano soddisfatti tutti i prerequisiti per il ripristino nell'istanza SAP HANA di destinazione. Per altre informazioni, vedere [Prerequisiti - Ripristinare database SAP HANA in VM di Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Perché il ripristino del database con sovrascrittura non riesce?

Assicurarsi che l'opzione **Forza sovrascrittura** sia selezionata durante il ripristino.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Perché viene visualizzato l'errore "I sistemi di origine e destinazione per il ripristino sono incompatibili"?

Per informazioni sui tipi di ripristino attualmente supportati, vedere la nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148).

### <a name="can-i-use-a-backup-of-a-database-running-on-sles-to-restore-to-an-rhel-hana-system-or-vice-versa"></a>È possibile usare un backup di un database in esecuzione su SLES per eseguire il ripristino in un sistema RHEL HANA o viceversa?

Sì, è possibile usare i backup in streaming attivati in un database HANA in esecuzione su SLES per ripristinarlo in un sistema RHEL HANA e viceversa. Ovvero il ripristino tra più sistemi operativi è possibile utilizzando i backup di flusso. Tuttavia, sarà necessario assicurarsi che il sistema HANA in cui si vuole eseguire il ripristino e il sistema HANA usato per il ripristino siano entrambi compatibili per il ripristino in base a SAP. Per informazioni sui tipi di ripristino compatibili, vedere la nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148) .

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [eseguire il backup di database SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) in esecuzione in VM di Azure.
