---
title: Preparare i computer per la migrazione con Azure Migrate
description: Informazioni su come preparare i computer locali per la migrazione con Azure Migrate.
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: MVC
ms.openlocfilehash: a5314e3f06c54921b12c242f884e02073edc773b
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84769643"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Preparare i computer locali per la migrazione ad Azure

Questo articolo descrive come preparare i computer locali prima di eseguirne la migrazione ad Azure con lo strumento [Azure Migrate: Migrazione del server](migrate-services-overview.md#azure-migrate-server-migration-tool).

In questo articolo si apprenderà come:
> [!div class="checklist"]
> * Verificare le limitazioni della migrazione.
> * Selezionare un metodo per la migrazione di macchine virtuali VMware.
> * Verificare i requisiti dell'hypervisor e del sistema operativo per i computer di cui si vuole eseguire la migrazione.
> * Verificare l'accesso agli URL e alle porte per i computer di cui si vuole eseguire la migrazione.
> * Esaminare le modifiche che potrebbero essere necessarie prima di iniziare la migrazione.
> * Verificare i requisiti delle macchine virtuali di Azure per le macchine virtuali con migrazione completata.
> * Preparare i computer in modo che sia possibile connettersi alle macchine virtuali di Azure dopo la migrazione.



## <a name="verify-migration-limitations"></a>Verificare le limitazioni della migrazione

Nella tabella seguente sono riepilogati i limiti per l'individuazione, la valutazione e la migrazione in Azure Migrate. È consigliabile, ma non necessario, valutare i computer prima della migrazione.

**Scenario** | **Progetto** | **Individuazione e valutazione** | **Migrazione**
--- | --- | --- | ---
**VM VMware** | Individuare e valutare fino a 35.000 macchine virtuali in un singolo progetto di Azure Migrate. | Individuare fino a 10.000 macchine virtuali VMware in una singola [appliance Azure Migrate](common-questions-appliance.md) per VMware. | **Migrazione senza agente**: è possibile replicare simultaneamente un massimo di 300 macchine virtuali. Per ottenere prestazioni ottimali, è consigliabile creare più batch di macchine virtuali se sono più di 50.<br/><br/> **Migrazione basata su agente**: è possibile [aumentare](/agent-based-migration-architecture.md#performance-and-scaling) le risorse dell'[appliance di replica](migrate-replication-appliance.md) per replicare un numero elevato di macchine virtuali.<br/><br/> Nel portale è possibile selezionare un massimo di 10 computer contemporaneamente per la replica. Per replicare più computer, aggiungerli in batch di 10.
**VM Hyper-V** | Individuare e valutare fino a 35.000 macchine virtuali in un singolo progetto di Azure Migrate. | Individuare fino a 5.000 macchine virtuali Hyper-V con una singola appliance Azure Migrate. | Per la migrazione di Hyper-V non viene usata un'appliance. Il provider di replica Hyper-V viene invece eseguito su ogni host Hyper-V.<br/><br/> La capacità di replica è influenzata da fattori legati alle prestazioni, come la varianza della macchina virtuale, e dalla larghezza di banda per il caricamento dei dati di replica.<br/><br/> Nel portale è possibile selezionare un massimo di 10 computer contemporaneamente per la replica. Per replicare più computer, aggiungerli in batch di 10.
**Computer fisici** | Individuare e valutare fino a 35.000 computer in un singolo progetto di Azure Migrate. | Individuare fino a 250 server fisici con una singola appliance Azure Migrate per i server fisici. | È possibile [aumentare](/agent-based-migration-architecture.md#performance-and-scaling) le risorse dell'[appliance di replica](migrate-replication-appliance.md) per replicare un numero elevato di server.<br/><br/> Nel portale è possibile selezionare un massimo di 10 computer contemporaneamente per la replica. Per replicare più computer, aggiungerli in batch di 10.

## <a name="select-a-vmware-migration-method"></a>Selezionare un metodo di migrazione VMware

Se si sta eseguendo la migrazione di macchine virtuali VMware in Azure, [confrontare](server-migrate-overview.md#compare-migration-methods) i metodi di migrazione senza agente e basata su agente per decidere quale sia quello più adatto.

## <a name="verify-hypervisor-requirements"></a>Verificare i requisiti dell'hypervisor

- Verificare i requisiti della migrazione [VMware senza agente](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) o [VMware basata su agente](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based).
- Verificare i requisiti dell'[host Hyper-V](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements).


## <a name="verify-operating-system-requirements"></a>Verificare i requisiti del sistema operativo

Verificare i sistemi operativi supportati per la migrazione:

- Se si sta eseguendo la migrazione di macchine virtuali VMware o Hyper-V, verificare i requisiti delle VM VMware per la migrazione [senza agente](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless) e [basata su agente](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) e i requisiti per le [macchine virtuali Hyper-V](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms).
- Verificare che i [sistemi operativi Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) siano supportati in Azure.
- Verificare che le [distribuzioni Linux](../virtual-machines/linux/endorsed-distros.md) siano supportate in Azure.

## <a name="review-url-and-port-access"></a>Verificare l'accesso agli URL e alle porte

Esaminare gli URL e le porte a cui viene eseguito l'accesso durante la migrazione.

**Scenario** | **Dettagli** |  **URL** | **Ports**
--- | --- | --- | ---
**Migrazione senza agente VMware** | Usa l'[appliance di Azure Migrate](migrate-appliance-architecture.md) per la migrazione. Non viene installato nulla nelle macchine virtuali VMware. | Esaminare gli [URL](migrate-appliance.md#url-access) del cloud pubblico e per enti pubblici necessari per l'individuazione, la valutazione e la migrazione con l'appliance. | [Esaminare](migrate-support-matrix-vmware-migration.md#port-requirements-agentless) i requisiti delle porte per la migrazione senza agente.
**Migrazione basata su agente VMware** | Usa l'[appliance di replica](migrate-replication-appliance.md) per la migrazione. L'agente del servizio di mobilità è installato nelle macchine virtuali. | Esaminare gli URL del [cloud pubblico](migrate-replication-appliance.md#url-access) e di [Azure per enti pubblici](migrate-replication-appliance.md#azure-government-url-access) a cui l'appliance di replica deve accedere. | [Esaminare](migrate-replication-appliance.md#port-access) le porte usate durante la migrazione basata su agente.
**Migrazione di Hyper-V** | Usa un provider installato negli host Hyper-V per la migrazione. Non viene installato nulla nelle macchine virtuali Hyper-V. | Esaminare gli URL del [cloud pubblico](migrate-support-matrix-hyper-v-migration.md#url-access-public-cloud) e di [Azure per enti pubblici](migrate-support-matrix-hyper-v-migration.md#url-access-azure-government) a cui il provider di replica in esecuzione negli host deve accedere. | Il provider di replica nell'host Hyper-V usa le connessioni in uscita sulla porta HTTPS 443 per inviare i dati di replica delle VM.
**Computer fisici** | Usa l'[appliance di replica](migrate-replication-appliance.md) per la migrazione. L'agente del servizio di mobilità è installato nei computer fisici. | Esaminare gli URL del [cloud pubblico](migrate-replication-appliance.md#url-access) e di [Azure per enti pubblici](migrate-replication-appliance.md#azure-government-url-access) a cui l'appliance di replica deve accedere. | [Esaminare](migrate-replication-appliance.md#port-access) le porte usate durante la migrazione dei computer fisici.

## <a name="verify-required-changes-before-migrating"></a>Verificare le modifiche necessarie prima della migrazione

Prima di eseguire la migrazione delle macchine virtuali ad Azure, è necessario apportarvi alcune modifiche.

- Per alcuni sistemi operativi, Azure Migrate apporta modifiche automaticamente durante il processo di replica o di migrazione.
- Per altri sistemi operativi occorre invece configurare le impostazioni manualmente.
- È importante configurare manualmente le impostazioni prima di iniziare la migrazione. Se si esegue la migrazione della VM prima di apportare la modifica, la VM potrebbe non avviarsi in Azure.

Esaminare le tabelle per identificare le modifiche da apportare.

### <a name="windows-machines"></a>Computer Windows

Le modifiche necessarie sono riepilogate nella tabella.

**Azione** | **VMware (migrazione senza agente)** | **VMware (migrazione basata su agente)/computer fisici** | **Windows su Hyper-V** 
--- | --- | --- | ---
**Configurare il criterio SAN Porta online tutti i dischi**<br/><br/> Questo criterio assicura che i volumi di Windows nella VM di Azure usino le stesse assegnazioni di lettere di unità della VM locale. | Impostato automaticamente per le macchine virtuali in esecuzione su Windows Server 2008 R2 o versione successiva.<br/><br/> Configurare manualmente per i sistemi operativi precedenti. | Impostato automaticamente nella maggior parte dei casi. | Configurare manualmente.
**Installare i servizi di integrazione guest Hyper-V** | [Installare manualmente](prepare-windows-server-2003-migration.md#install-on-vmware-vms) nei computer che eseguono Windows Server 2003. | [Installare manualmente](prepare-windows-server-2003-migration.md#install-on-vmware-vms) nei computer che eseguono Windows Server 2003. | [Installare manualmente](prepare-windows-server-2003-migration.md#install-on-hyper-v-vms) nei computer che eseguono Windows Server 2003.
**Abilitare la console seriale di Azure**<br/><br/>[Abilitare la console](../virtual-machines/troubleshooting/serial-console-windows.md) nelle VM di Azure per semplificare la risoluzione dei problemi. Non è necessario riavviare la VM. La macchina virtuale di Azure verrà avviata usando l'immagine del disco. L'avvio tramite l'immagine del disco equivale a un riavvio per la nuova macchina virtuale. | Abilitare manualmente | Abilitare manualmente | Abilitare manualmente
**Eseguire la connessione dopo la migrazione**<br/><br/> Per connettersi dopo la migrazione, è necessario eseguire una serie di passaggi prima della migrazione. | [Configurare](#prepare-to-connect-to-azure-windows-vms) manualmente. | [Configurare](#prepare-to-connect-to-azure-windows-vms) manualmente. | [Configurare](#prepare-to-connect-to-azure-windows-vms) manualmente.


#### <a name="configure-san-policy"></a>Configurare i criteri SAN

Per impostazione predefinita, alle macchine virtuali di Azure viene assegnata l'unità D da usare come spazio di archiviazione temporanea.

- Questa assegnazione di unità comporta l'incremento di una lettera per tutte le altre assegnazioni di unità di archiviazione collegate.
- Ad esempio, se l'installazione locale usa un disco dati assegnato all'unità D per le installazioni delle applicazioni, l'assegnazione per questa unità passerà all'unità E dopo la migrazione della macchina virtuale ad Azure. 
- Per impedire questa assegnazione automatica e per assicurarsi che Azure assegni la lettera di unità libera successiva al relativo volume temporaneo, impostare i criteri della rete di archiviazione (SAN) su **OnlineAll:

Configurare questa impostazione manualmente nel modo seguente:

1. Nel computer locale (non nel server host) aprire un prompt dei comandi con privilegi elevati.
2. Immettere **diskpart**.
3. Immettere **SAN**. Se la lettera di unità del sistema operativo guest non viene mantenuta, verrà restituito **Porta offline tutti i dischi** o **Porta offline dischi condivisi**.
4. Al prompt di **DISKPART** immettere **SAN Policy=OnlineAll**. Questa impostazione garantisce che i dischi vengano portati online e che sia possibile eseguire operazioni di lettura e scrittura su entrambi i dischi.
5. Durante la migrazione di test, è possibile verificare che le lettere di unità vengano mantenute.


### <a name="linux-machines"></a>Computer Linux

Azure Migrate completa automaticamente queste azioni per queste versioni

- Red Hat Enterprise Linux 7.0+, 6.5+
- CentOS 7.0+, 6.5+
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 18.04LTS, 16.04LTS, 14.04LTS
- Debian 8, 7

Per altre versioni, preparare i computer come riepilogato nella tabella.  


**Azione** | **Dettagli** | **Versione Linux**
--- | --- | ---
**Installare Linux Integration Services per Hyper-V** | Ricompilare l'immagine iniziale di Linux affinché includa i driver Hyper-V necessari. L'inclusione dell'immagine iniziale garantisce che la macchina virtuale possa essere avviata in Azure. | Nella maggior parte delle nuove versioni delle distribuzioni di Linux è presente per impostazione predefinita.<br/><br/> Se non è inclusa, installarla manualmente per tutte le versioni ad eccezione di quelle indicate sopra.
**Abilitare la registrazione della console seriale di Azure** | L'abilitazione della registrazione della console facilita la risoluzione dei problemi. Non è necessario riavviare la VM. La macchina virtuale di Azure verrà avviata usando l'immagine del disco. L'avvio tramite l'immagine del disco equivale a un riavvio per la nuova macchina virtuale.<br/><br/> Seguire [queste istruzioni](../virtual-machines/troubleshooting/serial-console-linux.md) per abilitare la registrazione.
**Aggiornare il file di mappa dei dispositivi** | Aggiornare il file di mapping dei dispositivi contenente le associazioni tra nome del dispositivo e volume, per usare identificatori di dispositivo permanenti. | Installarlo manualmente per tutte le versioni ad eccezione di quelle indicate sopra.
**Aggiornare le voci fstab** |  Aggiornare le voci in modo da usare identificatori di volume permanenti.    | Aggiornare manualmente per tutte le versioni ad eccezione di quelle indicate sopra.
**Rimuovere la regola di udev** | Rimuovere qualsiasi regola di udev che riservi i nomi di interfaccia in base all'indirizzo MAC e così via. | Rimuovere manualmente per tutte le versioni ad eccezione di quelle indicate sopra.
**Aggiornare le interfacce di rete** | Aggiornare le interfacce di rete in modo che ricevano l'indirizzo IP in base a DHCP. | Aggiornare manualmente per tutte le versioni ad eccezione di quelle indicate sopra.
**Abilitare ssh** | Assicurarsi che ssh sia abilitato e che il servizio sshd sia impostato per l'avvio automatico al riavvio.<br/><br/> Assicurarsi che le richieste di connessione ssh in ingresso non siano bloccate da regole gestibili tramite script o del firewall del sistema operativo.| Abilitare manualmente per tutte le versioni ad eccezione di quelle indicate sopra.

Altre informazioni sulla procedura per [eseguire una macchina virtuale Linux in Azure](../virtual-machines/linux/create-upload-generic.md) e istruzioni per alcune delle distribuzioni Linux più diffuse.


## <a name="check-azure-vm-requirements"></a>Controllare i requisiti delle macchine virtuali di Azure

I computer locali replicati in Azure devono essere conformi ai requisiti delle macchine virtuali di Azure per il sistema operativo e l'architettura, i dischi, le impostazioni di rete e le convenzioni di denominazione delle macchine virtuali.

Prima di eseguire la migrazione, esaminare i requisiti delle macchine virtuali di Azure per la migrazione di [VMware](migrate-support-matrix-vmware-migration.md#azure-vm-requirements), [Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) e dei [server fisici](migrate-support-matrix-physical-migration.md#azure-vm-requirements).



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


## <a name="next-steps"></a>Passaggi successivi

Stabilire il metodo da usare per la [migrazione di macchine virtuali VMware](server-migrate-overview.md) ad Azure oppure avviare la migrazione di [macchine virtuali Hyper-V](tutorial-migrate-hyper-v.md) o [server fisici o macchine virtuali virtualizzate/cloud](tutorial-migrate-physical-virtual-machines.md).

## <a name="see-whats-supported"></a>Attività supportate

Per le macchine virtuali VMware, Migrazione del server supporta la [migrazione senza agente o basata su agente](server-migrate-overview.md).

- **Macchine virtuali VMware**: Verificare il [supporto e i requisiti per la migrazione](migrate-support-matrix-vmware-migration.md) per le macchine virtuali VMware.
- **Macchine virtuali Hyper-V**: Verificare il [supporto e i requisiti per la migrazione](migrate-support-matrix-hyper-v-migration.md) per le macchine virtuali Hyper-V.
- **Computer fisici**: Verificare il [supporto e i requisiti per la migrazione](migrate-support-matrix-physical-migration.md) per computer fisici locali e altri server virtualizzati. 
