---
title: Preparare i server VMware locali per il ripristino di emergenza di macchine virtuali VMware in Azure| Microsoft Docs
description: Informazioni su come preparare i server VMware locali per il ripristino di emergenza in Azure usando il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6898f725d1d3cbf3f8d9d90faeafc13fbc8cb201
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Preparare i server VMware locali per il ripristino di emergenza in Azure

Questa esercitazione descrive come preparare l'infrastruttura VMware locale per replicare le macchine virtuali VMware in Azure. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Preparare un account nel server vCenter o nell'host ESXi vSphere per rendere automatica l'individuazione delle macchine virtuali
> * Preparare un account per l'installazione automatica del servizio Mobility nelle macchine virtuali VMware
> * Esaminare i requisiti dei server VMware
> * Esaminare i requisiti delle macchine virtuali VMware

In questa serie di esercitazioni viene illustrato come eseguire il backup di una singola macchina virtuale usando Azure Site Recovery. Se si prevede di proteggere più macchine virtuali VMware, è consigliabile scaricare lo strumento [Deployment Planner](https://aka.ms/asr-deployment-planner) per la replica VMware. Questo strumento consente di raccogliere informazioni sulla compatibilità delle macchine virtuali, sui dischi per ogni macchina virtuale e sulla varianza dati per ogni disco. Lo strumento specifica anche i requisiti della larghezza di banda di rete e l'infrastruttura di Azure necessaria per la corretta esecuzione della replica e il failover del test. Vedere le [altre informazioni](site-recovery-deployment-planner.md) sull'esecuzione dello strumento.

Questa è la seconda esercitazione della serie. Assicurarsi di aver [configurato i componenti di Azure](tutorial-prepare-azure.md) come descritto nell'esercitazione precedente.

## <a name="prepare-an-account-for-automatic-discovery"></a>Preparare un account per l'individuazione automatica

Site Recovery richiede l'accesso ai server VMware per:

- Individuare automaticamente le macchine virtuali. È necessario almeno un account di sola lettura.
- Controllare la replica, il failover e il failback. È necessario un account in grado di eseguire operazioni come la creazione e la rimozione di dischi e l'accensione delle macchine virtuali.

Creare l'account come illustrato di seguito:

1. Per usare un account dedicato, creare un ruolo a livello vCenter. Assegnare un nome al ruolo, ad esempio **Azure_Site_Recovery**.
2. Assegnare al ruolo le autorizzazioni elencate nella tabella seguente.
3. Creare un utente nel server vCenter o nell'host vSphere. Assegnare il ruolo all'utente.

### <a name="vmware-account-permissions"></a>Autorizzazioni dell'account VMware

**Task** | **Ruolo/Autorizzazioni** | **Dettagli**
--- | --- | ---
**Individuazione di macchine virtuali** | Almeno un utente in sola lettura<br/><br/> Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).
**Replica completa, failover, failback** |  Creare un ruolo (Azure_Site_Recovery) con le autorizzazioni necessarie e quindi assegnare il ruolo a un utente o gruppo VMware<br/><br/> Data Center object (Oggetto data center) –> Propagate to Child Object (Propaga a oggetto figlio), role=Azure_Site_Recovery (ruolo=Azure_Site_Recovery)<br/><br/> Datastore (Archivio dati) -> Allocate space (Alloca spazio), Browse datastore (Sfoglia archivio dati), Low level file operations (Operazioni file di livello basso), Remove file (Rimuovi file), Update virtual machine files (Aggiorna file macchina virtuale)<br/><br/> Network (Rete) -> Network assign (Assegnazione rete)<br/><br/> Risorsa -> Assegnare VM al pool di risorse, migrare la VM spenta, migrare la VM accesa<br/><br/> Tasks (Attività) -> Create task (Crea attività), Update task (Aggiorna attività)<br/><br/> Virtual machine (Macchina virtuale) -> Configuration (Configurazione)<br/><br/> Virtual machine (Macchina virtuale) -> Interact (Interagisci) -> Answer question (Rispondi alla domanda), Device connection (Connessione dispositivo), Configure CD media (Configura supporto CD), Configure floppy media (Configura supporto floppy), Power off (Spegni), Power on (Accendi), VMware tools install (Installazione strumenti VMware)<br/><br/> Virtual machine (Macchina virtuale) -> Inventory (Inventario) -> Create (Crea), Register (Registra), Unregister (Annulla registrazione)<br/><br/> Virtual machine (Macchina virtuale) -> Provisioning -> Allow virtual machine download (Consenti download macchina virtuale), Allow virtual machine files upload (Consenti upload file macchina virtuale)<br/><br/> Macchina virtuale -> Snapshots -> Remove snapshots | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).

