---
title: Domande frequenti - Backup di database SAP HANA in VM di Azure
description: In questo articolo è possibile trovare le risposte ad alcune domande comuni sul backup di database SAP HANA tramite il servizio Backup di Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a1d6012ec064b5ec582896ac3484161a6e25f2bf
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659965"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Domande frequenti - Backup di database SAP HANA in VM di Azure

Questo articolo presenta le risposte ad alcune domande comuni sul backup di database SAP HANA tramite il servizio Backup di Azure.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Quanti backup completi sono supportati al giorno?

È supportato un solo backup completo al giorno. Non è possibile eseguire il backup differenziale e il backup completo attivato nello stesso giorno.

### <a name="do-successful-backup-jobs-create-alerts"></a>I processi di backup con esito positivo determinano la creazione di avvisi?

No. I processi di backup con esito positivo non generano avvisi. Gli avvisi vengono inviati solo per i processi di backup con esito negativo. Il comportamento dettagliato degli avvisi del portale è documentato [qui](./backup-azure-monitoring-built-in-monitor.md). Tuttavia, se si è interessati ad avere avvisi anche per i processi riusciti, è possibile usare [monitoraggio di Azure](./backup-azure-monitoring-use-azuremonitor.md).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>È possibile vedere i processi di backup pianificati nel menu Processi di backup?

Il menu Processi di backup visualizza solo processi di backup ad hoc. Per i processi pianificati, usare [Monitoraggio di Azure](./backup-azure-monitoring-use-azuremonitor.md).

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

Vedere [questa sezione](./backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid) della Guida alla risoluzione dei problemi.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>È possibile configurare il backup di HANA in Azure per un IP virtuale (bilanciamento del carico) e non per una macchina virtuale?

Attualmente non è possibile configurare la soluzione in base a un indirizzo IP virtuale da solo. Per eseguire la soluzione è necessaria una macchina virtuale.

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>Come si può spostare un backup su richiesta nel file system locale anziché nell'insieme di credenziali di Azure?

1. Attendere il completamento del backup attualmente in esecuzione nel database desiderato (verificare da studio per il completamento).
1. Disabilitare i backup del log e impostare il backup del catalogo su **File system** per il database desiderato attenendosi alla procedura seguente:
1. Fare doppio clic su **SYSTEMDB** -> **Configurazione** -> **Seleziona database** -> **Filter (log)** (Filtro (log))
    1. Impostare enable_auto_log_backup su **No**
    1. Imposta catalog_backup_using_backint su **false**
1. Eseguire un backup su richiesta (completo/differenziale/incrementale) nel database desiderato e attendere il completamento del backup del catalogo e del backup.
1. Se si desidera spostare anche i backup del log nel file System, impostare enable_auto_log_backup su **Sì**
1. Ripristinare le impostazioni precedenti per consentire il flusso dei backup all'insieme di credenziali di Azure:
    1. Impostare enable_auto_log_backup su **sì**
    1. Imposta catalog_backup_using_backint su **true**

>[!NOTE]
>Lo spostamento dei backup nel file system locale e il nuovo ritorno nell'insieme di credenziali di Azure possono causare un'interruzione della catena di log dei backup del log nell'insieme di credenziali. Verrà avviato un backup completo che, una volta completato correttamente, avvierà il backup dei log.

### <a name="how-can-i-use-sap-hana-backup-with-my-hana-replication-set-up"></a>Come è possibile usare SAP HANA backup con la configurazione della replica HANA?

Attualmente, backup di Azure non è in grado di comprendere la configurazione di un HSR. Ciò significa che i nodi primari e secondari di HSR verranno considerati come due macchine virtuali singole e non correlate. Per prima cosa è necessario configurare il backup nel nodo primario. Quando si verifica un failover, il backup deve essere configurato nel nodo secondario, che ora diventa il nodo primario. Non esiste un failover automatico del backup nell'altro nodo.

