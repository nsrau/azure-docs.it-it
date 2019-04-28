---
title: Gestire i server VMware vCenter per il ripristino di emergenza di macchine virtuali VMware in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come aggiungere e gestire server VMware vCenter per il ripristino di emergenza di macchine virtuali VMware in Azure con Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 9694c682f171ab715812b05fed2064c9bbcd36b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60600428"
---
# <a name="manage-vmware-vcenter-server"></a>Gestire i server VMware vCenter

Questo articolo prende in esame le varie operazioni di Site Recovery che possono essere eseguite su un server VMware vCenter. Verificare il [prerequisiti](vmware-physical-azure-support-matrix.md#replicated-machines) prima di iniziare.


## <a name="set-up-an-account-for-automatic-discovery"></a>Configurare un account per l'individuazione automatica

Site Recovery deve avere accesso a VMware per l'individuazione automatica delle macchine virtuali da parte del server di elaborazione e per il failover e il failback delle macchine virtuali. Creare un account per l'accesso come segue:

1. Accedere al computer server di configurazione.
2. Aprire il file cspsconfigtool.exe usando il relativo collegamento sul desktop.
3. Nella scheda **Gestisci account** fare clic su **Aggiungi account**.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Fornire i dettagli dell'account e fare clic su **OK** per aggiungere l'account.  L'account deve disporre dei privilegi riepilogati nella tabella seguente. 

Sono necessari circa 15 minuti per completare la sincronizzazione delle informazioni dell'account con il servizio Site Recovery.

### <a name="account-permissions"></a>Autorizzazioni dell'account

|**Attività** | **Account** | **Autorizzazioni** | **Dettagli**|
|--- | --- | --- | ---|
|**Individuazione automatica/migrazione (senza failback)** | È necessario almeno un utente di sola lettura | Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).|
|**Replica/failover** | È necessario almeno un utente di sola lettura| Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).<br/><br/> È utile ai fini della migrazione, ma non per la replica completa, il failover e il failback.|
|**Replica/failover/failback** | È consigliabile creare un ruolo (AzureSiteRecoveryRole) con le necessarie autorizzazioni e poi assegnare il ruolo a un utente o gruppo VMware. | Data Center object (Oggetto data center) –> Propagate to Child Object (Propaga a oggetto figlio), role=Azure_Site_Recovery (ruolo=Azure_Site_Recovery)<br/><br/> Datastore (Archivio dati) -> Allocate space (Alloca spazio), Browse datastore (Sfoglia archivio dati), Low level file operations (Operazioni file di livello basso), Remove file (Rimuovi file), Update virtual machine files (Aggiorna file macchina virtuale)<br/><br/> Network (Rete) -> Network assign (Assegnazione rete)<br/><br/> Risorsa -> Assegnare VM al pool di risorse, migrare la VM spenta, migrare la VM accesa<br/><br/> Tasks (Attività) -> Create task (Crea attività), Update task (Aggiorna attività)<br/><br/> Virtual machine (Macchina virtuale) -> Configuration (Configurazione)<br/><br/> Virtual machine (Macchina virtuale) -> Interact (Interagisci) -> Answer question (Rispondi alla domanda), Device connection (Connessione dispositivo), Configure CD media (Configura supporto CD), Configure floppy media (Configura supporto floppy), Power off (Spegni), Power on (Accendi), VMware tools install (Installazione strumenti VMware)<br/><br/> Virtual machine (Macchina virtuale) -> Inventory (Inventario) -> Create (Crea), Register (Registra), Unregister (Annulla registrazione)<br/><br/> Virtual machine (Macchina virtuale) -> Provisioning -> Allow virtual machine download (Consenti download macchina virtuale), Allow virtual machine files upload (Consenti upload file macchina virtuale)<br/><br/> Macchina virtuale -> Snapshots -> Remove snapshots | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).|


## <a name="add-vmware-server-to-the-vault"></a>Aggiungere il server VMware all'insieme di credenziali

1. Nel portale di Azure aprire l'insieme di credenziali > **Infrastruttura di Site Recovery** > **Server di configurazione** e aprire il server di configurazione.
2. Nella pagina **Dettagli** fare clic su **+vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Modificare le credenziali

Modificare le credenziali usate per connettersi al server vCenter o all'host ESXi nel modo seguente:

