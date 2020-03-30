---
title: Informazioni sul backup del database SAP HANA nelle macchine virtuali di AzureAbout SAP HANA database backup in Azure VMs
description: In questo articolo vengono fornite informazioni sul backup dei database SAP HANA in esecuzione nelle macchine virtuali di Azure.In this article, learn about backing up SAP HANA databases that are running on Azure virtual machines.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 52c235c95cea73a0c51c62fcb55f7f711d2eff21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476458"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Informazioni sul backup del database SAP HANA nelle macchine virtuali di AzureAbout SAP HANA database backup in Azure VMs

I database SAP HANA sono carichi di lavoro mission-critical che richiedono un obiettivo del punto di ripristino basso (RPO) e un obiettivo di tempo di ripristino rapido (RTO). È ora possibile eseguire il backup dei [database SAP HANA in esecuzione nelle macchine virtuali](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) di Azure usando Backup di [Azure.](https://docs.microsoft.com/azure/backup/backup-overview)

Backup di Azure è [certificato backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) da SAP, per fornire supporto di backup nativo sfruttando le API native di SAP HANA. Questa offerta di Backup di Azure è in linea con il mantra di Backup di Azure di backup **con infrastruttura zero,** eliminando la necessità di distribuire e gestire l'infrastruttura di backup. È ora possibile eseguire il backup e il ripristino senza problemi dei database SAP HANA in esecuzione nelle macchine virtuali di Azure (macchine virtuali di serie M supportate ora!) e sfruttare le funzionalità di gestione aziendale fornite da Backup di Azure.You can now seamlessly back up and restore SAP HANA databases running on Azure[VMs (M series VMs](../virtual-machines/m-series.md) also supported now!) e sfruttare le funzionalità di gestione aziendale fornite da Azure Backup.

## <a name="added-value"></a>Valore aggiunto

Utilizzando Backup di Azure per eseguire il backup e il ripristino dei database SAP HANA, offrono i vantaggi seguenti:

* Obiettivi punto di **ripristino (RPO) di 15 minuti:** il recupero di dati critici fino a 15 minuti è ora possibile.
* **Ripristini click-in-time**e con un solo clic: il ripristino dei dati di produzione in server HANA alternativi è semplificato. Il concatenamento di backup e cataloghi per eseguire i ripristini è tutto gestito da Azure dietro le quinte.
* **Conservazione a lungo termine**: Per esigenze rigorose di conformità e audit. Conservare i backup per anni, in base alla durata di conservazione, oltre la quale i punti di ripristino verranno eliminati automaticamente dalla funzionalità di gestione del ciclo di vita incorporata.
* **Gestione dei backup da Azure:** usare le funzionalità di gestione e monitoraggio di Backup di Azure per migliorare l'esperienza di gestione. L'interfaccia della riga di comando di Azure è supportata.

Per visualizzare gli scenari di backup e ripristino supportati oggi, fare riferimento alla matrice di [supporto dello scenario SAP HANA](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support).

## <a name="backup-architecture"></a>Architettura del backup

![Diagramma dell'architettura di backup](./media/sap-hana-db-about/backup-architecture.png)

* Il processo di backup inizia creando un insieme di credenziali dei servizi di ripristino in Azure.The backup process begins by creating a [Recovery services vault](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault) in Azure. Questo insieme di credenziali verrà utilizzato per archiviare i backup e i punti di ripristino creati nel tempo.
* La macchina virtuale di Azure che esegue il server SAP HANA viene registrata nell'insieme di credenziali e vengono [individuati](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases)i database di cui eseguire il backup. Per consentire al servizio Backup di Azure di individuare i database, è necessario eseguire uno script di [preregistrazione](https://aka.ms/scriptforpermsonhana) nel server HANA come utente radice.
* Questo script consente di creare un utente DB **di AzureWLBACKUPHANAUSER** e una chiave corrispondente con lo stesso nome in **hdbuserstore**. Fare riferimento alla sezione [Che cosa fa lo script di pre-registrazione](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) per comprendere meglio ciò che lo script fa.
* Il servizio Backup di Azure installa ora il plug-in di Backup di **Azure per HANA** nel server SAP HANA registrato.
* L'utente DB **di AzureWLBACKUPHANAUSER** creato dallo script di preregistrazione viene utilizzato dal plug-in di Backup di **Azure per HANA** per eseguire tutte le operazioni di backup e ripristino. Se si tenta di configurare il backup per SAP HANA DBs senza eseguire questo script, è possibile che venga visualizzato il seguente errore: **UserErrorHanaScriptNotRun**.
* Per [configurare](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup) il backup nei database individuati, scegliere il criterio di backup richiesto e abilitare i backup.

* Dopo aver configurato il backup, il servizio Backup di Azure imposta i seguenti parametri Backint a livello DATABASE nel server SAP HANA protetto:
  * [catalog_backup_using_backint:vero]
  * [enable_accumulated_catalog_backup:false]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s:900)]
  * [backint_response_timeout:7200]

