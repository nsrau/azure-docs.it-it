---
title: "Risolvere un problema di Backup di Azure: stato dell'agente guest non disponibile"
description: Sintomi, cause e soluzioni per i problemi di Backup di Azure correlati all'agente, all'estensione e ai dischi.
services: backup
author: genlin
manager: cshepard
keywords: Backup di Azure; agente di macchine virtuali; connettività di rete;
ms.service: backup
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 55e4195e2666aed371a5a5664b331184afcf5e36
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420966"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Risolvere i problemi di Backup di Azure: problemi relativi all'agente o all'estensione

Questo articolo illustra le procedure di risoluzione dei problemi che possono essere utili per risolvere gli errori di Backup di Azure correlati alla comunicazione con l'agente di macchine virtuali e all'estensione.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - L'agente delle VM non riesce a comunicare con Backup di Azure

**Codice errore**: UserErrorGuestAgentStatusUnavailable <br>
**Messaggio di errore**: VM Agent unable to communicate with Azure Backup (L'agente delle VM non riesce a comunicare con Backup di Azure)<br>

Dopo la registrazione e la pianificazione di una macchina per il servizio Backup, tale servizio avvia il processo comunicando con l'agente di macchine virtuali per creare uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Quando uno snapshot non viene attivato, il backup potrebbe non riuscire. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:<br>
**Causa 1: [La macchina virtuale non ha accesso a Internet](#the-vm-has-no-internet-access)**  
**Causa 2: [L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Causa 3: [L'agente installato nella macchina virtuale non è aggiornato (per le macchine virtuali Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 4: [Non è possibile recuperare lo stato dello snapshot o acquisire uno snapshot](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**Causa 5: [Non è possibile aggiornare o caricare l'estensione di backup](#the-backup-extension-fails-to-update-or-load)**  

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - Non è possibile comunicare con l'agente delle VM per lo stato dello snapshot

**Codice errore**: GuestAgentSnapshotTaskStatusError<br>
**Messaggio di errore**: Could not communicate with the VM agent for snapshot status (Non è possibile comunicare con l'agente delle VM per lo stato dello snapshot) <br>

Dopo la registrazione e la pianificazione di una macchina virtuale per il servizio Backup di Azure, tale servizio avvia il processo comunicando con l'estensione di backup della macchina virtuale per la creazione di uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Se lo snapshot non viene attivato, può verificarsi un errore di backup. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:  
**Causa 1: [L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 2: [L'agente installato nella macchina virtuale non è aggiornato (per le macchine virtuali Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [La macchina virtuale non ha accesso a Internet](#the-vm-has-no-internet-access)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - È stato raggiunto il limite massimo di raccolte di punti di ripristino

**Codice di errore**: UserErrorRpCollectionLimitReached <br>
**Messaggio di errore**: The Restore Point collection max limit has reached (È stato raggiunto il limite massimo di raccolte di punti di ripristino) <br>
Descrizione:  
* Questo problema può verificarsi se è presente un blocco sul gruppo di risorse dei punti di ripristino che impedisce la pulizia automatica dei punti di ripristino.
* Questo problema può verificarsi anche se vengono attivate più operazioni di backup al giorno. Attualmente è consigliabile eseguire un solo backup al giorno perché i punti di ripristino immediati vengono conservati per 7 giorni e a una VM possono essere associati solo 18 punti di ripristino immediati in qualsiasi momento. <br>

Azione consigliata:<br>
Per risolvere questo problema, rimuovere il blocco sul gruppo di risorse e ripetere l'operazione per attivare la pulizia.

> [!NOTE]
    > Il servizio di backup crea un gruppo di risorse distinto da quello della macchina virtuale per archiviare la raccolta di punti di ripristino. È consigliabile che i clienti non blocchino il gruppo di risorse creato per l'uso dal servizio di backup. Il formato del nome del gruppo di risorse creato dal servizio Backup è: AzureBackupRG_`<Geo>`_`<number>`, ad esempio AzureBackupRG_nordeuropa_1.


**Passaggio 1: [Rimuovere il blocco dal gruppo di risorse dei punti di ripristino](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Passaggio 2: [Pulire la raccolta di punti di ripristino](#clean_up_restore_point_collection)**<br>

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - Operazione di creazione snapshot non riuscita a causa dell'assenza della connettività di rete nella macchina virtuale

**Codice errore**: ExtensionSnapshotFailedNoNetwork<br>
**Messaggio di errore**: Snapshot operation failed due to no network connectivity on the virtual machine (Operazione di creazione snapshot non riuscita a causa dell'assenza della connettività di rete nella macchina virtuale)<br>

Dopo la registrazione e la pianificazione di una macchina virtuale per il servizio Backup di Azure, tale servizio avvia il processo comunicando con l'estensione di backup della macchina virtuale per la creazione di uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Se lo snapshot non viene attivato, può verificarsi un errore di backup. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:    
**Causa 1: [La macchina virtuale non ha accesso a Internet](#the-vm-has-no-internet-access)**  
**Causa 2: [Non è possibile recuperare lo stato dello snapshot o acquisire uno snapshot](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 3: [Non è possibile aggiornare o caricare l'estensione di backup](#the-backup-extension-fails-to-update-or-load)**  

## <a name="ExtentionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtentionOperationFailed - Operazione dell'estensione VMSnapshot non riuscita

**Codice errore**: ExtentionOperationFailed <br>
**Messaggio di errore**: VMSnapshot extension operation failed (Operazione dell'estensione VMSnapshot non riuscita)<br>

Dopo la registrazione e la pianificazione di una macchina virtuale per il servizio Backup di Azure, tale servizio avvia il processo comunicando con l'estensione di backup della macchina virtuale per la creazione di uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Se lo snapshot non viene attivato, può verificarsi un errore di backup. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:  
**Causa 1: [Non è possibile recuperare lo stato dello snapshot o acquisire uno snapshot](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [Non è possibile aggiornare o caricare l'estensione di backup](#the-backup-extension-fails-to-update-or-load)**  
**Causa 3: [L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 4: [L'agente installato nella macchina virtuale non è aggiornato (per le macchine virtuali Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 - Errore interno di backup

**Codice errore**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Messaggio di errore**: Backup failed with an internal error - Please retry the operation in a few minutes (Errore interno di backup. Attendere qualche minuto prima di ripetere l'operazione) <br>

Dopo la registrazione e la pianificazione di una macchina virtuale per il servizio Backup di Azure, tale servizio avvia il processo comunicando con l'estensione di backup della macchina virtuale per la creazione di uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Se lo snapshot non viene attivato, può verificarsi un errore di backup. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:  
**Causa 1: [La macchina virtuale non ha accesso a Internet](#the-vm-has-no-internet-access)**  
**Causa 2: [L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 3: [L'agente installato nella macchina virtuale non è aggiornato (per le macchine virtuali Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 4: [Non è possibile recuperare lo stato dello snapshot o acquisire uno snapshot](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 5: [Non è possibile aggiornare o caricare l'estensione di backup](#the-backup-extension-fails-to-update-or-load)**  
**Causa 6: [Il servizio di backup non ha l'autorizzazione per eliminare i punti di ripristino precedenti a causa di un blocco del gruppo di risorse](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)**

## <a name="causes-and-solutions"></a>Cause e soluzioni

### <a name="the-vm-has-no-internet-access"></a>La macchina virtuale non ha accesso a Internet
In base al requisito di distribuzione, la macchina virtuale non ha accesso a Internet. In alternativa, potrebbero esserci restrizioni che impediscono l'accesso all'infrastruttura di Azure.

Per funzionare correttamente, l'estensione di backup richiede la connettività agli indirizzi IP pubblici di Azure. L'estensione invia comandi a un endpoint di archiviazione di Azure (URL HTTP) per gestire gli snapshot della macchina virtuale. Se l'estensione non ha accesso a Internet pubblico, il backup ha esito negativo.

È possibile distribuire un server proxy per instradare il traffico delle VM.
##### <a name="create-a-path-for-https-traffic"></a>Creare un percorso per il traffico HTTP

1. Se sono state applicate restrizioni di rete, ad esempio un gruppo di sicurezza di rete, distribuire un server proxy HTTP per indirizzare il traffico.
2. Per consentire l'accesso a Internet dal server proxy HTTP, aggiungere regole al gruppo di sicurezza di rete, se disponibile.

Per informazioni su come configurare un proxy HTTP per i backup delle macchine virtuali, vedere [Preparare l'ambiente per il backup di macchine virtuali di Azure](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

La VM di backup o il server proxy tramite cui viene indirizzato il traffico richiede l'accesso agli indirizzi IP pubblici di Azure

####  <a name="solution"></a>Soluzione
Per risolvere il problema, utilizzare uno dei seguenti metodi alternativi:

##### <a name="allow-access-to-azure-storage-that-corresponds-to-the-region"></a>Consentire l'accesso alle risorse di archiviazione di Azure corrispondenti all'area

È possibile usare i [tag di servizio](../virtual-network/security-overview.md#service-tags) per consentire le connessioni alle risorse di archiviazione dell'area specifica. Verificare che la regola che consente l'accesso all'account di archiviazione abbia priorità più alta rispetto alla regola che blocca l'accesso a Internet.

![Gruppo di sicurezza di rete con tag di archiviazione per un'area](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

Per comprendere la procedura dettagliata per configurare i tag di servizio, guardare [questo video](https://youtu.be/1EjLQtbKm1M).

> [!WARNING]
> I tag del servizio di archiviazione sono in versione di anteprima. Sono disponibili solo in aree specifiche. Per un elenco delle aree, vedere [Tag di servizio per l'archiviazione](../virtual-network/security-overview.md#service-tags).

Se si usa Azure Managed Disks, può essere necessario aprire una porta aggiuntiva (8443) nei firewall.

Inoltre, se la subnet non ha una route per il traffico in uscita a Internet sarà necessario aggiungere un endpoint di servizio con il tag di servizio "Microsoft.Storage" alla subnet.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)

#### <a name="solution"></a>Soluzione
L'agente di macchine virtuali può essere danneggiato o il servizio può essere stato arrestato. Reinstallando l'agente di macchine virtuali è possibile ottenere la versione più recente. In questo modo sarà anche possibile riavviare la comunicazione con il servizio.

1. Determinare se il servizio agente guest di Windows è in esecuzione nei servizi delle macchine virtuali (services.msc). Provare a riavviare il servizio agente guest di Windows e avviare il backup.    
2. Se il servizio agente guest di Windows non è visibile tra i servizi, nel Pannello di controllo passare a **Programmi e funzionalità** per determinare se è installato.
4. Se il servizio agente guest di Windows è elencato in **Programmi e funzionalità**, disinstallarlo.
5. Scaricare e installare la [versione più recente del file MSI dell'agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione sono necessari i diritti di amministratore.
6. Verificare che il servizio agente guest di Windows sia visualizzato tra i servizi.
7. Eseguire un backup su richiesta:
    * Nel portale selezionare **Esegui backup ora**.

Verificare anche che [Microsoft .NET 4.5 sia installato](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nella macchina virtuale. .NET 4.5 è necessario per la comunicazione dell'agente di macchine virtuali con il servizio.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>L'agente installato nella VM Linux non è aggiornato (per VM Linux)

#### <a name="solution"></a>Soluzione
La maggior parte degli errori relativi ad agenti o estensioni nelle macchine virtuali Linux è dovuta a problemi correlati ad agenti di macchine virtuali non aggiornati. Per risolvere il problema, seguire queste indicazioni generali:

1. Seguire le istruzioni per l'[aggiornamento dell'agente di macchine virtuali Linux](../virtual-machines/linux/update-agent.md).

 > [!NOTE]
 > È *fortemente consigliato* aggiornare l'agente solo tramite un repository di distribuzione. Non è consigliabile scaricare il codice dell'agente direttamente da GitHub e aggiornarlo. Se l'agente più recente per la distribuzione non è disponibile, contattare il supporto per la distribuzione per istruzioni su come installarlo. Per cercare l'agente più recente, passare alla pagina dell'[agente Linux di Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) nel repository GitHub.

2. Assicurarsi che l'agente di Azure sia in esecuzione nella macchina virtuale eseguendo il comando seguente: `ps -e`

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
| La macchina virtuale non riesce a ottenere l'indirizzo dell'host o dell'infrastruttura da DHCP. | DHCP deve essere abilitato nel computer guest per consentire il funzionamento del backup delle VM IaaS. Se la macchina virtuale non riesce a ottenere l'indirizzo dell'host o dell'infrastruttura da DHCP, con risposta 245, non è possibile scaricare o eseguire le estensioni. Se è necessario un indirizzo IP privato statico, configurarlo tramite la piattaforma. L'opzione DHCP all'interno della VM deve essere abilitata. Per altre informazioni, vedere [Impostare un indirizzo IP privato interno statico](../virtual-network/virtual-networks-reserved-private-ip.md). |

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
1. Accedere al [portale di Azure](http://portal.azure.com/).
2. Passare a **Tutte le risorse**, selezionare il gruppo di risorse della raccolta dei punti di ripristino con il formato AzureBackupRG_<Geo>_<number>.
3. Nella sezione **Impostazioni** selezionare **Blocchi** per visualizzare i blocchi.
4. Per rimuovere il blocco, selezionare i puntini di sospensione e fare clic su **Elimina**.

    ![Eliminare un blocco ](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a> Pulire la raccolta di punti di ripristino
Dopo aver rimosso il blocco, è necessario eseguire la pulizia dei punti di ripristino. A questo scopo, seguire uno dei metodi seguenti:<br>
* [Pulire la raccolta di punti di ripristino con l'esecuzione di un backup ad hoc](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
* [Pulire la raccolta di punti di ripristino dal portale creato dal servizio Backup](#clean-up-restore-point-collection-from-portal-created-by-backup-service)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>Pulire la raccolta di punti di ripristino con l'esecuzione di un backup ad hoc
Dopo aver rimosso il blocco, attivare un backup ad hoc/manuale. In tal modo, i punti di ripristino verranno automaticamente puliti. È probabile che questa operazione di backup ad hoc/manuale non riesca al primo tentativo. Servirà tuttavia a garantire la pulizia automatica anziché l'eliminazione manuale dei punti di ripristino. Dopo la pulizia, il successivo backup pianificato avrà esito positivo.

> [!NOTE]
    > La pulizia automatica viene eseguita qualche ora dopo l'attivazione del backup ad hoc/manuale. Se il backup pianificato continua ad avere esito negativo, provare a eliminare manualmente la raccolta di punti di ripristino seguendo la procedura illustrata [qui](#clean-up-restore-point-collection-from-portal-created-by-backup-service).

#### <a name="clean-up-restore-point-collection-from-portal-created-by-backup-service"></a>Pulire la raccolta di punti di ripristino dal portale creato dal servizio Backup<br>

Per cancellare manualmente la raccolta di punti di ripristino che non viene cancellata a causa del blocco sul gruppo di risorse, seguire questa procedura:
1. Accedere al [portale di Azure](http://portal.azure.com/).
2. Nel menu **Hub** fare clic su **Tutte le risorse** e selezionare il gruppo di risorse con il formato AzureBackupRG_`<Geo>`_`<number>` nella posizione in cui si trova la VM.

    ![Eliminare un blocco ](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Facendo clic sul gruppo di risorse viene visualizzato il pannello **Panoramica**.
4. Selezionare l'opzione **Mostra tipi nascosti** per visualizzare tutte le risorse nascoste. Selezionare le raccolte di punti di ripristino con il formato AzureBackupRG_`<VMName>`_`<number>`.

    ![Eliminare un blocco ](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Fare clic su **Elimina** per pulire la raccolta di punti di ripristino.
6. Ripetere l'operazione di backup.
