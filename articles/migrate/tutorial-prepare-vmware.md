---
title: Preparare le macchine virtuali VMware per la valutazione e la migrazione con Azure Migrate
description: Informazioni su come preparare la valutazione e la migrazione di macchine virtuali VMware con Azure Migrate.
ms.topic: tutorial
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: a3f9716d6302c41f139d9a2a8b1f994b79afd199
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677291"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Preparare le macchine virtuali VMware per la valutazione e la migrazione ad Azure

Questo articolo offre informazioni utili su come preparare le macchine virtuali VMware locali per la valutazione e/o la migrazione ad Azure usando [Azure Migrate](migrate-services-overview.md).



Questa esercitazione è la prima di una serie che illustra come valutare ed eseguire la migrazione di macchine virtuali VMware. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Preparare Azure per l'uso con Azure Migrate.
> * Preparare VMware per la valutazione delle macchine virtuali con lo strumento Azure Migrate: Valutazione server.
> * Preparare VMware per la migrazione delle macchine virtuali con lo strumento Azure Migrate: strumento Migrazione server. 

> [!NOTE]
> Le esercitazioni mostrano il percorso di distribuzione più semplice per uno scenario. Sono utili quando si apprenderà come configurare una distribuzione e come un modello di verifica rapido. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prepare-azure"></a>Preparare Azure

Queste autorizzazioni sono necessarie per queste attività in Azure prima di poter valutare o eseguire la migrazione di macchine virtuali VMware.

**Attività** | **Dettagli** 
--- | --- 
**Creare un progetto di Azure Migrate** | Per creare un progetto, è necessario che l'account Azure abbia autorizzazioni di Collaboratore o Proprietario. 
**Registrare i provider di risorse** | Azure Migrate usa un'appliance di Azure Migrate leggera per individuare e valutare le VM VMware e per eseguirne la migrazione ad Azure con lo strumento Valutazione server di Azure Migrate.<br/><br/> Durante la registrazione dell'appliance, i provider di risorse vengono registrati con la sottoscrizione scelta nell'appliance. [Altre informazioni](migrate-appliance-architecture.md#appliance-registration)<br/><br/> Per registrare i provider di risorse, è necessario il ruolo di proprietario o collaboratore della sottoscrizione.
**Creare app Azure AD** | Quando si registra l'appliance, Azure Migrate crea due app Azure Active Directory (Azure AD). <br/><br/> - La prima app viene usata per la comunicazione tra gli agenti in esecuzione nell'appliance e i rispettivi servizi in esecuzione in Azure.<br/><br/> - La seconda app viene usata esclusivamente per l'accesso all'insieme di credenziali delle chiavi creato nella sottoscrizione dell'utente per la migrazione di macchine virtuali VMware senza agente. [Altre informazioni](migrate-appliance-architecture.md#appliance-registration)<br/><br/> Sono necessarie le autorizzazioni per creare app di Azure AD (disponibili nel ruolo Sviluppatore applicazioni).
**Creare un insieme di credenziali delle chiavi** | Per eseguire la migrazione delle macchine virtuali VMware con la migrazione senza agente, Azure Migrate crea un insieme di credenziali delle chiavi per gestire le chiavi di accesso all'account di archiviazione di replica nella sottoscrizione.<br/><br/> Per creare l'insieme di credenziali, sono necessarie autorizzazioni di assegnazione di ruolo per il gruppo di risorse in cui si trova il progetto di Azure Migrate.




### <a name="assign-permissions-to-create-project"></a>Assegnare le autorizzazioni per creare il progetto

1. Nel portale di Azure aprire la sottoscrizione e selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.
3. È necessario avere le autorizzazioni di **Collaboratore** o **Proprietario**.
    - Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione.
    - Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare il ruolo.

### <a name="assign-permissions-to-register-the-appliance"></a>Assegnare le autorizzazioni per registrare l'appliance

Per registrare l'appliance, assegnare le autorizzazioni per consentire ad Azure Migrate di creare le app Azure AD durante la registrazione dell'appliance. È possibile assegnare queste autorizzazioni usando uno dei metodi seguenti:

- **Concedere le autorizzazioni**: Un amministratore tenant/globale può concedere agli utenti del tenant le autorizzazioni per creare e registrare app Azure AD.
- **Assegnare il ruolo Sviluppatore applicazione**: Un amministratore tenant/globale può assegnare il ruolo Sviluppatore di applicazioni (che include le autorizzazioni) all'account.

> [!NOTE]
> - Le app non hanno altre autorizzazioni di accesso per la sottoscrizione oltre a quelle descritte sopra.
> - Queste autorizzazioni sono necessarie solo quando si registra una nuova appliance. Dopo la configurazione dell'appliance è possibile rimuoverle.


#### <a name="grant-account-permissions"></a>Concedere le autorizzazioni all'account

Se si vuole che l'amministratore tenant o globale conceda le autorizzazioni, procedere come segue:

