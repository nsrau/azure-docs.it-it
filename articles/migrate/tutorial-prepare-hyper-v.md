---
title: Preparare le macchine virtuali Hyper-V per la valutazione e la migrazione ad Azure con Azure Migrate | Microsoft Docs
description: Questo articolo descrive come preparare le macchine virtuali Hyper-V per la valutazione e la migrazione ad Azure tramite Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 09/16/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2f45f70f1c131e1690997cda18a8d612d3af9dee
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010316"
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


## <a name="prepare-for-hyper-v-assessment"></a>Preparare la valutazione di Hyper-V

Per preparare la valutazione di Hyper-V, eseguire le operazioni seguenti:

1. Verificare le impostazioni degli host Hyper-V.
2. Configurare la comunicazione remota di PowerShell in ogni host, in modo che l'appliance di Azure Migrate possa eseguire i comandi di PowerShell nell'host, tramite una connessione WinRM.
3. Se i dischi delle macchine virtuali si trovano nell'archiviazione SMB remota, è necessaria la delega delle credenziali. 
    - Abilitare la delega CredSSP in modo che l'appliance di Azure Migrate possa fungere da client, delegando le credenziali a un host.
    - Abilitare ogni host in modo che funga da delegato per l'appliance, come descritto di seguito.
    - Successivamente, quando si configura il dispositivo, si abiliterà la delega nell'appliance.
4. Esaminare i requisiti dell'appliance e l'accesso a URL/porte necessario per l'appliance.
5. Configurare un account che l'appliance userà per individuare le macchine virtuali.
6. Configurare Integration Services Hyper-V in ogni macchina virtuale che si vuole individuare e valutare.


È possibile configurare queste impostazioni manualmente usando le procedure seguenti. In alternativa, è possibile eseguire lo script di configurazione dei prerequisiti di Hyper-V.

### <a name="hyper-v-prerequisites-configuration-script"></a>Script di configurazione dei prerequisiti di Hyper-V

Lo script convalida gli host Hyper-V e configura le impostazioni necessarie per individuare e valutare le macchine virtuali Hyper-V. Vengono eseguite le operazioni seguenti:

- Verifica che lo script sia in esecuzione in una versione di PowerShell supportata.
- Verifica che l'utente che esegue lo script disponga dei privilegi amministrativi per l'host Hyper-V.
- Consente di creare un account utente locale (non amministratore) usato per la comunicazione tra il servizio Azure Migrate e l'host Hyper-V. Questo account utente viene aggiunto ai gruppi seguenti nell'host:
    - Utenti gestione remota
    - Amministratori di Hyper-V
    - Performance Monitor Users
- Verifica che l'host esegua una versione supportata di Hyper-V e il ruolo Hyper-V.
- Abilita il servizio Gestione remota Windows e apre le porte 5985 (HTTP) e 5986 (HTTPS) nell'host (necessario per la raccolta dei metadati).
- Abilita la comunicazione remota di PowerShell nell'host.
- Verifica che il servizio di integrazione Hyper-V sia abilitato in tutte le macchine virtuali gestite dall'host. 
- Se necessario, abilita CredSSP nell'host.

Eseguire lo script nel modo seguente:

1. Assicurarsi che PowerShell 4.0 o versioni successive sia installato nell'host Hyper-V.
2. Scaricare lo script dall'[Area download Microsoft](https://aka.ms/migrate/script/hyperv). Lo script è firmato da Microsoft per la crittografia.
3. Convalidare l'integrità dello script usando i file hash MD5 o SHA256. I valori hashtag sono riportati di seguito. Eseguire questo comando per generare l'hash per lo script:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Esempio di utilizzo: 
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.  Dopo aver convalidato l'integrità dello script, eseguire lo script in ogni host Hyper-V con questo comando di PowerShell:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

#### <a name="hashtag-values"></a>Valori hashtag

I valori degli hash sono:

| **Hash** | **Valore** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |

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

Se nell'host sono presenti macchine virtuali con dischi che si trovano in condivisioni SMB, completare questo passaggio nell'host.

- È possibile eseguire questo comando in remoto in tutti gli host Hyper-V.
- I nuovi nodi host eventualmente aggiunti in un cluster vengono aggiunti automaticamente per l'individuazione, ma è necessario abilitare manualmente CredSSP, se applicabile.

Abilitarlo nel modo seguente:

1. Identificare gli host Hyper-V che eseguono le VM Hyper-V con dischi situati in condivisioni SMB.
2. Eseguire il comando seguente in ogni host Hyper-V identificato:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

Quando si configura l'appliance, si completa la configurazione di CredSSP [abilitandolo nell'appliance](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Questa operazione viene descritta nell'esercitazione successiva di questa serie.


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

In ogni VM da individuare e valutare abilitare [Servizi di integrazione Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services). 

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
