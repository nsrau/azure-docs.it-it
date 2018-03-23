---
title: " Gestire un server VMware vCenter in Azure Site Recovery | Microsoft Docs"
description: Questo articolo descrive come aggiungere e gestire un server VMware vCenter in Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: be415340da09043eccd361b0168bb304d8904bef
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="manage-vmware-vcenter-servers"></a>Gestire i server VMware vCenter 

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

|**Task** | **Account** | **Autorizzazioni** | **Dettagli**|
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

> [!NOTE]
Se è necessario modificare l'indirizzo IP, il nome di dominio completo (FQDN) o la porta del server vCenter, è necessario eliminare il server vCenter e aggiungerlo di nuovo al portale.
