---
title: Supporto per la migrazione di Hyper-V in Azure Migrate
description: Informazioni sul supporto per la migrazione di Hyper-V con Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 4ca946597417ccde0e00c8bf09c70207bc4f85b9
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031647"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Matrice di supporto per la migrazione di Hyper-V

Questo articolo riepiloga le impostazioni di supporto e le limitazioni per la migrazione di macchine virtuali Hyper-V con [Azure migrate: migrazione del server](migrate-services-overview.md#azure-migrate-server-migration-tool) . Per informazioni sulla valutazione delle macchine virtuali Hyper-V per la migrazione ad Azure, vedere la matrice di [supporto](migrate-support-matrix-hyper-v.md)per la valutazione.

## <a name="migration-limitations"></a>Limitazioni della migrazione

È possibile selezionare fino a 10 macchine virtuali contemporaneamente per la replica. Se si desidera eseguire la migrazione di più computer, eseguire la replica in gruppi di 10.


## <a name="hyper-v-hosts"></a>Host Hyper-V

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione**       | L'host Hyper-V può essere autonomo o distribuito in un cluster. |
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore per l'host Hyper-V. |
| **Sistema operativo host** | Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2. |
| **Accesso con URL** | Gli host Hyper-V devono accedere a questi URL:<br/><br/> -login.microsoftonline.com: controllo di accesso e gestione delle identità con Active Directory.<br/><br/> -*. backup.windowsazure.com: trasferimento e coordinamento dei dati di replica. Eseguire la migrazione degli URL del servizio.<br/><br/> -*. blob.core.windows.net: caricare i dati negli account di archiviazione.<br/><br/> -dc.services.visualstudio.com: caricare i log delle app usati per il monitoraggio interno.<br/><br/> - time.windows.com | Verifica la sincronizzazione dell'ora tra il sistema e l'ora globale.
| **Accesso alla porta** |  Connessioni in uscita sulla porta HTTPS 443 per inviare i dati di replica della macchina virtuale.

## <a name="hyper-v-vms"></a>VM Hyper-V

| **Supporto**                  | **Dettagli**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Tutti i sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) supportati da Azure. |
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore per ogni macchina virtuale Hyper-V che si desidera valutare. |
| **Integration Services**       | Per acquisire le informazioni sul sistema operativo, è necessario che [Hyper-V Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) sia in esecuzione in macchine virtuali valutate. |
| **Modifiche necessarie per Azure** | Alcune macchine virtuali potrebbero richiedere alcune modifiche per la corretta esecuzione in Azure. È necessario apportare modifiche manualmente prima della migrazione. Gli articoli pertinenti contengono istruzioni su come eseguire questa operazione. |
| **Avvio di Linux**                 | Se/boot si trova in una partizione dedicata, deve risiedere nel disco del sistema operativo e non essere distribuito tra più dischi.<br/> Se/boot fa parte della partizione radice (/), la partizione '/' deve trovarsi nel disco del sistema operativo e non si estende su altri dischi. |
| **Avvio UEFI**                  | La macchina virtuale di cui è stata eseguita la migrazione in Azure verrà automaticamente convertita in una macchina virtuale di avvio BIOS. Nella macchina virtuale deve essere in esecuzione solo Windows Server 2012 e versioni successive. Il disco del sistema operativo deve avere fino a cinque partizioni o meno e la dimensione del disco del sistema operativo deve essere inferiore a 300 GB.
  |