1. Accedere al server di configurazione e avviare cspsconfigtool.exe dal desktop.
2. Nella scheda **Gestisci account** fare clic su **Aggiungi account**.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Fornire i dettagli del nuovo account e fare clic su **OK** per aggiungere l'account. L'account deve avere i privilegi elencati [sopra](#account-permissions).
4. Nel portale di Azure aprire l'insieme di credenziali > **Infrastruttura di Site Recovery** > **Server di configurazione** e aprire il server di configurazione.
5. Nella pagina **Dettagli** fare clic su **Aggiorna server**.
6. Dopo aver completato il processo di aggiornamento del server, selezionare il server vCenter per aprire la pagina di **riepilogo** di vCenter.
7. Selezionare l'account appena aggiunto nel campo del **server vCenter/account host vSphere** e fare clic sul pulsante **Salva**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Eliminare un server vCenter

1. Nel portale di Azure aprire l'insieme di credenziali > **Infrastruttura di Site Recovery** > **Server di configurazione** e aprire il server di configurazione.
2. Nella pagina **Dettagli** selezionare il server vCenter.
3. Fare clic sul pulsante **Elimina**.

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>Modificare l'indirizzo IP di vCenter e la porta

1. Accedere al portale di Azure.
2. Passare a **insieme di credenziali dei servizi di ripristino** > **infrastruttura di Site Recovery** > **Configuration server**.
3. Fare clic sul server di configurazione il server vCenter è assegnato a.
4. Nel **vCenter server** fare clic su in vCenter che si desidera modificare.
5. Nella pagina di riepilogo vCenter, aggiornare l'indirizzo IP e la porta di vCenter nei rispettivi campi e quindi salvare le modifiche.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. Per le modifiche diventano effettive, attendere 15 minuti o [aggiornare il server di configurazione](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>Eseguire la migrazione delle macchine virtuali protette tutto a un nuovo vCenter

Per eseguire la migrazione di tutte le macchine virtuali per il nuovo vCenter, non aggiungere un altro account vCenter. Questo può causare la duplicazione delle voci. Aggiornare solo l'indirizzo IP del nuovo vCenter:

1. Accedere al portale di Azure.
2. Passare a **insieme di credenziali dei servizi di ripristino** > **infrastruttura di Site Recovery** > **Configuration server**.
3. Fare clic sul server di configurazione il server vCenter precedente viene assegnato a.
4. Nel **vCenter server** sezione, fare clic su si prevede di eseguire la migrazione da vCenter.
5. Nella pagina di riepilogo vCenter, aggiornare l'indirizzo IP del nuovo vCenter nel campo **vCenter server/vSphere nome host o indirizzo IP**. Salvare le modifiche.

Non appena viene aggiornato l'indirizzo IP, i componenti di Site Recovery inizieranno a ricevere le informazioni di individuazione delle macchine virtuali dal nuovo vCenter. Ciò non avrà impatto sulle attività di replica in corso.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>Eseguire la migrazione di alcune macchine virtuali protette per un nuovo vCenter

> [!NOTE]
> In questa sezione è applicabile solo quando si esegue la migrazione alcune delle macchine virtuali protette per un nuovo vCenter. Se si desidera proteggere un nuovo set di macchine virtuali da un nuovo vCenter [aggiungere i dettagli del nuovo vCenter al server di configurazione](#add-vmware-server-to-the-vault) e che iniziano con  **[Abilita protezione](vmware-azure-tutorial.md#enable-replication)**.

Per spostare alcune macchine virtuali in un nuovo vCenter:

1. [Aggiungere i dettagli del nuovo vCenter al server di configurazione](#add-vmware-server-to-the-vault).
2. [Disabilitare la replica delle macchine virtuali](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) si prevede di eseguire la migrazione.
3. Completare la migrazione di macchine virtuali selezionate per il nuovo vCenter.
4. A questo punto, proteggere le macchine virtuali migrate da [selezionando il nuovo vCenter quando si abilita la protezione](vmware-azure-tutorial.md#enable-replication).

> [!TIP]
> Se il numero di macchine virtuali in fase di migrazione **superiore** che il numero del numero di macchine virtuali conservati in vCenter precedente, aggiornare l'indirizzo IP del nuovo vCenter usando le istruzioni fornite qui. Per alcune macchine virtuali che vengono mantenute in vCenter precedente, [disabilitare la replica](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure); [aggiungere i dettagli del nuovo vCenter al server di configurazione](#add-vmware-server-to-the-vault)e avviare  **[Abilita protezione](vmware-azure-tutorial.md#enable-replication)**.

## <a name="frequently-asked-questions"></a>Domande frequenti

1. Se le macchine virtuali protette vengono spostate da un host ESXi a altro, sarà interessato dal cambiamento replica?

    No, ciò non influirà sulla replica in corso. Tuttavia, [assicurarsi di distribuire il server di destinazione master con privilegi sufficienti](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. Quali sono i numeri di porta utilizzato per la comunicazione tra vCenter e altri Site Recovery componenti?

    La porta predefinita è 443. Server di configurazione accederanno informazioni host vCenter/vSphere tramite questa porta. Se si vuole aggiornare queste informazioni, fare clic su [qui](#modify-the-vcenter-ip-address-and-port).
