---
title: Preparare le macchine virtuali VMware per la valutazione e la migrazione con Azure Migrate
description: Informazioni su come preparare la valutazione e la migrazione di macchine virtuali VMware con Azure Migrate.
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc
ms.openlocfilehash: f3bfaf7c2396e0f1091299375aae4bfaa1d7e8ff
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84771190"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Preparare le macchine virtuali VMware per la valutazione e la migrazione ad Azure

Questo articolo offre informazioni utili su come preparare le macchine virtuali VMware locali per la valutazione e la migrazione ad Azure usando [Azure Migrate](migrate-services-overview.md).

Questa esercitazione è la prima di una serie che illustra come valutare ed eseguire la migrazione di macchine virtuali VMware. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Preparare Azure per l'uso con Azure Migrate.
> * Predisporre la valutazione delle macchine virtuali VMware con lo strumento Azure Migrate: Valutazione server.
> * Predisporre la migrazione delle macchine virtuali VMware con lo strumento Azure Migrate: Migrazione del server. 

> [!NOTE]
> Le esercitazioni mostrano il percorso di distribuzione più semplice per uno scenario. Possono essere usate come modello di verifica rapida. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prepare-azure"></a>Preparare Azure

La tabella riepiloga le attività da completare in Azure. Dopo la tabella sono disponibili le istruzioni per ogni attività.

