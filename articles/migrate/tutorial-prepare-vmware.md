---
title: Preparare le macchine virtuali VMware per la valutazione e la migrazione con Azure Migrate
description: Informazioni su come preparare la valutazione e la migrazione di macchine virtuali VMware con Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 4dec76140f61c433561ccfea07b833d9821acfc5
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028900"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Preparare le macchine virtuali VMware per la valutazione e la migrazione ad Azure

Questo articolo offre informazioni utili su come preparare le macchine virtuali VMware locali per la valutazione e/o la migrazione ad Azure usando [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti.


Questa esercitazione è la prima di una serie che illustra come valutare ed eseguire la migrazione di macchine virtuali VMware. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Preparare Azure per l'uso con Azure Migrate.
> * Preparare VMware per la valutazione di macchine virtuali.
> * Preparare VMware per la migrazione di macchine virtuali.

> [!NOTE]
> Le esercitazioni mostrano il percorso di distribuzione più semplice per uno scenario. Sono utili quando si apprenderà come configurare una distribuzione e come un modello di verifica rapido. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere le procedure relative alla valutazione e alla migrazione di VMware.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prepare-azure"></a>Preparare Azure

Sono necessarie queste autorizzazioni.

**Attività** | **Autorizzazioni**
--- | ---
**Creare un progetto di Azure Migrate** | L'account di Azure necessita di autorizzazioni per creare un progetto.
**Registrare l'appliance Azure Migrate** | Azure Migrate usa un'appliance leggera di Azure Migrate per valutare le macchine virtuali VMware con lo strumento Valutazione server di Azure Migrate e per eseguire [migrazioni senza agente](server-migrate-overview.md) di macchine virtuali VMware tramite il servizio Migrazione server di Azure Migrate. L'appliance individua le macchine virtuali, ne invia i metadati e i dati sulle prestazioni ad Azure Migrate.<br/><br/>Durante la registrazione, Azure Migrate crea due app Azure Active Directory (Azure AD) che identificano l'appliance in modo univoco. Per crearle, è necessario fornire le relative autorizzazioni.<br/> - La prima app comunica con gli endpoint del servizio Azure Migrate.<br/> - La seconda app accede a un'istanza di Azure Key Vault creata durante la registrazione per archiviare le informazioni delle app Azure AD e le impostazioni di configurazione dell'appliance.
**Creare un insieme di credenziali delle chiavi** | Per eseguire la migrazione delle macchine virtuali VMware con il servizio Migrazione server di Azure Migrate, Azure Migrate crea un insieme di credenziali delle chiavi per gestire le chiavi di accesso all'account di archiviazione di replica nella sottoscrizione. Per creare l'insieme di credenziali, sono necessarie autorizzazioni di assegnazione di ruolo per il gruppo di risorse in cui si trova il progetto di Azure Migrate.


### <a name="assign-permissions-to-create-project"></a>Assegnare le autorizzazioni per creare il progetto

1. Nel portale di Azure aprire la sottoscrizione e selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.
3. È necessario avere le autorizzazioni di **Collaboratore** o **Proprietario**.
    - Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione.
    - Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare il ruolo.

### <a name="assign-permissions-to-register-the-appliance"></a>Assegnare le autorizzazioni per registrare l'appliance

Per registrare l'appliance, assegnare le autorizzazioni per consentire ad Azure Migrate di creare le app Azure AD durante la registrazione dell'appliance. È possibile assegnare queste autorizzazioni usando uno dei metodi seguenti:

- Un amministratore tenant/globale può concedere agli utenti del tenant le autorizzazioni per creare e registrare app Azure AD.
- Un amministratore tenant/globale può assegnare il ruolo Sviluppatore di applicazioni (che include le autorizzazioni) all'account.

> [!NOTE]
> - Le app non hanno altre autorizzazioni di accesso per la sottoscrizione oltre a quelle descritte sopra.
> - Queste autorizzazioni sono necessarie solo quando si registra una nuova appliance. Dopo la configurazione dell'appliance è possibile rimuoverle.


#### <a name="grant-account-permissions"></a>Concedere le autorizzazioni all'account

L'amministratore tenant/globale può concedere le autorizzazioni nel modo seguente:

1. In Azure AD, l'amministratore globale/tenant deve passare a **Azure Active Directory** > **Utenti** > **Impostazioni utente**.
2. L'amministratore deve impostare **Registrazioni app**su **Sì**. Si tratta di un'impostazione predefinita che non è sensibile. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)

    ![Autorizzazioni di Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Assegnare il ruolo Sviluppatore di applicazioni

L'amministratore tenant/globale può assegnare il ruolo Sviluppatore di applicazioni a un account. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)

### <a name="assign-role-assignment-permissions"></a>Assegnare le autorizzazioni di assegnazione di ruolo

Per abilitare Azure Migrate a creare un insieme di credenziali delle chiavi, assegnare le autorizzazioni di assegnazione di ruolo come segue:

1. Nel gruppo di risorse nel portale di Azure selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.

    - Per eseguire la valutazione dei server sono sufficienti le autorizzazioni di **Collaboratore**.
    - Per eseguire una migrazione dei server senza agente sono necessarie le autorizzazioni di **Proprietario** (o di **Collaboratore** e **Amministratore Accesso utenti** ).

3. Se non si hanno le autorizzazioni necessarie, richiederle al proprietario del gruppo di risorse.



## <a name="prepare-for-vmware-vm-assessment"></a>Preparare le macchine virtuali VMware per la valutazione

Per preparare la valutazione delle macchine virtuali VMware è necessario:

- **Verificare le impostazioni di VMware**. Verificare che il server vCenter e le macchine virtuali di cui si vuole eseguire la migrazione soddisfino i requisiti.
- **Configurare un account per la valutazione**. Azure Migrate deve avere accesso al server vCenter per individuare le macchine virtuali di cui eseguire la valutazione.
- **Verificare i requisiti dell'appliance**. Verificare i requisiti di distribuzione per l'appliance Azure Migrate usata per la valutazione.

### <a name="verify-vmware-settings"></a>Verificare le impostazioni di VMware

1. [Controllare](migrate-support-matrix-vmware.md#vmware-requirements) i requisiti del server VMware per la valutazione.
2. [Assicurarsi](migrate-support-matrix-vmware.md#port-access) che le porte necessarie siano aperte sul server vCenter.
3. Nel server vCenter verificare che l'account abbia le autorizzazioni per creare una macchina virtuale mediante un file OVA. L'appliance di Azure Migrate viene distribuita come macchina virtuale VMware tramite un file OVA.


### <a name="set-up-an-account-for-assessment"></a>Configurare un account per la valutazione

Azure Migrate deve avere accesso al server vCenter per individuare le macchine virtuali di cui eseguire la valutazione e la migrazione senza agente.

- Se si prevede di individuare le applicazioni o di visualizzare le dipendenze in modalità senza agente, creare un account del server vCenter con accesso in sola lettura insieme ai privilegi abilitati per **Macchine virtuali** > **Operazioni guest**.

  ![Privilegi dell'account del server vCenter](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Se non si prevede di eseguire l'individuazione delle applicazioni e la visualizzazione delle dipendenze senza agente, configurare un account di sola lettura per il server vCenter.

### <a name="verify-appliance-settings-for-assessment"></a>Verificare le impostazioni dell'appliance per la valutazione

Prima di configurare l'appliance di Azure Migrate e iniziare la valutazione nella prossima esercitazione, preparare la distribuzione dell'appliance.

1. [Verificare](migrate-appliance.md#appliance---vmware) i requisiti dell'appliance per le VM VMware.
2. [Rivedere](migrate-appliance.md#url-access) gli URL di Azure a cui l'appliance dovrà accedere. Se si usa un firewall o un proxy basato su URL, assicurarsi che consenta l'accesso agli URL necessari.
3. [Esaminare](migrate-appliance.md#collected-data---vmware) i dati che l'appliance raccoglierà durante l'individuazione e la valutazione.
4. [Prendere nota](migrate-support-matrix-vmware.md#port-access) dei requisiti di accesso alle porte per l'appliance.




## <a name="prepare-for-agentless-vmware-migration"></a>Preparare le macchine virtuali VMWare per la migrazione senza agente

Rivedere i requisiti per la migrazione senza agente delle macchine virtuali VMWare.

1. [Esaminare](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) i requisiti del server VMware e le [autorizzazioni](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) necessarie per permettere ad Azure Migrate di accedere al server vCenter per la migrazione senza agente tramite il servizio Migrazione del server di Azure Migrate.
2. [Verificare](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) i requisiti per le macchine virtuali VMware di cui si vuole eseguire la migrazione ad Azure con il metodo senza agente.
4. [Verificare](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) i requisiti per l'uso dell'appliance Azure Migrate per la migrazione senza agente.
5. Prendere nota dei requisiti di accesso agli [URL](migrate-appliance.md#url-access) e alle [porte](migrate-support-matrix-vmware-migration.md#agentless-ports) per la migrazione senza agente.


## <a name="prepare-for-agent-based-vmware-migration"></a>Preparare le macchine virtuali VMWare per la migrazione basata su agente

Rivedere i requisiti per la [migrazione basata su agente](server-migrate-overview.md) delle macchine virtuali VMware.

1. [Esaminare](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) i requisiti del server VMware e le autorizzazioni necessarie per permettere ad Azure Migrate di accedere al server vCenter per la migrazione basata su agente tramite il servizio Migrazione del server di Azure Migrate.
2. [Verificare](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) i requisiti per le macchine virtuali VMware di cui si vuole eseguire la migrazione in Azure usando il metodo basato su agente, inclusa l'installazione del servizio Mobility in ogni macchina virtuale di cui eseguire la migrazione.
3. Le migrazioni basate su agente usano un'appliance di replica:
    - [Esaminare](migrate-replication-appliance.md#appliance-requirements) i requisiti di distribuzione per l'appliance di replica e le [opzioni](migrate-replication-appliance.md#mysql-installation) per l'installazione di MySQL nell'appliance.
    - Esaminare i requisiti di accesso agli [URL](migrate-replication-appliance.md#url-access) e alle [porte](migrate-replication-appliance.md#port-access) per l'appliance di replica.
    
## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

> [!div class="checklist"]
> * Sono state configurate le autorizzazioni di Azure.
> * Sono state preparate la valutazione e la migrazione di VM VMware.


Continuare con la seconda esercitazione per configurare un progetto di Azure Migrate e valutare le macchine virtuali VMware per la migrazione ad Azure.

> [!div class="nextstepaction"]
> [Valutare le macchine virtuali VMware](./tutorial-assess-vmware.md)
