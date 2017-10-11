---
title: " Gestire un server VMware vCenter in Azure Site Recovery | Microsoft Docs"
description: Questo articolo descrive come aggiungere e gestire un server VMware vCenter in Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: 091f0884417535427c52beee7bcdc5ed1dd83315
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="manage-vmware-vcenter-server-in-azure-site-recovery"></a>Gestire un server VMware vCenter in Azure Site Recovery
Questo articolo prende in esame le varie operazioni di Site Recovery che possono essere eseguite su un server VMware vCenter.

## <a name="prerequisites"></a>Prerequisiti

**Supporto per host VMware vCenter e VMware vSphere ESX** | **Dettagli** |
|--- | --- |
|**Server VMware locali** | Uno o più server VMware vSphere, che eseguono la versione 6.0, 5.5 o 5.1 con gli ultimi aggiornamenti. I server devono trovarsi nella stessa rete del server di configurazione (o del server di elaborazione separato).<br/><br/> È consigliabile usare un server vCenter, che esegue la versione 6.0 o 5.5 con gli ultimi aggiornamenti, per gestire gli host. Quando si distribuisce la versione 6.0, sono supportate solo le funzionalità disponibili nella versione 5.5.|

## <a name="prepare-an-account-for-automatic-discovery"></a>Preparare un account per l'individuazione automatica
Site Recovery deve avere accesso a VMware per l'individuazione automatica delle macchine virtuali da parte del server di elaborazione e per il failover e il failback delle macchine virtuali.

* **Migrazione**: se si vuole eseguire solo la migrazione di macchine virtuali VMware in Azure, senza failback, è possibile usare un account VMware con un ruolo di sola lettura. Un ruolo di questo tipo può eseguire il failover, ma non arrestare i computer di origine protetti. Ciò non è necessario per la migrazione.
* **Replica/ripristino**: se si vuole distribuire una replica completa (replica, failover e failback), l'account deve poter eseguire operazioni come la creazione e la rimozione di dischi, l'accensione di macchine virtuali e così via.
* **Individuazione automatica**: è necessario almeno un account di sola lettura.


|**Attività** | **Account/ruolo necessario** | **Autorizzazioni** | **Dettagli**|
|--- | --- | --- | ---|
|**Individuazione automatica delle macchine virtuali VMware da parte del server di elaborazione** | È necessario almeno un utente di sola lettura | Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).|
|**Failover** | È necessario almeno un utente di sola lettura | Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).<br/><br/> È utile ai fini della migrazione, ma non per la replica completa, il failover e il failback.|
|**Failover e failback** | È consigliabile creare un ruolo (AzureSiteRecoveryRole) con le necessarie autorizzazioni e poi assegnare il ruolo a un utente o gruppo VMware. | Data Center object (Oggetto data center) –> Propagate to Child Object (Propaga a oggetto figlio), role=Azure_Site_Recovery (ruolo=Azure_Site_Recovery)<br/><br/> Datastore (Archivio dati) -> Allocate space (Alloca spazio), Browse datastore (Sfoglia archivio dati), Low level file operations (Operazioni file di livello basso), Remove file (Rimuovi file), Update virtual machine files (Aggiorna file macchina virtuale)<br/><br/> Network (Rete) -> Network assign (Assegnazione rete)<br/><br/> Risorsa -> Assegnare VM al pool di risorse, migrare la VM spenta, migrare la VM accesa<br/><br/> Tasks (Attività) -> Create task (Crea attività), Update task (Aggiorna attività)<br/><br/> Virtual machine (Macchina virtuale) -> Configuration (Configurazione)<br/><br/> Virtual machine (Macchina virtuale) -> Interact (Interagisci) -> Answer question (Rispondi alla domanda), Device connection (Connessione dispositivo), Configure CD media (Configura supporto CD), Configure floppy media (Configura supporto floppy), Power off (Spegni), Power on (Accendi), VMware tools install (Installazione strumenti VMware)<br/><br/> Virtual machine (Macchina virtuale) -> Inventory (Inventario) -> Create (Crea), Register (Registra), Unregister (Annulla registrazione)<br/><br/> Virtual machine (Macchina virtuale) -> Provisioning -> Allow virtual machine download (Consenti download macchina virtuale), Allow virtual machine files upload (Consenti upload file macchina virtuale)<br/><br/> Macchina virtuale -> Snapshots -> Remove snapshots | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).|

## <a name="create-an-account-to-connect-to-vmware-vcenter-server-vmware-vsphere-exsi-host"></a>Creare un account per connettersi al server VMware vCenter o all'host VMware vSphere EXSi
1. Accedere al server di configurazione e avviare cspsconfigtool.exe tramite il collegamento presente sul Desktop.
2. Nella scheda **Gestisci account** fare clic su **Aggiungi account**.

  ![add-account](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. Fornire i dettagli dell'account e fare clic su OK per aggiungere l'account. L'account deve disporre dei privilegi elencati nella sezione [Preparare un account per l'individuazione automatica](#prepare-an-account-for-automatic-discovery).

  >[!NOTE]
  Sono necessari circa 15 minuti per completare la sincronizzazione delle informazioni dell'account con il servizio Site Recovery.


## <a name="associate-a-vmware-vcenter-vmware-vsphere-esx-host-add-vcenter"></a>Associare un host VMware vCenter/vSphere di VMware ESX (Aggiungi vCenter)
* Nel portale di Azure, andare in *YourRecoveryServicesVault* > **Infrastruttura di Site Recovery** > **Server di configurazione** > *ConfigurationServer*.
* Nella pagina dei dettagli del server di configurazione fare clic sul pulsante + vCenter.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials-used-to-connect-to-the-vcenter-server-vsphere-esxi-host"></a>Modificare le credenziali usate per connettersi al server vCenter o all'host ESXi vSphere.

1. Accedere al server di configurazione e avviare CSPSConfigtool.exe.
2. Nella scheda **Gestisci account** fare clic su **Aggiungi account**.

  ![add-account](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. Fornire i nuovi dettagli dell'account e fare clic su OK per aggiungere l'account. L'account deve disporre dei privilegi elencati nella sezione [Preparare un account per l'individuazione automatica](#prepare-an-account-for-automatic-discovery).
4. Nel portale di Azure, andare in *YourRecoveryServicesVault* > **Infrastruttura di Site Recovery** > **Server di configurazione** > *ConfigurationServer*.
5. Nella pagina dei dettagli del server di configurazione fare clic sul pulsante **Aggiorna server**.
6. Dopo aver completato il processo di aggiornamento del server, selezionare il server vCenter per aprire la pagina di riepilogo vCenter.
7. Selezionare l'account appena aggiunto nel campo del **server vCenter server/account host vSphere** e fare clic sul pulsante **Salva**.

  ![modify-account](./media/site-recovery-vmware-to-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-in-azure-site-recovery"></a>Eliminare un server vCenter in Azure Site Recovery
1. Nel portale di Azure, andare in *YourRecoveryServicesVault* > **Infrastruttura di Site Recovery** > **Server di configurazione** > *ConfigurationServer*.
2. Nella pagina dei dettagli del server di configurazione selezionare il server vCenter per aprire la relativa pagina di riepilogo.
3. Fare clic sul pulsante **Elimina** per eliminare il server vCenter.

  ![delete-account](./media/site-recovery-vmware-to-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
Se è necessario modificare l'indirizzo IP o FQDN dei server vCenter o i dettagli della porta è necessario eliminare il server vCenter e aggiungerlo di nuovo.
