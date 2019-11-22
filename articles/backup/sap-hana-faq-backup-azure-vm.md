---
title: Domande frequenti-eseguire il backup di database SAP HANA nelle VM di Azure
description: In questo articolo vengono fornite le risposte alle domande più comuni sul backup dei database di SAP HANA con il servizio backup di Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: e5684024668103ccbe13be4af3d7a9336651df77
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287840"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Domande frequenti: eseguire il backup di database SAP HANA nelle VM di Azure

Questo articolo risponde a domande comuni sul backup di SAP HANA database usando il servizio backup di Azure.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Quanti backup completi sono supportati al giorno?

È supportato un solo backup completo al giorno. Non è possibile eseguire il backup differenziale e il backup completo attivato nello stesso giorno.

### <a name="do-successful-backup-jobs-create-alerts"></a>I processi di backup con esito positivo determinano la creazione di avvisi?

No. I processi di backup con esito positivo non generano avvisi. Gli avvisi vengono inviati solo per i processi di backup con esito negativo. Il comportamento dettagliato per gli avvisi del portale è illustrato [qui](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). Tuttavia, se si è interessati ad avere avvisi anche per i processi riusciti, è possibile usare [monitoraggio di Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>È possibile visualizzare i processi di backup pianificati nel menu processi di backup?

Il menu processo di backup mostrerà solo i processi di backup ad hoc. Per i processi pianificati, usare [monitoraggio di Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>I database futuri vengono aggiunti automaticamente per il backup?

No, questa operazione non è attualmente supportata.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Se si elimina un database da un'istanza, cosa accadrà ai backup?

Se un database viene eliminato da un'istanza di SAP HANA, vengono comunque tentati i backup del database. Ciò implica che il database eliminato inizia a essere visualizzato come non integro in **elementi di backup** ed è ancora protetto.
Il modo corretto per arrestare la protezione di questo database consiste nell'eseguire l' **arresto del backup con Elimina dati** in questo database.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Se si modifica il nome del database dopo che è stato protetto, quale sarà il comportamento?

Un database rinominato viene considerato come un nuovo database. Il servizio considererà pertanto questa situazione come se il database non venisse trovato e con i backup con esito negativo. Il database rinominato verrà visualizzato come un nuovo database e deve essere configurato per la protezione.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Quali sono i prerequisiti per eseguire il backup dei database SAP HANA in una macchina virtuale di Azure?

Vedere le sezioni [prerequisiti](tutorial-backup-sap-hana-db.md#prerequisites) e [impostazione delle autorizzazioni](tutorial-backup-sap-hana-db.md#setting-up-permissions) .

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Quali autorizzazioni devono essere impostate per consentire ad Azure di eseguire il backup SAP HANA database?

L'esecuzione dello script di pre-registrazione consente di impostare le autorizzazioni necessarie per consentire ad Azure di eseguire il backup dei database SAP HANA. Altre informazioni sulle autorizzazioni sono disponibili [qui](tutorial-backup-sap-hana-db.md#setting-up-permissions).

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>Il backup del lavoro viene eseguito dopo la migrazione SAP HANA da 1,0 a 2,0?

Fare riferimento a [questa sezione](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) della Guida alla risoluzione dei problemi.

## <a name="restore"></a>Ripristino

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Perché non è possibile visualizzare il sistema HANA a cui si vuole ripristinare il database?

Verificare che siano soddisfatti tutti i prerequisiti per l'istanza di ripristino per SAP HANA di destinazione. Per altre informazioni, vedere [prerequisiti-ripristinare database di SAP Hana in una macchina virtuale di Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Perché la sovrascrittura del ripristino del database non riesce per il database?

Verificare che l'opzione **forza sovrascrittura** sia selezionata durante il ripristino.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Perché viene visualizzato l'errore "i sistemi di origine e di destinazione per il ripristino sono incompatibili"?

Per informazioni sui tipi di ripristino attualmente supportati, vedere la nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148) .

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [eseguire il backup di SAP Hana database](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) in esecuzione in macchine virtuali di Azure.
