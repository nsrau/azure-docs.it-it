---
title: Preparare i server fisici per la valutazione/migrazione con Azure Migrate
description: Informazioni su come preparare la valutazione e la migrazione di server fisici con Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: ed648458416bacb091212bb569a27ecdf13fe8ee
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84771275"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Effettuare la preparazione per la valutazione e la migrazione di server fisici ad Azure

Questo articolo descrive come effettuare la preparazione per la valutazione di server fisici locali con [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti. 

Questa esercitazione è la prima di una serie che illustra come valutare i server fisici con Azure Migrate. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Preparare Azure. Configurare le autorizzazioni per consentire all'account e alle risorse di Azure di interagire con Azure Migrate.
> * Preparare i server fisici locali per la valutazione dei server.


> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario, per consentire di configurare rapidamente un modello di verifica. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere le procedure relative alla valutazione dei server fisici.


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prepare-azure-for-server-assessment"></a>Preparare Azure per la valutazione del server

Configurare Azure per l'uso con Azure Migrate. 

**Attività** | **Dettagli** 
--- | --- 
**Creare un progetto di Azure Migrate** | Per creare un progetto, è necessario che l'account Azure abbia autorizzazioni di Collaboratore o Proprietario. 
**Registrare i provider di risorse (solo valutazione)** | Azure Migrate usa un'appliance di Azure Migrate leggera per individuare e valutare le macchine virtuali con lo strumento Valutazione server di Azure Migrate.<br/><br/> Durante la registrazione dell'appliance, i provider di risorse vengono registrati con la sottoscrizione scelta nell'appliance. [Altre informazioni](migrate-appliance-architecture.md#appliance-registration)<br/><br/> Per registrare i provider di risorse, è necessario il ruolo di proprietario o collaboratore della sottoscrizione.
**Creare l'app Azure AD (solo valutazione)** | Quando si registra l'appliance, Azure Migrate crea un'app di Azure Active Directory (Azure AD) usata per la comunicazione tra gli agenti in esecuzione nell'appliance con i rispettivi servizi in esecuzione in Azure. [Altre informazioni](migrate-appliance-architecture.md#appliance-registration)<br/><br/> Sono necessarie le autorizzazioni per creare app di Azure AD (disponibili nel ruolo Sviluppatore applicazioni).


### <a name="assign-permissions-to-create-project"></a>Assegnare le autorizzazioni per creare il progetto 

Verificare di avere le autorizzazioni per creare un progetto di Azure Migrate.

1. Nel portale di Azure aprire la sottoscrizione e selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.
3. È necessario avere le autorizzazioni di **Collaboratore** o **Proprietario**.
    - Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione.
    - Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare il ruolo.


### <a name="assign-permissions-to-register-the-appliance"></a>Assegnare le autorizzazioni per registrare l'appliance 

È possibile assegnare le autorizzazioni per consentire ad Azure Migrate di creare l'app Azure AD durante la registrazione dell'appliance usando uno dei metodi seguenti:

- Un amministratore tenant/globale può concedere agli utenti del tenant le autorizzazioni per creare e registrare app Azure AD.
- Un amministratore tenant/globale può assegnare il ruolo Sviluppatore di applicazioni (che include le autorizzazioni) all'account.

> [!NOTE]
> - L'app non ha altre autorizzazioni di accesso per la sottoscrizione oltre a quelle descritte sopra.
> - Queste autorizzazioni sono necessarie solo quando si registra una nuova appliance. Dopo la configurazione dell'appliance è possibile rimuoverle.


#### <a name="grant-account-permissions"></a>Concedere le autorizzazioni all'account

L'amministratore tenant/globale può concedere le autorizzazioni nel modo seguente:

1. In Azure AD, l'amministratore globale/tenant deve passare a **Azure Active Directory** > **Utenti** > **Impostazioni utente**.
2. L'amministratore deve impostare **Registrazioni app**su **Sì**.

    ![Autorizzazioni di Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Si tratta di un'impostazione predefinita che non è sensibile. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)

#### <a name="assign-application-developer-role"></a>Assegnare il ruolo Sviluppatore di applicazioni

L'amministratore tenant/globale può assegnare il ruolo Sviluppatore di applicazioni a un account. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)


## <a name="prepare-azure-for-physical-server-migration"></a>Preparare Azure per la migrazione dei server fisici

Preparare Azure per la migrazione dei server fisici tramite Migrazione server.

**Attività** | **Dettagli**
--- | ---
**Creare un progetto di Azure Migrate** | Per creare un progetto, è necessario che l'account Azure abbia autorizzazioni di Collaboratore o Proprietario.
**Verificare le autorizzazioni per l'account Azure** | L'account Azure necessita delle autorizzazioni per creare una macchina virtuale ed eseguire la scrittura su un disco gestito di Azure.
**Creare una rete di Azure** | Configurare una rete in Azure.


### <a name="assign-permissions-to-create-project"></a>Assegnare le autorizzazioni per creare il progetto

1. Nel portale di Azure aprire la sottoscrizione e selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.
3. È necessario avere le autorizzazioni di **Collaboratore** o **Proprietario**.
    - Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione.
    - Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare il ruolo.


### <a name="assign-azure-account-permissions"></a>Assegnare le autorizzazioni all'account Azure

Assegnare il ruolo Collaboratore Macchina virtuale all'account Azure. Questo ruolo fornisce le autorizzazioni per:
  - Creare una macchina virtuale nel gruppo di risorse selezionato.
  - Creare una macchina virtuale nella rete virtuale selezionata.
  - Scrivere in un disco gestito di Azure. 

### <a name="create-an-azure-network"></a>Creare una rete di Azure

[Configurare](../virtual-network/manage-virtual-network.md#create-a-virtual-network) una rete virtuale di Azure. Quando si esegue la replica in Azure, le macchine virtuali di Azure vengono create e aggiunte alla rete virtuale di Azure specificata durante la configurazione della migrazione.


## <a name="prepare-for-physical-server-assessment"></a>Effettuare la preparazione per la valutazione dei server fisici

Per la preparazione per la valutazione dei server fisici, è necessario verificare le impostazioni dei server fisici e le impostazioni per la distribuzione dell'appliance:

### <a name="verify-physical-server-settings"></a>Verificare le impostazioni dei server fisici

1. Verificare i [requisiti dei server fisici](migrate-support-matrix-physical.md#physical-server-requirements) per la valutazione dei server.
2. Assicurarsi che nei server fisici siano aperte le [porte necessarie](migrate-support-matrix-physical.md#port-access).


### <a name="verify-appliance-settings"></a>Verificare le impostazioni dell'appliance

Prima di configurare l'appliance di Azure Migrate e iniziare la valutazione nella prossima esercitazione, preparare la distribuzione dell'appliance.

1. [Verificare](migrate-appliance.md#appliance---physical) i requisiti dell'appliance per i server fisici.
2. Esaminare gli URL di Azure a cui l'appliance deve accedere nei cloud [pubblico](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls).
3. [Esaminare](migrate-appliance.md#collected-data---vmware) i dati che l'appliance raccoglierà durante l'individuazione e la valutazione.
4. [Prendere nota](migrate-support-matrix-physical.md#port-access) dei requisiti di accesso alle porte per la valutazione dei server fisici.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Configurare un account per l'individuazione di server fisici

Azure Migrate necessita delle autorizzazioni per individuare i server locali.

- **Windows:** è necessario essere un amministratore di dominio o un amministratore locale in tutti i server Windows da individuare. L'account utente deve essere aggiunto a questi gruppi: Utenti Gestione remota, Performance Monitor Users e Performance Log Users.
- **Linux:** È necessario un account radice nei server Linux che si desidera individuare.

## <a name="prepare-for-physical-server-migration"></a>Preparare i server fisici per la migrazione

Esaminare i requisiti per la migrazione dei server fisici.

> [!NOTE]
> Per la migrazione dei computer fisici, lo strumento Migrazione server di Azure Migrate usa la stessa architettura di replica della funzionalità di ripristino di emergenza basata su agente del servizio Azure Site Recovery e alcuni componenti condividono la stessa codebase. Alcuni contenuti potrebbero includere collegamenti alla documentazione di Site Recovery.

1. [Esaminare](migrate-support-matrix-physical-migration.md#physical-server-requirements) requisiti dei server fisici per la migrazione.
2. Migrazione server di Azure Migrate usa un server di replica per la migrazione dei server fisici:
    - [Esaminare](migrate-replication-appliance.md#appliance-requirements) i requisiti di distribuzione per l'appliance di replica e le [opzioni](migrate-replication-appliance.md#mysql-installation) per l'installazione di MySQL nell'appliance.
    - Esaminare gli [URL di Azure](migrate-appliance.md#url-access) necessari all'appliance di replica per accedere ai cloud pubblico e per enti pubblici.
    - Esaminare i requisiti di accesso alle [porte] (migrate-replication-appliance.md#port-access) per l'appliance di replica.
3. Prima di eseguire la migrazione delle macchine virtuali ad Azure, è necessario apportarvi alcune modifiche.
    - È importante apportare queste modifiche prima di avviare la migrazione. Se si esegue la migrazione della VM prima di apportare la modifica, la VM potrebbe non avviarsi in Azure.
    - Esaminare le modifiche che è necessario apportare in [Windows](prepare-for-migration.md#windows-machines) e [Linux](prepare-for-migration.md#linux-machines).



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

> [!div class="checklist"]
> * Sono state configurate le autorizzazioni dell'account Azure.
> * Sono stati preparati i server fisici per la valutazione.

Continuare con l'esercitazione successiva per creare un progetto di Azure Migrate e valutare i server fisici per la migrazione ad Azure

> [!div class="nextstepaction"]
> [Valutare i server fisici](./tutorial-assess-physical.md)
