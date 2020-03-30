---
title: Domande frequenti - Backup di database SAP HANA in VM di Azure
description: In questo articolo vengono fornite le risposte alle domande comuni sul backup dei database SAP HANA usando il servizio Backup di Azure.In this article, discover answers to common questions about backing up SAP HANA databases using the Azure Backup service.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a46c4d6cccc00452a56567880400ef5779e6aed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155393"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Domande frequenti: eseguire il backup dei database SAP HANA nelle macchine virtuali di AzureFrequently asked questions – Back up SAP HANA databases on Azure VMs

Questo articolo risponde alle domande comuni sul backup dei database SAP HANA usando il servizio Backup di Azure.This article answers common questions about backing up SAP HANA databases using the Azure Backup service.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Quanti backup completi sono supportati al giorno?

Supportiamo un solo backup completo al giorno. Non è possibile attivare il backup differenziale e il backup completo nello stesso giorno.

### <a name="do-successful-backup-jobs-create-alerts"></a>I processi di backup con esito positivo determinano la creazione di avvisi?

No. I processi di backup con esito positivo non generano avvisi. Gli avvisi vengono inviati solo per i processi di backup con esito negativo. Il comportamento dettagliato per gli avvisi del portale è documentato [qui](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). Tuttavia, se si è interessati a ricevere avvisi anche per processi riusciti, è possibile usare [Monitoraggio di Azure.](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>È possibile visualizzare i processi di backup pianificati nel menu Processi di backup?

Il menu Processo di backup mostrerà solo i processi di backup ad hoc. Per i processi pianificati, usare [Monitoraggio di Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>I database futuri vengono aggiunti automaticamente per il backup?

No, questa operazione non è attualmente supportata.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Se si elimina un database da un'istanza, cosa accadrà ai backup?

Se un database viene eliminato da un'istanza SAP HANA, i backup del database vengono comunque tentati. Ciò implica che il database eliminato inizia a essere visualizzato come non integro in Elementi di **backup** ed è ancora protetto.
Il modo corretto per interrompere la protezione di questo database consiste nell'eseguire **Interrompi backup con i dati** di eliminazione in questo database.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Se si modifica il nome del database dopo che è stato protetto, quale sarà il comportamento?

Un database rinominato viene considerato come un nuovo database. Pertanto, il servizio tratterà questa situazione come se il database non fosse stato trovato e con esito negativo i backup. Il database rinominato verrà visualizzato come un nuovo database e deve essere configurato per la protezione.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Quali sono i prerequisiti per eseguire il backup dei database SAP HANA in una macchina virtuale di Azure?

Fare riferimento [ai prerequisiti](tutorial-backup-sap-hana-db.md#prerequisites) e alle sezioni [Che cosa fa lo script di pre-registrazione.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Quali autorizzazioni devono essere impostate per il backup dei database SAP HANA da parte di Azure?

L'esecuzione dello script di pre-registrazione imposta le autorizzazioni necessarie per consentire ad Azure di eseguire il backup dei database SAP HANA. Puoi trovare di più su ciò che lo script di pre-registrazione fa [qui](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>I backup funzioneranno dopo la migrazione di SAP HANA dalla 1.0 alla 2.0?

Fare riferimento a [questa sezione](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) della guida alla risoluzione dei problemi.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>È possibile configurare Azure HANA Backup con un IP virtuale (bilanciamento del carico) e non con una macchina virtuale?

Attualmente non abbiamo la capacità di impostare la soluzione contro un IP virtuale da solo. Abbiamo bisogno di una macchina virtuale per eseguire la soluzione.

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>Si dispone di una replica di sistema SAP HANA (HSR), come è necessario configurare il backup per questa configurazione?

I nodi primario e secondario dell'HSR verranno considerati come due singole macchine virtuali non correlate. È necessario configurare il backup nel nodo primario e, quando si verifica il failover, è necessario configurare il backup nel nodo secondario (che ora diventa il nodo primario). Non esiste alcun "failover" automatico del backup nell'altro nodo.

## <a name="restore"></a>Restore

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Perché non è possibile visualizzare il sistema HANA in cui si desidera ripristinare il database?

Verificare se tutti i prerequisiti per il ripristino nell'istanza SAP HANA di destinazione sono soddisfatti. Per altre informazioni, vedere Prerequisiti - Ripristinare i database SAP HANA nella macchina virtuale di Azure.For more information, see [Prerequisites - Restore SAP HANA databases in Azure VM](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Perché il ripristino del database di sovrascrittura non riesce per il database?

Assicurarsi che l'opzione **Forza sovrascrittura** sia selezionata durante il ripristino.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Perché viene visualizzato l'errore "I sistemi di origine e di destinazione per il ripristino non sono compatibili"?

Fare riferimento alla nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148) per vedere quali tipi di ripristino sono attualmente supportati.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come eseguire il backup dei database SAP HANA in esecuzione nelle macchine virtuali di Azure.Learn how to [back up SAP HANA databases](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) running on Azure VMs.
