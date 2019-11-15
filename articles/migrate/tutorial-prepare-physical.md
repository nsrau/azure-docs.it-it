---
title: Preparare i server fisici per la valutazione con Valutazione server di Azure Migrate
description: Questo articolo descrive come effettuare la preparazione per la valutazione e la migrazione di server fisici ad Azure tramite Valutazione server di Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/07/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: df425f723aa6a5e261ed6dcd15abfe87b367ad68
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747966"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Effettuare la preparazione per la valutazione e la migrazione di server fisici ad Azure

Questo articolo descrive come effettuare la preparazione per la valutazione di server fisici locali con [Azure Migrate](migrate-services-overview.md).


> [!NOTE]
> Se alcune di queste funzionalità non sono ancora visibili nel portale di Azure Migrate, aspettare. Compariranno all'incirca la prossima settimana.

[Azure Migrate](migrate-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti. 

Questa esercitazione è la prima di una serie che illustra come valutare i server fisici con Azure Migrate. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Preparare Azure. Configurare le autorizzazioni per consentire all'account e alle risorse di Azure di interagire con Azure Migrate.
> * Preparare i server fisici locali per la valutazione dei server.


> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario, per consentire di configurare rapidamente un modello di verifica. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere le procedure relative alla valutazione dei server fisici.


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prepare-azure"></a>Preparare Azure

### <a name="azure-permissions"></a>Autorizzazioni di Azure

È necessario configurare le autorizzazioni per la distribuzione di Azure Migrate.

- Autorizzazioni per l'account Azure per la creazione di un progetto Azure Migrate. 
- Autorizzazioni per l'account per la registrazione dell'appliance di Azure Migrate. L'appliance viene usato per l'individuazione e la migrazione di Hyper-V. Durante la registrazione dell'appliance, Azure Migrate crea due app Azure Active Directory (Azure AD) che la identificano in modo univoco:
    - La prima app comunica con gli endpoint del servizio Azure Migrate.
    - La seconda app accede a un'istanza di Azure Key Vault creata durante la registrazione per archiviare le informazioni delle app Azure AD e le impostazioni di configurazione dell'appliance.



### <a name="assign-permissions-to-create-project"></a>Assegnare le autorizzazioni per creare il progetto

Verificare di avere le autorizzazioni per creare un progetto di Azure Migrate.

1. Nel portale di Azure aprire la sottoscrizione e selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.
3. È necessario avere le autorizzazioni di **Collaboratore** o **Proprietario**.
    - Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione.
    - Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare il ruolo.


### <a name="assign-permissions-to-register-the-appliance"></a>Assegnare le autorizzazioni per registrare l'appliance

È possibile assegnare le autorizzazioni per consentire ad Azure Migrate di creare le app Azure AD create durante la registrazione dell'appliance usando uno dei metodi seguenti:

- Un amministratore tenant/globale può concedere agli utenti del tenant le autorizzazioni per creare e registrare app Azure AD.
- Un amministratore tenant/globale può assegnare il ruolo Sviluppatore di applicazioni (che include le autorizzazioni) all'account.

Vale la pena notare che:

- Le app non hanno altre autorizzazioni di accesso per la sottoscrizione oltre a quelle descritte sopra.
- Queste autorizzazioni sono necessarie solo quando si registra una nuova appliance. Dopo la configurazione dell'appliance è possibile rimuovere le autorizzazioni. 


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

1. Verificare i [requisiti dei server fisici](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements) per la valutazione dei server.
2. Assicurarsi che nei server fisici siano aperte le [porte necessarie](migrate-support-matrix-hyper-v.md#assessment-port-requirements).


### <a name="verify-appliance-settings"></a>Verificare le impostazioni dell'appliance

Prima di configurare l'appliance di Azure Migrate e iniziare la valutazione nella prossima esercitazione, preparare la distribuzione dell'appliance.

1. [Verificare](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) i requisiti dell'appliance.
2. [Rivedere](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) gli URL di Azure a cui l'appliance dovrà accedere.
3. Esaminare i dati che l'appliance raccoglierà durante l'individuazione e la valutazione.
4. [Prendere nota](migrate-support-matrix-hyper-v.md#assessment-port-requirements) dei requisiti di accesso alle porte per l'appliance.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Configurare un account per l'individuazione di server fisici

Azure Migrate necessita delle autorizzazioni per individuare i server locali.

- **Windows:** Configurare un account utente locale in tutti i server Windows che si desidera includere nell'individuazione. L'account utente deve essere aggiunto ai gruppi seguenti:       - Utenti desktop remoto       - Performance Monitor Users       - Performance Log Users
- **Linux:** È necessario un account radice nei server Linux che si desidera individuare.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:
 
> [!div class="checklist"] 
> * Sono state configurate le autorizzazioni dell'account Azure.
> * Sono stati preparati i server fisici per la valutazione.

Continuare con l'esercitazione successiva per creare un progetto di Azure Migrate e valutare i server fisici per la migrazione ad Azure

> [!div class="nextstepaction"] 
> [Valutare i server fisici](./tutorial-assess-physical.md) 