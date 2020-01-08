---
title: Informazioni sul backup del database SAP HANA nelle macchine virtuali di Azure
description: Questo articolo illustra come eseguire il backup di SAP HANA database in esecuzione in macchine virtuali di Azure.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: aad9e7e89c54100f460a7f348702d0a59e88f519
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479462"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Informazioni sul backup del database SAP HANA nelle macchine virtuali di Azure

SAP HANA database sono carichi di lavoro mission-critical che richiedono un obiettivo del punto di ripristino (RPO) basso e un obiettivo del tempo di ripristino rapido (RTO). È ora possibile [eseguire il backup di SAP Hana database in esecuzione in macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) con [backup di Azure](https://docs.microsoft.com/azure/backup/backup-overview).

Backup di Azure è [Backint Certified](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) by SAP, per offrire supporto per il backup nativo sfruttando le API native di SAP Hana. Questa offerta da backup di Azure è allineata al Mantra di backup di Azure per i backup di **infrastruttura zero** , eliminando la necessità di distribuire e gestire l'infrastruttura di backup. È ora possibile eseguire facilmente il backup e il ripristino di SAP HANA database in esecuzione su macchine virtuali di Azure ([macchine virtuali della serie M](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#m-series) ora supportate) e sfruttare le funzionalità di gestione aziendali fornite da backup di Azure.

## <a name="added-value"></a>Valore aggiunto

L'uso di backup di Azure per eseguire il backup e il ripristino di SAP HANA database offre i vantaggi seguenti:

* **obiettivo del punto di ripristino di 15 minuti (RPO)** : ora è possibile eseguire il ripristino di dati critici fino a 15 minuti.
* Ripristini **temporizzati con un clic**: il ripristino dei dati di produzione in server Hana alternativi è semplificato. Il concatenamento di backup e cataloghi per eseguire i ripristini è tutto gestito da Azure in background.
* **Conservazione a lungo termine**: per esigenze di conformità e controllo rigorose. Conservare i backup per anni, in base al periodo di conservazione, oltre il quale i punti di ripristino verranno eliminati automaticamente dalla funzionalità di gestione del ciclo di vita incorporata.
* **Gestione dei backup da Azure**: usare le funzionalità di gestione e monitoraggio di backup di Azure per migliorare l'esperienza di gestione. È supportata anche l'interfaccia della riga di comando di Azure.

Per visualizzare gli scenari di backup e ripristino attualmente supportati, fare riferimento alla [matrice di supporto dello scenario SAP Hana](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support).

## <a name="backup-architecture"></a>Architettura del backup

![Diagramma dell'architettura di backup](./media/sap-hana-db-about/backup-architecture.png)

* Il processo di backup inizia con la creazione di un insieme di credenziali di [servizi di ripristino](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault) in Azure. Questo insieme di credenziali verrà usato per archiviare i backup e i punti di ripristino creati nel corso del tempo.
* La VM di Azure che esegue SAP HANA server è registrata con l'insieme di credenziali e vengono [individuati](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases)i database da sottoporre a backup. Per consentire al servizio backup di Azure di individuare i database, è necessario eseguire uno [script di preregistrazione](https://aka.ms/scriptforpermsonhana) nel server Hana come utente root.
* Questo script crea un utente del database **AZUREWLBACKUPHANAUSER** e una chiave corrispondente con lo stesso nome in **hdbuserstore**. Per ulteriori informazioni sulle operazioni svolte dallo script, vedere la [sezione Impostazione delle autorizzazioni](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#setting-up-permissions) .
* Il servizio backup di Azure ora installa il plug-in **di backup di Azure per Hana** nel server SAP Hana registrato.
* L'utente di database **AZUREWLBACKUPHANAUSER** creato dallo script di preregistrazione viene usato dal plug-in di **backup di Azure per Hana** per eseguire tutte le operazioni di backup e ripristino. Se si tenta di configurare il backup per SAP HANA database senza eseguire questo script, è possibile che venga visualizzato l'errore seguente: **UserErrorHanaScriptNotRun**.
* Per [configurare il backup](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup) nei database individuati, scegliere i criteri di backup necessari e abilitare i backup.

* Una volta configurato il backup, il servizio backup di Azure imposta i parametri Backint seguenti a livello di DATABASE nel server SAP HANA protetto:
  * [catalog_backup_using_backint: true]
  * [enable_accumulated_catalog_backup: false]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s: 900)]
  * [backint_response_timeout:7200]

>[!NOTE]
>Assicurarsi che questi parametri *non* siano presenti a livello di host. I parametri a livello di host eseguiranno l'override di questi parametri e potrebbero causare un comportamento imprevisto.
>

* Il **plug-in di backup di Azure per Hana** gestisce tutte le pianificazioni e i dettagli dei criteri di backup. Attiva i backup pianificati e comunica con il motore di **backup Hana** tramite le API Backint.
* Il **motore di backup di Hana** restituisce un flusso Backint con i dati di cui eseguire il backup.
* Tutti i backup pianificati e i backup su richiesta (attivati dal portale di Azure) che sono completi o differenziali vengono avviati dal plug-in di **backup di Azure per Hana**. Tuttavia, i backup del log vengono gestiti e attivati dal **motore di backup di Hana** .

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [ripristinare un database di SAP Hana in esecuzione in una macchina virtuale di Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Informazioni su come [gestire i database SAP HANA di cui è stato eseguito il backup con Backup di Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
