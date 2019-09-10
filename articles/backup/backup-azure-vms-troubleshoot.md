---
title: Risolvere i problemi relativi al backup di macchine virtuali di Azure
description: Risolvere i problemi relativi al backup e al ripristino delle macchine virtuali di Azure
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: dacurwin
ms.openlocfilehash: 1b3d02d5cfdae2f196f2f35f075dd8c250b5ece1
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860339"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Risoluzione degli errori di backup nelle macchine virtuali di Azure

È possibile risolvere gli errori rilevati durante l'uso di backup di Azure con le informazioni elencate di seguito:

## <a name="backup"></a>Backup

Questa sezione descrive l'errore dell'operazione di backup della macchina virtuale di Azure.

### <a name="basic-troubleshooting"></a>Risoluzione dei problemi di base

* Verificare che l'agente di macchine virtuali (WA Agent) sia la [versione più recente](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* Verificare che la versione del sistema operativo della VM Windows o Linux sia supportata. vedere la matrice di supporto per il [backup delle VM IaaS](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas).
* Verificare che un altro servizio di backup non sia in esecuzione.
   * Per assicurarsi che non ci siano problemi di estensione dello snapshot, [disinstallare le estensioni per forzare il ricaricamento e quindi ripetere il backup](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#the-backup-extension-fails-to-update-or-load).
* Verificare che la macchina virtuale abbia la connettività Internet.
   * Verificare che un altro servizio di backup non sia in esecuzione.
* Da `Services.msc`verificare che il servizio **agente guest di Microsoft Azure** sia **in esecuzione**. Se il servizio **agente guest di Azure** non è presente, installarlo dal [backup di macchine virtuali di Azure in un insieme di credenziali di servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* È possibile che nel **registro eventi** vengano visualizzati errori di backup provenienti da altri prodotti di backup, ad esempio Windows Server backup, e che non siano dovuti a backup di Azure. Usare la procedura seguente per determinare se il problema si verifica con backup di Azure:
   * Se si verifica un errore con un **backup** delle voci nell'origine o nel messaggio dell'evento, controllare se i backup di backup delle macchine virtuali IaaS di Azure siano stati completati e se è stato creato un punto di ripristino con il tipo di snapshot desiderato.
    * Se backup di Azure funziona, il problema è probabilmente con un'altra soluzione di backup.
    * Di seguito è riportato un esempio di errore del Visualizzatore eventi in cui backup di Azure funzionava correttamente, ma "Windows Server Backup" ha avuto esito negativo:<br>
    ![Errore Windows Server Backup](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
    * Se backup di Azure ha esito negativo, cercare il codice di errore corrispondente nella sezione Errori comuni di backup delle VM in questo articolo.

## <a name="common-issues"></a>Problemi comuni

Di seguito sono riportati i problemi comuni relativi agli errori di backup nelle macchine virtuali di Azure.

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime: si è verificato un timeout durante la copia dei dati di backup dall'insieme di credenziali

Codice errore: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Messaggio di errore: Timeout della copia dei dati sottoposti a backup dall'insieme di credenziali

Questo problema può verificarsi a causa di errori di archiviazione temporanei o di IOPS per il servizio di backup per trasferire i dati nell'insieme di credenziali entro il periodo di timeout. Configurare il backup della macchina virtuale usando queste [procedure consigliate](backup-azure-vms-introduction.md#best-practices) e ripetere l'operazione di backup.

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState-la macchina virtuale non è in uno stato che consente i backup.

Codice errore: UserErrorVmNotInDesirableState <br/>
Messaggio di errore: Lo stato della macchina virtuale non consente i backup.<br/>

L'operazione di backup non è riuscita perché la macchina virtuale è in stato di errore. Per eseguire correttamente il backup, lo stato della macchina virtuale deve essere in esecuzione, arrestato o arrestato (deallocato).

* Se la macchina virtuale si trova in uno stato temporaneo tra **In esecuzione** e **Arresto**, attendere che lo stato cambi. Quindi attivare il processo di backup.
* Se la macchina virtuale è Linux e usa il modulo del kernel Security Enhanced Linux, escludere il percorso dell'agente Linux di Azure **/var/lib/waagent** dai criteri di sicurezza e assicurarsi che l'estensione di backup sia installata.

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed: non è stato possibile bloccare uno o più punti di montaggio della macchina virtuale per eseguire uno snapshot coerente con il file System

Codice errore: UserErrorFsFreezeFailed <br/>
Messaggio di errore: Impossibile bloccare uno o più punti di montaggio della macchina virtuale per creare uno snapshot coerente con il file system.

* Verificare lo stato file System di tutti i dispositivi montati usando il comando **tune2fs** , ad esempio **tune2fs- \\l/dev/sdb1** .\| grep **filesystem state**.
* Smontare i dispositivi per cui lo stato di file system non è stato pulito, usando il comando **umount** .
* Eseguire una verifica di coerenza file system su questi dispositivi usando il comando **fsck** .
* Montare nuovamente i dispositivi e ripetere l'operazione di backup.</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM/ExtensionInstallationFailedCOM/ExtensionInstallationFailedMDTC-l'operazione o l'installazione dell'estensione non è riuscita a causa di un errore COM+

Codice errore: ExtensionSnapshotFailedCOM <br/>
Messaggio di errore: L'operazione di creazione snapshot non è riuscita a causa di un errore COM+

Codice errore: ExtensionInstallationFailedCOM  <br/>
Messaggio di errore: L'installazione o l'operazione di estensione non è riuscita a causa di un errore COM+

Codice errore: ExtensionInstallationFailedMDTC <br/>
Messaggio di errore: L'installazione dell'estensione non è riuscita con un errore che indica che il servizio COM+ non può comunicare con Microsoft Distributed Transaction Coordinator <br/>

L'operazione di backup non è riuscita a causa di un problema con l'applicazione di **sistema com+** del servizio Windows.  Per risolvere il problema, seguire questa procedura:

* Provare a avviare/riavviare l' **applicazione di sistema com+** del servizio Windows (da un prompt dei comandi con privilegi elevati **-net start COMSysApp**).
* Verificare che **Distributed Transaction Coordinator** Services sia in esecuzione come account del **servizio di rete** . In caso contrario, modificarlo in modo che venga eseguito come account del **servizio di rete** e riavviare l' **applicazione di sistema com+** .
* Se non è possibile riavviare il servizio, reinstallare **Distributed Transaction Coordinator** servizio attenendosi alla procedura seguente:
    * Arrestare il servizio MSDTC
    * Aprire un prompt dei comandi (cmd)
    * Eseguire il comando "msdtc-uninstall"
    * comando "MSDTC-install"
    * Avviare il servizio MSDTC
* Avviare il servizio di Windows **Applicazione di sistema COM+** . Dopo che il servizio **Applicazione sistema COM+** è stato avviato, attivare un processo di backup dal portale di Azure.</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>L'operazione ExtensionFailedVssWriterInBadState-snapshot non è riuscita perché i writer VSS erano in uno stato non valido

Codice errore: ExtensionFailedVssWriterInBadState <br/>
Messaggio di errore: L'operazione di creazione snapshot non è riuscita perché i writer VSS erano in uno stato non valido.

Riavviare i writer VSS in uno stato non valido. Da un prompt dei comandi con privilegi elevati, eseguire ```vssadmin list writers```. L'output contiene tutti i writer VSS con lo stato. Riavviare ogni VSS writer con stato diverso da **[1] Stable** eseguendo i comandi seguenti da un prompt dei comandi con privilegi elevati:

  * ```net stop serviceName```
  * ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure-errore durante l'analisi della configurazione per l'estensione di backup

Codice errore: ExtensionConfigParsingFailure<br/>
Messaggio di errore: Si è verificato un errore durante l'analisi della configurazione per l'estensione del backup.

Questo errore si verifica a causa della modifica delle autorizzazioni nella directory **MachineKeys**: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Eseguire il comando seguente e verificare che le autorizzazioni per la directory **MachineKeys** siano quelle predefinite:**icacls%SystemDrive%\ProgramData\Microsoft\Crypto\RSA\MachineKeys**.

Le autorizzazioni predefinite sono:
* Everyone: (R,W)
* BUILTIN\Administrators: (F)

Se nella directory **MachineKeys** vengono visualizzate autorizzazioni diverse da quelle predefinite, seguire la procedura riportata di seguito per correggere le autorizzazioni, eliminare il certificato e attivare il backup:

1. Correggere le autorizzazioni nella directory **MachineKeys**. Usando le impostazioni di sicurezza avanzate e le proprietà di sicurezza di Explorer nella directory, ripristinare le autorizzazioni ai valori predefiniti. Rimuovere tutti gli oggetti utente tranne quelli predefiniti dalla directory e assicurarsi che l'autorizzazione **Everyone** abbia accesso speciale per:

    * Visualizzazione cartella/lettura dati
    * Lettura attributi
    * Lettura attributi estesi
    * Creazione file/scrittura dati
    * Creazione cartelle/aggiunta dati
    * Scrittura attributi
    * Scrittura attributi estesi
    * Autorizzazioni di lettura
2. Eliminare tutti i certificati in cui **Rilasciato a** è il modello di distribuzione classica o **Windows Azure CRP Certificate Generator**:
    * [Aprire i certificati in una console del computer locale](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx).
    * In **Personale** > **Certificati** eliminare tutti i certificati in cui **Rilasciato a** o **Windows Azure CRP Certificate Generator** è il modello di distribuzione classica.
3. Attivare un processo di backup della macchina virtuale.

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState-lo stato dell'estensione non è supportato per l'operazione di backup

Codice errore: ExtensionStuckInDeletionState <br/>
Messaggio di errore: Lo stato dell'estensione non è supportato per l'operazione di backup

Operazione di backup non riuscita a causa di uno stato incoerente dell'estensione di backup. Per risolvere il problema, seguire questa procedura:

* Verificare che l'agente Guest sia installato e reattivo
* Nel portale di Azure passare a **Macchina virtuale** > **Tutte le impostazioni** > **Estensioni**
* Selezionare l'estensione di backup VmSnapshot o VmSnapshotLinux e fare clic su **Disinstalla**
* Dopo aver eliminato l'estensione di backup, ripetere l'operazione di backup
* L'operazione di backup successiva installerà la nuova estensione nello stato desiderato

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>L'operazione ExtensionFailedSnapshotLimitReachedError-snapshot non è riuscita perché è stato superato il limite di snapshot per alcuni dischi collegati

Codice errore: ExtensionFailedSnapshotLimitReachedError  <br/>
Messaggio di errore: L'operazione di creazione snapshot non è riuscita perché è stato superato il limite di snapshot per alcuni dischi collegati

L'operazione di snapshot non è riuscita perché è stato superato il limite di snapshot per alcuni dischi collegati. Completare i passaggi seguenti per la risoluzione dei problemi, quindi ripetere l'operazione.

* Eliminare gli snapshot BLOB del disco che non sono necessari. Prestare attenzione a non eliminare i BLOB del disco. è necessario eliminare solo i BLOB di snapshot.
* Se l'eliminazione temporanea è abilitata negli account di archiviazione su disco della macchina virtuale, configurare la conservazione dell'eliminazione temporanea in modo che gli snapshot esistenti siano inferiori al massimo consentito in qualsiasi momento.
* Se Azure Site Recovery è abilitato nella macchina virtuale di cui è stato eseguito il backup, eseguire le operazioni seguenti:

    * Verificare che il valore di **isanysnapshotfailed** sia impostato su false in/etc/Azure/VMBackup.conf
    * Pianificare Azure Site Recovery in un momento diverso, in modo che non sia in conflitto con l'operazione di backup.

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>L'operazione ExtensionFailedTimeoutVMNetworkUnresponsive-snapshot non è riuscita a causa di risorse insufficienti per le macchine virtuali.

Codice errore: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Messaggio di errore: Operazione di creazione snapshot non riuscita a causa di risorse insufficienti per le macchine virtuali.

L'operazione di backup nella macchina virtuale non è riuscita a causa di un ritardo nelle chiamate di rete durante l'esecuzione dell'operazione di snapshot. Per risolvere il problema, seguire il passaggio 1. Se il problema persiste, provare i passaggi 2 e 3.

**Passaggio 1**: Creare uno snapshot tramite host

Da un prompt dei comandi elevato (amministratore) eseguire il comando seguente:

```text
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Questo garantirà che gli snapshot vengano creati tramite host invece che guest. Ripetere l'operazione di backup.

**Passaggio 2**: Provare a modificare la pianificazione del backup in un momento in cui la VM è sotto carico minore (meno CPU/IOps e così via)

**Passaggio 3**: Provare ad [aumentare le dimensioni della macchina virtuale](https://azure.microsoft.com/blog/resize-virtual-machines/) e ripetere l'operazione

## <a name="common-vm-backup-errors"></a>Errori comuni di backup delle VM

| Dettagli errore | Soluzione alternativa |
| ------ | --- |
| **Codice errore**: 320001<br/> **Messaggio di errore**: Non è stato possibile eseguire l'operazione perché la macchina virtuale non esiste più. <br/> <br/> **Codice errore**: 400094 <br/> **Messaggio di errore**: La macchina virtuale non esiste <br/> <br/>  La macchina virtuale di Azure non è stata trovata.  |Questo errore si verifica quando la macchina virtuale primaria viene eliminata, ma i criteri di backup continuano a cercare una macchina virtuale di cui eseguire il backup. Per risolvere l'errore, procedere come segue: <ol><li> Ricreare la macchina virtuale con lo stesso nome e lo stesso nome del gruppo di risorse, **nome del servizio cloud**,<br>**or**</li><li> Interrompere la protezione della macchina virtuale cancellando o senza eliminare i dati del backup. Per altre informazioni, vedere [Arrestare la protezione delle macchine virtuali](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>|
| Lo stato di provisioning della macchina virtuale è Non riuscito. <br>Riavviare la macchina virtuale e assicurarsi che lo stato della macchina virtuale sia In esecuzione o Arresto. | Questo errore si verifica quando gli errori di una delle estensioni provocano lo stato non riuscito del provisioning della macchina virtuale. Passare all'elenco di estensioni, verificare se è presente un'estensione con errore, rimuoverla e provare a riavviare la macchina virtuale. Se tutte le estensioni sono in stato di esecuzione, verificare se è in esecuzione il servizio agente di macchine virtuali. In caso contrario, riavviare il servizio agente di macchine virtuali. |
|**Codice errore**: UserErrorBCMPremiumStorageQuotaError<br/> **Messaggio di errore**: Non è stato possibile copiare lo snapshot della macchina virtuale a causa dello spazio disponibile insufficiente nell'account di archiviazione | Per le macchine virtuali Premium nello stack V1 di backup di macchine virtuali, lo snapshot viene copiato nell'account di archiviazione, in modo da assicurare che il traffico di gestione dei backup, che funziona sullo snapshot, non limiti il numero di IOPS disponibili all'applicazione che usa i dischi Premium. <br><br>Si consiglia di allocare solo il 50%, 17,5 TB, dello spazio totale dell'account di archiviazione. In questo modo il servizio Backup di Azure può copiare lo snapshot nell'account di archiviazione e trasferire i dati da questa posizione copiata nell'account di archiviazione all'insieme di credenziali. |
| **Codice errore: 380008** <br/> **Messaggio di errore**: Non è stato possibile installare l'estensione di servizi di ripristino di Microsoft perché la macchina virtuale non è in esecuzione | L'agente VM è un prerequisito dell'estensione Servizi di ripristino di Azure. Installare l'agente VM di Azure e riavviare l'operazione di registrazione. <br> <ol> <li>Controllare se l'agente di macchine virtuali è stato installato correttamente. <li>Assicurarsi che il flag sul file di configurazione della macchina virtuale sia impostato correttamente.</ol> Altre informazioni sull'installazione dell'agente di macchine virtuali e su come convalidarlo. |
| **Codice errore**: ExtensionSnapshotBitlockerError <br/> **Messaggio di errore**: L'operazione di creazione snapshot non è riuscita con l'errore dell'operazione del Servizio Copia Shadow del volume **Unità bloccata da Crittografia unità BitLocker. Sbloccare l'unità dal Pannello di controllo.** |Disattivare BitLocker per tutte le unità della macchina virtuale e verificare se il problema relativo al Servizio Copia Shadow del volume è stato risolto. |
| **Codice errore**: VmNotInDesirableState <br/> **Messaggio di errore**:  Lo stato della macchina virtuale non consente i backup. |<ul><li>Se la macchina virtuale si trova in uno stato temporaneo tra **In esecuzione** e **Arresto**, attendere che lo stato cambi. Quindi attivare il processo di backup. <li> Se la macchina virtuale è Linux e usa il modulo del kernel Security Enhanced Linux, escludere il percorso dell'agente Linux di Azure **/var/lib/waagent** dai criteri di sicurezza e assicurarsi che l'estensione di backup sia installata.  |
| L'agente di macchine virtuali non è presente nella macchina virtuale. <br>Installare i prerequisiti necessari e l'agente di macchine virtuali. Quindi ripetere l'operazione. |Altre informazioni sull'[installazione dell'agente di macchine virtuali e su come convalidarla](#vm-agent). |
| **Codice errore**: ExtensionSnapshotFailedNoSecureNetwork <br/> **Messaggio di errore**: L'operazione di creazione snapshot non è riuscita a causa di un errore durante la creazione del canale di comunicazione di rete protetta. | <ol><li> Aprire l'editor del Registro di sistema eseguendo **regedit.exe** con privilegi elevati. <li> Identificare tutte le versioni di .NET Framework presenti nel sistema, disponibili nella gerarchia della chiave del Registro di sistema **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> Per ogni versione di .NET Framework presente nella chiave del Registro di sistema, aggiungere la chiave seguente: <br> **SchUseStrongCrypto"=dword:00000001**. </ol>|
| **Codice errore**: ExtensionVCRedistInstallationFailure <br/> **Messaggio di errore**: L'operazione di creazione snapshot non è riuscita a causa di un errore durante l'installazione di Visual C++ Redistributable per Visual Studio 2012. | Passare a C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion e installare vcredist2013_x64.<br/>Verificare che il valore della chiave del registro di sistema che consente l'installazione del servizio sia impostato sul valore corretto. Ovvero, impostare il valore **iniziale** in **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** su **3** e non su **4**. <br><br>Se i problemi di installazione persistono, riavviare il servizio di installazione eseguendo **MSIEXEC /UNREGISTER** e quindi **MSIEXEC /REGISTER** da un prompt dei comandi con privilegi elevati.  |


## <a name="jobs"></a>Processi

| Dettagli errore | Soluzione alternativa |
| --- | --- |
| L'annullamento non è supportato per questo tipo di processo. <br>Attendere il completamento del processo. |Nessuna |
| Il processo non si trova in uno stato annullabile. <br>Attendere il completamento del processo. <br>**or**<br> Il processo selezionato non si trova in uno stato annullabile. <br>Attendere il completamento del processo. |È probabile che il processo sia quasi terminato. Attendere fino al termine dell'esecuzione del processo.|
| Non è possibile annullare il processo perché non è in corso. <br>L'annullamento è supportato solo per i processi in corso. Provare ad annullare un processo in corso. |Questo errore si verifica a causa di uno stato temporaneo. Attendere un minuto e ripetere l'operazione di annullamento. |
| Non è stato possibile annullare il processo. <br>Attendere il completamento del processo. |Nessuna |

## <a name="restore"></a>Ripristina

| Dettagli errore | Soluzione alternativa |
| --- | --- |
| Ripristino non riuscito con errore interno del cloud. |<ol><li>Il servizio cloud in cui si sta tentando di eseguire il ripristino è configurato con le impostazioni DNS. Verificare: <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings**.<br>Se è presente un **indirizzo** configurato, significa che le impostazioni DNS sono configurate.<br> <li>Il servizio cloud che si sta tentando di ripristinare è configurato con **ReservedIP** e le macchine virtuali esistenti nel servizio cloud sono in stato di arresto. È possibile controllare che il servizio cloud abbia un IP riservato usando i cmdlet di PowerShell seguenti: **$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName**. <br><li>Si sta tentando di ripristinare una macchina virtuale con le configurazioni di rete speciali seguenti nello stesso servizio cloud: <ul><li>Macchine virtuali con configurazione del servizio di bilanciamento del carico, interno ed esterno.<li>Macchine virtuali con più indirizzi IP riservati. <li>Macchine virtuali con più schede di rete. </ul><li>Selezionare un nuovo servizio cloud nell'interfaccia utente o vedere le [considerazioni sul ripristino](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) per le macchine virtuali con configurazioni di rete speciali.</ol> |
| Il nome DNS selezionato è già utilizzato. <br>Specificare un nome DNS diverso e riprovare. |Il nome DNS fa riferimento al nome del servizio cloud, che in genere termina con **.cloudapp.net**. Questo nome deve essere univoco. Se si verifica questo errore, è necessario scegliere un altro nome di macchina virtuale durante il ripristino. <br><br> Questo errore viene visualizzato solo dagli utenti del portale di Azure. L'operazione di ripristino tramite PowerShell riesce perché ripristina solo i dischi e non crea la macchina virtuale. L'errore viene restituito quando la macchina virtuale viene creata in modo esplicito dall'utente dopo l'operazione di ripristino dei dischi. |
| La configurazione della rete virtuale specificata non è corretta. <br>Specificare una configurazione della rete virtuale diversa e riprovare. |Nessuna |
| Il servizio cloud specificato usa un IP riservato che non corrisponde alla configurazione della macchina virtuale da ripristinare. <br>Specificare un servizio cloud diverso che non usa un IP riservato o scegliere un altro punto di ripristino da cui eseguire il ripristino. |Nessuna |
| Il servizio cloud ha raggiunto il limite per il numero di endpoint di input. <br>Ripetere l'operazione specificando un servizio cloud diverso o usando un endpoint esistente. |Nessuna |
| L'insieme di credenziali di Servizi di ripristino e l'account di archiviazione di destinazione si trovano in due aree diverse. <br>Verificare che l'account di archiviazione specificato nell'operazione di ripristino si trovi nella stessa area di Azure dell'insieme di credenziali di Servizi di ripristino. |Nessuna |
| L'account di archiviazione specificato per l'operazione di ripristino non è supportato. <br>Sono supportati solo account di archiviazione Basic/Standard con impostazioni di replica con ridondanza locale o con ridondanza geografica. Selezionare un account di archiviazione supportato. |Nessuna |
| Il tipo di account di archiviazione specificato per l'operazione di ripristino non è online. <br>Assicurarsi che l'account di archiviazione specificato nell'operazione di ripristino sia online. |Questo problema può essere provocato da un errore temporaneo in Archiviazione di Azure o da un'interruzione del servizio. Scegliere un altro account di archiviazione. |
| È stata raggiunta la quota di gruppi di risorse. <br>Eliminare alcuni gruppi di risorse dal portale di Azure o contattare il supporto tecnico di Azure per aumentare i limiti. |Nessuna |
| La subnet selezionata non esiste. <br>Selezionare una subnet esistente. |Nessuna |
| Il servizio Backup non ha l'autorizzazione per accedere alle risorse nella sottoscrizione. |Per risolvere questo errore, ripristinare prima di tutto i dischi seguendo la procedura illustrata in [Ripristinare i dischi di cui è stato eseguito il backup](backup-azure-arm-restore-vms.md#restore-disks). Seguire quindi la procedura di PowerShell descritta in [Creare una macchina virtuale da dischi ripristinati](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Il backup o il ripristino richiede del tempo
Se il backup impiega più di 12 ore o il ripristino impiega più di 6 ore, rivedere le [procedure consigliate](backup-azure-vms-introduction.md#best-practices) e le [considerazioni sulle prestazioni](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>Agente di macchine virtuali
### <a name="set-up-the-vm-agent"></a>Configurare l'agente di macchine virtuali
L'agente di VM è in genere già presente nelle VM create dalla raccolta di Azure. Nelle macchine virtuali di cui viene eseguita la migrazione da data center locali, tuttavia, l'agente di macchine virtuali non è installato. Per queste macchine virtuali è necessario installare esplicitamente l'agente VM.

#### <a name="windows-vms"></a>Macchine virtuali Windows

* Scaricare e installare il file [MSI per l'agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione è necessario disporre dei privilegi di amministratore.
* Per le macchine virtuali create con il modello di distribuzione classica, [aggiornare le proprietà della macchina virtuale](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) per indicare che l'agente è stato installato. Questo passaggio non è necessario per le macchine virtuali di Azure Resource Manager.

#### <a name="linux-vms"></a>Macchine virtuali Linux

* Installare la versione più recente dell'agente dal repository di distribuzione. Per informazioni dettagliate sul nome del pacchetto, vedere il [repository dell'agente Linux](https://github.com/Azure/WALinuxAgent).
* Per le macchine virtuali create con il modello di distribuzione classica, [usare questo blog](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) per aggiornare le proprietà della macchina virtuale e verificare che l'agente sia installato. Questo passaggio non è necessario per le macchine virtuali di Resource Manager.

### <a name="update-the-vm-agent"></a>Aggiornare l'agente di macchine virtuali
#### <a name="windows-vms"></a>Macchine virtuali Windows

* Per aggiornare l'agente di macchine virtuali, reinstallare i [file binari dell'agente di macchine virtuali](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Prima di aggiornare l'agente, assicurarsi che non venga eseguita alcuna operazione di backup durante l'aggiornamento dell'agente di macchine virtuali.

#### <a name="linux-vms"></a>Macchine virtuali Linux

* Per aggiornare l'agente di macchine virtuali Linux, seguire le istruzioni nell'articolo [Aggiornamento dell'agente di macchine virtuali Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Usare sempre il repository di distribuzione per aggiornare l'agente.

    Non scaricare il codice dell'agente da GitHub. Se l'agente più recente non è disponibile per la distribuzione, contattare il supporto per la distribuzione per istruzioni su come reperire l'agente più recente. È anche possibile trovare le informazioni più recenti sull'[agente Linux di Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) nel repository GitHub.

### <a name="validate-vm-agent-installation"></a>Convalidare l'installazione dell'agente di macchine virtuali

Verificare la versione dell'agente di macchine virtuali nelle macchine virtuali Windows:

1. Accedere alla macchina virtuale di Azure e passare alla cartella **C:\WindowsAzure\Packages**. La cartella dovrebbe contenere il file **WaAppAgent.exe**.
2. Fare clic con il pulsante destro del mouse sul file e scegliere **Proprietà**. Quindi selezionare la scheda **Dettagli**. Il campo **Versione prodotto** dovrebbe contenere 2.6.1198.718 o un numero più elevato.

## <a name="troubleshoot-vm-snapshot-issues"></a>Risoluzione dei problemi relativi agli snapshot delle macchine virtuali
Il backup delle macchine virtuali si basa sull'esecuzione dei comandi di snapshot sull'archiviazione sottostante. La mancanza di accesso all'archiviazione o ritardi nell'esecuzione dell'attività di snapshot possono causare il fallimento del processo di backup. Le condizioni seguenti possono causare errori dell'attività di snapshot:

- **L'accesso alla rete per l'archiviazione è bloccato mediante NSG**. Altre informazioni su come [stabilire l'accesso di rete](backup-azure-arm-vms-prepare.md#establish-network-connectivity) all'archiviazione usando un elenco di indirizzi IP consentiti o tramite un server proxy.
- **Le macchine virtuali con il backup di SQL Server configurato possono causare ritardi nelle attività di snapshot**. Per impostazione predefinita, il backup delle macchine virtuali genera un backup completo Servizio Copia Shadow del volume nelle macchine virtuali Windows. Le macchine virtuali che eseguono SQL Server, con il backup di SQL Server configurato, possono sperimentare ritardi degli snapshot. Se i ritardi degli snapshot causano errori di backup, impostare la chiave del Registro di sistema seguente:

   ```text
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

- **Lo stato della macchina virtuale viene segnalato in modo non corretto perché la macchina virtuale viene arrestata in RDP**. Se è stato usato il desktop remoto per arrestare la macchina virtuale, verificare che lo stato della macchina virtuale nel portale sia corretto. Se lo stato non è corretto, usare l'opzione **Arresto** nel dashboard della macchina virtuale del portale per arrestare la macchina virtuale.
- **Se più di quattro macchine virtuali condividono il servizio cloud, distribuirle tra più criteri di backup**. Distribuire gli orari del backup in modo che non più di quattro backup delle macchine virtuali vengano avviati nello stesso momento. Provare a separare di almeno un'ora gli orari di inizio dei criteri.
- **L'esecuzione della macchina virtuale fa un uso elevato della CPU o della memoria**. Se la macchina virtuale è in esecuzione con un uso elevato della memoria o della CPU, superiore al 90%, l'attività di snapshot viene messa in coda e ritardata. Infine, raggiunge il timeout. In questo caso, provare a eseguire un backup su richiesta.

## <a name="networking"></a>Rete
Analogamente a tutte le estensioni, per il funzionamento delle estensioni di Backup è necessario l'accesso a Internet pubblico. L'assenza di accesso a Internet pubblico può manifestarsi in diversi modi:

* Possono verificarsi errori di installazione dell'estensione.
* Possono verificarsi errori delle operazioni di backup, ad esempio lo snapshot del disco.
* Possono verificarsi errori di visualizzazione dello stato dell'operazione di backup.

La necessità di risolvere gli indirizzi Internet pubblici è discussa in [questo blog del supporto di Azure](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Controllare le configurazioni DNS per la rete virtuale e assicurarsi che sia possibile risolvere gli URI di Azure.

Dopo la corretta risoluzione dei nomi, sarà necessario fornire anche l'accesso agli IP di Azure. Per sbloccare l'accesso all'infrastruttura di Azure, eseguire la procedura seguente:

- Consenti l'elenco degli intervalli IP del Data Center di Azure:
   1. Ottenere l'elenco di [indirizzi IP del Data Center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) da consentire nell'elenco Consenti.
   1. Sbloccare gli indirizzi IP usando il cmdlet [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute). Eseguire questo cmdlet all'interno della macchina virtuale di Azure, in una finestra di PowerShell con privilegi elevati. Eseguire come amministratore.
   1. Aggiungere regole al gruppo di sicurezza di rete, se esistente, per consentire l'accesso agli indirizzi IP.
- Creare un percorso per il flusso del traffico HTTP:
   1. Se si hanno restrizioni di rete, distribuire un server proxy HTTP per indirizzare il traffico. Un esempio è un gruppo di sicurezza di rete. Vedere i passaggi per distribuire un server proxy HTTP in [Stabilire la connettività di rete](backup-azure-arm-vms-prepare.md#establish-network-connectivity).
   1. Aggiungere regole al gruppo di sicurezza di rete, se esistente, per consentire l'accesso a Internet dal proxy HTTP.

> [!NOTE]
> DHCP deve essere abilitato nel computer guest per consentire il funzionamento del backup delle macchine virtuali IaaS. Se è necessario un indirizzo IP privato statico, configurarlo tramite il portale di Azure o PowerShell. Assicurarsi che l'opzione DHCP all'interno della macchina virtuale sia abilitata.
> Per altre informazioni su come configurare un indirizzo IP statico tramite PowerShell, vedere:
> - [Come aggiungere un indirizzo IP interno statico a una macchina virtuale esistente](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
> - [Modificare il metodo di allocazione per un indirizzo IP privato assegnato a un'interfaccia di rete](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
>
>