Per eseguire il backup dei dati dal nodo attivo (primario) in un determinato momento, è possibile **passare la protezione**  al nodo secondario, che ora diventa primario dopo il failover.

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

Verificare che siano soddisfatti tutti i prerequisiti per il ripristino nell'istanza SAP HANA di destinazione. Per altre informazioni, vedere [Prerequisiti - Ripristinare database SAP HANA in VM di Azure](./sap-hana-db-restore.md#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Perché il ripristino del database con sovrascrittura non riesce?

Assicurarsi che l'opzione **Forza sovrascrittura** sia selezionata durante il ripristino.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Perché viene visualizzato l'errore "I sistemi di origine e destinazione per il ripristino sono incompatibili"?

Per informazioni sui tipi di ripristino attualmente supportati, vedere la nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148).

### <a name="can-i-use-a-backup-of-a-database-running-on-sles-to-restore-to-an-rhel-hana-system-or-vice-versa"></a>È possibile usare un backup di un database in esecuzione su SLES per eseguire il ripristino in un sistema RHEL HANA o viceversa?

Sì, è possibile usare i backup in streaming attivati in un database HANA in esecuzione su SLES per ripristinarlo in un sistema RHEL HANA e viceversa. Ovvero il ripristino tra più sistemi operativi è possibile utilizzando i backup di flusso. Tuttavia, sarà necessario assicurarsi che il sistema HANA in cui si vuole eseguire il ripristino e il sistema HANA usato per il ripristino siano entrambi compatibili per il ripristino in base a SAP. Per informazioni sui tipi di ripristino compatibili, vedere la nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148) .

## <a name="policy"></a>Policy

### <a name="different-options-available-during-creation-of-a-new-policy-for-sap-hana-backup"></a>Opzioni diverse disponibili durante la creazione di un nuovo criterio per il backup SAP HANA

Prima di creare un criterio, è necessario chiarire i requisiti di RPO e RTO e le relative implicazioni relative ai costi.

RPO (Recovery-Point-Objective) indica la quantità di perdita di dati accettabile per l'utente e il cliente. Questa operazione è determinata dalla frequenza di backup del log. Più frequenti backup del log indicano una RPO inferiore e il valore minimo supportato dal servizio backup di Azure è 15 minuti, ovvero la frequenza di backup del log può essere di 15 minuti o superiore.

RTO (Recovery-Time-Objective) indica la velocità con cui i dati devono essere ripristinati fino all'ultimo punto nel tempo disponibile dopo uno scenario di perdita dei dati. Questo dipende dalla strategia di ripristino utilizzata da HANA, che dipende in genere dal numero di file necessari per il ripristino. Questa operazione comporta anche implicazioni di costo e la tabella seguente dovrebbe aiutare a comprendere tutti gli scenari e le relative implicazioni.

|Criteri di backup  |RTO  |Cost  |
|---------|---------|---------|
|Log completi giornalieri +     |   Più veloce perché è necessaria una sola copia completa + log necessari per il ripristino temporizzato      |    Opzione costliest perché una copia completa viene eseguita giornalmente e un numero sempre maggiore di dati viene accumulato nel back-end fino al periodo di conservazione   |
|Totale settimanale + differenziale giornaliera + log     |   Più lenta rispetto all'opzione precedente ma più veloce di quanto riportato di seguito poiché è necessaria una copia completa + una copia differenziale + log per il ripristino temporizzato      |    Opzione meno costosa poiché il differenziale giornaliero è in genere inferiore a completo e una copia completa viene eseguita solo una volta alla settimana      |
|Settimanale completo + + log giornaliero + incrementale     |  Più lento perché è necessaria una copia completa +' n'incrementali + log per il recupero temporizzato       |     Opzione meno costosa poiché il valore incrementale giornaliero sarà minore del differenziale e una copia completa viene eseguita solo settimanalmente    |

