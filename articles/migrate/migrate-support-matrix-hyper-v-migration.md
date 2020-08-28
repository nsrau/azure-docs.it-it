---
title: Supporto per la migrazione di Hyper-V in Azure Migrate
description: Informazioni sul supporto per la migrazione di Hyper-V con Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 4f3609560fa59c08c4d92f4faa36c7fbbffb95d7
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051152"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Matrice di supporto per la migrazione di Hyper-V

Questo articolo riepiloga le impostazioni di supporto e le limitazioni per la migrazione di macchine virtuali Hyper-V con [Azure migrate: migrazione del server](migrate-services-overview.md#azure-migrate-server-migration-tool) . Per informazioni sulla valutazione delle macchine virtuali Hyper-V per la migrazione ad Azure, vedere la matrice di [supporto](migrate-support-matrix-hyper-v.md)per la valutazione.

## <a name="migration-limitations"></a>Limitazioni della migrazione

È possibile selezionare fino a 10 macchine virtuali contemporaneamente per la replica. Se si desidera eseguire la migrazione di più computer, eseguire la replica in gruppi di 10.


## <a name="hyper-v-host-requirements"></a>Requisiti dell'host Hyper-V

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione**       | L'host Hyper-V può essere autonomo o distribuito in un cluster. <br/>Azure Migrate software di replica (provider di replica Hyper-V) viene installato negli host Hyper-V.|
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore per l'host Hyper-V. |
| **Sistema operativo host** | Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2 con gli aggiornamenti più recenti. Si noti che è supportata anche l'installazione dei componenti di base del server di questi sistemi operativi. |
| **Altri requisiti software** | .NET Framework 4,7 o versione successiva |
| **Accesso alla porta** |  Connessioni in uscita sulla porta HTTPS 443 per inviare i dati di replica della macchina virtuale.
| **Spazio libero su disco (cache)** |  600 GB |
| **Spazio libero su disco (disco di conservazione)** |  600 GB |


## <a name="hyper-v-vms"></a>VM Hyper-V

| **Supporto**                  | **Dettagli**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Tutti i sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](../virtual-machines/linux/endorsed-distros.md) supportati da Azure. |
**Windows Server 2003** | Per le macchine virtuali che eseguono Windows Server 2003, è necessario [installare Hyper-V Integration Services](prepare-windows-server-2003-migration.md) prima della migrazione. | 
**VM Linux in Azure** | Alcune macchine virtuali potrebbero richiedere alcune modifiche per la corretta esecuzione in Azure.<br/><br/> Per Linux, Azure Migrate apporta automaticamente le modifiche per questi sistemi operativi:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8. Per gli altri sistemi operativi apportare manualmente le [modifiche necessarie](prepare-for-migration.md#linux-machines) .
| **Modifiche necessarie per Azure** | Alcune macchine virtuali potrebbero richiedere alcune modifiche per la corretta esecuzione in Azure. Apportare modifiche manualmente prima della migrazione. Gli articoli pertinenti contengono istruzioni su come eseguire questa operazione. |
| **Avvio di Linux**                 | Se/boot si trova in una partizione dedicata, deve risiedere nel disco del sistema operativo e non essere distribuito tra più dischi.<br/> Se/boot fa parte della partizione radice (/), la partizione '/' deve trovarsi nel disco del sistema operativo e non si estende su altri dischi. |
| **Avvio UEFI**                  | Supportata. Assicurarsi di selezionare le dimensioni della macchina virtuale supportate dalla VM di generazione 2 di Azure  |
| **UEFI-avvio protetto**         | Non supportato per la migrazione.|
| **Dimensioni disco**                  | 2 TB per il disco del sistema operativo, 4 TB per i dischi dati.|
| **Numero disco** | Un massimo di 16 dischi per macchina virtuale.|
| **Dischi/volumi crittografati**    | Non supportato per la migrazione.|
| **Dischi RDM/passthrough**      | Non supportato per la migrazione.|
| **Disco condiviso** | Le macchine virtuali che usano dischi condivisi non sono supportate per la migrazione.|
| **NFS**                        | I volumi NFS montati come volumi nelle macchine virtuali non verranno replicati.|
| **ISCSI**                      | Le macchine virtuali con destinazioni iSCSI non sono supportate per la migrazione.
| **Disco di destinazione**                | È possibile eseguire la migrazione solo a macchine virtuali di Azure con Managed Disks. |
| **IPv6** | Non supportata.|
| **Gruppo NIC** | Non supportata.|
| **Azure Site Recovery** | Non è possibile eseguire la replica usando Azure Migrate migrazione del server se la macchina virtuale è abilitata per la replica con Azure Site Recovery.|
| **Ports** | Connessioni in uscita sulla porta HTTPS 443 per inviare i dati di replica della macchina virtuale.|

### <a name="url-access-public-cloud"></a>Accesso con URL (cloud pubblico)

Il software del provider di replica negli host Hyper-V dovrà avere accesso a questi URL.

**URL** | **Dettagli**
--- | ---
login.microsoftonline.com | Controllo di accesso e gestione delle identità con Active Directory.
backup.windowsazure.com | Trasferimento e coordinamento dei dati di replica.
*.hypervrecoverymanager.windowsazure.com | Utilizzato per la gestione della replica.
*.blob.core.windows.net | Caricare i dati negli account di archiviazione. 
dc.services.visualstudio.com | Caricare i log delle app usati per il monitoraggio interno.
time.windows.com | Verifica la sincronizzazione dell'ora tra il sistema e l'ora globale.

### <a name="url-access-azure-government"></a>Accesso con URL (Azure per enti pubblici)

Il software del provider di replica negli host Hyper-V dovrà avere accesso a questi URL.

**URL** | **Dettagli**
--- | ---
login.microsoftonline.us | Controllo di accesso e gestione delle identità con Active Directory.
backup.windowsazure.us | Trasferimento e coordinamento dei dati di replica.
*.hypervrecoverymanager.windowsazure.us | Utilizzato per la gestione della replica.
*.blob.core.usgovcloudapi.net | Caricare i dati negli account di archiviazione.
dc.services.visualstudio.com | Caricare i log delle app usati per il monitoraggio interno.
time.nist.gov | Verifica la sincronizzazione dell'ora tra il sistema e l'ora globale.

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

Tutte le macchine virtuali locali replicate in Azure devono soddisfare i requisiti della macchina virtuale di Azure riepilogati in questa tabella.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
Dimensioni disco del sistema operativo | Fino a 2.048 GB. | Il controllo ha esito negativo se non supportato.
Conteggio dischi del sistema operativo | 1 | Il controllo ha esito negativo se non supportato.
Conteggio dischi dati | 16 o meno. | Il controllo ha esito negativo se non supportato.
Dimensioni del disco dati | Fino a 4.095 GB | Il controllo ha esito negativo se non supportato.
Schede di rete | Sono supportate più schede. |
VHD condiviso | Non supportata. | Il controllo ha esito negativo se non supportato.
Disco FC | Non supportata. | Il controllo ha esito negativo se non supportato.
BitLocker | Non supportata. | Prima di abilitare la replica per un computer, occorre disabilitare BitLocker.
Nome della VM. | Da 1 a 63 caratteri.<br/> Limitato a lettere, numeri e trattini.<br/><br/> Il nome del computer deve iniziare e terminare con una lettera o un numero. |  Aggiornare il valore nelle proprietà del computer in Site Recovery.
Connetti dopo la migrazione-Windows | Per connettersi alle macchine virtuali di Azure che eseguono Windows dopo la migrazione:<br/><br/> -Prima della migrazione, abilitare RDP nella macchina virtuale locale. Assicurarsi che vengano aggiunte regole TCP e UDP per il profilo **pubblico** e che il protocollo RDP sia consentito in **Windows Firewall**  >  **app consentite**per tutti i profili.<br/><br/> -Per l'accesso VPN da sito a sito, abilitare RDP e consentire il protocollo RDP in **Windows Firewall**  ->  **app e funzionalità consentite** per le reti di **dominio e private** . Verificare inoltre che il criterio SAN del sistema operativo sia impostato su onlineal **.** [Altre informazioni](prepare-for-migration.md) |
Connetti dopo la migrazione-Linux | Per connettersi alle macchine virtuali di Azure dopo la migrazione tramite SSH:<br/><br/> -Prima della migrazione, nel computer locale controllare che il servizio Secure Shell sia impostato su avvio e che le regole del firewall consentano una connessione SSH.<br/><br/> -Dopo la migrazione, nella macchina virtuale di Azure consentire le connessioni in ingresso alla porta SSH per le regole del gruppo di sicurezza di rete nella macchina virtuale sottoposta a failover e per la subnet di Azure a cui è connessa. Aggiungere inoltre un indirizzo IP pubblico per la macchina virtuale. |  

## <a name="next-steps"></a>Passaggi successivi

Eseguire la migrazione di [macchine virtuali Hyper-V](tutorial-migrate-hyper-v.md) per la migrazione.