1. In Azure AD, l'amministratore globale/tenant deve passare a **Azure Active Directory** > **Utenti** > **Impostazioni utente**.
2. L'amministratore deve impostare **Registrazioni app**su **Sì**. Si tratta di un'impostazione predefinita che non è sensibile. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)

    ![Autorizzazioni di Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Assegnare il ruolo Sviluppatore di applicazioni

In alternativa, l'amministratore tenant/globale può assegnare il ruolo Sviluppatore applicazione a un account. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) sull'assegnazione dei ruoli.

### <a name="assign-permissions-to-create-a-key-vault"></a>Assegnare le autorizzazioni per creare un insieme di credenziali delle chiavi

Per consentire ad Azure Migrate di creare un insieme di credenziali delle chiavi, assegnare le autorizzazioni come segue:

1. Nel gruppo di risorse nel portale di Azure selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.

    - Per eseguire la valutazione dei server sono sufficienti le autorizzazioni di **Collaboratore**.
    - Per eseguire una migrazione dei server senza agente sono necessarie le autorizzazioni di **Proprietario** (o di **Collaboratore** e **Amministratore Accesso utenti** ).

3. Se non si hanno le autorizzazioni necessarie, richiederle al proprietario del gruppo di risorse.



## <a name="prepare-for-vmware-vm-assessment"></a>Preparare le macchine virtuali VMware per la valutazione

Per preparare la valutazione delle macchine virtuali VMware è necessario:

- **Verificare le impostazioni di VMware**. Verificare che il server vCenter e le macchine virtuali di cui si vuole eseguire la migrazione soddisfino i requisiti.
- **Configurare un account per la valutazione**. Azure Migrate usa questo account per accedere al server vCenter per individuare le macchine virtuali di cui eseguire la valutazione.
- **Verificare i requisiti dell'appliance**. Verificare i requisiti di distribuzione per l'appliance Azure Migrate, prima di procedere alla distribuzione.

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

1. [Verificare](migrate-appliance.md#appliance---vmware) i requisiti dell'appliance di Azure Migrate.
2. Esaminare gli URL di Azure a cui l'appliance dovrà accedere nei cloud [pubblico](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls).
3. [Esaminare i dati](migrate-appliance.md#collected-data---vmware) che l'appliance raccoglierà durante l'individuazione e la valutazione.
4. [Prendere nota](migrate-support-matrix-vmware.md#port-access) dei requisiti di accesso alle porte per l'appliance.




## <a name="prepare-for-agentless-vmware-migration"></a>Preparare le macchine virtuali VMWare per la migrazione senza agente

Rivedere i requisiti per la [migrazione senza agente](server-migrate-overview.md) delle macchine virtuali VMware.

1. [Verificare](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) i requisiti del server VMware.
2. [Verificare le autorizzazioni](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) necessarie affinché Azure Migrate possa accedere al server vCenter.
3. [Esaminare](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) i requisiti delle macchine virtuali VMware.
4. [Esaminare](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) i requisiti dell'appliance di Azure Migrate.
5. Prendere nota dell'accesso URL necessario per i cloud [pubblico](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls).
6. Verificare i requisiti di [accesso alle porte](migrate-support-matrix-vmware-migration.md#agentless-ports).

## <a name="prepare-for-agent-based-vmware-migration"></a>Preparare le macchine virtuali VMWare per la migrazione basata su agente

Rivedere i requisiti per la [migrazione basata su agente](server-migrate-overview.md) delle macchine virtuali VMware.

1. [Verificare](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) i requisiti del server VMware.
2. [Verificare le autorizzazioni](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) necessarie affinché Azure Migrate possa accedere al server vCenter.
2. [Esaminare](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) i requisiti delle macchine virtuali VMware, inclusa l'installazione del servizio di mobilità in ogni macchina virtuale di cui si vuole eseguire la migrazione.
3. La migrazione basata su agente usa un'appliance di replica:
    - [Esaminare](migrate-replication-appliance.md#appliance-requirements) i requisiti di distribuzione per l'appliance di replica.
    - [Esaminare le opzioni](migrate-replication-appliance.md#mysql-installation) per l'installazione di MySQL nell'appliance.
    - Prendere nota dell'accesso URL necessario per i cloud [pubblico](migrate-replication-appliance.md#url-access) e per [enti pubblici](migrate-replication-appliance.md#azure-government-url-access).
    - Esaminare i requisiti di [accesso alle porte](migrate-replication-appliance.md#port-access) per l'appliance di replica.
    
## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

> [!div class="checklist"]
> * Sono state configurate le autorizzazioni di Azure.
> * Sono state preparate la valutazione e la migrazione di VM VMware.


Continuare con la seconda esercitazione per configurare un progetto di Azure Migrate e valutare le macchine virtuali VMware per la migrazione ad Azure.

> [!div class="nextstepaction"]
> [Valutare le macchine virtuali VMware](./tutorial-assess-vmware.md)
