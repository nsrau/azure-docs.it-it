---
title: Gestire i server VMware vCenter in Azure Site Recovery
description: Questo articolo descrive come aggiungere e gestire VMware vCenter per il ripristino di emergenza delle macchine virtuali VMware in Azure con Azure Site Recovery.This article describes how to add and manage VMware vCenter for disaster recovery of VMware VMs to Azure with Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257264"
---
# <a name="manage-vmware-vcenter-server"></a>Gestire VMware vCenter Server

Questo articolo riepiloga le azioni di gestione in un server vCenter VMware in [Azure Site Recovery](site-recovery-overview.md).

## <a name="verify-prerequisites-for-vcenter-server"></a>Verificare i prerequisiti per vCenter Server

I prerequisiti per i server vCenter e le macchine virtuali durante il ripristino di emergenza delle macchine virtuali VMware in Azure sono elencati nella matrice di [supporto.](vmware-physical-azure-support-matrix.md#replicated-machines)

## <a name="set-up-an-account-for-automatic-discovery"></a>Configurare un account per l'individuazione automatica

Quando si configura il ripristino di emergenza per le macchine virtuali VMware locali, Site Recovery deve accedere all'host vCenter Server/vSphere. Il server di elaborazione di Site Recovery può quindi individuare automaticamente le macchine virtuali ed eseguirne il failover in base alle esigenze. Per impostazione predefinita, il server di elaborazione viene eseguito nel server di configurazione di Site Recovery. Aggiungere un account per il server di configurazione per connettersi all'host vCenter Server/vSphere come segue:

1. Accedere al server di configurazione.
1. Aprire lo strumento del server di configurazione (_cspsconfigtool.exe_) utilizzando il collegamento Desktop.
1. Nella scheda **Gestisci account** fare clic su **Aggiungi account.**

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Fornire i dettagli dell'account e fare clic su **OK** per aggiungere l'account. L'account deve disporre dei privilegi riepilogati nella tabella delle autorizzazioni dell'account.

   > [!NOTE]
   > La sincronizzazione delle informazioni dell'account con Site Recovery richiede circa 15 minuti.

### <a name="account-permissions"></a>Autorizzazioni dell'account

|**Attività** | **Account** | **Autorizzazioni** | **Dettagli**|
|--- | --- | --- | ---|
|**Individuazione/migrazione delle macchine virtuali (senza failback)VM discovery/migration (without failback)** | Almeno un account utente di sola lettura. | Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).|
|**Replica/failover** | Almeno un account utente di sola lettura. | Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **Nessun accesso** con **Propaga all'oggetto figlio** agli oggetti figlio (host vSphere, archivi dati, macchine virtuali e reti).<br/><br/> È utile ai fini della migrazione, ma non per la replica completa, il failover e il failback.|
|**Replica/failover/failback** | È consigliabile creare un ruolo (AzureSiteRecoveryRole) con le autorizzazioni necessarie e quindi assegnare il ruolo a un utente o un gruppo VMware.We suggest you create a role (AzureSiteRecoveryRole) with the required permissions, and then assign the role to a VMware user or group. | Data Center object (Oggetto data center) –> Propagate to Child Object (Propaga a oggetto figlio), role=Azure_Site_Recovery (ruolo=Azure_Site_Recovery)<br/><br/> Datastore (Archivio dati) -> Allocate space (Alloca spazio), Browse datastore (Sfoglia archivio dati), Low level file operations (Operazioni file di livello basso), Remove file (Rimuovi file), Update virtual machine files (Aggiorna file macchina virtuale)<br/><br/> Network (Rete) -> Network assign (Assegnazione rete)<br/><br/> Risorsa -> Assegnare VM al pool di risorse, migrare la VM spenta, migrare la VM accesa<br/><br/> Tasks (Attività) -> Create task (Crea attività), Update task (Aggiorna attività)<br/><br/> Virtual machine (Macchina virtuale) -> Configuration (Configurazione)<br/><br/> Virtual machine (Macchina virtuale) -> Interact (Interagisci) -> Answer question (Rispondi alla domanda), Device connection (Connessione dispositivo), Configure CD media (Configura supporto CD), Configure floppy media (Configura supporto floppy), Power off (Spegni), Power on (Accendi), VMware tools install (Installazione strumenti VMware)<br/><br/> Virtual machine (Macchina virtuale) -> Inventory (Inventario) -> Create (Crea), Register (Registra), Unregister (Annulla registrazione)<br/><br/> Virtual machine (Macchina virtuale) -> Provisioning -> Allow virtual machine download (Consenti download macchina virtuale), Allow virtual machine files upload (Consenti upload file macchina virtuale)<br/><br/> Macchina virtuale -> Snapshots -> Remove snapshots | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).|

## <a name="add-vmware-server-to-the-vault"></a>Aggiungere il server VMware all'insieme di credenziali

Quando si configura il ripristino di emergenza per le macchine virtuali VMware locali, si aggiunge l'host vCenter Server/vSphere in cui si stanno individuando le macchine virtuali nell'insieme di credenziali di Site Recovery, come indicato di seguito:

1. Nell'insieme di credenziali > **Site Recovery Infrastructure** > **Configuration Severs**aprire il server di configurazione.
1. Nella pagina **Dettagli** fare clic su **vCenter**.
1. In **Aggiungi vCenter**specificare un nome descrittivo per l'host vSphere o il server vCenter.
1. Specificare l'indirizzo IP o il nome di dominio completo del server.
1. Lasciare la porta 443, a meno che i server VMware siano configurati per l'ascolto delle richieste su una porta diversa.
1. Selezionare l'account utilizzato per connettersi al server VMware vCenter o vSphere ESXi. Fare quindi clic su **OK**.