> [!NOTE]
> Le opzioni precedenti sono le opzioni più comuni ma non le uniche. Ad esempio, uno può avere un backup completo settimanale + differenziali due volte alla settimana + log.

Quindi, è possibile selezionare la variante dei criteri in base agli obiettivi RPO e RTO e alle considerazioni sui costi.

### <a name="impact-of-modifying-a-policy"></a>Effetti della modifica di un criterio

Per determinare l'effetto del cambio di criteri di un elemento di backup da Policy 1 (P1) a Policy 2 (P2) o di modifica dei criteri 1 (P1), è necessario tenere presenti alcuni principi.

- Tutte le modifiche vengono applicate anche in maniera retroattiva. Il criterio di backup più recente viene applicato anche ai punti di ripristino eseguiti in precedenza. Si supponga, ad esempio, che la conservazione completa giornaliera sia di 30 giorni e che siano stati effettuati 10 punti di ripristino in base ai criteri attualmente attivi. Se la conservazione completa giornaliera viene modificata in 10 giorni, l'ora di scadenza del punto precedente viene ricalcolata anche come ora di inizio + 10 giorni ed eliminata se è scaduta.
- L'ambito di modifica include anche il giorno del backup, il tipo di backup insieme alla conservazione. Ad esempio, se un criterio viene modificato da giornaliero completo a settimanale completo domenica, tutte le versioni precedenti che non sono di domenica verranno contrassegnate per l'eliminazione.
- Un elemento padre non viene eliminato fino a quando l'elemento figlio non è attivo o non è scaduto. Ogni tipo di backup ha una data di scadenza in base ai criteri attualmente attivi. Tuttavia, un tipo di backup completo viene considerato come padre per i successivi ' differenziali ',' incrementali ' è logs '. ' Differenziale ' è log ' non sono padre per altri. Un "incrementale" può essere un elemento padre per "incrementale" successivo. Anche se un elemento ' Parent ' è contrassegnato per l'eliminazione, non viene effettivamente eliminato se il ' differenziale ' o ' logs ' figlio non è scaduto. Se, ad esempio, un criterio viene modificato da giornaliero completo a settimanale completo domenica, tutte le versioni precedenti che non sono di domenica verranno contrassegnate per l'eliminazione. Ma non vengono effettivamente eliminati fino alla scadenza dei log che sono stati rilevati al giorno precedente. In altre parole, vengono mantenuti in base alla durata del log più recente. Una volta che i log scadono, verranno eliminati sia i log che questi completi.

Con questi principi, è possibile leggere la tabella seguente per comprendere le implicazioni di una modifica dei criteri.

|Criterio precedente/nuovo criterio  |Completamenti giornalieri + log  | Completamenti settimanali + differenziali giornalieri + log  |Completamenti settimanali + incrementi giornalieri + log  |
|---------|---------|---------|---------|
|Completamenti giornalieri + log     |   -      |    I precedenti completi che non si trovano nello stesso giorno della settimana sono contrassegnati per l'eliminazione, ma conservati fino al periodo di conservazione del log     |    I precedenti completi che non si trovano nello stesso giorno della settimana sono contrassegnati per l'eliminazione, ma conservati fino al periodo di conservazione del log     |
|Completamenti settimanali + differenziali giornalieri + log     |   Il periodo di conservazione completo settimanale precedente viene ricalcolato in base ai criteri più recenti. I differenziali precedenti vengono eliminati immediatamente      |    -     |    I differenziali precedenti vengono eliminati immediatamente     |
|Completamenti settimanali + incrementi giornalieri + log     |     Il periodo di conservazione completo settimanale precedente viene ricalcolato in base ai criteri più recenti. Gli incrementi precedenti vengono immediatamente eliminati    |     Gli incrementi precedenti vengono immediatamente eliminati    |    -     |

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [eseguire il backup di database SAP HANA](./backup-azure-sap-hana-database.md) in esecuzione in VM di Azure.