**Attività** | **Dettagli** | **Autorizzazioni**
--- | --- | ---
**Creare un progetto di Azure Migrate** | Un progetto Azure Migrate offre una posizione centralizzata per l'orchestrazione e la gestione di valutazioni e migrazioni con strumenti Azure Migrate, strumenti Microsoft e offerte di terze parti. | L'account Azure deve disporre delle autorizzazioni del ruolo Collaboratore o Proprietario nel gruppo di risorse in cui si trova il progetto.
**Registrare l'appliance** | Azure Migrate usa un'appliance di Azure Migrate leggera per individuare le macchine virtuali, valutarle con lo strumento Valutazione server ed eseguirne la migrazione usando la modalità di migrazione senza agente con lo strumento Migrazione del server. [Altre informazioni](migrate-appliance-architecture.md#appliance-registration) sulla registrazione. | Per registrare l'appliance, l'account Azure deve disporre delle autorizzazioni del ruolo Collaboratore o Proprietario nella sottoscrizione di Azure.
**Creare app Azure AD** | Quando si registra un'appliance, Azure Migrate crea due app Azure Active Directory (Azure AD). <br/><br/> - La prima app viene usata per la comunicazione tra gli agenti in esecuzione nell'appliance e Azure Migrate. <br/><br/> - La seconda app viene usata esclusivamente per l'accesso all'insieme di credenziali delle chiavi creato nella sottoscrizione dell'utente per la migrazione di macchine virtuali VMware senza agente.   | L'account di Azure necessita di autorizzazioni per creare app di Azure AD.
**Creare un insieme di credenziali delle chiavi** | Per eseguire la migrazione delle macchine virtuali VMware con il metodo senza agente, Azure Migrate crea un insieme di credenziali delle chiavi per gestire le chiavi di accesso all'account di replica nella sottoscrizione. | Per consentire ad Azure Migrate di creare l'insieme di credenziali delle chiavi, è necessario impostare le autorizzazioni (Proprietario, Collaboratore e Amministratore Accesso utenti) nel gruppo di risorse in cui risiede il progetto Azure Migrate.


### <a name="assign-permissions-to-create-project"></a>Assegnare le autorizzazioni per creare il progetto

1. Nel portale di Azure aprire la sottoscrizione e selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.
3. È necessario avere le autorizzazioni di **Collaboratore** o **Proprietario**.
    - Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione.
    - Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare il ruolo.

### <a name="assign-permissions-to-create-azure-ad-apps"></a>Assegnare le autorizzazioni per creare app Azure AD

Per registrare l'appliance, l'account Azure deve avere le autorizzazioni per creare app Azure AD. Per assegnare le autorizzazioni, usare uno dei metodi seguenti:

- **Metodo 1: concedere le autorizzazioni all'account**. Un amministratore tenant/globale può concedere agli account utente del tenant le autorizzazioni per creare e registrare app Azure AD.
- **Metodo 2: assegnare un ruolo con le autorizzazioni a un account utente**. Un amministratore tenant/globale può assegnare il ruolo Sviluppatore di applicazioni (che include le autorizzazioni necessarie) all'account utente.

> [!NOTE]
> Queste autorizzazioni sono necessarie solo quando si registra una nuova appliance. Dopo la configurazione dell'appliance è possibile rimuoverle.


#### <a name="method-1-grant-permissions-to-the-account"></a>Metodo 1: concedere le autorizzazioni all'account

Per concedere le autorizzazioni all'account, seguire questa procedura:

1. Verificare di avere i privilegi di amministratore tenant o globale. Quindi, in Azure AD, passare a **Azure Active Directory** > **Utenti** > **Impostazioni utente**.
2. Impostare **Registrazioni app** su **Sì**. Si tratta di un'impostazione predefinita che non è sensibile. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)

    ![Autorizzazioni di Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="method-2-assign-application-developer-role"></a>Metodo 2: Assegnare il ruolo Sviluppatore di applicazioni

In alternativa, l'amministratore tenant/globale può assegnare il ruolo Sviluppatore applicazione a un account. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) sull'assegnazione dei ruoli.

### <a name="assign-permissions-to-create-a-key-vault"></a>Assegnare le autorizzazioni per creare un insieme di credenziali delle chiavi

Per consentire ad Azure Migrate di creare un insieme di credenziali delle chiavi, assegnare le autorizzazioni come segue:

1. Nel gruppo di risorse nel portale di Azure selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.

    - Per eseguire la valutazione dei server sono sufficienti le autorizzazioni di **Collaboratore**.
    - Per eseguire una migrazione dei server senza agente sono necessarie le autorizzazioni di **Proprietario** (o di **Collaboratore** e **Amministratore Accesso utenti** ).

3. Se non si hanno le autorizzazioni necessarie, richiederle al proprietario del gruppo di risorse.

## <a name="prepare-for-assessment"></a>Preparare la valutazione

Per preparare la valutazione delle macchine virtuali VMware è necessario:

1. **Verificare le impostazioni di VMware**. Verificare che il server vCenter e le macchine virtuali di cui si vuole eseguire la migrazione soddisfino i requisiti.
2. **Configurare le autorizzazioni per la valutazione**. Azure Migrate usa un account vCenter per accedere al server vCenter per individuare e valutare le macchine virtuali.
3. **Verificare i requisiti dell'appliance**. Verificare i requisiti di distribuzione per l'appliance di Azure Migrate, prima di procedere alla distribuzione nell'esercitazione successiva.

### <a name="verify-vmware-settings"></a>Verificare le impostazioni di VMware

1. [Verificare i requisiti di VMware](migrate-support-matrix-vmware.md#vmware-requirements) per la valutazione.
2. [Assicurarsi](migrate-support-matrix-vmware.md#port-access-requirements) che le porte necessarie siano aperte sul server vCenter.
3. Nel server vCenter controllare che l'account abbia le autorizzazioni per creare una macchina virtuale mediante un file OVA. Queste autorizzazioni sono necessarie quando l'appliance di Azure Migrate viene distribuita come macchina virtuale VMware tramite un file OVA.
4. Prima di eseguire la migrazione delle macchine virtuali ad Azure, è necessario apportarvi alcune modifiche.

    - Per alcuni sistemi operativi, Azure Migrate apporta automaticamente queste modifiche. 
    - È importante apportare queste modifiche prima di avviare la migrazione. Se si esegue la migrazione della VM prima di apportare la modifica, la VM potrebbe non avviarsi in Azure.
    - Esaminare le modifiche che è necessario apportare in [Windows](prepare-for-migration.md#windows-machines) e [Linux](prepare-for-migration.md#linux-machines).


### <a name="set-up-permissions-for-assessment"></a>Configurare le autorizzazioni per la valutazione

Azure Migrate deve avere accesso al server vCenter per consentire all'appliance di Azure Migrate di individuare le macchine virtuali di cui eseguire la valutazione e la migrazione senza agente. Configurare un account seguendo questa procedura:

1. Nel client Web vSphere aprire **Administration** > **Access** > **SSO Users and Groups** (Amministrazione > Accesso > Utenti e gruppi SSO).
2. In **Users** (Utenti) fare clic sull'icona **New User** (Nuovo utente).
3. Digitare i dettagli del nuovo utente.
4. Selezionare le autorizzazioni in base ai valori della tabella.

    **Funzionalità** | **Autorizzazioni dell'account**
    --- | ---
    [Valutare le macchine virtuali](tutorial-assess-vmware.md) | Per la valutazione, l'account deve avere accesso in sola lettura.
    [Individuare app, ruoli e funzionalità delle VM](how-to-discover-applications.md) | Se si vuole usare l'individuazione di app, nell'account di sola lettura usato per la valutazione devono essere abilitati i privilegi per **Macchine virtuali** > **Operazioni guest**.
    [Analizzare le dipendenze dalle macchine virtuali (senza agente)](how-to-create-group-machine-dependencies-agentless.md) | Se si vogliono analizzare le dipendenze, nell'account di sola lettura usato per la valutazione devono essere abilitati i privilegi per **Macchine virtuali** > **Operazioni guest**.
    
> [!NOTE]
> Se si vuole limitare l'individuazione delle macchine virtuali per la valutazione a un ambito specifico, vedere [questo articolo](set-discovery-scope.md#assign-a-role-for-assessment).

### <a name="verify-appliance-settings-for-assessment"></a>Verificare le impostazioni dell'appliance per la valutazione

Nell'[esercitazione successiva](tutorial-assess-vmware.md) si configurerà l'appliance di Azure Migrate e si inizierà la valutazione. Prima di procedere, esaminare i requisiti dell'appliance nel modo seguente: 

1. [Esaminare i requisiti](migrate-appliance.md#appliance---vmware) per la distribuzione dell'appliance di Azure Migrate.
2. Esaminare gli URL di Azure a cui l'appliance deve accedere nei cloud [pubblico](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls).
3. [Prendere nota](migrate-support-matrix-vmware.md#port-access-requirements) delle porte usate dall'appliance.
4. [Esaminare i dati](migrate-appliance.md#collected-data---vmware) raccolti dall'appliance durante l'individuazione e la valutazione.

## <a name="prepare-for-agentless-vmware-migration"></a>Preparare le macchine virtuali VMWare per la migrazione senza agente

Per eseguire la migrazione di macchine virtuali VMware, è possibile usare la [migrazione senza agente o basata su agente](server-migrate-overview.md). Questa sezione riepiloga i requisiti per la migrazione senza agente.

1. [Decidere](server-migrate-overview.md#compare-migration-methods) se si vuole usare la migrazione senza agente.
2. [Verificare](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) i requisiti dell'hypervisor per le macchine virtuali di cui si vuole eseguire la migrazione.
3. [Verificare](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless) i requisiti per le macchine virtuali VMware di cui si vuole eseguire la migrazione con il metodo senza agente.
4. [Verificare](migrate-support-matrix-vmware-migration.md#appliance-requirements-agentless) i requisiti dell'appliance di Azure Migrate per la migrazione senza agente.
5. Prendere nota dell'accesso URL necessario per i cloud [pubblico](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls).
6. Verificare i requisiti di [accesso alle porte](migrate-support-matrix-vmware-migration.md#port-requirements-agentless).
7. Configurare le autorizzazioni per la migrazione senza agente, come descritto nella procedura seguente.


### <a name="assign-permissions-to-an-account"></a>Assegnare autorizzazioni a un account

L'appliance di Azure Migrate si connette al server vCenter per individuare le VM ed eseguirne la migrazione con il metodo senza agente. È possibile assegnare le autorizzazioni necessarie per l'appliance all'account utente oppure creare un ruolo con le autorizzazioni e assegnare tale ruolo a un account utente.

1. Nel client Web vSphere aprire **Administration** > **Access** > **SSO Users and Groups** (Amministrazione > Accesso > Utenti e gruppi SSO).
2. In **Users** (Utenti) fare clic sull'icona **New User** (Nuovo utente).
3. Digitare i dettagli del nuovo utente.
4. Assegnare [queste autorizzazioni](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)

#### <a name="create-a-role-and-assign-to-an-account"></a>Creare un ruolo e assegnarlo a un account

In alternativa, è possibile creare un account, quindi creare un ruolo e assegnarlo all'account, seguendo questa procedura:

1. Accedere al client Web vSphere come amministratore del server vCenter.
2. Selezionare l'istanza del server vCenter > **Create role** (Crea ruolo).
3. Specificare un nome per il ruolo, ad esempio <em>Azure_Migrate</em>, e assegnare le [autorizzazioni necessarie](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) al ruolo.

    ![Privilegi dell'account del server vCenter](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

4. A questo punto, creare un account e assegnargli il ruolo.

> [!NOTE]
> Se si vuole limitare l'individuazione delle macchine virtuali per la migrazione senza agente a un ambito specifico, vedere [questo articolo](set-discovery-scope.md#assign-a-role-for-agentless-migration).



## <a name="prepare-for-agent-based-vmware-migration"></a>Preparare le macchine virtuali VMWare per la migrazione basata su agente

Per eseguire la migrazione di macchine virtuali VMware, è possibile usare la [migrazione senza agente o basata su agente](server-migrate-overview.md). Questa sezione riepiloga i requisiti per la migrazione basata su agente.

1. [Decidere](server-migrate-overview.md#compare-migration-methods) se si vuole usare la migrazione basata su agente.
1. [Verificare](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) i requisiti dell'hypervisor per le macchine virtuali di cui si vuole eseguire la migrazione.
2. [Esaminare](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) i requisiti delle macchine virtuali VMware, inclusa l'installazione del servizio di mobilità in ogni macchina virtuale di cui si vuole eseguire la migrazione.
3. La migrazione basata su agente usa un'appliance di replica:
    - [Esaminare](migrate-replication-appliance.md#appliance-requirements) i requisiti di distribuzione per l'appliance di replica.
    - [Esaminare le opzioni](migrate-replication-appliance.md#mysql-installation) per l'installazione di MySQL nell'appliance.
    - Prendere nota dell'accesso URL necessario per i cloud [pubblico](migrate-replication-appliance.md#url-access) e per [enti pubblici](migrate-replication-appliance.md#azure-government-url-access).
    - Esaminare i requisiti di [accesso alle porte](migrate-replication-appliance.md#port-access) per l'appliance di replica.
4. Prima di eseguire la migrazione delle VM ad Azure, è necessario apportarvi alcune modifiche. Esaminare le modifiche che è necessario apportare in [Windows](prepare-for-migration.md#windows-machines) e [Linux](prepare-for-migration.md#linux-machines).



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

> [!div class="checklist"]
> * Sono state configurate le autorizzazioni di Azure.
> * Sono state preparate la valutazione e la migrazione di VM VMware.


Continuare con la seconda esercitazione per configurare un progetto di Azure Migrate e valutare le macchine virtuali VMware per la migrazione ad Azure.

> [!div class="nextstepaction"]
> [Valutare le macchine virtuali VMware](./tutorial-assess-vmware.md)
