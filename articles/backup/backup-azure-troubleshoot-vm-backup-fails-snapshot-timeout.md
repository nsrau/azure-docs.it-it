---
title: "Risolvere l'errore di Backup di Azure: stato dell'agente guest sconosciuto"
description: Sintomi, cause e soluzioni per i problemi di Backup di Azure correlati all'agente, all'estensione e ai dischi.
services: backup
author: genlin
manager: cshepard
keywords: Backup di Azure; agente di macchine virtuali; connettività di rete;
ms.service: backup
ms.topic: troubleshooting
ms.date: 12/03/2018
ms.author: genli
ms.openlocfilehash: ae89ab811015fca9bcb50fcc149534754533c25f
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491515"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Risolvere l'errore di Backup di Azure: problemi relativi all'agente o all'estensione

Questo articolo illustra le procedure di risoluzione dei problemi che possono essere utili per risolvere gli errori di Backup di Azure correlati alla comunicazione con l'agente di macchine virtuali e all'estensione.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]



## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - L'agente delle VM non riesce a comunicare con Backup di Azure

**Codice errore**: UserErrorGuestAgentStatusUnavailable <br>
**Messaggio di errore**: L'agente delle VM non riesce a comunicare con Backup di Azure.<br>

Dopo la registrazione e la pianificazione di una macchina per il servizio Backup, tale servizio avvia il processo comunicando con l'agente di macchine virtuali per creare uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Quando uno snapshot non viene attivato, il backup potrebbe non riuscire. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:<br>
**Causa 1: [L'agente viene installato nella macchina virtuale, ma non risponde (per VM Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Causa 2: [L'agente installato nella macchina virtuale non è aggiornato (per VM Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [Non è possibile recuperare lo stato dello snapshot o acquisire uno snapshot](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**Causa 4: [Non possibile aggiornare o caricare l'estensione di backup](#the-backup-extension-fails-to-update-or-load)**  
**Causa 5: [La macchina virtuale non ha accesso a internet](#the-vm-has-no-internet-access)**

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - Non è stato possibile comunicare con l'agente della macchina virtuale per lo stato dello snapshot

**Codice errore**: GuestAgentSnapshotTaskStatusError<br>
**Messaggio di errore**: Non è stato possibile comunicare con l'agente della macchina virtuale per lo stato dello snapshot. <br>

Dopo la registrazione e la pianificazione di una macchina virtuale per il servizio Backup di Azure, tale servizio avvia il processo comunicando con l'estensione di backup della macchina virtuale per la creazione di uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Se lo snapshot non viene attivato, può verificarsi un errore di backup. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:  
**Causa 1: [L'agente viene installato nella macchina virtuale, ma non risponde (per VM Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 2: [L'agente installato nella macchina virtuale non è aggiornato (per VM Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [La macchina virtuale non ha accesso a internet](#the-vm-has-no-internet-access)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - È stato raggiunto il limite massimo di raccolte di punti di ripristino

**Codice errore**: UserErrorRpCollectionLimitReached <br>
**Messaggio di errore**: È stato raggiunto il limite massimo di raccolte di punti di ripristino. <br>
* Questo problema può verificarsi se è presente un blocco sul gruppo di risorse dei punti di ripristino che impedisce la pulizia automatica dei punti di ripristino.
* Questo problema può verificarsi anche se vengono attivate più operazioni di backup al giorno. Attualmente, è consigliabile un solo backup al giorno come da 1 a 5 giorni in base al periodo di conservazione configurato snapshot vengono mantenuti i punti di ripristino immediato e solo 18 RPs immediata può essere associata a una macchina virtuale in un determinato momento. <br>

Azione consigliata:<br>
Per risolvere il problema, rimuovere il blocco sul gruppo di risorse della macchina virtuale e ripetere l'operazione per attivare la pulizia.
> [!NOTE]
> Il servizio di backup crea un gruppo di risorse distinto da quello della macchina virtuale per archiviare la raccolta di punti di ripristino. È consigliabile che i clienti non blocchino il gruppo di risorse creato per l'uso dal servizio di backup. Il formato di denominazione del gruppo di risorse creato dal servizio di backup è: AzureBackupRG_`<Geo>`_`<number>` Ad esempio: AzureBackupRG_northeurope_1

**Passaggio 1: [Rimuovi blocco dal gruppo di risorse del punto di ripristino](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Passaggio 2: [Raccolta di punti di ripristino la pulizia](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - Il servizio Backup non dispone delle autorizzazioni sufficienti per accedere all'insieme di credenziali delle chiavi per il backup di macchine virtuali crittografate

**Codice errore**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Messaggio di errore**: Il servizio Backup non dispone delle autorizzazioni sufficienti per Accedere a Key Vault per il backup di macchine virtuali crittografate. <br>

Per la corretta esecuzione di un'operazione di backup nelle macchine virtuali crittografate, è necessario disporre delle autorizzazioni per accedere all'insieme di credenziali delle chiavi. Questa operazione può essere eseguita usando il [portale di Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) o tramite [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection).

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - L'operazione di creazione snapshot non è riuscita perché la connettività di rete è assente nella macchina virtuale

**Codice errore**: ExtensionSnapshotFailedNoNetwork<br>
**Messaggio di errore**: L'operazione di creazione snapshot non è riuscita perché la connettività di rete è assente nella macchina virtuale.<br>

Dopo la registrazione e la pianificazione di una macchina virtuale per il servizio Backup di Azure, tale servizio avvia il processo comunicando con l'estensione di backup della macchina virtuale per la creazione di uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Se lo snapshot non viene attivato, può verificarsi un errore di backup. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:    
**Causa 1: [Non è possibile recuperare lo stato dello snapshot o acquisire uno snapshot](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [Non possibile aggiornare o caricare l'estensione di backup](#the-backup-extension-fails-to-update-or-load)**  
**Causa 3: [La macchina virtuale non ha accesso a internet](#the-vm-has-no-internet-access)**

## <a name="ExtentionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtentionOperationFailedForManagedDisks - L'operazione di estensione VMSnapshot non è riuscita

**Codice errore**: ExtentionOperationFailedForManagedDisks <br>
**Messaggio di errore**: L'operazione di estensione VMSnapshot non è riuscita.<br>

Dopo la registrazione e la pianificazione di una macchina virtuale per il servizio Backup di Azure, tale servizio avvia il processo comunicando con l'estensione di backup della macchina virtuale per la creazione di uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Se lo snapshot non viene attivato, può verificarsi un errore di backup. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:  
**Causa 1: [Non è possibile recuperare lo stato dello snapshot o acquisire uno snapshot](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [Non possibile aggiornare o caricare l'estensione di backup](#the-backup-extension-fails-to-update-or-load)**  
**Causa 3: [L'agente viene installato nella macchina virtuale, ma non risponde (per VM Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 4: [L'agente installato nella macchina virtuale non è aggiornato (per VM Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 - Il backup non è riuscito generando un errore interno

**Codice errore**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Messaggio di errore**: Il backup non è riuscito generando un errore interno. Attendere qualche minuto prima di ripetere l'operazione. <br>

Dopo la registrazione e la pianificazione di una macchina virtuale per il servizio Backup di Azure, tale servizio avvia il processo comunicando con l'estensione di backup della macchina virtuale per la creazione di uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Se lo snapshot non viene attivato, può verificarsi un errore di backup. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:  
**Causa 1: [L'agente installato nella macchina virtuale, ma che non risponde (per VM Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 2: [L'agente installato nella macchina virtuale non è aggiornato (per VM Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [Non è possibile recuperare lo stato dello snapshot o acquisire uno snapshot](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 4: [Non possibile aggiornare o caricare l'estensione di backup](#the-backup-extension-fails-to-update-or-load)**  
**Causa 5: Servizio di backup non è autorizzato a eliminare i vecchi punti di ripristino a causa di un blocco del gruppo di risorse** <br>
**Causa 6: [La macchina virtuale non ha accesso a internet](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-4095gb"></a>UserErrorUnsupportedDiskSize - Backup di Azure attualmente non supporta dischi di dimensioni superiori a 4095GB

**Codice errore**: UserErrorUnsupportedDiskSize <br>
**Messaggio di errore**: Backup di Azure non supporta attualmente dimensioni superiori a 4095GB <br>

L'operazione di backup potrebbe non riuscire durante il backup della macchina virtuale con dischi di dimensioni superiori a 4095GB. Supporto per dischi di grandi dimensioni sarà presto disponibile.  

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - Impossibile avviare il backup perché è attualmente in corso un'altra operazione di backup

**Codice errore**: UserErrorBackupOperationInProgress <br>
**Messaggio di errore**: Impossibile avviare il backup perché è attualmente in corso un'altra operazione di backup<br>

La recente operazione di backup ha avuto esito negativo poiché è presente e in corso un altro processo di backup. Non è possibile avviare un nuovo processo di backup fino al termine del processo corrente. Verificare che l'operazione di backup attualmente in corso sia stata completata prima di attivare o pianificare altre operazioni di backup. Per controllare lo stato dei processi di backup, seguire questa procedura:

1. Accedere al portale di Azure e fare clic su **Tutti i servizi**. Digitare Servizi di ripristino e fare clic su **Insiemi di credenziali di Servizi di ripristino**. Verrà visualizzato l'elenco degli insiemi di credenziali dei servizi di ripristino.
2. Nell'elenco degli insiemi di credenziali di Servizi di ripristino selezionare un insieme di credenziali in cui è configurato il backup.
3. Scegliere **Processi di backup** dal menu del dashboard dell'insieme di credenziali per visualizzare tutti i processi di backup.

    * Se è in corso un processo di backup, attenderne il completamento o annullarlo.
        * Per annullare il processo di backup, fare clic sul processo di backup con il pulsante destro del mouse e scegliere **Annulla** oppure usare [PowerShell](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
    * Se è stato riconfigurato il backup in un insieme di credenziali diverso, assicurarsi che non siano presenti processi di backup in esecuzione nell'insieme di credenziali precedente. Se è presente un processo di backup, annullarlo.
        * Per annullare il processo di backup, fare clic sul processo di backup con il pulsante destro del mouse e scegliere **Annulla** oppure usare [PowerShell](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)
4. Ripetere l'operazione di backup.

Se l'operazione di backup pianificata sta impiegando più tempo del previsto, causando conflitti con la configurazione del backup successivo, rivedere [Procedure consigliate](backup-azure-vms-introduction.md#best-practices), [Prestazioni del backup](backup-azure-vms-introduction.md#backup-performance) e [Considerazioni sul ripristino](backup-azure-vms-introduction.md#backup-and-restore-considerations).


## <a name="causes-and-solutions"></a>Cause e soluzioni

### <a name="the-vm-has-no-internet-access"></a>La macchina virtuale non ha accesso a Internet
In base al requisito di distribuzione, la macchina virtuale non ha accesso a Internet. In alternativa, potrebbero esserci restrizioni che impediscono l'accesso all'infrastruttura di Azure.

Per funzionare correttamente, l'estensione di backup richiede la connettività agli indirizzi IP pubblici di Azure. L'estensione invia comandi a un endpoint di archiviazione di Azure (URL HTTP) per gestire gli snapshot della macchina virtuale. Se l'estensione non ha accesso a Internet pubblico, il backup ha esito negativo.

####  <a name="solution"></a>Soluzione
Per risolvere il problema di rete, vedere [Stabilire la connettività di rete](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)

#### <a name="solution"></a>Soluzione
L'agente di macchine virtuali può essere danneggiato o il servizio può essere stato arrestato. Reinstallando l'agente di macchine virtuali è possibile ottenere la versione più recente. In questo modo sarà anche possibile riavviare la comunicazione con il servizio.

1. Determinare se il servizio agente guest di Microsoft Azure è in esecuzione nei servizi delle macchine virtuali (services.msc). Provare a riavviare il servizio agente guest di Microsoft Azure e avviare il backup.    
2. Se il servizio agente guest di Microsoft Azure non è visibile tra i servizi, nel Pannello di controllo passare a **Programmi e funzionalità** per controllare se è installato.
4. Se il servizio agente guest di Microsoft Azure è elencato in **Programmi e funzionalità**, disinstallarlo.
5. Scaricare e installare la [versione più recente del file MSI dell'agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione sono necessari i diritti di amministratore.
6. Verificare che il servizio agente guest di Microsoft Azure sia visualizzato tra i servizi.
7. Eseguire un backup su richiesta:
    * Nel portale selezionare **Esegui backup ora**.

Verificare anche che [Microsoft .NET 4.5 sia installato](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nella macchina virtuale. .NET 4.5 è necessario per la comunicazione dell'agente di macchine virtuali con il servizio.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>L'agente installato nella VM Linux non è aggiornato (per VM Linux)

#### <a name="solution"></a>Soluzione
La maggior parte degli errori relativi ad agenti o estensioni nelle macchine virtuali Linux è dovuta a problemi correlati ad agenti di macchine virtuali non aggiornati. Per risolvere il problema, seguire queste indicazioni generali:

1. Seguire le istruzioni per l'[aggiornamento dell'agente di macchine virtuali Linux](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > È *fortemente consigliato* aggiornare l'agente solo tramite un repository di distribuzione. Non è consigliabile scaricare il codice dell'agente direttamente da GitHub e aggiornarlo. Se l'agente più recente per la distribuzione non è disponibile, contattare il supporto per la distribuzione per istruzioni su come installarlo. Per cercare l'agente più recente, passare alla pagina dell'[agente Linux di Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) nel repository GitHub.

2. Verificare che l'agente di Azure sia in esecuzione nella macchina virtuale eseguendo il comando seguente: `ps -e`

   Se il processo non è in esecuzione, riavviarlo usando i comandi seguenti:

   * Per Ubuntu: `service walinuxagent start`
   * Per altre distribuzioni: `service waagent start`

3. [Configurare l'agente per il riavvio automatico](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Eseguire un nuovo backup di prova. Se l'errore persiste, raccogliere i log seguenti dalla macchina virtuale:

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

Se è necessaria una registrazione dettagliata per waagent, seguire questa procedura:

1. Nel file /etc/waagent.conf individuare la riga seguente: **Enable verbose logging (y|n)**
2. Modificare il valore di **Logs.Verbose** da *n* a *y*.
3. Salvare la modifica e quindi riavviare waagent seguendo la procedura descritta in precedenza in questa sezione.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Non è possibile recuperare lo stato dello snapshot o acquisire uno snapshot
Il backup delle macchine virtuali si basa sull'esecuzione del comando di snapshot sull'account di archiviazione sottostante. Il backup può avere esito negativo perché non ha accesso all'account di archiviazione o perché l'esecuzione dell'attività di snapshot è stata ritardata.

#### <a name="solution"></a>Soluzione
Le condizioni seguenti possono causare errori dell'attività di snapshot:

| Causa | Soluzione |
| --- | --- |
| Lo stato della macchina virtuale viene segnalato in modo non corretto perché la macchina virtuale viene arrestata in RDP (Remote Desktop Protocol). | Se si arresta la macchina virtuale in RDP, controllare il portale per determinare se lo stato della macchina virtuale è corretto. In caso contrario, arrestare la macchina virtuale nel portale tramite l'opzione **Spegni** nel dashboard della macchina virtuale. |
| La macchina virtuale non riesce a ottenere l'indirizzo dell'host o dell'infrastruttura da DHCP. | DHCP deve essere abilitato nel computer guest per consentire il funzionamento del backup delle VM IaaS. Se la macchina virtuale non riesce a ottenere l'indirizzo dell'host o dell'infrastruttura da DHCP, con risposta 245, non è possibile scaricare o eseguire le estensioni. Se è necessario un indirizzo IP privato statico, è necessario configurarlo tramite il **portale di Azure** oppure **PowerShell** e assicurarsi che l'opzione DHCP all'interno della VM sia abilitato. [Altre informazioni](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) sulla configurazione di un indirizzo IP statico con PowerShell.

### <a name="the-backup-extension-fails-to-update-or-load"></a>Non è possibile aggiornare o caricare l'estensione di backup
Se non è possibile caricare le estensioni, si verifica un errore del backup perché non è possibile acquisire uno snapshot.

#### <a name="solution"></a>Soluzione

Disinstallare l'estensione per forzare il ricaricamento dell'estensione VMSnapshot. L'estensione viene ricaricata al successivo tentativo di backup.

Per disinstallare l'estensione:

1. Nel [portale di Azure](https://portal.azure.com/) passare alla macchina virtuale in cui si è verificato l'errore di backup.
2. Selezionare **Impostazioni**.
3. Selezionare **Estensioni**.
4. Selezionare **Vmsnapshot Extension** (Estensione Vmsnapshot).
5. Selezionare **Disinstalla**.

Per le macchine virtuali Linux, se l'estensione VMSnapshot non è visualizzata nel portale di Azure [aggiornare l'agente Linux di Azure](../virtual-machines/linux/update-agent.md) e quindi eseguire il backup.

Questa procedura fa in modo che l'estensione venga reinstallata durante il backup successivo.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>Rimuovere il blocco dal gruppo di risorse dei punti di ripristino
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare a **Tutte le risorse**, selezionare il gruppo di risorse della raccolta dei punti di ripristino con il formato AzureBackupRG_`<Geo>`_`<number>`.
3. Nella sezione **Impostazioni** selezionare **Blocchi** per visualizzare i blocchi.
4. Per rimuovere il blocco, selezionare i puntini di sospensione e fare clic su **Elimina**.

    ![Eliminare un blocco](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a> Pulire la raccolta di punti di ripristino
Dopo aver rimosso il blocco, è necessario eseguire la pulizia dei punti di ripristino. A questo scopo, seguire uno dei metodi seguenti:<br>
* [Pulizia ripristino raccolta di punti mediante l'esecuzione di backup ad hoc](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
* [Ripristino di pulizia scegliere raccolta dal portale di Azure](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>Pulizia ripristino raccolta di punti mediante l'esecuzione di backup ad hoc
Dopo la rimozione di blocco, attivare un backup ad hoc/manuali di Active Directory. In tal modo, i punti di ripristino verranno automaticamente puliti. Prevede che questa operazione ad hoc o manuale ad eseguire la prima volta. Tuttavia, garantirà la pulizia automatica anziché l'eliminazione manuale di punti di ripristino. Dopo la pulizia, il successivo backup pianificato avrà esito positivo.

> [!NOTE]
> La pulizia automatica verrà eseguito dopo alcune ore dall'attivazione di backup ad hoc/manuali. Se il backup pianificato continua ad avere esito negativo, provare a eliminare manualmente la raccolta di punti di ripristino seguendo la procedura illustrata [qui](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Pulire la raccolta di punti di ripristino dal portale di Azure <br>

Per cancellare manualmente la raccolta di punti di ripristino che non viene cancellata a causa del blocco sul gruppo di risorse, provare questa procedura:
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nel menu **Hub** fare clic su **Tutte le risorse** e selezionare il gruppo di risorse con il formato AzureBackupRG_`<Geo>`_`<number>` nella posizione in cui si trova la VM.

    ![Eliminare un blocco](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Facendo clic sul gruppo di risorse viene visualizzato il pannello **Panoramica**.
4. Selezionare l'opzione **Mostra tipi nascosti** per visualizzare tutte le risorse nascoste. Selezionare le raccolte di punti di ripristino con il formato AzureBackupRG_`<VMName>`_`<number>`.

    ![Eliminare un blocco](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Fare clic su **Elimina** per pulire la raccolta di punti di ripristino.
6. Ripetere l'operazione di backup.

> [!NOTE]
 >Se la risorsa (raccolta di punti di ripristino) ha un numero elevato di punti di ripristino, l'eliminazione dal portale può restituire un errore di timeout e non riuscire. Questo è un problema noto di CRP, dovuto al fatto che non tutti i punti di ripristino vengono eliminati nel tempo stabilito e si verifica il timeout dell'operazione. L'operazione di eliminazione, tuttavia, viene in genere completata dopo due o tre tentativi.
