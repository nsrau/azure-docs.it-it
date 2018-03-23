---
title: "Risolvere un problema di Backup di Azure: stato dell'agente guest non disponibile | Microsoft Docs"
description: Sintomi, cause e soluzioni per i problemi di Backup di Azure correlati all'agente, all'estensione e ai dischi.
services: backup
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
keywords: Backup di Azure; agente di macchine virtuali; connettività di rete;
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: genli;markgal;sogup;
ms.openlocfilehash: a18718aba3ef7f70caa541c6eb56311082d02bed
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Risolvere i problemi di Backup di Azure: problemi relativi all'agente o all'estensione

Questo articolo illustra le procedure di risoluzione dei problemi che possono essere utili per risolvere gli errori di Backup di Azure correlati alla comunicazione con l'agente di macchine virtuali e all'estensione.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>Errore di comunicazione dell'agente di macchine virtuali con Backup di Azure

Messaggio di errore: "L'agente di macchine virtuali non riesce a comunicare con il servizio Backup di Azure"

Dopo la registrazione e la pianificazione di una macchina per il servizio Backup, tale servizio avvia il processo comunicando con l'agente di macchine virtuali per creare uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Quando uno snapshot non viene attivato, il backup potrebbe non riuscire. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:

**Causa 1: [La macchina virtuale non ha accesso a Internet](#the-vm-has-no-internet-access)**  
**Causa 2: [L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Causa 3: [L'agente installato nella macchina virtuale non è aggiornato (per le macchine virtuali Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 4: [Non è possibile recuperare lo stato dello snapshot o acquisire uno snapshot](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**Causa 5: [Non è possibile aggiornare o caricare l'estensione di backup](#the-backup-extension-fails-to-update-or-load)**  

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>Errore dell'operazione di creazione snapshot a causa dell'assenza di connettività di rete nella macchina virtuale

Messaggio di errore: "L'operazione di creazione snapshot non è riuscita perché la connettività di rete è assente nella macchina virtuale"

Dopo la registrazione e la pianificazione di una macchina virtuale per il servizio Backup di Azure, tale servizio avvia il processo comunicando con l'estensione di backup della macchina virtuale per la creazione di uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Se lo snapshot non viene attivato, può verificarsi un errore di backup. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:    
**Causa 1: [La macchina virtuale non ha accesso a Internet](#the-vm-has-no-internet-access)**  
**Causa 2: [Non è possibile recuperare lo stato dello snapshot o acquisire uno snapshot](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 3: [Non è possibile aggiornare o caricare l'estensione di backup](#the-backup-extension-fails-to-update-or-load)**  

## <a name="vmsnapshot-extension-operation-failed"></a>Errore dell'operazione di estensione VMSnapshot

Messaggio di errore: "L'operazione di estensione VMSnapshot non è riuscita"

Dopo la registrazione e la pianificazione di una macchina virtuale per il servizio Backup di Azure, tale servizio avvia il processo comunicando con l'estensione di backup della macchina virtuale per la creazione di uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Se lo snapshot non viene attivato, può verificarsi un errore di backup. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:  
**Causa 1: [Non è possibile recuperare lo stato dello snapshot o acquisire uno snapshot](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [Non è possibile aggiornare o caricare l'estensione di backup](#the-backup-extension-fails-to-update-or-load)**  
**Causa 3: [L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 4: [L'agente installato nella macchina virtuale non è aggiornato (per le macchine virtuali Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backup-fails-because-the-vm-agent-is-unresponsive"></a>Errore di backup a causa della mancata risposta da parte dell'agente di macchine virtuali

Messaggio di errore: "Non è possibile eseguire l'operazione perché l'agente di macchine virtuali non risponde"

Dopo la registrazione e la pianificazione di una macchina virtuale per il servizio Backup di Azure, tale servizio avvia il processo comunicando con l'estensione di backup della macchina virtuale per la creazione di uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Se lo snapshot non viene attivato, può verificarsi un errore di backup. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:  
**Causa 1: [L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 2: [L'agente installato nella macchina virtuale non è aggiornato (per le macchine virtuali Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [La macchina virtuale non ha accesso a Internet](#the-vm-has-no-internet-access)**  

## <a name="backup-fails-with-an-internal-error"></a>Errore interno di backup

Messaggio di errore: "Il backup non è riuscito e si è verificato un errore interno. Ripetere l'operazione tra qualche minuto"

Dopo la registrazione e la pianificazione di una macchina virtuale per il servizio Backup di Azure, tale servizio avvia il processo comunicando con l'estensione di backup della macchina virtuale per la creazione di uno snapshot temporizzato. Una delle condizioni seguenti può impedire l'attivazione dello snapshot. Se lo snapshot non viene attivato, può verificarsi un errore di backup. Seguire questi passaggi per la risoluzione dei problemi nell'ordine specificato e provare a eseguire di nuovo l'operazione:  
**Causa 1: [La macchina virtuale non ha accesso a Internet](#the-vm-has-no-internet-access)**  
**Causa 2: [L'agente è installato nella macchina virtuale ma non risponde (per le macchine virtuali Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 3: [L'agente installato nella macchina virtuale non è aggiornato (per le macchine virtuali Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 4: [Non è possibile recuperare lo stato dello snapshot o acquisire uno snapshot](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 5: [Non è possibile aggiornare o caricare l'estensione di backup](#the-backup-extension-fails-to-update-or-load)**  
**Causa 6: [Il servizio di backup non ha l'autorizzazione per eliminare i punti di ripristino precedenti a causa di un blocco del gruppo di risorse](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)**

## <a name="disk-configuration-is-not-supported"></a>Configurazione del disco non supportata

Messaggio di errore: "The specified Disk configuration is not supported" (La configurazione del disco specificata non è supportata)

> [!NOTE]
> È disponibile un'anteprima privata per il supporto di backup per macchine virtuali con dischi di dimensioni superiori a 1 TB. Per informazioni dettagliate, vedere l'[anteprima privata per il supporto del backup di macchine virtuali con dischi di grandi dimensioni](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a).
>
>

Attualmente Backup di Azure non supporta dischi di dimensioni [superiori a 1023 GB](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm). Se si hanno dischi con dimensioni superiori a 1 TB:  
1. [Collegare nuovi dischi](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) con dimensioni inferiori a 1 TB.  
2. Copiare i dati dai dischi con dimensioni superiori a 1 TB nei nuovi dischi creati con dimensioni inferiori a 1 TB.  
3. Verificare che tutti i dati siano stati copiati. Rimuovere quindi i dischi con dimensioni superiori a 1 TB.  
4. Avviare il backup.

## <a name="causes-and-solutions"></a>Cause e soluzioni

### <a name="the-vm-has-no-internet-access"></a>La macchina virtuale non ha accesso a Internet
In base al requisito di distribuzione, la macchina virtuale non ha accesso a Internet. In alternativa, potrebbero esserci restrizioni che impediscono l'accesso all'infrastruttura di Azure.

Per funzionare correttamente, l'estensione di backup richiede la connettività agli indirizzi IP pubblici di Azure. L'estensione invia comandi a un endpoint di archiviazione di Azure (URL HTTP) per gestire gli snapshot della macchina virtuale. Se l'estensione non ha accesso a Internet pubblico, il backup ha esito negativo.

####  <a name="solution"></a>Soluzione
Per risolvere il problema, utilizzare uno dei seguenti metodi alternativi:

##### <a name="allow-access-to-azure-storage-that-corresponds-to-the-region"></a>Consentire l'accesso alle risorse di archiviazione di Azure corrispondenti all'area

È possibile usare i [tag di servizio](../virtual-network/security-overview.md#service-tags) per consentire le connessioni alle risorse di archiviazione dell'area specifica. Verificare che la regola che consente l'accesso all'account di archiviazione abbia priorità più alta rispetto alla regola che blocca l'accesso a Internet. 

![Gruppo di sicurezza di rete con tag di archiviazione per un'area](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

> [!WARNING]
> I tag del servizio di archiviazione sono in versione di anteprima. Sono disponibili solo in aree specifiche. Per un elenco delle aree, vedere [Tag di servizio per l'archiviazione](../virtual-network/security-overview.md#service-tags).

##### <a name="create-a-path-for-http-traffic"></a>Creare un percorso per il traffico HTTP

1. Se sono state applicate restrizioni di rete, ad esempio un gruppo di sicurezza di rete, distribuire un server proxy HTTP per indirizzare il traffico.
2. Per consentire l'accesso a Internet dal server proxy HTTP, aggiungere regole al gruppo di sicurezza di rete, se disponibile.

Per informazioni su come configurare un proxy HTTP per i backup delle macchine virtuali, vedere [Preparare l'ambiente per il backup di macchine virtuali di Azure](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

Se si usa Azure Managed Disks, può essere necessario aprire una porta aggiuntiva (8443) nei firewall.

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

Questa procedura fa in modo che l'estensione venga reinstallata durante il backup successivo.

### <a name="backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock"></a>Il servizio di backup non ha l'autorizzazione per eliminare i punti di ripristino precedenti a causa di un blocco del gruppo di risorse
Questo problema è specifico delle macchine virtuali gestite in cui l'utente blocca il gruppo di risorse. In questo caso, il servizio di backup non può eliminare i punti di ripristino precedenti. Poiché è previsto un limite di 18 punti di ripristino, i nuovi backup iniziano ad avere esito negativo.

#### <a name="solution"></a>Soluzione

Per risolvere il problema, seguire la procedura illustrata di seguito per rimuovere la raccolta di punti di ripristino: <br>
 
1. Rimuovere il blocco nel gruppo di risorse in cui si trova la macchina virtuale. 
2. Installare ARMClient usando Chocolatey: <br>
   https://github.com/projectkudu/ARMClient
3. Accedere ad ARMClient: <br>
    `.\armclient.exe login`
4. Ottenere la raccolta di punti di ripristino corrispondente alla macchina virtuale: <br>
    `.\armclient.exe get https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30`

    Esempio: `.\armclient.exe get https://management.azure.com/subscriptions/f2edfd5d-5496-4683-b94f-b3588c579006/resourceGroups/winvaultrg/providers/Microsoft.Compute/restorepointcollections/AzureBackup_winmanagedvm?api-version=2017-03-30`
5. Eliminare la raccolta di punti di ripristino: <br>
    `.\armclient.exe delete https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30` 
6. Il successivo backup pianificato crea automaticamente una raccolta di punti di ripristino e i nuovi punti di ripristino.

 
Il problema si verificherà di nuovo se il gruppo di risorse viene bloccato nuovamente. 

