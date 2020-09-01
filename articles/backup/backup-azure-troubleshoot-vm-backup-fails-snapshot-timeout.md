---
title: Risolvere i problemi relativi a agenti ed estensioni
description: Sintomi, cause e soluzioni per i problemi di Backup di Azure correlati all'agente, all'estensione e ai dischi.
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: e5fc26231cc5d3ad412371c2f8c187b2d0033ee4
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182038"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Risolvere i problemi di Backup di Azure: problemi relativi all'agente o all'estensione

Questo articolo illustra le procedure di risoluzione dei problemi che possono essere utili per risolvere gli errori di Backup di Azure correlati alla comunicazione con l'agente di macchine virtuali e all'estensione.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="step-by-step-guide-to-troubleshoot-backup-failures"></a>Guida dettagliata per risolvere gli errori di backup

Gli errori di backup più comuni possono essere risolti autonomamente seguendo i passaggi di risoluzione dei problemi elencati di seguito:

### <a name="step-1-check-azure-vm-health"></a>Passaggio 1: controllare l'integrità delle macchine virtuali di Azure

- **Verificare che lo stato di provisioning della macchina virtuale di Azure sia ' running '**: se lo stato di [provisioning della macchina virtuale](../virtual-machines/states-lifecycle.md#provisioning-states) è in stato di **arresto/deallocazione/aggiornamento** , interferisce con l'operazione di backup. Aprire *portale di Azure > vm > Overview >* e controllare lo stato della macchina virtuale per verificare che sia **in esecuzione**  e ripetere l'operazione di backup.
- **Esaminare gli aggiornamenti o i riavvii del sistema operativo in sospeso**: assicurarsi che non ci siano aggiornamenti del sistema operativo in sospeso o riavvii in sospeso nella macchina virtuale.

### <a name="step-2-check-azure-vm-guest-agent-service-health"></a>Passaggio 2: controllare l'integrità del servizio agente guest della macchina virtuale di Azure

- **Verificare che il servizio agente guest di macchine virtuali di Azure sia avviato e aggiornato**:
  - In una macchina virtuale Windows:
    - Passare a **Services. msc** e verificare che il **servizio agente guest della macchina virtuale di Microsoft Azure** sia in esecuzione. Verificare inoltre che sia installata la [versione più recente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) . Per altre informazioni, vedere [problemi relativi all'agente guest della macchina virtuale Windows](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms).
    - L'agente di macchine virtuali di Azure viene installato per impostazione predefinita in qualsiasi VM Windows distribuita da un'immagine di Azure Marketplace dal portale, da PowerShell, dall'interfaccia della riga di comando o da un modello di Azure Resource Manager. Quando si crea un'immagine di macchina virtuale personalizzata distribuita in Azure, potrebbe essere necessaria un' [installazione manuale dell'agente](../virtual-machines/extensions/agent-windows.md#manual-installation) .
    - Esaminare la matrice di supporto per verificare se la macchina virtuale è in esecuzione nel [sistema operativo Windows supportato](backup-support-matrix-iaas.md#operating-system-support-windows).
  - Nella VM Linux,
    - Verificare che il servizio agente guest della macchina virtuale di Azure sia in esecuzione eseguendo il comando `ps-e` . Verificare inoltre che sia installata la [versione più recente](../virtual-machines/extensions/update-linux-agent.md) . Per altre informazioni, vedere [problemi relativi all'agente guest della macchina virtuale Linux](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms).
    - Verificare che le [dipendenze dell'agente VM Linux nei pacchetti di sistema](../virtual-machines/extensions/agent-linux.md#requirements) abbiano la configurazione supportata. Ad esempio: la versione di Python supportata è 2,6 e successive.
    - Esaminare la matrice di supporto per verificare se la macchina virtuale è in esecuzione nel [sistema operativo Linux supportato.](backup-support-matrix-iaas.md#operating-system-support-linux)

### <a name="step-3-check-azure-vm-extension-health"></a>Passaggio 3: controllare l'integrità dell'estensione VM di Azure

- **Assicurarsi che tutte le estensioni di macchina virtuale di Azure siano nello stato "provisioning riuscito"**: se un'estensione è in stato di errore, può interferire con il backup.
- *Aprire portale di Azure > impostazioni > VM > estensioni > stato estensioni* e verificare se tutte le estensioni sono in stato di **provisioning riuscito** .
- Verificare che tutti i [problemi di estensione](../virtual-machines/extensions/overview.md#troubleshoot-extensions) vengano risolti e ripetere l'operazione di backup.
- **Verificare che l'applicazione di sistema com+** sia attiva e in esecuzione. Inoltre, il **servizio di Distributed Transaction Coordinator** dovrebbe essere in esecuzione come **account del servizio di rete**. Eseguire la procedura descritta in questo articolo per risolvere i problemi relativi a [com+ e MSDTC](backup-azure-vms-troubleshoot.md#extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error).

### <a name="step-4-check-azure-backup-vm-extension-health"></a>Passaggio 4: controllare lo stato dell'estensione della VM di backup di Azure

Backup di Azure usa l'estensione di snapshot VM per eseguire un backup coerente con l'applicazione della macchina virtuale di Azure. Backup di Azure installerà l'estensione come parte del primo backup pianificato attivato dopo l'abilitazione del backup.

- Verificare che l' **estensione VMSnapshot non sia in uno stato di errore**: seguire i passaggi elencati in questa [sezione](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) per verificare che l'estensione di backup di Azure sia integra.

- **Controllare se l'estensione antivirus sta bloccando l'estensione**: alcuni software antivirus possono impedire l'esecuzione delle estensioni.
  
  Al momento dell'errore di backup, verificare se sono presenti voci di log nei ***registri applicazioni Visualizzatore eventi*** con ***nome dell'applicazione con errori: IaaSBcdrExtension.exe***. Se vengono visualizzate le voci, potrebbe trattarsi dell'antivirus configurato nella macchina virtuale che limita l'esecuzione dell'estensione di backup. Eseguire un test escludendo le directory seguenti nella configurazione dell'antivirus e ripetere l'operazione di backup.
  - `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
  - `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

- **Controllare se è necessario l'accesso alla rete**: i pacchetti di estensione vengono scaricati dal repository dell'estensione di archiviazione di Azure e i caricamenti dello stato dell'estensione vengono inseriti in archiviazione di Azure. [Altre informazioni](../virtual-machines/extensions/features-windows.md#network-access).
  - Se si usa una versione non supportata dell'agente, è necessario consentire l'accesso in uscita ad archiviazione di Azure in tale area dalla macchina virtuale.
  - Se è stato bloccato l'accesso a `168.63.129.16` tramite il firewall Guest o con un proxy, le estensioni avranno esito negativo indipendentemente da quanto sopra. Sono necessarie le porte 80, 443 e 32526, [altre informazioni](../virtual-machines/extensions/features-windows.md#network-access).

- **Verificare che DHCP sia abilitato all'interno della VM guest**: questa operazione è necessaria per ottenere l'indirizzo dell'host o dell'infrastruttura da DHCP affinché il backup della macchina virtuale IaaS funzioni. Se è necessario un indirizzo IP privato statico, è necessario configurarlo tramite il portale di Azure o PowerShell e assicurarsi che l'opzione DHCP all'interno della VM sia abilitata. [altre informazioni](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken).

- **Verificare che il servizio VSS Writer sia attivo e in esecuzione**: seguire questa procedura per [risolvere i problemi VSS Writer](backup-azure-vms-troubleshoot.md#extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state).
- **Seguire**le procedure consigliate per il backup: esaminare le [procedure consigliate per abilitare il backup delle macchine virtuali di Azure](backup-azure-vms-introduction.md#best-practices).
- **Esaminare le linee guida per i dischi crittografati**: se si Abilita il backup per le macchine virtuali con dischi crittografati, assicurarsi di avere fornito tutte le autorizzazioni necessarie. Per altre informazioni, vedere [backup e ripristino di macchine virtuali crittografate di Azure](backup-azure-vms-encryption.md).

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable: l'agente di macchine virtuali non riesce a comunicare con Backup di Azure

**Codice di errore**: UserErrorGuestAgentStatusUnavailable <br>
**Messaggio di errore**: l'agente di macchine virtuali non riesce a comunicare con backup di Azure<br>

L'agente di macchine virtuali di Azure potrebbe essere arrestato, obsoleto, in uno stato incoerente o non installato. Questi stati impediscono al servizio backup di Azure di attivare snapshot.

- **Aprire portale di Azure > > VM impostazioni > riquadro proprietà** > verificare che **lo stato** della macchina virtuale sia **in esecuzione** e che **lo stato dell'agente** sia **pronto**. Se l'agente di macchine virtuali è stato arrestato o è in uno stato incoerente, riavviare l'agente<br>
  - Per le macchine virtuali Windows, attenersi alla [procedura seguente](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) per riavviare l'agente guest.<br>
  - Per le macchine virtuali Linux, attenersi alla [procedura seguente](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) per riavviare l'agente guest.
- **Aprire portale di Azure > impostazioni > VM > estensioni** > assicurarsi che tutte le estensioni siano in stato di **provisioning completato** . In caso contrario, attenersi alla [seguente procedura](#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) per risolvere il problema.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - Non è stato possibile comunicare con l'agente della macchina virtuale per lo stato dello snapshot

**Codice di errore**: GuestAgentSnapshotTaskStatusError<br>
**Messaggio di errore**: Could not communicate with the VM agent for snapshot status (Non è possibile comunicare con l'agente delle VM per lo stato dello snapshot) <br>

Dopo aver registrato e pianificato una macchina virtuale per il servizio backup di Azure, il backup avvia il processo comunicando con l'estensione di backup della macchina virtuale per eseguire uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Se lo snapshot non viene attivato, può verificarsi un errore di backup. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:  

**Causa 1: [L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Causa 2: [L'agente installato nella macchina virtuale non è aggiornato (per le macchine virtuali Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Causa 3: [Non è possibile recuperare lo stato dello snapshot o acquisire uno snapshot](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Motivo 4: le [Opzioni di configurazione dell'agente VM non sono impostate (per VM Linux)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

**Causa 5: [la soluzione di controllo delle applicazioni sta bloccando IaaSBcdrExtension.exe](#application-control-solution-is-blocking-iaasbcdrextensionexe)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed: lo stato di provisioning della macchina virtuale è Non riuscito

**Codice di errore**: UserErrorVmProvisioningStateFailed<br>
**Messaggio di errore**: lo stato di provisioning della macchina virtuale non è riuscito<br>

Questo errore si verifica quando uno degli errori di estensione inserisce la macchina virtuale in stato di errore di provisioning.<br>**Aprire portale di Azure > impostazioni > VM > estensioni > stato estensioni** e verificare se tutte le estensioni sono in stato di **provisioning riuscito** . Per altre informazioni, vedere [Stati di provisioning](../virtual-machines/states-lifecycle.md#provisioning-states).

- Se lo stato dell'estensione VMSnapshot è failed, fare clic con il pulsante destro del mouse sull'estensione non riuscita e rimuoverlo. Attivare un backup su richiesta. Questa azione reinstallerà le estensioni ed eseguirà il processo di backup.  <br>
- Se un'altra estensione si trova in uno stato di errore, può interferire con il backup. Verificare che i problemi relativi all'estensione vengano risolti e ripetere l'operazione di backup.
- Se lo stato di provisioning della macchina virtuale è in stato di aggiornamento, può interferire con il backup. Verificare che sia integro e ripetere l'operazione di backup.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - È stato raggiunto il limite massimo di raccolte di punti di ripristino

**Codice di errore**: UserErrorRpCollectionLimitReached <br>
**Messaggio di errore**: The Restore Point collection max limit has reached (È stato raggiunto il limite massimo di raccolte di punti di ripristino) <br>

- Questo problema può verificarsi se è presente un blocco sul gruppo di risorse del punto di ripristino che impedisce la pulizia automatica dei punti di ripristino.
- Questo problema può verificarsi anche se vengono attivate più operazioni di backup al giorno. Attualmente si consiglia un solo backup al giorno, perché i punti di ripristino istantaneo vengono conservati per 1-5 giorni per la conservazione dello snapshot configurato e solo 18 il RPs istantaneo può essere associato a una macchina virtuale in un determinato momento. <br>
- Il numero di punti di ripristino tra le raccolte di punti di ripristino e i gruppi di risorse per una macchina virtuale non può superare i 18. Per creare un nuovo punto di ripristino, eliminare i punti di ripristino esistenti.

Azione consigliata:<br>
Per risolvere il problema, rimuovere il blocco sul gruppo di risorse della macchina virtuale e ripetere l'operazione per attivare la pulizia.
> [!NOTE]
> Il servizio di backup crea un gruppo di risorse distinto da quello della macchina virtuale per archiviare la raccolta di punti di ripristino. Si consiglia di non bloccare il gruppo di risorse creato per l'uso da parte del servizio di backup. Il formato di denominazione del gruppo di risorse creato dal servizio di backup è: AzureBackupRG_ `<Geo>` _ `<number>` . Ad esempio: *AzureBackupRG_northeurope_1*

**Passaggio 1: [Rimuovere il blocco dal gruppo di risorse dei punti di ripristino](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Passaggio 2: [Pulire la raccolta di punti di ripristino](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - Il servizio Backup non dispone delle autorizzazioni sufficienti per accedere all'insieme di credenziali delle chiavi per il backup di macchine virtuali crittografate

**Codice di errore**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Messaggio di errore**: Backup doesn't have sufficient permissions to the key vault for backup of encrypted VMs (Il servizio Backup non dispone delle autorizzazioni sufficienti per accedere all'insieme di credenziali delle chiavi per il backup di macchine virtuali crittografate). <br>

Per eseguire un'operazione di backup nelle VM crittografate, è necessario disporre delle autorizzazioni per accedere all'insieme di credenziali delle chiavi. Le autorizzazioni possono essere impostate tramite il [portale di Azure](./backup-azure-vms-encryption.md) o tramite [PowerShell](./backup-azure-vms-automation.md#enable-protection).

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - Operazione di creazione snapshot non riuscita a causa dell'assenza della connettività di rete nella macchina virtuale

**Codice di errore**: ExtensionSnapshotFailedNoNetwork<br>
**Messaggio di errore**: Snapshot operation failed due to no network connectivity on the virtual machine (Operazione di creazione snapshot non riuscita a causa dell'assenza della connettività di rete nella macchina virtuale)<br>

Dopo aver registrato e pianificato una macchina virtuale per il servizio backup di Azure, il backup avvia il processo comunicando con l'estensione di backup della macchina virtuale per eseguire uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Se lo snapshot non viene attivato, può verificarsi un errore di backup. Completare il passaggio seguente per la risoluzione dei problemi, quindi ripetere l'operazione:

**[Non è possibile recuperare lo stato dello snapshot oppure non è possibile creare uno snapshot](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks: il funzionamento dell'estensione VMSnapshot non è riuscita

**Codice di errore**: ExtensionOperationFailedForManagedDisks <br>
**Messaggio di errore**: VMSnapshot extension operation failed (Operazione dell'estensione VMSnapshot non riuscita)<br>

Dopo aver registrato e pianificato una macchina virtuale per il servizio backup di Azure, il backup avvia il processo comunicando con l'estensione di backup della macchina virtuale per eseguire uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Se lo snapshot non viene attivato, può verificarsi un errore di backup. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:  
**Causa 1: [Non è possibile recuperare lo stato dello snapshot o acquisire uno snapshot](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 3: [L'agente installato nella macchina virtuale non è aggiornato (per le macchine virtuali Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 - Il backup non è riuscito generando un errore interno

**Codice errore**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Messaggio di errore**: Backup failed with an internal error - Please retry the operation in a few minutes (Errore interno di backup. Attendere qualche minuto prima di ripetere l'operazione) <br>

Dopo la registrazione e la pianificazione di una macchina virtuale per il servizio Backup di Azure, tale servizio avvia il processo comunicando con l'estensione di backup della macchina virtuale per la creazione di uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Se lo snapshot non viene attivato, può verificarsi un errore di backup. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:  
**Causa 1: [L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 2: [L'agente installato nella macchina virtuale non è aggiornato (per le macchine virtuali Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [Non è possibile recuperare lo stato dello snapshot o acquisire uno snapshot](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 4: [il servizio di backup non dispone dell'autorizzazione per eliminare i punti di ripristino precedenti a causa di un blocco del gruppo di risorse](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize: le dimensioni del disco configurate non sono attualmente supportate da backup di Azure

**Codice di errore**: UserErrorUnsupportedDiskSize <br>
**Messaggio di errore**: le dimensioni del disco configurate non sono attualmente supportate da backup di Azure. <br>

L'operazione di backup potrebbe non riuscire quando si esegue il backup di una macchina virtuale con una dimensione del disco superiore a 32 TB. Inoltre, il backup dei dischi crittografati di dimensioni superiori a 4 TB non è attualmente supportato. Verificare che le dimensioni del disco siano inferiori o uguali al limite supportato suddividendo il disco o i dischi.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - Impossibile avviare il backup perché è attualmente in corso un'altra operazione di backup

**Codice di errore**: UserErrorBackupOperationInProgress <br>
**Messaggio di errore**: Impossibile avviare il backup perché è attualmente in corso un'altra operazione di backup<br>

Il processo di backup recente non è riuscito perché è in corso un processo di backup esistente. Non è possibile avviare un nuovo processo di backup fino al termine del processo corrente. Verificare che l'operazione di backup attualmente in corso sia stata completata prima di attivare o pianificare altre operazioni di backup. Per controllare lo stato dei processi di backup, attenersi alla procedura seguente:

1. Accedere alla portale di Azure e selezionare **tutti i servizi**. Digitare servizi di ripristino e selezionare insiemi di credenziali **dei servizi di ripristino**. Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino.
2. Nell'elenco degli insiemi di credenziali di servizi di ripristino selezionare un insieme di credenziali in cui è configurato il backup.
3. Nel menu del dashboard dell'insieme di credenziali selezionare **processi di backup** per visualizzare tutti i processi di backup.
   - Se è in corso un processo di backup, attenderne il completamento o annullarlo.
     - Per annullare il processo di backup, fare clic con il pulsante destro del mouse sul processo di backup e scegliere **Annulla** o usa [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob).
   - Se il backup è stato riconfigurato in un insieme di credenziali diverso, assicurarsi che non siano presenti processi di backup in esecuzione nell'insieme di credenziali precedente. Se esiste, annullare il processo di backup.
     - Per annullare il processo di backup, fare clic con il pulsante destro del mouse sul processo di backup e scegliere **Annulla** o usa [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) .
4. Ripetere l'operazione di backup.

Se l'operazione di backup pianificato richiede più tempo, in conflitto con la configurazione del backup successiva, esaminare le [procedure](backup-azure-vms-introduction.md#best-practices)consigliate, le [prestazioni di backup](backup-azure-vms-introduction.md#backup-performance)e la considerazione del [ripristino](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError: il backup non è riuscito a causa di un errore. Per informazioni dettagliate, vedere i dettagli del messaggio di errore del processo

**Codice di errore**: UserErrorCrpReportedUserError <br>
**Messaggio di errore**: backup non riuscito a causa di un errore. Per informazioni dettagliate, vedere dettagli del messaggio di errore del processo.

Questo errore viene segnalato dalla macchina virtuale IaaS. Per identificare la causa principale del problema, passare alle impostazioni dell'insieme di credenziali di servizi di ripristino. Nella sezione **monitoraggio** selezionare processi di **backup** per filtrare e visualizzare lo stato. Selezionare **errori** per esaminare i dettagli del messaggio di errore sottostante. Eseguire ulteriori azioni in base alle indicazioni contenute nella pagina dei dettagli dell'errore.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent-backup non riuscito: questa macchina virtuale non è (attivamente) protetta da backup di Azure

**Codice di errore**: UserErrorBcmDatasourceNotPresent <br>
**Messaggio di errore**: backup non riuscito: questa macchina virtuale non è (attivamente) protetta da backup di Azure.

Verificare che la macchina virtuale specificata sia attiva (non in stato di sospensione) protetta da backup di Azure. Per ovviare a questo problema, verificare che la macchina virtuale sia attiva, quindi ripetere l'operazione.

## <a name="causes-and-solutions"></a>Cause e soluzioni

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)

#### <a name="solution-for-this-error"></a>Soluzione per questo errore

L'agente di macchine virtuali può essere danneggiato o il servizio può essere stato arrestato. Reinstallando l'agente di macchine virtuali è possibile ottenere la versione più recente. In questo modo sarà anche possibile riavviare la comunicazione con il servizio.

1. Determinare se il servizio agente guest di Microsoft Azure è in esecuzione nei servizi delle macchine virtuali (services.msc). Provare a riavviare il servizio agente guest di Microsoft Azure e avviare il backup.
2. Se il servizio agente guest di Microsoft Azure non è visibile tra i servizi, nel Pannello di controllo passare a **Programmi e funzionalità** per controllare se è installato.
3. Se il servizio agente guest di Microsoft Azure è elencato in **Programmi e funzionalità**, disinstallarlo.
4. Scaricare e installare la [versione più recente del file MSI dell'agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione sono necessari i diritti di amministratore.
5. Verificare che il servizio agente guest di Microsoft Azure sia visualizzato tra i servizi.
6. Eseguire un backup su richiesta:
   - Nel portale selezionare **Esegui backup ora**.

Verificare anche che [Microsoft .NET 4.5 sia installato](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nella macchina virtuale. .NET 4.5 è necessario per la comunicazione dell'agente di macchine virtuali con il servizio.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>L'agente installato nella VM Linux non è aggiornato (per VM Linux)

#### <a name="solution"></a>Soluzione

La maggior parte degli errori relativi ad agenti o estensioni nelle macchine virtuali Linux è dovuta a problemi correlati ad agenti di macchine virtuali non aggiornati. Per risolvere il problema, seguire queste indicazioni generali:

1. Seguire le istruzioni per l'[aggiornamento dell'agente di macchine virtuali Linux](../virtual-machines/extensions/update-linux-agent.md).

   > [!NOTE]
   > È *fortemente consigliato* aggiornare l'agente solo tramite un repository di distribuzione. Non è consigliabile scaricare il codice dell'agente direttamente da GitHub e aggiornarlo. Se l'agente più recente per la distribuzione non è disponibile, contattare il supporto per la distribuzione per istruzioni su come installarlo. Per cercare l'agente più recente, passare alla pagina dell'[agente Linux di Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) nel repository GitHub.

2. Assicurarsi che l'agente di Azure sia in esecuzione nella macchina virtuale eseguendo il comando seguente: `ps -e`

   Se il processo non è in esecuzione, riavviarlo usando i comandi seguenti:

   - Per Ubuntu: `service walinuxagent start`
   - Per altre distribuzioni: `service waagent start`

3. [Configurare l'agente per il riavvio automatico](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Eseguire un nuovo backup di prova. Se l'errore persiste, raccogliere i log seguenti dalla macchina virtuale:

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

Se è necessaria la registrazione dettagliata per waagent, attenersi alla procedura seguente:

1. Nel file /etc/waagent.conf individuare la riga seguente: **Enable verbose logging (y|n)**
2. Modificare il valore di **Logs.Verbose** da *n* a *y*.
3. Salvare la modifica e quindi riavviare waagent seguendo la procedura descritta in precedenza in questa sezione.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>Le opzioni di configurazione dell'agente VM non sono impostate (per le macchine virtuali Linux)

Un file di configurazione (/etc/waagent.conf) controlla le azioni dell'agente waagent. Estensioni per le opzioni del file di configurazione **. abilitare** deve essere impostato su **y** e sul **provisioning. Agent** deve essere impostato su **auto** per consentire il funzionamento del backup.
Per un elenco completo delle opzioni del file di configurazione dell'agente VM, vedere <https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="application-control-solution-is-blocking-iaasbcdrextensionexe"></a>La soluzione di controllo delle applicazioni sta bloccando IaaSBcdrExtension.exe

Se si esegue [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/what-is-applocker) (o un'altra soluzione di controllo delle applicazioni) e le regole sono basate su server di pubblicazione o percorso, possono impedire l'esecuzione del **IaaSBcdrExtension.exe** eseguibile.

#### <a name="solution-to-this-issue"></a>Soluzione per questo problema

Escludere il `/var/lib` percorso o l'eseguibile **IaaSBcdrExtension.exe** da AppLocker (o da un altro software di controllo delle applicazioni).

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Non è possibile recuperare lo stato dello snapshot o acquisire uno snapshot

Il backup delle macchine virtuali si basa sull'esecuzione del comando di snapshot sull'account di archiviazione sottostante. Il backup può avere esito negativo perché non ha accesso all'account di archiviazione o perché l'esecuzione dell'attività di snapshot è stata ritardata.

#### <a name="solution-for-this-issue"></a>Soluzione per questo problema

Le condizioni seguenti possono causare errori dell'attività di snapshot:

| Causa | Soluzione |
| --- | --- |
| Lo stato della macchina virtuale viene segnalato in modo non corretto perché la macchina virtuale viene arrestata in RDP (Remote Desktop Protocol). | Se si arresta la macchina virtuale in RDP, controllare il portale per determinare se lo stato della macchina virtuale è corretto. Se non è corretto, arrestare la VM nel portale usando l'opzione **Shutdown** nel dashboard della macchina virtuale. |
| La macchina virtuale non riesce a ottenere l'indirizzo dell'host o dell'infrastruttura da DHCP. | DHCP deve essere abilitato nel computer guest per consentire il funzionamento del backup delle VM IaaS. Se la macchina virtuale non riesce a ottenere l'indirizzo dell'host o dell'infrastruttura da DHCP, con risposta 245, non è possibile scaricare o eseguire le estensioni. Se è necessario un indirizzo IP privato statico, è necessario configurarlo tramite il **portale di Azure** o **PowerShell** e assicurarsi che l'opzione DHCP all'interno della VM sia abilitata. [Altre](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) informazioni sulla configurazione di un indirizzo IP statico con PowerShell.

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>Rimuovere il blocco dal gruppo di risorse dei punti di ripristino

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare a **tutte le risorse**, selezionare il gruppo di risorse raccolta punti di ripristino nel formato seguente AzureBackupRG_ `<Geo>` _ `<number>` .
3. Nella sezione **Impostazioni** selezionare **Blocchi** per visualizzare i blocchi.
4. Per rimuovere il blocco, selezionare i puntini di sospensione e selezionare **Elimina**.

    ![Eliminare un blocco](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a> Pulisci raccolta punti di ripristino

Dopo aver rimosso il blocco, è necessario eseguire la pulizia dei punti di ripristino.

Se si elimina il gruppo di risorse della VM o la macchina virtuale stessa, le istantanee di ripristino istantaneo dei dischi gestiti rimangono attive e scadono in base al set di conservazione. Per eliminare gli snapshot di ripristino istantaneo (se non sono più necessari) archiviati nella raccolta di punti di ripristino, eliminare la raccolta di punti di ripristino in base ai passaggi indicati di seguito.

A questo scopo, seguire uno dei metodi seguenti:<br>

- [Pulire la raccolta di punti di ripristino eseguendo il backup su richiesta](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Pulire la raccolta di punti di ripristino dal portale di Azure](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Pulire la raccolta di punti di ripristino eseguendo il backup su richiesta

Dopo aver rimosso il blocco, attivare un backup su richiesta. Questa azione assicura che i punti di ripristino vengano puliti automaticamente. Si prevede che questa operazione su richiesta abbia esito negativo la prima volta. Tuttavia, verrà garantita la pulizia automatica anziché l'eliminazione manuale dei punti di ripristino. Dopo la pulizia, il successivo backup pianificato avrà esito positivo.

> [!NOTE]
> La pulizia automatica viene eseguita dopo alcune ore dall'attivazione del backup su richiesta. Se il backup pianificato continua ad avere esito negativo, provare a eliminare manualmente la raccolta di punti di ripristino seguendo la procedura illustrata [qui](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Pulire la raccolta di punti di ripristino dal portale di Azure <br>

Per cancellare manualmente la raccolta di punti di ripristino, che non è stata cancellata a causa del blocco sul gruppo di risorse, provare a eseguire i passaggi seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nel menu **Hub** selezionare tutte le **risorse**, selezionare il gruppo di risorse con il formato seguente AzureBackupRG_ `<Geo>` _ in `<number>` cui si trova la macchina virtuale.

    ![Selezionare il gruppo di risorse](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Selezionare gruppo di risorse. verrà visualizzato il riquadro **Panoramica** .
4. Selezionare l'opzione **Mostra tipi nascosti** per visualizzare tutte le risorse nascoste. Selezionare le raccolte di punti di ripristino con il formato AzureBackupRG_`<VMName>`_`<number>`.

    ![Selezionare la raccolta di punti di ripristino](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Selezionare **Elimina** per pulire la raccolta di punti di ripristino.
6. Ripetere l'operazione di backup.

> [!NOTE]
 >Se la risorsa (raccolta RP) ha un numero elevato di punti di ripristino, la loro eliminazione dal portale può avere un timeout e un errore. Si tratta di un problema di CRP noto, in cui tutti i punti di ripristino non vengono eliminati nel tempo previsto e si verifica il timeout dell'operazione. Tuttavia, l'operazione di eliminazione viene in genere completata dopo due o tre tentativi.