| **Dimensioni disco**                  | 2 TB per il disco del sistema operativo, 4 TB per i dischi dati.
| **Numero disco** | Un massimo di 16 dischi per macchina virtuale.
| **Dischi/volumi crittografati**    | Non supportato per la migrazione. |
| **Dischi RDM/passthrough**      | Non supportato per la migrazione. |
| **Disco condiviso** | Le macchine virtuali che usano dischi condivisi non sono supportate per la migrazione.
| **NFS**                        | I volumi NFS montati come volumi nelle macchine virtuali non verranno replicati. |
| **ISCSI**                      | Le macchine virtuali con destinazioni iSCSI non sono supportate per la migrazione.
| **Disco di destinazione**                | È possibile eseguire la migrazione solo a macchine virtuali di Azure con Managed Disks. |
| **IPv6** | Non supportato.
| **Gruppo NIC** | Non supportato.
| **Azure Site Recovery** | Non è possibile eseguire la replica usando Azure Migrate migrazione del server se la macchina virtuale è abilitata per la replica con Azure Site Recovery.
| **Ports** | Connessioni in uscita sulla porta HTTPS 443 per inviare i dati di replica della macchina virtuale.

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

Tutte le macchine virtuali locali replicate in Azure devono soddisfare i requisiti della macchina virtuale di Azure riepilogati in questa tabella. Quando Site Recovery esegue un controllo dei prerequisiti per la replica, il controllo ha esito negativo se alcuni dei requisiti non vengono soddisfatti.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
Sistema operativo guest | Verifica i sistemi operativi della macchina virtuale VMware supportati per la migrazione.<br/> È possibile eseguire la migrazione di qualsiasi carico di lavoro in esecuzione in un sistema operativo supportato. | Il controllo ha esito negativo se non supportato.
Architettura del sistema operativo guest | 64 bit. | Il controllo ha esito negativo se non supportato.
Dimensioni disco del sistema operativo | Fino a 2.048 GB. | Il controllo ha esito negativo se non supportato.
Conteggio dischi del sistema operativo | 1 | Il controllo ha esito negativo se non supportato.
Conteggio dischi dati | 64 o un numero inferiore. | Il controllo ha esito negativo se non supportato.
Dimensioni del disco dati | Fino a 4.095 GB | Il controllo ha esito negativo se non supportato.
Schede di rete | Sono supportate più schede. |
VHD condiviso | Non supportato. | Il controllo ha esito negativo se non supportato.
Disco FC | Non supportato. | Il controllo ha esito negativo se non supportato.
BitLocker | Non supportato. | Prima di abilitare la replica per un computer, occorre disabilitare BitLocker.
Nome della VM. | Da 1 a 63 caratteri.<br/> Limitato a lettere, numeri e trattini.<br/><br/> Il nome del computer deve iniziare e terminare con una lettera o un numero. |  Aggiornare il valore nelle proprietà del computer in Site Recovery.
Connetti dopo la migrazione-Windows | Per connettersi alle macchine virtuali di Azure che eseguono Windows dopo la migrazione:<br/> -Prima della migrazione Abilita RDP nella macchina virtuale locale. Assicurarsi che siano aggiunte regole TCP e UDP per il profilo **Pubblico** e che RDP sia consentito in **Windows Firewall** > **App consentite** per tutti i profili.<br/> Per l'accesso VPN da sito a sito, abilitare RDP e consentire il protocollo RDP in **Windows Firewall** -> **le app e le funzionalità consentite** per le reti di **dominio e private** . Verificare inoltre che il criterio SAN del sistema operativo sia impostato su onlineal **.** [Altre informazioni](prepare-for-migration.md). |
Connetti dopo la migrazione-Linux | Per connettersi alle macchine virtuali di Azure dopo la migrazione tramite SSH:<br/> Prima della migrazione, nel computer locale controllare che il servizio Secure Shell sia impostato su avvio e che le regole del firewall consentano una connessione SSH.<br/> Dopo il failover, nella macchina virtuale di Azure, consentire le connessioni in ingresso alla porta SSH per le regole del gruppo di sicurezza di rete nella macchina virtuale sottoposta a failover e per la subnet di Azure a cui è connessa. Aggiungere inoltre un indirizzo IP pubblico per la macchina virtuale. |  

## <a name="next-steps"></a>Passaggi successivi

Eseguire la migrazione di [macchine virtuali Hyper-V](tutorial-migrate-hyper-v.md) per la migrazione.