## <a name="prepare-an-account-for-mobility-service-installation"></a>Preparare un account per l'installazione del servizio Mobility

È necessario installare il servizio Mobility nella macchina virtuale da replicare. Site Recovery installa il servizio automaticamente quando si abilita la replica per la macchina virtuale. Per l'installazione automatica, è necessario preparare un account che Site Recovery userà per accedere alla macchina virtuale. È possibile specificare l'account quando si configura il ripristino di emergenza nella console di Azure.

1. Preparare un account di dominio o locale con le autorizzazioni per l'installazione nella macchina virtuale.
2. Per installare nelle macchine virtuali Windows, se non si usa un account di dominio, disabilitare il controllo Accesso utente remoto nel computer locale.
   - Nel Registro di sistema in **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** aggiungere la voce DWORD **LocalAccountTokenFilterPolicy** con valore 1.
3. Per installare nelle macchine virtuali Linux, preparare un account radice nel server Linux di origine.


## <a name="check-vmware-requirements"></a>Verificare i requisiti di VMware

Assicurarsi che i server e le VM VMware siano conformi ai requisiti.

1. [Verificare](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers) i requisiti dei server VMware.
2. Per Linux, [verificare](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) i requisiti per archiviazione e file system. 
3. Verificare il supporto per [rete](vmware-physical-azure-support-matrix.md#network) e [archiviazione](vmware-physical-azure-support-matrix.md#storage) locale. 
4. Controllare cosa è supportato per le [funzionalità di rete di Azure](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), l'[archiviazione](vmware-physical-azure-support-matrix.md#azure-storage) e il [calcolo](vmware-physical-azure-support-matrix.md#azure-compute), dopo il failover.
5. Le macchine virtuali locali replicate in Azure devono essere conformi ai [requisiti delle macchine virtuali di Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparare la connessione alle macchine virtuali di Azure dopo il failover

In uno scenario di failover può essere necessario connettersi alle macchine virtuali replicate in Azure dalla rete locale.

Per connettersi alle macchine virtuali Windows tramite RDP dopo il failover, eseguire le operazioni seguenti:

1. Per accedere tramite Internet, abilitare RDP nella macchina virtuale locale prima del failover. Assicurarsi che le regole TCP e UDP siano aggiunte per il profilo **Pubblico** e che RDP sia consentito in **Windows Firewall** > **App consentite** per tutti i profili.
2. Per accedere tramite VPN da sito a sito, abilitare RDP nel computer locale. RDP deve essere consentito in **Windows Firewall** -> **App e funzionalità consentite** per le reti di **dominio e private**.
   Verificare che il criterio SAN del sistema operativo sia impostato su **OnlineAll**. [Altre informazioni](https://support.microsoft.com/kb/3031135). Quando si attiva un failover, nella macchina virtuale non devono essere presenti aggiornamenti di Windows in sospeso. Se sono presenti aggiornamenti in sospeso, non sarà possibile accedere alla macchina virtuale fino al completamento dell'aggiornamento.
3. Dopo il failover nella macchina virtuale Windows di Azure selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale. Se non è possibile connettersi, controllare che la macchina virtuale sia in esecuzione e rivedere i [suggerimenti per la risoluzione dei problemi](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Per connettersi alle macchine virtuali Linux tramite SSH dopo il failover, eseguire le operazioni seguenti:

1. Nel computer locale prima del failover verificare che il servizio Secure Shell sia impostato per essere avviato automaticamente all'avvio del sistema. Controllare che le regole del firewall consentano una connessione SSH.

2. Nella macchina virtuale di Azure dopo il failover autorizzare le connessioni in entrata nella porta SSH per le regole del gruppo di sicurezza di rete nella macchina virtuale in cui è stato eseguito il failover e per la subnet di Azure a cui è connessa.
   [Aggiungere un indirizzo IP pubblico](site-recovery-monitoring-and-troubleshooting.md) per la macchina virtuale. È possibile selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza in Azure per le macchine virtuali VMware locali](vmware-azure-tutorial.md)