## <a name="modify-credentials"></a>Modificare le credenziali

Se necessario, è possibile modificare le credenziali utilizzate per connettersi all'host vCenter Server/vSphere come segue:

1. Accedere al server di configurazione.
1. Aprire lo strumento del server di configurazione (_cspsconfigtool.exe_) utilizzando il collegamento Desktop.
1. Nella scheda **Gestisci account** fare clic su **Aggiungi account**.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Specificare i dettagli del nuovo account e fare clic su **OK**. L'account deve disporre delle autorizzazioni elencate nella tabella [delle autorizzazioni dell'account.](#account-permissions)
1. Nell'insieme di > server di configurazione **Site Recovery Infrastructure** > **Configuration Severs**aprire il server di configurazione.
1. In **Dettagli**fare clic su **Aggiorna server**.
1. Al termine del processo Aggiorna server, selezionare il server vCenter.
1. In **Riepilogo**selezionare l'account appena aggiunto nell'account **host server/vSphere**di vCenter e fare clic su **Salva**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Eliminare un server vCenter

1. Nell'insieme di > server di configurazione **Site Recovery Infrastructure** > **Configuration Severs**aprire il server di configurazione.
1. Nella pagina **Dettagli** selezionare il server vCenter.
1. Fare clic sul pulsante **Elimina**.

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>Modificare l'indirizzo IP e la porta

È possibile modificare l'indirizzo IP del server vCenter o le porte utilizzate per la comunicazione tra il server e Site Recovery. Per impostazione predefinita, Site Recovery accede alle informazioni sull'host vCenter Server/vSphere tramite la porta 443.

1. Nell'insieme di > server di**configurazione** **dell'infrastruttura** > di Site Recovery fare clic sul server di configurazione a cui viene aggiunto il server vCenter.
1. Nei **server vCenter**, fare clic sul server vCenter che si desidera modificare.
1. In **Riepilogo**aggiornare l'indirizzo IP e la porta e salvare le modifiche.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. Affinché le modifiche diventino effettive, attendere 15 minuti o [aggiornare il server](vmware-azure-manage-configuration-server.md#refresh-configuration-server)di configurazione.

## <a name="migrate-all-vms-to-a-new-server"></a>Eseguire la migrazione di tutte le macchine virtuali in un nuovo serverMigrate all VMs to a new server

Se si desidera eseguire la migrazione di tutte le macchine virtuali per l'utilizzo di un nuovo server vCenter, è sufficiente aggiornare l'indirizzo IP assegnato al server vCenter. Non aggiungere un altro account VMware, poiché ciò potrebbe causare voci duplicate. Aggiornare l'indirizzo come segue:

1. Nell'insieme di > server di**configurazione** **dell'infrastruttura** > di Site Recovery fare clic sul server di configurazione a cui viene aggiunto il server vCenter.
1. Nella sezione **server vCenter** fare clic sul server vCenter da cui si desidera eseguire la migrazione.
1. In **Riepilogo**aggiornare l'indirizzo IP a quello del nuovo server vCenter e salvare le modifiche.
1. Non appena l'indirizzo IP viene aggiornato, Site Recovery inizia a ricevere informazioni sull'individuazione delle macchine virtuali dal nuovo server vCenter. Ciò non influisce sulle attività di replica in corso.

## <a name="migrate-a-few-vms-to-a-new-server"></a>Eseguire la migrazione di alcune macchine virtuali in un nuovo serverMigrate a few VMs to a new server

Se si vuole eseguire la migrazione solo di alcune delle macchine virtuali di replica in un nuovo server vCenter, eseguire le operazioni seguenti:If you only want to migrate a few of your replicating VMs to a new vCenter Server, do the following:

1. [Aggiungere](#add-vmware-server-to-the-vault) il nuovo vCenter Server al server di configurazione.
1. [Disabilitare](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) la replica per le macchine virtuali che verranno spostate nel nuovo server.
1. In VMware eseguire la migrazione delle macchine virtuali nel nuovo server vCenter.
1. [Abilitare](vmware-azure-tutorial.md#enable-replication) di nuovo la replica per le macchine virtuali migrate, selezionando il nuovo server vCenter.

## <a name="migrate-most-vms-to-a-new-server"></a>Eseguire la migrazione della maggior parte delle macchine virtuali in un nuovo serverMigrate most VMs to a new server

Se il numero di macchine virtuali di cui si desidera eseguire la migrazione a un nuovo server vCenter è superiore al numero di macchine virtuali che rimarranno nel server vCenter originale, eseguire le operazioni seguenti:

1. [Aggiornare l'indirizzo IP](#modify-the-ip-address-and-port) assegnato al server vCenter nelle impostazioni del server di configurazione all'indirizzo del nuovo server vCenter.
1. [Disabilitare](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) la replica per le poche macchine virtuali che rimangono nel server precedente.
1. [Aggiungere il vecchio vCenter Server](#add-vmware-server-to-the-vault) e il relativo indirizzo IP al server di configurazione.
1. [Riattivare la replica](vmware-azure-tutorial.md#enable-replication) per le macchine virtuali che rimangono nel server precedente.

## <a name="next-steps"></a>Passaggi successivi

In caso di problemi, vedere Risolvere gli errori di individuazione di [vCenter Server](vmware-azure-troubleshoot-vcenter-discovery-failures.md).
