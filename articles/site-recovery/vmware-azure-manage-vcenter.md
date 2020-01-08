---
title: Gestire i server VMware vCenter in Azure Site Recovery
description: Questo articolo descrive come aggiungere e gestire server VMware vCenter per il ripristino di emergenza di macchine virtuali VMware in Azure con Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: ba5f31049b599cd55a4a9a4261080c1672d336b1
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495337"
---
# <a name="manage-vmware-vcenter-server"></a>Gestire i server VMware vCenter

Questo articolo riepiloga le azioni di gestione di un server VMware vCenter in [Azure Site Recovery](site-recovery-overview.md). 

## <a name="verify-prerequisites-for-vcenter-server"></a>Verificare i prerequisiti per server vCenter

I prerequisiti per i server vCenter e le macchine virtuali durante il ripristino di emergenza di macchine virtuali VMware in Azure sono elencati nella [matrice di supporto](vmware-physical-azure-support-matrix.md#replicated-machines).


## <a name="set-up-an-account-for-automatic-discovery"></a>Configurare un account per l'individuazione automatica

Quando si configura il ripristino di emergenza per le macchine virtuali VMware locali, Site Recovery necessario accedere all'host server vCenter/vSphere, in modo che il server di elaborazione Site Recovery possa individuare automaticamente le macchine virtuali ed eseguirne il failover in base alle esigenze. Per impostazione predefinita, il server di elaborazione viene eseguito nel server di configurazione Site Recovery. Aggiungere un account per il server di configurazione per connettersi all'host server vCenter/vSphere come indicato di seguito:

1. Accedere al computer del server di configurazione.
2. Aprire lo strumento server di configurazione (cspsconfigtool. exe) utilizzando il collegamento sul desktop.
3. Nella scheda **Gestisci account** fare clic su **Aggiungi account**. 

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
4. Fornire i dettagli dell'account e fare clic su **OK** per aggiungere l'account.  L'account deve disporre dei privilegi riepilogati nella tabella seguente. 

Sono necessari circa 15 minuti per sincronizzare le informazioni sull'account con Site Recovery.

### <a name="account-permissions"></a>Autorizzazioni dell'account

|**Attività** | **Account** | **Autorizzazioni** | **Dettagli**|
|--- | --- | --- | ---|
|**Individuazione/migrazione di macchine virtuali (senza failback)** | Almeno un account utente di sola lettura. | Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).|
|**Replica/failover** | Almeno un account utente di sola lettura. | Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).<br/><br/> È utile ai fini della migrazione, ma non per la replica completa, il failover e il failback.|
|**Replica/failover/failback** | Si consiglia di creare un ruolo (AzureSiteRecoveryRole) con le autorizzazioni necessarie e quindi assegnare il ruolo a un utente o a un gruppo VMware. | Data Center object (Oggetto data center) –> Propagate to Child Object (Propaga a oggetto figlio), role=Azure_Site_Recovery (ruolo=Azure_Site_Recovery)<br/><br/> Datastore (Archivio dati) -> Allocate space (Alloca spazio), Browse datastore (Sfoglia archivio dati), Low level file operations (Operazioni file di livello basso), Remove file (Rimuovi file), Update virtual machine files (Aggiorna file macchina virtuale)<br/><br/> Network (Rete) -> Network assign (Assegnazione rete)<br/><br/> Risorsa -> Assegnare VM al pool di risorse, migrare la VM spenta, migrare la VM accesa<br/><br/> Tasks (Attività) -> Create task (Crea attività), Update task (Aggiorna attività)<br/><br/> Virtual machine (Macchina virtuale) -> Configuration (Configurazione)<br/><br/> Virtual machine (Macchina virtuale) -> Interact (Interagisci) -> Answer question (Rispondi alla domanda), Device connection (Connessione dispositivo), Configure CD media (Configura supporto CD), Configure floppy media (Configura supporto floppy), Power off (Spegni), Power on (Accendi), VMware tools install (Installazione strumenti VMware)<br/><br/> Virtual machine (Macchina virtuale) -> Inventory (Inventario) -> Create (Crea), Register (Registra), Unregister (Annulla registrazione)<br/><br/> Virtual machine (Macchina virtuale) -> Provisioning -> Allow virtual machine download (Consenti download macchina virtuale), Allow virtual machine files upload (Consenti upload file macchina virtuale)<br/><br/> Macchina virtuale -> Snapshots -> Remove snapshots | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).|


## <a name="add-vmware-server-to-the-vault"></a>Aggiungere il server VMware all'insieme di credenziali

Quando si configura il ripristino di emergenza per le macchine virtuali VMware locali, è necessario aggiungere l'host server vCenter/vSphere in cui vengono individuate le VM nell'insieme di credenziali di Site Recovery, come indicato di seguito:

1. In insieme di credenziali > **infrastruttura Site Recovery** > **configurazione**, aprire il server di configurazione.
2. Nella pagina **Dettagli** fare clic su **vCenter**.
3. In **Aggiungi vCenter**specificare un nome descrittivo per l'host vSphere o il server vCenter.
4. Specificare l'indirizzo IP o il nome di dominio completo del server.
5. Lasciare la porta 443, a meno che i server VMware siano configurati per l'ascolto delle richieste su una porta diversa.
6. Selezionare l'account usato per la connessione al server VMware vCenter o vSphere ESXi. Fare quindi clic su **OK**.



