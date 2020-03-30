---
title: Supporto per la migrazione Hyper-V in Azure MigrateSupport for Hyper-V migration in Azure Migrate
description: Informazioni sul supporto per la migrazione Hyper-V con Azure Migrate.Learn about support for Hyper-V migration with Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1eab96df7ee58a8170f75b41c5a2a06f033ced19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245824"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Matrice di supporto per la migrazione hyper-VSupport matrix for Hyper-V migration

Questo articolo riepiloga le impostazioni di supporto e le limitazioni per la migrazione delle macchine virtuali Hyper-V con [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) . Per informazioni sulla valutazione delle macchine virtuali Hyper-V per la migrazione ad Azure, esaminare la matrice di supporto di [valutazione.](migrate-support-matrix-hyper-v.md)

## <a name="migration-limitations"></a>Limitazioni della migrazione

È possibile selezionare fino a 10 macchine virtuali contemporaneamente per la replica. Se si desidera eseguire la migrazione di più computer, replicare in gruppi di 10.If you want to migrate more machines, replicate in groups of 10.


## <a name="hyper-v-hosts"></a>Host Hyper-V

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione**       | L'host Hyper-V può essere autonomo o distribuito in un cluster. <br/>Il software di replica di Azure Migrate (provider di replica Hyper-V) deve essere installato negli host Hyper-V.Azure Migrate replication software (Hyper-V Replication provider) needs to be installed on the Hyper-V hosts.|
| **Autorizzazioni**           | Sono necessarie le autorizzazioni di amministratore per l'host Hyper-V. |
| **Sistema operativo host** | Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2. |
| **Accesso tramite URL** | Il software del provider di replica negli host Hyper-V dovrà accedere a questi URL:<br/><br/> - login.microsoftonline.com: controllo degli accessi e gestione delle identità tramite Active Directory.- for a or: Access control and identity management using Active Directory.<br/><br/> - Backup.windowsazure.com: trasferimento e coordinamento dei dati di replica. Eseguire la migrazione degli URL del servizio.<br/><br/> - .blob.core.windows.net: caricare i dati in account di archiviazione.<br/><br/> - dc.services.visualstudio.com: caricare i log dell'app usati per il monitoraggio interno.- for a l' used for app logs used for internal monitoring.<br/><br/> - time.windows.com: verifica la sincronizzazione dell'ora tra il sistema e l'ora globale.- time.windows.com: Verifies time synchronization between system and global time.
| **Accesso alla porta** |  Connessioni in uscita sulla porta HTTPS 443 per inviare i dati di replica della macchina virtuale.

## <a name="hyper-v-vms"></a>VM Hyper-V

| **Supporto**                  | **Dettagli**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Tutti i sistemi operativi Windows e Linux supportati da Azure.All [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) and [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) operating systems that are supported by Azure. |
| **Modifiche necessarie per AzureRequired changes for Azure** | Alcune macchine virtuali potrebbero richiedere alcune modifiche per la corretta esecuzione in Azure. È necessario apportare le modifiche manualmente prima della migrazione. Gli articoli pertinenti contengono istruzioni su come eseguire questa operazione. |
| **Avvio Linux**                 | Se /boot si trova in una partizione dedicata, deve risiedere sul disco del sistema operativo e non essere distribuito su più dischi.<br/> Se /boot fa parte della partizione radice (/), la partizione '/' deve trovarsi sul disco del sistema operativo e non estendersi su altri dischi. |
| **Stivale UEFI**                  | La macchina virtuale migrata in Azure verrà convertita automaticamente in una macchina virtuale di avvio del BIOS. La macchina virtuale deve eseguire solo Windows Server 2012 e versioni successive. Il disco del sistema operativo deve avere fino a cinque partizioni o meno e la dimensione del disco del sistema operativo deve essere inferiore a 300 GB.
  |
