---
title: Informazioni sul backup del database SAP HANA nelle macchine virtuali di Azure
description: Questo articolo illustra come eseguire il backup di SAP HANA database in esecuzione in macchine virtuali di Azure.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: e30510817401fd8db23dc9f1d62fab495fac7ab2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022310"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Informazioni sul backup del database SAP HANA nelle macchine virtuali di Azure

SAP HANA database sono carichi di lavoro mission-critical che richiedono un obiettivo del punto di ripristino (RPO) basso e un obiettivo del tempo di ripristino rapido (RTO). È ora possibile [eseguire il backup di SAP Hana database in esecuzione in macchine virtuali di Azure](./tutorial-backup-sap-hana-db.md) con [backup di Azure](./backup-overview.md).

Backup di Azure è [Backint Certified](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) by SAP, per offrire supporto per il backup nativo sfruttando le API native di SAP Hana. Questa offerta da backup di Azure è allineata al Mantra di backup di Azure per i backup di **infrastruttura zero** , eliminando la necessità di distribuire e gestire l'infrastruttura di backup. È ora possibile eseguire facilmente il backup e il ripristino di SAP HANA database in esecuzione su macchine virtuali di Azure ([macchine virtuali della serie M](../virtual-machines/m-series.md) ora supportate) e sfruttare le funzionalità di gestione aziendali fornite da backup di Azure.

## <a name="added-value"></a>Valore aggiunto

L'uso di backup di Azure per eseguire il backup e il ripristino di SAP HANA database offre i vantaggi seguenti:

* **obiettivo del punto di ripristino di 15 minuti (RPO)**: ora è possibile eseguire il ripristino di dati critici fino a 15 minuti.
* Ripristini **temporizzati con un clic**: il ripristino dei dati di produzione in server Hana alternativi è semplificato. Il concatenamento di backup e cataloghi per eseguire i ripristini è tutto gestito da Azure in background.
* **Conservazione a lungo termine**: per esigenze di conformità e controllo rigorose. Conservare i backup per anni, in base al periodo di conservazione, oltre il quale i punti di ripristino verranno eliminati automaticamente dalla funzionalità di gestione del ciclo di vita incorporata.
* **Gestione dei backup da Azure**: usare le funzionalità di gestione e monitoraggio di backup di Azure per migliorare l'esperienza di gestione. È supportata anche l'interfaccia della riga di comando di Azure.

