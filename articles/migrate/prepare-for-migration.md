---
title: Preparare i computer per la migrazione con Azure Migrate
description: Informazioni su come preparare i computer locali per la migrazione con Azure Migrate.
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: eba177a254606bb847e0866ae48281a889c53f9b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78927480"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Preparare i computer locali per la migrazione ad Azure

Questo articolo descrive come preparare i computer locali prima di iniziare la migrazione ad Azure con [Azure Migrate: Migrazione server](migrate-services-overview.md#azure-migrate-server-migration-tool).

In questo articolo si apprenderà come:
> [!div class="checklist"]
> * Verificare le limitazioni della migrazione.
> * Verificare i requisiti del sistema operativo e le limitazioni del supporto.
> * Verificare l'accesso agli URL e alle porte per i computer di cui si vuole eseguire la migrazione.
> * Esaminare le modifiche che potrebbero essere necessarie prima di iniziare la migrazione.
> * Configurare le impostazioni in modo che le lettere di unità vengano mantenute dopo la migrazione.
> * Preparare i computer in modo che sia possibile connettersi alle macchine virtuali di Azure dopo la migrazione.

## <a name="verify-migration-limitations"></a>Verificare le limitazioni della migrazione

- È possibile valutare fino a 35.000 macchine virtuali VMware/Hyper-V in un singolo progetto di Azure Migrate usando la funzionalità Migrazione del server di Azure Migrate. Un progetto può combinare macchine virtuali VMware e macchine virtuali Hyper-V, fino ai limiti per ogni tipologia.
- È possibile selezionare fino a 10 macchine virtuali alla volta per la migrazione. Se è necessario replicarne di più, eseguire la replica per gruppi di 10.
- Per la migrazione senza agente VMware, è possibile eseguire fino a 100 repliche simultanee.

## <a name="verify-operating-system-requirements"></a>Verificare i requisiti del sistema operativo

- Verificare che i [sistemi operativi Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) in uso siano supportati in Azure.
- Verificare che le [distribuzioni Linux](../virtual-machines/linux/endorsed-distros.md) in uso siano supportate in Azure.

## <a name="see-whats-supported"></a>Attività supportate

Per le macchine virtuali VMware, Migrazione del server supporta la [migrazione senza agente o basata su agente](server-migrate-overview.md).

- **Macchine virtuali VMware**: Verificare il [supporto e i requisiti per la migrazione](migrate-support-matrix-vmware-migration.md) per le macchine virtuali VMware.
- **Macchine virtuali Hyper-V**: Verificare il [supporto e i requisiti per la migrazione](migrate-support-matrix-hyper-v-migration.md) per le macchine virtuali Hyper-V.
- **Computer fisici**: Verificare il [supporto e i requisiti per la migrazione](migrate-support-matrix-physical-migration.md) per computer fisici locali e altri server virtualizzati. 

## <a name="review-url-and-port-access"></a>Verificare l'accesso agli URL e alle porte

Durante la migrazione, i computer potrebbero richiedere l'accesso a Internet.

- **Appliance di Azure Migrate**: Verificare gli [URL](migrate-appliance.md#url-access) e le [porte](migrate-support-matrix-vmware-migration.md#agentless-ports) a cui l'appliance di Azure Migrate deve accedere durante la migrazione senza agente.
- **Migrazione basata su agente delle macchine virtuali VMware**: Verificare gli [URL](migrate-replication-appliance.md#url-access) e le [porte](migrate-replication-appliance.md#port-access) che l'appliance di replica usa durante la migrazione basata su agente delle macchine virtuali VMware. 
- **Host Hyper-V**: Verificare gli [URL e le porte](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) a cui gli host Hyper-V dovranno accedere durante la migrazione. 
- **Server fisici**: Verificare gli [URL](migrate-replication-appliance.md#url-access) e le [porte](migrate-replication-appliance.md#port-access) che l'appliance di replica usa durante la migrazione dei server fisici.

## <a name="verify-required-changes-before-migrating"></a>Verificare le modifiche necessarie prima della migrazione

Alcune macchine virtuali potrebbero richiedere alcune modifiche per la corretta esecuzione in Azure. Azure Migrate apporta automaticamente queste modifiche per le macchine virtuali che eseguono questi sistemi operativi:

- Red Hat Enterprise Linux 7.0+, 6.5+
- CentOS 7.0+, 6.5+
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 18.04LTS, 16.04LTS, 14.04LTS
- Debian 8, 7

Per altri sistemi operativi, è necessario preparare i computer manualmente prima della migrazione. 

### <a name="prepare-windows-machines"></a>Preparare i computer Windows

Per i computer Windows, apportare queste modifiche prima della migrazione. Se si esegue la migrazione della macchina virtuale prima di apportare la modifica, la macchina virtuale potrebbe non avviarsi in Azure.

1. [Abilitare la console seriale di Azure](../virtual-machines/troubleshooting/serial-console-windows.md) per la macchina virtuale di Azure. L'abilitazione della console facilita la risoluzione dei problemi. Non è necessario riavviare la VM. La macchina virtuale di Azure verrà avviata usando l'immagine del disco. L'avvio tramite l'immagine del disco equivale a un riavvio per la nuova macchina virtuale. 
2. In caso di migrazione di macchine virtuali che eseguono Windows Server 2003, installare i servizi di integrazione guest Hyper-V nel sistema operativo delle macchine virtuali. [Altre informazioni](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services)

### <a name="prepare-linux-machines"></a>Preparare i computer Linux

1. Installare Linux Integration Services per Hyper-V. La maggior parte delle nuove versioni delle distribuzioni Linux include Hyper-V Linux Integration Services per impostazione predefinita.
2. Ricompilare l'immagine iniziale di Linux affinché includa i driver Hyper-V necessari. Questa operazione, necessaria solo in alcune distribuzioni, garantisce che la macchina virtuale possa essere avviata in Azure.
3. [Abilitare la registrazione della console seriale di Azure](../virtual-machines/troubleshooting/serial-console-linux.md). L'abilitazione della registrazione della console facilita la risoluzione dei problemi. Non è necessario riavviare la VM. La macchina virtuale di Azure verrà avviata usando l'immagine del disco. L'avvio tramite l'immagine del disco equivale a un riavvio per la nuova macchina virtuale.
4. Aggiornare il file di mapping dei dispositivi contenente le associazioni tra nome del dispositivo e volume, per usare identificatori di dispositivo permanenti.
5. Aggiornare le voci nel file fstab in modo da usare identificatori di volume permanenti.
6. Rimuovere le eventuali regole di Udev che riservino i nomi di interfaccia in base all'indirizzo MAC e così via.
7. Aggiornare le interfacce di rete in modo che ricevano l'indirizzo IP da DHCP.

Altre informazioni sulla [procedura necessaria per eseguire una macchina virtuale Linux in Azure](../virtual-machines/linux/create-upload-generic.md) e ottenere istruzioni per alcune delle distribuzioni Linux più diffuse.

## <a name="preserve-drive-letters-after-migration"></a>Mantenere le lettere di unità dopo la migrazione

Quando si esegue la migrazione di un computer locale a Microsoft Azure, le lettere di unità dei dischi dati aggiuntivi potrebbero variare rispetto ai valori originali. 

Per impostazione predefinita, alle macchine virtuali di Azure viene assegnata l'unità D da usare come spazio di archiviazione temporanea. Questa assegnazione di unità comporta l'incremento di una lettera per tutte le altre assegnazioni di unità di archiviazione collegate. Ad esempio, se l'installazione locale usa un disco dati assegnato all'unità D per le installazioni delle applicazioni, l'assegnazione per questa unità passerà all'unità E dopo la migrazione della macchina virtuale ad Azure. Per evitare questa assegnazione automatica e per assicurarsi che Azure assegni la lettera di unità libera successiva al relativo volume temporaneo, impostare i criteri della rete di archiviazione (SAN) su **OnlineAll**:

1. Nel computer locale (non nel server host) aprire un prompt dei comandi con privilegi elevati.
2. Immettere **diskpart**.
3. Immettere **SAN**. Se la lettera di unità del sistema operativo guest non viene mantenuta, verrà restituito **Porta offline tutti i dischi** o **Porta offline dischi condivisi**.
4. Al prompt di **DISKPART** immettere **SAN Policy=OnlineAll**. Questa impostazione garantisce che i dischi vengano portati online e che sia possibile eseguire operazioni di lettura e scrittura su entrambi i dischi.
5. Durante la migrazione di test, è possibile verificare che le lettere di unità vengano mantenute.

## <a name="check-azure-vm-requirements"></a>Controllare i requisiti delle macchine virtuali di Azure

I computer locali replicati in Azure devono essere conformi ai requisiti delle macchine virtuali di Azure per il sistema operativo e l'architettura, i dischi, le impostazioni di rete e le convenzioni di denominazione delle macchine virtuali. Verificare i requisiti per [macchine virtuali VMware e server fisici](migrate-support-matrix-vmware-migration.md#azure-vm-requirements) e [macchine virtuali Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) prima della migrazione.

## <a name="prepare-to-connect-after-migration"></a>Preparare la connessione dopo la migrazione

Le macchine virtuali di Azure vengono create durante la migrazione ad Azure. Dopo la migrazione, deve essere possibile connettersi alle nuove macchine virtuali di Azure. Per stabilire una connessione sono necessari più passaggi.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Preparare la connessione per le macchine virtuali Windows di Azure

Nei computer Windows locali:

1. Configurare le impostazioni di Windows. Procedere alla rimozione di eventuali route permanenti statiche o proxy WinHTTP.
2. Verificare che tutti i [servizi necessari](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) siano in esecuzione.
3. Abilitare Desktop remoto (RDP) per consentire le connessioni remote al computer locale. Informazioni su come [usare PowerShell per abilitare RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. Per accedere a una macchina virtuale di Azure tramite Internet dopo la migrazione, in Windows Firewall nel computer locale consentire i protocolli TCP e UDP nel profilo pubblico e impostare RDP come app consentita per tutti i profili.
5. Se si vuole accedere a una macchina virtuale di Azure tramite una VPN da sito a sito dopo la migrazione, in Windows Firewall nel computer locale consentire il protocollo RDP per il profilo di dominio e privato. Informazioni su come [consentire il traffico RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Prima di eseguire la migrazione, assicurarsi che nella macchina virtuale locale non siano in sospeso aggiornamenti di Windows. In tal caso, l'installazione degli aggiornamenti nella macchina virtuale di Azure potrebbe iniziare dopo la migrazione e non sarà possibile accedere alla macchina virtuale fino al termine degli aggiornamenti.

### <a name="prepare-to-connect-with-linux-azure-vms"></a>Preparare la connessione per le macchine virtuali Linux di Azure

Nei computer Linux locali:

1. Assicurarsi che il servizio Secure Shell sia impostato per l'avvio automatico all'avvio del sistema.
2. Controllare che le regole del firewall consentano una connessione SSH.

### <a name="configure-azure-vms-after-migration"></a>Configurare le macchine virtuali di Azure dopo la migrazione

Dopo la migrazione, eseguire queste operazioni nelle macchine virtuali di Azure create:

1. Per connettersi alla macchina virtuale tramite Internet, assegnare alla macchina virtuale un indirizzo IP pubblico. Per la macchina virtuale di Azure è necessario usare un indirizzo IP pubblico diverso rispetto a quello usato in precedenza per il computer locale. [Altre informazioni](../virtual-network/virtual-network-public-ip-address.md)
2. Verificare che le regole del gruppo di sicurezza di rete nella macchina virtuale consentano le connessioni in ingresso alla porta RDP o SSH.
3. Controllare la [diagnostica di avvio](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) per visualizzare la macchina virtuale.

> [!NOTE]
> Il servizio Azure Bastion offre accesso privato RDP e SSH alle macchine virtuali di Azure. [Altre informazioni](../bastion/bastion-overview.md) su questo servizio.

## <a name="next-steps"></a>Passaggi successivi

Stabilire il metodo da usare per la [migrazione di macchine virtuali VMware](server-migrate-overview.md) ad Azure oppure avviare la migrazione di [macchine virtuali Hyper-V](tutorial-migrate-hyper-v.md) o [server fisici o macchine virtuali virtualizzate/cloud](tutorial-migrate-physical-virtual-machines.md).