| **Dimensioni disco**                  | 2 TB per il disco del sistema operativo, 4 TB per i dischi dati.
| **Numero disco** | Un massimo di 16 dischi per macchina virtuale.
| **Dischi/volumi crittografati**    | Non supportato per la migrazione. |
| **Dischi RDM/passthrough**      | Non supportato per la migrazione. |
| **Disco condiviso** | Le macchine virtuali che usano dischi condivisi non sono supportate per la migrazione.
| **NFS**                        | I volumi NFS montati come volumi nelle macchine virtuali non verranno replicati. |
| **Iscsi**                      | Le macchine virtuali con destinazioni iSCSI non sono supportate per la migrazione.
| **Disco di destinazione**                | È possibile eseguire la migrazione a macchine virtuali di Azure solo con dischi gestiti. |
| **IPv6** | Non supportato.
| **Raggruppamento NIC** | Non supportato.
| **Ripristino del sito di AzureAzure Site Recovery** | Non è possibile eseguire la replica usando Migrazione server di migrazione di Azure se la macchina virtuale è abilitata per la replica con Azure Site Recovery.You can't replicate using Azure Migrate Server Migration if the VM is enabled for replication with Azure Site Recovery.
| **Ports** | Connessioni in uscita sulla porta HTTPS 443 per inviare i dati di replica della macchina virtuale.

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

Tutte le macchine virtuali locali replicate in Azure devono soddisfare i requisiti delle macchine virtuali di Azure riepilogati in questa tabella.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
Dimensioni disco del sistema operativo | Fino a 2.048 GB. | Il controllo ha esito negativo se non supportato.
Conteggio dischi del sistema operativo | 1 | Il controllo ha esito negativo se non supportato.
Conteggio dischi dati | 16 o meno. | Il controllo ha esito negativo se non supportato.
Dimensioni del disco dati | Fino a 4.095 GB | Il controllo ha esito negativo se non supportato.
Schede di rete | Sono supportate più schede. |
VHD condiviso | Non supportato. | Il controllo ha esito negativo se non supportato.
Disco FC | Non supportato. | Il controllo ha esito negativo se non supportato.
BitLocker | Non supportato. | Prima di abilitare la replica per un computer, occorre disabilitare BitLocker.
Nome della VM. | Da 1 a 63 caratteri.<br/> Limitato a lettere, numeri e trattini.<br/><br/> Il nome del computer deve iniziare e terminare con una lettera o un numero. |  Aggiornare il valore nelle proprietà del computer in Site Recovery.
Connettersi dopo la migrazione di Windows | Per connettersi alle macchine virtuali di Azure che eseguono Windows dopo la migrazione:To connect to Azure VMs running Windows after migration:<br/> - Prima che la migrazione abiliti RDP nella macchina virtuale locale.- Before migration enables RDP on the on-premises VM. Assicurarsi che le regole TCP e UDP vengano aggiunte per il profilo **pubblico** e che RDP sia consentito in **Windows Firewall** > **Allowed Apps**per tutti i profili.<br/> Per l'accesso VPN da sito a sito, abilitare RDP e consentire RDP in **Windows Firewall** -> **Allowed apps and features** for Domain **and Private** networks. Verificare inoltre che il criterio SAN del sistema operativo sia impostato su **OnlineAll**. [Scopri di più](prepare-for-migration.md). |
Connessione dopo la migrazione Linux | To connect to Azure VMs after migration using SSH:<br/> Prima della migrazione, nel computer locale, verificare che il servizio Secure Shell sia impostato su Avvia e che le regole del firewall consentano una connessione SSH.<br/> Dopo il failover, nella macchina virtuale di Azure, consentire le connessioni in ingresso alla porta SSH per le regole del gruppo di sicurezza di rete nella macchina virtuale di cui è stato eseguito il failover e per la subnet di Azure a cui è connessa. Aggiungere inoltre un indirizzo IP pubblico per la macchina virtuale. |  

## <a name="next-steps"></a>Passaggi successivi

[Eseguire la migrazione delle macchine virtuali Hyper-V](tutorial-migrate-hyper-v.md) per la migrazione.