Per visualizzare gli scenari di backup e ripristino attualmente supportati, fare riferimento alla [matrice di supporto dello scenario SAP Hana](./sap-hana-backup-support-matrix.md#scenario-support).

## <a name="backup-architecture"></a>Architettura del backup

![Diagramma dell'architettura di backup](./media/sap-hana-db-about/backup-architecture.png)

* Il processo di backup inizia con la creazione di un insieme di credenziali di [servizi di ripristino](./tutorial-backup-sap-hana-db.md#create-a-recovery-services-vault) in Azure. Questo insieme di credenziali verrà usato per archiviare i backup e i punti di ripristino creati nel corso del tempo.
* La VM di Azure che esegue SAP HANA server è registrata con l'insieme di credenziali e vengono [individuati](./tutorial-backup-sap-hana-db.md#discover-the-databases)i database da sottoporre a backup. Per consentire al servizio backup di Azure di individuare i database, è necessario eseguire uno [script di preregistrazione](https://aka.ms/scriptforpermsonhana) nel server Hana come utente root.
* Questo script crea un utente del database **AZUREWLBACKUPHANAUSER** e una chiave corrispondente con lo stesso nome in **hdbuserstore**. Per ulteriori informazioni sulle operazioni svolte dallo script, vedere la sezione  [operazioni dello script di pre-registrazione](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) .
* Il servizio backup di Azure ora installa il plug-in **di backup di Azure per Hana** nel server SAP Hana registrato.
* L'utente di database **AZUREWLBACKUPHANAUSER** creato dallo script di preregistrazione viene usato dal plug-in di **backup di Azure per Hana** per eseguire tutte le operazioni di backup e ripristino. Se si tenta di configurare il backup per SAP HANA database senza eseguire questo script, è possibile che venga visualizzato l'errore seguente: **UserErrorHanaScriptNotRun**.
* Per [configurare il backup](./tutorial-backup-sap-hana-db.md#configure-backup) nei database individuati, scegliere i criteri di backup necessari e abilitare i backup.

* Una volta configurato il backup, il servizio backup di Azure imposta i parametri Backint seguenti a livello di DATABASE nel server SAP HANA protetto:
  *  [catalog_backup_using_backint:true]
  *  [enable_accumulated_catalog_backup:false]
  *  [parallel_data_backup_backint_channels:1]
  *  [log_backup_timeout_s:900)]
  *  [backint_response_timeout:7200]

>[!NOTE]
>Assicurarsi che questi parametri *non* siano presenti a livello di host. I parametri a livello di host eseguiranno l'override di questi parametri e potrebbero causare un comportamento imprevisto.
>

* Il **plug-in di backup di Azure per Hana** gestisce tutte le pianificazioni e i dettagli dei criteri di backup. Attiva i backup pianificati e comunica con il motore di **backup Hana** tramite le API Backint.
* Il **motore di backup di Hana** restituisce un flusso Backint con i dati di cui eseguire il backup.
* Tutti i backup pianificati e i backup su richiesta (attivati dal portale di Azure) che sono completi o differenziali vengono avviati dal plug-in di **backup di Azure per Hana**. Tuttavia, i backup del log vengono gestiti e attivati dal **motore di backup di Hana** .
* Backup di Azure per SAP HANA, essendo una soluzione certificata per BackInt, non dipende dai tipi di dischi o macchine virtuali sottostanti. Il backup viene eseguito da flussi generati da HANA.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Uso del backup di macchine virtuali di Azure con Azure SAP HANA backup

Oltre a usare il backup SAP HANA in Azure che fornisce backup e ripristino a livello di database, è possibile usare la soluzione di backup delle macchine virtuali di Azure per eseguire il backup dei dischi del sistema operativo e non di database.

Per il backup e il ripristino del database è possibile usare la [soluzione di backup di Azure SAP Hana certificata Backint](#backup-architecture) .

Il [backup delle VM di Azure](backup-azure-vms-introduction.md) può essere usato per eseguire il backup del sistema operativo e di altri dischi non di database. Il backup della macchina virtuale viene eseguito una volta al giorno e viene eseguito il backup di tutti i dischi, ad eccezione dei dischi **acceleratore di scrittura (WA)** e dei dischi **ultra**. Poiché viene eseguito il backup del database con la soluzione Azure SAP HANA backup, è possibile eseguire un backup coerente con i file solo del sistema operativo e dei dischi non di database usando la funzionalità di [backup e ripristino del disco selettivo per le VM di Azure](selective-disk-backup-restore.md) .

>[!NOTE]
> L'uso degli script pre-post con il backup delle macchine virtuali di Azure consentirà backup coerenti con l'app dei volumi di dati del database. Tuttavia, se l'area del log risiede nei dischi WA, l'esecuzione di uno snapshot di questi dischi potrebbe non garantire la coerenza dell'area di log. HANA dispone di un modo esplicito per generare backup del log per questo motivo esatto. Abilitare lo stesso nell'SAP HANA e il backup può essere eseguito tramite il backup SAP HANA di Azure.

Per ripristinare una macchina virtuale che esegue SAP HANA, attenersi alla procedura seguente:

* [Ripristinare una nuova macchina virtuale dal backup della macchina virtuale di Azure](backup-azure-arm-restore-vms.md) dal punto di ripristino più recente. In alternativa, creare una nuova VM vuota e alleghi i dischi dal punto di ripristino più recente.
* Poiché non viene eseguito il backup dei dischi WA, non vengono ripristinati. Creare dischi WA vuoti e un'area di log.
* Dopo aver impostato tutte le altre configurazioni (ad esempio IP, nome di sistema e così via), la macchina virtuale è impostata in modo da ricevere i dati del database da backup di Azure.
* Ripristinare ora il database nella macchina virtuale dal [backup del database di Azure SAP Hana](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) al momento desiderato.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [ripristinare un database di SAP Hana in esecuzione in una macchina virtuale di Azure](./sap-hana-db-restore.md)
* Informazioni su come [gestire i database SAP HANA di cui è stato eseguito il backup con Backup di Azure](./sap-hana-db-manage.md)
