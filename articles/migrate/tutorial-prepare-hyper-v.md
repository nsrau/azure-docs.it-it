---
title: Preparare le macchine virtuali Hyper-V per la valutazione e la migrazione ad Azure con Azure Migrate | Microsoft Docs
description: Questo articolo descrive come preparare le macchine virtuali Hyper-V per la valutazione e la migrazione ad Azure tramite Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9e0d29770aa36f8e79bf08b7c5435ea2dbc4ae38
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840377"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Preparare le VM Hyper-V per la valutazione e la migrazione ad Azure

Questo articolo descrive come preparare le macchine virtuali Hyper-V locali per la valutazione e la migrazione ad Azure tramite [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti. 

Questa esercitazione è la prima di una serie che illustra come valutare ed eseguire la migrazione di VM Hyper-V ad Azure. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Preparare Azure. Configurare le autorizzazioni per consentire all'account e alle risorse di Azure di interagire con Azure Migrate.
> * Preparare gli host Hyper-V e le VM locali per la valutazione server.
> * Preparare gli host Hyper-V e le VM locali per la migrazione server.


> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario, per consentire di configurare rapidamente un modello di verifica. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere le procedure relative alla valutazione e alla migrazione di Hyper-V.


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prepare-azure"></a>Preparare Azure

### <a name="azure-permissions"></a>Autorizzazioni di Azure

Per distribuire Azure Migrate, sono necessarie un paio di autorizzazioni:

- L'account Azure deve avere le autorizzazioni per creare un progetto di Azure Migrate per la valutazione e la migrazione. 
- L'account Azure deve avere le autorizzazioni per registrare l'appliance di Azure Migrate.
    - Per la valutazione, Azure Migrate esegue un'appliance leggera che individua le VM Hyper-V e invia i relativi metadati e i dati sulle prestazioni ad Azure Migrate.
    - Durante la registrazione dell'appliance, Azure Migrate crea due app Azure Active Directory (Azure AD) che la identificano in modo univoco:
        - La prima app comunica con gli endpoint del servizio Azure Migrate.
        - La seconda app accede a un'istanza di Azure Key Vault creata durante la registrazione per archiviare le informazioni delle app Azure AD e le impostazioni di configurazione dell'appliance.
    - È possibile assegnare le autorizzazioni per consentire ad Azure Migrate di creare queste app Azure AD usando uno dei metodi seguenti:
        - Un amministratore tenant/globale può concedere agli utenti del tenant le autorizzazioni per creare e registrare app Azure AD.
        - Un amministratore tenant/globale può assegnare il ruolo Sviluppatore di applicazioni (che include le autorizzazioni) all'account.
    - Vale la pena notare che:
        - Le app non hanno altre autorizzazioni di accesso per la sottoscrizione oltre a quelle descritte sopra.
        - Queste autorizzazioni sono necessarie solo quando si registra una nuova appliance. Dopo la configurazione dell'appliance è possibile rimuoverle. 


### <a name="assign-permissions-to-create-project"></a>Assegnare le autorizzazioni per creare il progetto

Verificare di avere le autorizzazioni per creare un progetto di Azure Migrate.

1. Nel portale di Azure aprire la sottoscrizione e selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso**  trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.
3. È necessario avere le autorizzazioni di **Collaboratore** o **Proprietario**.
    - Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione.
    - Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare il ruolo.


### <a name="assign-permissions-to-register-the-appliance"></a>Assegnare le autorizzazioni per registrare l'appliance

Se si intende distribuire l'appliance di Azure Migrate per valutare le VM, è necessario registrarla.

- Durante la registrazione dell'appliance, Azure Migrate crea due app Azure Active Directory (Azure AD) che la identificano in modo univoco
    - La prima app comunica con gli endpoint del servizio Azure Migrate.
    - La seconda app accede a un'istanza di Azure Key Vault creata durante la registrazione per archiviare le informazioni delle app Azure AD e le impostazioni di configurazione dell'appliance.
- È possibile assegnare le autorizzazioni per consentire ad Azure Migrate di creare queste app Azure AD usando uno dei metodi seguenti:
    - Un amministratore tenant/globale può concedere agli utenti del tenant le autorizzazioni per creare e registrare app Azure AD.
    - Un amministratore tenant/globale può assegnare il ruolo Sviluppatore di applicazioni (che include le autorizzazioni) all'account.

Vale la pena notare che:

- Le app non hanno altre autorizzazioni di accesso per la sottoscrizione oltre a quelle descritte sopra.
- Queste autorizzazioni sono necessarie solo quando si registra una nuova appliance. Dopo la configurazione dell'appliance è possibile rimuoverle. 


#### <a name="grant-account-permissions"></a>Concedere le autorizzazioni all'account

L'amministratore tenant/globale può concedere le autorizzazioni nel modo seguente:

1. In Azure AD, l'amministratore globale/tenant deve passare a **Azure Active Directory** > **Utenti** > **Impostazioni utente**.
2. L'amministratore deve impostare **Registrazioni app**su **Sì**.

    ![Autorizzazioni di Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Si tratta di un'impostazione predefinita che non è sensibile. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)



#### <a name="assign-application-developer-role"></a>Assegnare il ruolo Sviluppatore di applicazioni 

L'amministratore tenant/globale può assegnare il ruolo Sviluppatore di applicazioni a un account. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)


## <a name="prepare-for-hyper-v-assessment"></a>Preparare la valutazione di Hyper-V

Per preparare la valutazione di Hyper-V, verificare le impostazioni degli host e delle VM Hyper-V, nonché le impostazioni per la distribuzione dell'appliance.

### <a name="verify-hyper-v-host-settings"></a>Verificare le impostazioni degli host Hyper-V

1. Verificare i [requisiti degli host Hyper-V](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements) per la valutazione dei server.
2. Assicurarsi che le [porte necessarie](migrate-support-matrix-hyper-v.md#assessment-port-requirements) siano aperte negli host Hyper-V.

### <a name="enable-powershell-remoting-on-hosts"></a>Abilitare la comunicazione remota di PowerShell negli host

Configurare la comunicazione remota di PowerShell in ogni host, come indicato di seguito:

1. In ogni host aprire una console di PowerShell come amministratore.
2. Eseguire questo comando:

    ```
    Enable-PSRemoting -force
    ```

### <a name="enable-credssp-on-hosts"></a>Abilitare CredSSP negli host

Se i dischi delle VM virtuali si trovano in condivisioni SMB, completare questo passaggio in ogni host Hyper-V pertinente. Questo passaggio consente di individuare le informazioni di configurazione per le VM Hyper-V con dischi situati in condivisioni SMB. Se non si hanno dischi delle VM in condivisioni SMB, è possibile ignorare il passaggio.

1. Identificare gli host Hyper-V che eseguono le VM Hyper-V con dischi situati in condivisioni SMB.
2. Eseguire il comando seguente in ogni host Hyper-V identificato:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

- L'autenticazione CredSSP consente all'host Hyper-V di delegare le credenziali per conto del client Azure Migrate.
- È possibile eseguire questo comando in remoto in tutti gli host Hyper-V.
- I nuovi nodi host eventualmente aggiunti in un cluster vengono aggiunti automaticamente per l'individuazione, ma è necessario abilitare manualmente CredSSP, se applicabile.

### <a name="verify-appliance-settings"></a>Verificare le impostazioni dell'appliance

Prima di configurare l'appliance di Azure Migrate e iniziare la valutazione nella prossima esercitazione, preparare la distribuzione dell'appliance.

1. [Verificare](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) i requisiti dell'appliance.
2. [Rivedere](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) gli URL di Azure a cui l'appliance dovrà accedere.
3. Esaminare i dati che l'appliance raccoglierà durante l'individuazione e la valutazione.
4. [Prendere nota](migrate-support-matrix-hyper-v.md#assessment-port-requirements) dei requisiti di accesso alle porte per l'appliance.


### <a name="set-up-an-account-for-vm-discovery"></a>Configurare un account per l'individuazione di macchine virtuali

Azure Migrate necessita delle autorizzazioni per individuare le macchine virtuali locali.

- Configurare un account utente locale o di dominio con autorizzazioni di amministratore per gli host o i cluster Hyper-V.

    - È necessario un singolo account per tutti gli host e i cluster da includere nell'individuazione.
    - L'account può essere locale o di dominio. È consigliabile che abbia autorizzazioni di amministratore per gli host o i cluster Hyper-V.
    - In alternativa, se non si vogliono assegnare autorizzazioni di amministratore, sono necessarie le autorizzazioni seguenti:
        - Utenti gestione remota
        - Amministratori di Hyper-V
        - Performance Monitor Users

### <a name="enable-integration-services-on-vms"></a>Abilitare i servizi di integrazione nelle VM

I servizi di integrazione devono essere abilitati in ogni VM, in modo che Azure Migrate possa acquisire informazioni sul relativo sistema operativo.

- In ogni VM da individuare e valutare abilitare [Servizi di integrazione Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services). 

## <a name="prepare-for-hyper-v-migration"></a>Preparare la migrazione di Hyper-V

1. [Esaminare](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) i requisiti degli host Hyper-V per la migrazione.
2. [Esaminare](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) i requisiti delle VM Hyper-V di cui eseguire la migrazione ad Azure.
3. [Prendere nota ](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) degli URL di Azure a cui gli host e i cluster Hyper-V dovranno accedere per la migrazione delle VM.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:
 
> [!div class="checklist"] 
> * Sono state configurate le autorizzazioni dell'account Azure.
> * Sono stati preparati gli host e le VM Hyper-V per la valutazione e la migrazione.

Continuare con l'esercitazione successiva per creare un progetto di Azure Migrate e valutare le VM Hyper-V per la migrazione ad Azure

> [!div class="nextstepaction"] 
> [Valutare le VM Hyper-V](./tutorial-assess-hyper-v.md) 