## <a name="modify-credentials"></a>Modificare le credenziali

Se necessario, è possibile modificare le credenziali usate per connettersi all'host server vCenter/vSphere come indicato di seguito:

1. Accedere alla configurazione.
2. Aprire lo strumento server di configurazione (cspsconfigtool. exe) utilizzando il collegamento sul desktop.
2. Nella scheda **Gestisci account** fare clic su **Aggiungi account**.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
   
3. Specificare i nuovi dettagli dell'account e fare clic su **OK**. L'account deve disporre delle autorizzazioni elencate in [precedenza](#account-permissions).
4. Nell'insieme di credenziali > **Site Recovery infrastruttura** > **configurazione**, aprire il server di configurazione.
5. In **Dettagli**fare clic su **Aggiorna server**.
6. Al termine del processo di aggiornamento del server, selezionare il server vCenter.
7. In **Riepilogo**selezionare l'account appena aggiunto nel **Server Center/account host vSphere**e fare clic su **Salva**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Eliminare un server vCenter 

1. Nell'insieme di credenziali > **Site Recovery infrastruttura** > **configurazione**, aprire il server di configurazione.
2. Nella pagina **Dettagli** selezionare il server vCenter.
3. Fare clic sul pulsante **Elimina**.

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>Modificare l'indirizzo IP e la porta

È possibile modificare l'indirizzo IP del server vCenter o le porte utilizzate per la comunicazione tra il server e Site Recovery. Per impostazione predefinita, Site Recovery accede alle informazioni sull'host server vCenter/vSphere tramite la porta 443.

1. Nell'insieme di credenziali > **Site Recovery infrastruttura** > **server di configurazione**, fare clic sul server di configurazione a cui viene aggiunta la server vCenter.
2. In **vCenter Server**, fare clic sul server vCenter che si desidera modificare.
5. In **Riepilogo**aggiornare l'indirizzo IP e la porta e salvare le modifiche.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. Per rendere effettive le modifiche, attendere 15 minuti o [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server).


## <a name="migrate-all-vms-to-a-new-server"></a>Eseguire la migrazione di tutte le macchine virtuali in un nuovo server

Se si vuole eseguire la migrazione di tutte le macchine virtuali per usare una nuova server vCenter, è sufficiente aggiornare l'indirizzo IP assegnato alla server vCenter. Non aggiungere un altro account VMware, perché questo potrebbe generare voci duplicate. Aggiornare l'indirizzo come indicato di seguito:

1. Nell'insieme di credenziali > **Site Recovery infrastruttura** > i **server di configurazione**, Lick sul server di configurazione a cui viene aggiunta la server vCenter.
2. Nella sezione **server vCenter** fare clic sull'server vCenter da cui si desidera eseguire la migrazione.
5. In **Riepilogo**aggiornare l'indirizzo IP a quello del nuovo server vCenter e salvare le modifiche.
6. Non appena viene aggiornato l'indirizzo IP, Site Recovery inizia a ricevere le informazioni di individuazione della macchina virtuale dalla nuova server vCenter. Questa operazione non influisca sulle attività di replica in corso.

## <a name="migrate-a-few-vms-to-a-new-server"></a>Eseguire la migrazione di alcune macchine virtuali a un nuovo server

Se si vuole solo eseguire la migrazione di alcune macchine virtuali di replica in un nuovo server vCenter, seguire questa procedura:

1. [Aggiungere](#add-vmware-server-to-the-vault) il nuovo server vCenter al server di configurazione.
2. [Disabilitare la replica](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) per le macchine virtuali che vengono spostate nel nuovo server.
3. In VMware eseguire la migrazione delle macchine virtuali alla nuova server vCenter. 
4. [Abilitare](vmware-azure-tutorial.md#enable-replication) di nuovo la replica per le VM migrate, selezionando il nuovo server vCenter.


## <a name="migrate-most-vms-to-a-new-server"></a>Eseguire la migrazione della maggior parte delle VM in un nuovo server
 Se il numero di macchine virtuali di cui si vuole eseguire la migrazione a una nuova server vCenter è superiore al numero di macchine virtuali che rimarranno nella server vCenter originale, eseguire le operazioni seguenti:

1. [Aggiornare l'indirizzo IP](#modify-the-ip-address-and-port) assegnato all'server vCenter nelle impostazioni del server di configurazione, all'indirizzo della nuova server vCenter.
2. [Disabilitare la replica](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) per le poche VM rimanenti nel server precedente.
3. [Aggiungere il server vCenter precedente](#add-vmware-server-to-the-vault) e il relativo indirizzo IP al server di configurazione.
4. [Abilitare di nuovo la replica](vmware-azure-tutorial.md#enable-replication) per le macchine virtuali che rimangono nel server precedente.
 
 ## <a name="next-steps"></a>Passaggi successivi
Se si verificano problemi, [risolvere](vmware-azure-troubleshoot-vcenter-discovery-failures.md) server vCenter errori.