>[!NOTE]
>Assicurarsi che questi parametri *non* siano presenti a livello HOST. I parametri a livello di host eseguiranno l'override di questi parametri e potrebbero causare un comportamento imprevisto.
>

* Il **plug-in di Backup** di Azure per HANA mantiene tutte le pianificazioni di backup e i dettagli dei criteri. Attiva i backup pianificati e comunica con il motore di **backup HANA** tramite le API Backint.
* Il motore di **backup HANA** restituisce un flusso Backint con i dati di cui eseguire il backup.
* Tutti i backup pianificati e i backup su richiesta (attivati dal portale di Azure) completi o differenziali vengono avviati dal plug-in di Backup di **Azure per HANA.** Tuttavia, i backup del log vengono gestiti e attivati dallo stesso **motore di backup HANA.**
* Backup di Azure per SAP HANA, essendo una soluzione certificata BackInt, non dipende dai tipi di disco o VM sottostanti. Il backup viene eseguito da flussi generati da HANA.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Uso del backup della macchina virtuale di Azure con il backup di Azure SAP HANAUsing Azure VM backup with Azure SAP HANA backup

Oltre a usare il backup SAP HANA in Azure che offre il backup e il ripristino a livello di database, è possibile usare la soluzione di backup della macchina virtuale di Azure per eseguire il backup del sistema operativo e dei dischi non di database.

La soluzione di [backup di Azure SAP HANA certificata Backint](#backup-architecture) può essere usata per il backup e il ripristino del database.

[Il backup della macchina virtuale](backup-azure-vms-introduction.md) di Azure può essere usato per eseguire il backup del sistema operativo e di altri dischi non di database. Il backup della macchina virtuale viene eseguito una volta al giorno ed esegue il backup di tutti i dischi (ad eccezione dei **dischi di Write Accelerator (WA)** e **degli Ultradisk).** Poiché è in corso il backup del database usando la soluzione di backup di Azure SAP HANA, è possibile eseguire un backup coerente con i file solo del sistema operativo e dei dischi non di database usando la funzionalità di esclusione del disco, attualmente in anteprima.

>[!NOTE]
> L'uso di script pre-post con il backup della macchina virtuale di Azure consentirà backup coerenti con le app dei volumi di dati del database. Tuttavia, se l'area di registro risiede su dischi WA, l'acquisizione di uno snapshot di questi dischi potrebbe non garantire la coerenza di un'area di log. HANA ha un modo esplicito di generare backup del log per questo motivo esatto. Abilitare lo stesso in SAP HANA e può essere sottoposto a backup usando il backup DI Azure SAP HANA.

Per ripristinare una macchina virtuale che esegue SAP HANA, attenersi alla seguente procedura:

* [Ripristinare una nuova macchina virtuale dal backup della macchina virtuale](backup-azure-arm-restore-vms.md) di Azure dal punto di ripristino più recente. In alternativa, creare una nuova macchina virtuale vuota e collegare i dischi dal punto di ripristino più recente.
* Poiché non viene eseguito il backup dei dischi WA, non vengono ripristinati. Creare dischi WA vuoti e l'area di registro.
* Dopo aver impostato tutte le altre configurazioni (ad esempio IP, nome del sistema e così via), la macchina virtuale viene impostata per ricevere i dati del database dal backup di Azure.After all the other configurations (such as IP, system name, and so on) are set, the VM is set to receive DB data from Azure backup.
* Ripristinare ora il database nella macchina virtuale dal backup del database [HANA di Azure SAP](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) al punto nel tempo desiderato.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [ripristinare un database SAP HANA in esecuzione in una macchina virtuale](https://docs.microsoft.com/azure/backup/sap-hana-db-restore) di AzureLearn how to restore an SAP HANA database running on an Azure VM
* Informazioni su come [gestire i database SAP HANA di cui è stato eseguito il backup con Backup di Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
