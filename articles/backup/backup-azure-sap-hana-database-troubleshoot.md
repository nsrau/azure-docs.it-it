---
title: Risolvere gli errori di backup dei database SAP HANATroubleshoot SAP HANA databases backup errors
description: Viene descritto come risolvere gli errori comuni che potrebbero verificarsi quando si usa Backup di Azure per eseguire il backup dei database SAP HANA.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 6520f106011b632da2725f456aeb278c7748ddc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459311"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Risolvere i problemi relativi al backup dei database SAP HANA in AzureTroubleshoot backup of SAP HANA databases on Azure

Questo articolo fornisce informazioni sulla risoluzione dei problemi per il backup dei database SAP HANA nelle macchine virtuali di Azure.This article provides troubleshooting information for backing up SAP HANA databases on Azure virtual machines. Per ulteriori informazioni sugli scenari di backup SAP HANA attualmente supportati, vedere [Supporto degli scenari](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Prerequisiti e autorizzazioni

Fare riferimento [ai prerequisiti](tutorial-backup-sap-hana-db.md#prerequisites) e alle sezioni Operazioni con lo script di [preregistrazione](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) prima di configurare i backup.

## <a name="common-user-errors"></a>Errori comuni degli utenti

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Messaggio di errore**      | <span style="font-weight:normal">Il backup di Azure non dispone dei privilegi di ruolo necessari per eseguire il backupAzure backup does not have required role privileges to carry out backup</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Possibili cause**    | Il ruolo potrebbe essere stato sovrascritto.                          |
| **Azione consigliata** | Per risolvere il problema, eseguire lo script dal riquadro **Individua database** o scaricarlo [qui](https://aka.ms/scriptforpermsonhana). In alternativa, aggiungere il ruolo 'SAP_INTERNAL_HANA_SUPPORT' all'utente di backup del carico di lavoro (One-WLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Messaggio di errore      | <span style="font-weight:normal">Impossibile connettersi al sistema HANA</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | L'istanza SAP HANA potrebbe essere inattivo.<br/>Le autorizzazioni necessarie per il backup di Azure per interagire con il database HANA non sono impostate. |
| **Azione consigliata** | Verificare se il database SAP HANA è in funzione. Se il database è in esecuzione, verificare se tutte le autorizzazioni necessarie sono impostate. Se manca una delle autorizzazioni, eseguire lo script di [preregistrazione](https://aka.ms/scriptforpermsonhana) per aggiungere le autorizzazioni mancanti. |

### <a name="usererrorhanainstancenameinvalid"></a>UtenteErrorHanaInstanceNameInvalid

| Messaggio di errore      | <span style="font-weight:normal">L'istanza SAP HANA specificata non è valida o non è possibile trovarla</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | Non è possibile eseguire il backup di più istanze SAP HANA in una singola macchina virtuale di Azure.Multiple SAP HANA instances on a single Azure VM can't be backed up. |
| **Azione consigliata** | Eseguire lo [script di preregistrazione](https://aka.ms/scriptforpermsonhana) nell'istanza SAP HANA di cui si desidera eseguire il backup. Se il problema persiste, contattare il supporto tecnico Microsoft.If the issue still persists, contact Microsoft support. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Messaggio di errore      | <span style="font-weight:normal">L'operazione SAP HANA specificata non è supportata</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | Il backup di Azure per SAP HANA non supporta il backup incrementale e le azioni eseguite sui client nativi SAP HANA (Studio/ Cockpit/ DBA Cockpit) |
| **Azione consigliata** | Per ulteriori informazioni, fare riferimento [qui](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UtenteErrorHANAPODoesNotSupportBackupType

| Messaggio di errore      | <span style="font-weight:normal">Questo database SAP HANA non supporta il tipo di backup richiesto</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | Il backup di Azure non supporta il backup incrementale e il backup tramite snapshotAzure backup doesn't support incremental backup and backup using snapshots |
| **Azione consigliata** | Per ulteriori informazioni, fare riferimento [qui](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Messaggio di errore      | <span style="font-weight:normal">La catena del log di backup è interrotta</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | La destinazione di backup del log potrebbe essere stata aggiornata da backint al file system o l'eseguibile backint potrebbe essere stato modificato |
| **Azione consigliata** | Attivare un backup completo per risolvere il problema                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UtenteErrorIncomaptibleSrcTargetSystsemsForRestore

| Messaggio di errore      | <span style="font-weight:normal">I sistemi di origine e di destinazione per il ripristino sono incompatibili</span>    |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | Il sistema di destinazione per il ripristino non è compatibile con |
| **Azione consigliata** | Fare riferimento alla nota SAP [1642148](https://launchpad.support.sap.com/#/notes/1642148) per informazioni sui tipi di ripristino supportati oggi |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Messaggio di errore      | <span style="font-weight:normal">Rilevato aggiornamento da DSC a MDC</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | L'istanza SAP HANA è stata aggiornata da SDC a MDC. I backup avranno esito negativo dopo l'aggiornamento. |
| **Azione consigliata** | Seguire i passaggi elencati nella [sezione Aggiornamento da SAP HANA 1.0 a 2.0](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) per risolvere il problema |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Messaggio di errore      | <span style="font-weight:normal">Rilevata configurazione backint non valida</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Possibili cause**    | I parametri di backup sono specificati in modo non corretto per il backup di AzureThe backing parameters are in correctly specified for Azure backup |
| **Azione consigliata** | Controllare se sono impostati i seguenti parametri (backint):<br/>\*[catalog_backup_using_backint:vero]<br/>\*[enable_accumulated_catalog_backup:false]<br/>\*[parallel_data_backup_backint_channels:1]<br/>\*[log_backup_timeout_s:900)]<br/>\*[backint_response_timeout:7200]<br/>Se in HOST sono presenti parametri basati su backint, rimuoverli. Se i parametri non sono presenti a livello di HOST ma sono stati modificati manualmente a livello di database, ripristinarli ai valori appropriati come descritto in precedenza. In alternativa, eseguire [la protezione di arresto e mantenere](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) i dati di backup dal portale di Azure e quindi selezionare **Riprendi backup**. |

## <a name="restore-checks"></a>Controlli di ripristino

### <a name="single-container-database-sdc-restore"></a>Ripristino del database a contenitore singolo

Prestare attenzione agli input durante il ripristino di un singolo database contenitore (SDC) per HANA in un altro computer SDC. Il nome del database deve essere specificato con lettere minuscole e con "sdc" aggiunto tra parentesi quadre. L'istanza HANA verrà visualizzata in maiuscolo.

Si supponga che venga eseguito il backup di un'istanza HANA SDC "H21". Nella pagina degli elementi di backup il nome dell'elemento di backup verrà visualizzato come **"h21(sdc)".** Se si tenta di ripristinare il database in un altro Dominio SDC di destinazione, ad esempio H11, è necessario fornire gli input seguenti.

![Nome del database SDC ripristinato](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Tenere presente quanto segue:

- Per impostazione predefinita, il nome db ripristinato verrà popolato con il nome dell'elemento di backup. In questo caso, h21(sdc).
- Se si seleziona la destinazione come H11, IL nome db ripristinato non verrà modificato automaticamente. Deve essere modificato in **h11(sdc)**. Per quanto riguarda la DSC, il nome db ripristinato sarà l'ID dell'istanza di destinazione con lettere minuscole e 'sdc' aggiunto tra parentesi quadre.
- Poiché la DSC può avere un solo database, è necessario fare clic sulla casella di controllo per consentire la sostituzione dei dati del database esistente con i dati del punto di ripristino.
- Linux fa distinzione tra maiuscole e minuscole. Quindi fate attenzione a preservare il caso.

### <a name="multiple-container-database-mdc-restore"></a>Ripristino di più database contenitore (MDC)Multiple Container Database (MDC) restore

In più database contenitore per HANA, la configurazione standard è SYSTEMDB - 1 o più DB tenant. Il ripristino di un'intera istanza SAP HANA significa ripristinare sia I database SYSTEMDB che i database tenant. Uno ripristina prima SYSTEMDB e quindi procede per il database tenant. Db di sistema significa essenzialmente sostituire le informazioni di sistema sulla destinazione selezionata. Questo ripristino esegue anche l'override delle informazioni correlate a BackInt nell'istanza di destinazione. Pertanto, dopo il ripristino del database di sistema in un'istanza di destinazione, eseguire nuovamente lo script di pre-registrazione. Solo allora i successivi ripristini del database tenant avranno esito positivo.

## <a name="upgrading-from-sap-hana-10-to-20"></a>Aggiornamento da SAP HANA 1.0 a 2.0

Se si proteggono i database SAP HANA 1.0 e si desidera eseguire l'aggiornamento alla versione 2.0, eseguire la procedura seguente:

- [Arrestare la protezione](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) con conservare i dati per il vecchio database SDC.
- Eseguire l'aggiornamento. Dopo il completamento, l'HANA è ora MDC con un DB di sistema e un tenant DB(s)
- Eseguire nuovamente [lo script di pre-registrazione](https://aka.ms/scriptforpermsonhana) con i dettagli corretti di (sid e mdc).
- Registrare nuovamente l'estensione per lo stesso computer nel portale di Azure (Backup -> visualizzare i dettagli -> selezionare la macchina virtuale di Azure pertinente > eseguire nuovamente la registrazione).
- Fare clic su Riscopri i database dei database per la stessa macchina virtuale. Questa azione dovrebbe mostrare i nuovi DB nel passaggio 2 con i dettagli corretti (SYSTEMDB e Tenant DB, non SDC).
- Configurare il backup per questi nuovi database.

## <a name="upgrading-without-an-sid-change"></a>Aggiornamento senza modifica del SID

Gli aggiornamenti al sistema operativo o SAP HANA che non causano una modifica SID possono essere gestiti come descritto di seguito:

- [Arrestare la protezione](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) con i dati di conservazione per il databaseStop protection with retain data for the database
- Eseguire l'aggiornamento.
- Eseguire nuovamente lo [script di pre-registrazione](https://aka.ms/scriptforpermsonhana). Di solito, abbiamo visto processo di aggiornamento rimuove i ruoli necessari. L'esecuzione dello script di preregistrazione consentirà di verificare tutti i ruoli necessari.
- [Riprendere](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) nuovamente la protezione per il database

## <a name="re-registration-failures"></a>Errori di nuova registrazioneRe-registration failures

Verificare la presenza di uno o più dei seguenti sintomi prima di attivare l'operazione di registrazione di nuovo:

- Tutte le operazioni (ad esempio backup, ripristino e configurazione del backup) hanno esito negativo nella macchina virtuale con uno dei seguenti codici di errore: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidn, WorkloadExtensionDidn, WorkloadExtensionDidn, WorkloadExtensionDidnTQueueMsg**.
- Se nell'area **Stato backup** per l'elemento di backup è visualizzato **Non raggiungibile**, escludere tutte le altre cause che potrebbero avere lo stesso stato:

  - Mancanza di autorizzazione per eseguire operazioni relative al backup nella macchina virtuale
  - La macchina virtuale è in arresto, pertanto i backup non possono essere eseguiti
  - Problemi di rete

Questi sintomi possono verificarsi per uno o più dei seguenti motivi:

- Un'estensione è stata eliminata o disinstallata dal portale.
- La macchina virtuale è stata ripristinata nel tempo tramite il ripristino sul posto del disco.
- La macchina virtuale è stata arrestata per un periodo prolungato, pertanto la configurazione dell'estensione su di essa è scaduta.
- La macchina virtuale è stata eliminata e un'altra macchina virtuale è stata creata con lo stesso nome e nello stesso gruppo di risorse della macchina virtuale eliminata.

Negli scenari precedenti è consigliabile attivare un'operazione di registrazione nella macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare le domande frequenti sul backup dei database SAP HANA nelle macchine virtuali di Azure.Review the [frequently asked questions](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) about backing up SAP HANA databases on Azure VMs.
