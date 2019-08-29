---
title: Preparare le macchine virtuali VMware per la valutazione e la migrazione ad Azure con Azure Migrate | Microsoft Docs
description: Questo articolo descrive come preparare macchine virtuali VMware locali per la valutazione e la migrazione ad Azure usando Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5bd4926494e13c1dd0cfb6283292ade967471238
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013426"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Preparare le macchine virtuali VMware per la valutazione e la migrazione ad Azure

Questo articolo descrive come preparare macchine virtuali VMware locali per la valutazione e la migrazione ad Azure usando [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti. 


Questa esercitazione è la prima di una serie che illustra come valutare ed eseguire la migrazione di macchine virtuali VMware. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Preparare Azure. Configurare le autorizzazioni per consentire all'account e alle risorse di Azure di interagire con Azure Migrate.
> * Preparare i server e le macchine virtuali VMware locali per la valutazione delle macchine virtuali.
> * Preparare i server e le macchine virtuali VMware locali per la migrazione delle macchine virtuali.

> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario, per consentire di configurare rapidamente un modello di verifica. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere le procedure relative alla valutazione e alla migrazione di VMware.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prepare-azure"></a>Preparare Azure

Sono necessarie le autorizzazioni di Azure seguenti:

- L'account Azure deve avere le autorizzazioni per creare un progetto di Azure Migrate per la valutazione e la migrazione. 
- Per la valutazione e la migrazione senza agente delle macchine virtuali VMware, Azure Migrate esegue un'appliance leggera che individua le VM e invia i relativi metadati e i dati sulle prestazioni ad Azure Migrate. In Azure occorre avere le autorizzazioni per registrare l'appliance di Azure Migrate.
- Per eseguire la migrazione delle macchine virtuali VMware con Migrazione server di Azure Migrate, Azure Migrate crea un insieme di credenziali delle chiavi nel gruppo di risorse per gestire le chiavi di accesso all'account di archiviazione di replica nella sottoscrizione. Per creare l'insieme di credenziali delle chiavi sono necessarie autorizzazioni di assegnazione di ruolo per il gruppo di risorse in cui si trova il progetto Azure Migrate. 


### <a name="assign-permissions-to-create-project"></a>Assegnare le autorizzazioni per creare il progetto

1. Nel portale di Azure aprire la sottoscrizione e selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.
3. È necessario avere le autorizzazioni di **Collaboratore** o **Proprietario**.
    - Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione.
    - Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare il ruolo.

### <a name="assign-permissions-to-register-the-appliance"></a>Assegnare le autorizzazioni per registrare l'appliance

Se si intende distribuire l'appliance di Azure Migrate per valutare o eseguire una migrazione senza agente delle macchine virtuali, è necessario registrarla.

- Durante la registrazione dell'appliance, Azure Migrate crea due app di Azure Active Directory (Azure AD) che identificano l'appliance in modo univoco.
    - La prima app comunica con gli endpoint del servizio Azure Migrate.
    - La seconda app accede a un'istanza di Azure Key Vault creata durante la registrazione per archiviare le informazioni delle app Azure AD e le impostazioni di configurazione dell'appliance.
- È possibile assegnare le autorizzazioni per consentire ad Azure Migrate di creare queste app Azure AD usando uno dei metodi seguenti:
    - Un amministratore tenant/globale può concedere agli utenti del tenant le autorizzazioni per creare e registrare app Azure AD.
    - Un amministratore tenant/globale può assegnare il ruolo Sviluppatore di applicazioni (che include le autorizzazioni) all'account.

Vale la pena notare che:

- Le app non hanno altre autorizzazioni di accesso per la sottoscrizione oltre a quelle descritte sopra.
- Queste autorizzazioni sono necessarie solo quando si registra una nuova appliance. Dopo la configurazione dell'appliance è possibile rimuovere le autorizzazioni. 


#### <a name="grant-account-permissions"></a>Concedere le autorizzazioni all'account

L'amministratore tenant/globale può concedere le autorizzazioni nel modo seguente:

1. In Azure AD, l'amministratore globale/tenant deve passare a **Azure Active Directory** > **Utenti** > **Impostazioni utente**.
2. L'amministratore deve impostare **Registrazioni app**su **Sì**.

    ![Autorizzazioni di Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Si tratta di un'impostazione predefinita che non è sensibile. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)



#### <a name="assign-application-developer-role"></a>Assegnare il ruolo Sviluppatore di applicazioni 

L'amministratore tenant/globale può assegnare il ruolo Sviluppatore di applicazioni a un account. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)

## <a name="assign-role-assignment-permissions"></a>Assegnare le autorizzazioni di assegnazione di ruolo

Per assegnare le autorizzazioni di assegnazione di ruolo per il gruppo di risorse in cui si trova il progetto di Azure Migrate, eseguire questa procedura:

1. Nel gruppo di risorse nel portale di Azure selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.

    - Per eseguire la valutazione dei server sono sufficienti le autorizzazioni di **Collaboratore**.
    - Per eseguire una migrazione dei server senza agente sono necessarie le autorizzazioni di **Proprietario** (o di **Collaboratore** e **Amministratore Accesso utenti** ).

3. Se non si hanno le autorizzazioni necessarie, richiederle al proprietario del gruppo di risorse. 



## <a name="prepare-for-vmware-vm-assessment"></a>Preparare le macchine virtuali VMware per la valutazione

Per preparare le macchine virtuali VMware per la valutazione, è necessario verificare le impostazioni dell'host VMware e delle macchine virtuali e le impostazioni per la distribuzione dell'appliance.

### <a name="verify-vmware-settings"></a>Verificare le impostazioni di VMware

1. [Verificare](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) i requisiti dei server VMware per la valutazione delle macchine virtuali.
2. [Assicurarsi](migrate-support-matrix-vmware.md#assessment-port-requirements) che le porte necessarie siano aperte sui server vCenter.


### <a name="set-up-an-account-for-assessment"></a>Configurare un account per la valutazione

Azure Migrate deve avere accesso al server vCenter per individuare le macchine virtuali di cui eseguire la valutazione e la migrazione senza agente. Per la sola valutazione, è necessario un account di sola lettura per il server vCenter.

Se si usa un proxy/firewall basato su URL, consentire l'accesso agli [URL di Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements) necessari.

Assicurarsi che il proxy risolva gli eventuali record CNAME ricevuti durante la ricerca degli URL.


### <a name="verify-appliance-settings-for-assessment"></a>Verificare le impostazioni dell'appliance per la valutazione

Prima di configurare l'appliance di Azure Migrate e iniziare la valutazione nella prossima esercitazione, preparare la distribuzione dell'appliance.

1. [Verificare](migrate-support-matrix-vmware.md#assessment-appliance-requirements) i requisiti per configurare l'appliance di Azure Migrate in VMware.
2. [Rivedere](migrate-support-matrix-vmware.md#assessment-url-access-requirements) gli URL di Azure a cui l'appliance dovrà accedere.
3. Esaminare i dati che l'appliance raccoglierà durante l'individuazione e la valutazione.
4. [Prendere nota](migrate-support-matrix-vmware.md#assessment-port-requirements) dei requisiti di accesso alle porte per l'appliance.
5. L'appliance di Azure Migrate viene distribuita come macchina virtuale VMware tramite un file OVA. Nel server vCenter verificare che l'account abbia le autorizzazioni per creare una macchina virtuale mediante un file OVA.


## <a name="prepare-for-agentless-vmware-migration"></a>Preparare le macchine virtuali VMWare per la migrazione senza agente

Rivedere i requisiti per la migrazione senza agente delle macchine virtuali VMWare.

1. [Rivedere](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) i requisiti del server VMware per la migrazione senza agente.
2. Configurare un account per l'accesso al server vCenter con le [autorizzazioni necessarie](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions) per la migrazione senza agente.
3. [Prendere nota](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) dei requisiti per le macchine virtuali VMware di cui si vuole eseguire la migrazione ad Azure con il metodo senza agente.
4. [Esaminare](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) i requisiti dell'appliance per la migrazione senza agente.
5. Prendere nota dei requisiti di [accesso agli URL](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) e [accesso alle porte](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) dell'appliance per la migrazione senza agente.


## <a name="prepare-for-agent-based-vmware-migration"></a>Preparare le macchine virtuali VMWare per la migrazione basata su agente

Rivedere i requisiti per la migrazione basata su agente delle macchine virtuali VMWare.

1. [Rivedere](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) i requisiti del server VMware per la migrazione basata su agente. 
2. Configurare un account per l'accesso al server vCenter con le [autorizzazioni necessarie](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions) per la migrazione basata su agente.
3. [Prendere nota](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) dei requisiti per le macchine virtuali VMware di cui si vuole eseguire la migrazione in Azure usando il metodo basato su agente, inclusa l'installazione del servizio Mobility in ogni macchina virtuale di cui eseguire la migrazione.
4. Prendere nota dell'[accesso agli l'URL](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Rivedere i requisiti di [accesso alle porte](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) per il servizio Mobility in esecuzione su ogni macchina virtuale e per il server di configurazione di Azure Migrate.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:
 
> [!div class="checklist"] 
> * Sono state configurate le autorizzazioni di Azure.
> * Sono state preparate la valutazione e la migrazione di VM VMware.


Continuare con la seconda esercitazione per configurare un progetto di Azure Migrate e valutare le macchine virtuali VMware per la migrazione ad Azure.

> [!div class="nextstepaction"] 
> [Valutare le macchine virtuali VMware](./tutorial-assess-vmware.md) 

