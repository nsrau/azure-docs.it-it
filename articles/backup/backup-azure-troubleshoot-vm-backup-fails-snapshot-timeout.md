---
title: "Errore di backup delle VM di Azure: impossibile comunicare con l&quot;agente della macchina virtuale sullo stato dello snapshot a causa del timeout della sottoattività di creazione snapshot della macchina virtuale | Documentazione Microsoft"
description: "Sintomi, cause e soluzioni per gli errori di backup delle macchine virtuali di Azure correlati a: Non è stato possibile comunicare con l&quot;agente della macchina virtuale per lo stato dello snapshot. Si è verificato il timeout della sottoattività di creazione snapshot della macchina virtuale."
services: backup
documentationcenter: 
author: genlin
manager: cfreeman
editor: 
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jimpark; markgal;genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 87387e4f182214fa0c34a6a1358c6cc2648be906


---
# <a name="azure-vm-backup-fails-could-not-communicate-with-the-vm-agent-for-snapshot-status---snapshot-vm-sub-task-timed-out"></a>Errore di backup delle VM di Azure: non è stato possibile comunicare con l'agente della macchina virtuale per lo stato dello snapshot a causa del timeout della sottoattività di creazione snapshot della macchina virtuale
## <a name="summary"></a>Riepilogo
Dopo la registrazione e la pianificazione di una macchina virtuale (VM) per il servizio Backup di Azure, il servizio avvia il processo di backup all'ora pianificata comunicando con l'estensione di backup nella macchina virtuale per acquisire uno snapshot temporizzato. Alcune condizioni potrebbero impedire l'attivazione dello snapshot e determinare un errore di backup. Questo articolo fornisce istruzioni per la risoluzione di problemi relativi a errori di backup di VM di Azure correlati all'errore di timeout dello snapshot.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Sintomo
Backup di Microsoft Azure per VM IaaS (infrastruttura distribuita come servizio) ha esito negativo e restituisce il messaggio di errore seguente nei dettagli errore processo nel [portale di Azure](https://portal.azure.com/):

**Non è stato possibile comunicare con l'agente della macchina virtuale per lo stato dello snapshot. Si è verificato il timeout della sottoattività di creazione snapshot della macchina virtuale.**

## <a name="cause"></a>Causa
Questo errore può essere dovuto a quattro cause comuni:

* La macchina virtuale non ha accesso a Internet.
* L'agente di macchine virtuali di Microsoft Azure installato nella VM Linux non è aggiornato.
* Non è possibile aggiornare o caricare l'estensione di backup.
* Non è possibile recuperare lo stato degli snapshot o non è possibile acquisire gli snapshot.

## <a name="cause-1-the-vm-does-not-have-internet-access"></a>Causa 1: la macchina virtuale non ha accesso a Internet
In base al requisito di distribuzione, la macchina virtuale non ha accesso a Internet o sono presenti restrizioni che impediscono l'accesso all'infrastruttura di Azure.

L'estensione di backup richiede che la connettività agli indirizzi IP pubblici di Azure funzioni correttamente, perché invia comandi a un endpoint di Archiviazione di Azure (URL HTTP) per la gestione degli snapshot della macchina virtuale. Se l'estensione non ha accesso a Internet pubblico, il backup ha esito negativo.

### <a name="solution"></a>Soluzione
In questo scenario usare uno dei metodi seguenti per risolvere il problema:

* Aggiungere gli intervalli IP dei data center di Azure all'elenco elementi consentiti.
* Creare un percorso per il flusso del traffico HTTP

### <a name="to-whitelist-the-azure-datacenter-ip-ranges"></a>Per aggiungere gli intervalli IP dei data center di Azure all'elenco elementi consentiti
1. Ottenere l'elenco di [indirizzi IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) da aggiungere all'elenco elementi consentiti.
2. Sbloccare gli indirizzi IP usando il cmdlet New-NetRoute. Eseguire questo cmdlet nella macchina virtuale di Azure, in una finestra di PowerShell con privilegi elevati, eseguita come amministratore.
3. Aggiungere regole al gruppo di sicurezza di rete, se presente, per consentire l'accesso agli indirizzi IP.

### <a name="to-create-a-path-for-http-traffic-to-flow"></a>Per creare un percorso per il flusso del traffico HTTP
1. Se sono presenti restrizioni di rete, ad esempio come gruppo di sicurezza di rete, distribuire un server proxy HTTP per instradare il traffico.
2. Se è presente un gruppo di sicurezza di rete (NSG), aggiungere regole per consentire l'accesso a Internet dal proxy HTTP.

Sono disponibili informazioni su come [configurare un proxy HTTP per i backup di macchine virtuali](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

## <a name="cause-2-the-microsoft-azure-vm-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Causa 2: l'agente di macchine virtuali di Microsoft Azure installato nella VM Linux non è aggiornato
### <a name="solution"></a>Soluzione
La maggior parte degli errori relativi ad agenti o estensioni nelle macchine virtuali Linux è dovuta a problemi correlati ad agenti di macchine virtuali non aggiornati. In generale, per risolvere questo problema occorre prima di tutto seguire questa procedura:

1. [Installare la versione più recente dell'agente di macchine virtuali di Azure](https://github.com/Azure/WALinuxAgent).
2. Assicurarsi che l'agente di Azure sia in esecuzione nella macchina virtuale. A tale scopo, eseguire il comando seguente:      ```ps -e```
   
    Se il processo non è in esecuzione, usare i comandi seguenti per riavviarlo.
   
    Per Ubuntu:     ```service walinuxagent start```
   
    Per altre distribuzioni:     ```avvio waagent del servizio
   ```
3. [Configure the auto restart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Run a new test backup. If the failures persist, please collect logs from the following folders for further debugging.
   
    We require the following logs from the customer’s VM:
   
   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

If we require verbose logging for waagent, follow these steps to enable this:

1. In the /etc/waagent.conf file, locate the following line:
   
    Enable verbose logging (y|n)
2. Change the **Logs.Verbose** value from n to y.
3. Save the change, and then restart waagent by following the previous steps in this section.

## Cause 3: The backup extension fails to update or load
If extensions cannot be loaded, then Backup fails because a snapshot cannot be taken.

### Solution
For Windows guests:

1. Verify that the iaasvmprovider service is enabled and has a startup type of automatic.
2. If this is not the configuration, enable the service to determine whether the next backup succeeds.

For Linux guests:

The latest version of VMSnapshot Linux (extension used by backup) is 1.0.91.0

If the backup extension still fails to update or load, you can force the VMSnapshot extension to be reloaded by uninstalling the extension. The next backup attempt will reload the extension.

### To uninstall the extension
1. Go to the [Azure portal](https://portal.azure.com/).
2. Locate the particular VM that has backup problems.
3. Click **Settings**.
4. Click **Extensions**.
5. Click **Vmsnapshot Extension**.
6. Click **uninstall**.

This will cause the extension to be reinstalled during the next backup.

## Cause 4: The snapshots status cannot be retrieved or the snapshots cannot be taken
VM backup relies on issuing snapshot command to underlying storage. The backup can fail because it has no access to storage or because of a delay in snapshot task execution.

### Solution
The following conditions can cause snapshot task failure:

| Cause | Solution |
| --- | --- |
| VMs that have Microsoft SQL Server Backup configured. By default, VM Backup runs a VSS Full backup on Windows VMs. On VMs that are running SQL Server-based servers and on which SQL Server Backup is configured, snapshot execution delays may occur. |Set following registry key if you are experiencing backup failures because of snapshot issues.<br><br>[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| VM status is reported incorrectly because the VM is shut down in RDP. If you shut down the virtual machine in RDP, check the portal to determine whether that VM status is reflected correctly. |If it’s not, shut down the VM in the portal by using the ”Shutdown” option in the VM dashboard. |
| Many VMs from the same cloud service are configured to back up at the same time. |It’s a best practice to spread out the VMs from the same cloud service to have different backup schedules. |
| The VM is running at high CPU or memory usage. |If the VM is running at high CPU usage (more than 90 percent) or high memory usage, the snapshot task is queued and delayed and eventually times out. In this situation, try on-demand backup. |
| The VM cannot get host/fabric address from DHCP. |DHCP must be enabled inside the guest for IaaS VM Backup to work.  If the VM cannot get host/fabric address from DHCP response 245, then it cannot download ir run any extensions. If you need a static private IP, you should configure it through the platform. The DHCP option inside the VM should be left enabled. View more information about [Setting a Static Internal Private IP](../virtual-network/virtual-networks-reserved-private-ip.md). |




<!--HONumber=Nov16_HO3-->


