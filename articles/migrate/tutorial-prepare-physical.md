---
title: Preparare i server fisici per la valutazione/migrazione con Azure Migrate
description: Informazioni su come preparare la valutazione e la migrazione di server fisici con Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 42eb603be0152b9e8cfb36d02e8f0602c40afe54
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77031204"
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


## <a name="prepare-azure"></a>Preparare Azure

### <a name="azure-permissions"></a>Autorizzazioni di Azure

È necessario configurare le autorizzazioni per la distribuzione di Azure Migrate.

**Attività** | **Autorizzazioni**
--- | ---
**Creare un progetto di Azure Migrate** | L'account di Azure necessita di autorizzazioni per creare un progetto.
**Registrare l'appliance Azure Migrate** | Azure Migrate usa un'appliance di Azure Migrate leggera per individuare e valutare i server fisici con lo strumento Valutazione server di Azure Migrate. L'appliance individua i server e ne invia i metadati e i dati sulle prestazioni ad Azure Migrate.<br/><br/>Durante la registrazione dell'appliance, i provider di risorse Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault vengono registrati con la sottoscrizione scelta nell'appliance. La registrazione di un provider di risorse configura la sottoscrizione per l'utilizzo del provider di risorse. Per registrare i provider di risorse, è necessario il ruolo di proprietario o collaboratore della sottoscrizione.<br/><br/> Nell'ambito dell'onboarding, Azure Migrate crea un'app Azure Active Directory (Azure AD):<br/> L'app Azure AD viene usata per la comunicazione (autenticazione e autorizzazione) tra gli agenti in esecuzione nell'appliance con i rispettivi servizi in esecuzione in Azure. Questa app non dispone dei privilegi necessari per effettuare chiamate ARM né dell'accesso basato sul controllo degli accessi in base al ruolo su alcuna risorsa.



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


## <a name="prepare-for-physical-server-assessment"></a>Effettuare la preparazione per la valutazione dei server fisici

Per la preparazione per la valutazione dei server fisici, è necessario verificare le impostazioni dei server fisici e le impostazioni per la distribuzione dell'appliance:

### <a name="verify-physical-server-settings"></a>Verificare le impostazioni dei server fisici

1. Verificare i [requisiti dei server fisici](migrate-support-matrix-physical.md#physical-server-requirements) per la valutazione dei server.
2. Assicurarsi che nei server fisici siano aperte le [porte necessarie](migrate-support-matrix-physical.md#port-access).


### <a name="verify-appliance-settings"></a>Verificare le impostazioni dell'appliance

Prima di configurare l'appliance di Azure Migrate e iniziare la valutazione nella prossima esercitazione, preparare la distribuzione dell'appliance.

1. [Verificare](migrate-appliance.md#appliance---physical) i requisiti dell'appliance per i server fisici.
2. [Rivedere](migrate-appliance.md#url-access) gli URL di Azure a cui l'appliance dovrà accedere.
3. [Esaminare](migrate-appliance.md#collected-data---vmware) i dati che l'appliance raccoglierà durante l'individuazione e la valutazione.
4. [Prendere nota](migrate-support-matrix-physical.md#port-access) dei requisiti di accesso alle porte per la valutazione dei server fisici.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Configurare un account per l'individuazione di server fisici

Azure Migrate necessita delle autorizzazioni per individuare i server locali.

- **Windows:** Configurare un account utente locale in tutti i server Windows che si vuole includere nell'individuazione. L'account utente deve essere aggiunto ai gruppi seguenti:       - Utenti gestione remota       - Performance Monitor Users       - Performance Log Users
- **Linux:** È necessario un account radice nei server Linux che si desidera individuare.

## <a name="prepare-for-physical-server-migration"></a>Preparare i server fisici per la migrazione

Esaminare i requisiti per la migrazione dei server fisici.

- [Esaminare](migrate-support-matrix-physical-migration.md#physical-server-requirements) requisiti dei server fisici per la migrazione.
- Azure Migrate: Migrazione del server usa un server di replica per la migrazione di server fisici:
    - [Esaminare](migrate-replication-appliance.md#appliance-requirements) i requisiti di distribuzione per l'appliance di replica e le [opzioni](migrate-replication-appliance.md#mysql-installation) per l'installazione di MySQL nell'appliance.
    - Esaminare i requisiti di accesso agli [URL](migrate-replication-appliance.md#url-access) e alle [porte] (migrate-replication-appliance.md#port-access) per l'appliance di replica.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

> [!div class="checklist"]
> * Sono state configurate le autorizzazioni dell'account Azure.
> * Sono stati preparati i server fisici per la valutazione.

Continuare con l'esercitazione successiva per creare un progetto di Azure Migrate e valutare i server fisici per la migrazione ad Azure

> [!div class="nextstepaction"]
> [Valutare i server fisici](./tutorial-assess-physical.md)
