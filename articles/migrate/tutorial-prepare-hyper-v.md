---
title: Preparare le macchine virtuali Hyper-V per la valutazione e la migrazione con Azure Migrate
description: Informazioni su come preparare la valutazione e la migrazione di macchine virtuali Hyper-V con Azure Migrate.
ms.topic: tutorial
ms.date: 01/01/2020
ms.custom: mvc
ms.openlocfilehash: 1d327f558806e0205540c183c56b92ba31e33cb7
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031221"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Preparare le VM Hyper-V per la valutazione e la migrazione ad Azure

Questo articolo descrive come preparare le macchine virtuali Hyper-V locali per la valutazione e la migrazione ad Azure tramite [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti.

Questa esercitazione è la prima di una serie che illustra come valutare ed eseguire la migrazione di VM Hyper-V ad Azure. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Preparare Azure. Configurare le autorizzazioni per consentire all'account e alle risorse di Azure di interagire con Azure Migrate.
> * Preparare gli host Hyper-V e le VM locali per la valutazione server. È possibile eseguire la preparazione con uno script di configurazione oppure manualmente.
> * Preparare la distribuzione dell'appliance di Azure Migrate. L'appliance viene usata per l'individuazione e la valutazione delle VM locali.
> * Preparare gli host Hyper-V e le VM locali per la migrazione server.


> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario, per consentire di configurare rapidamente un modello di verifica. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere le procedure relative alla valutazione e alla migrazione di Hyper-V.


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prepare-azure"></a>Preparare Azure

### <a name="azure-permissions"></a>Autorizzazioni di Azure

È necessario configurare le autorizzazioni per la distribuzione di Azure Migrate.

**Attività** | **Autorizzazioni**
--- | ---
**Creare un progetto di Azure Migrate** | L'account di Azure necessita di autorizzazioni per creare un progetto.
**Registrare l'appliance Azure Migrate** | Azure Migrate usa un'appliance di Azure Migrate leggera per individuare e valutare le VM Hyper-V con lo strumento Valutazione server di Azure Migrate. L'appliance individua le macchine virtuali, ne invia i metadati e i dati sulle prestazioni ad Azure Migrate.<br/><br/>Durante la registrazione dell'appliance, i provider di risorse Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault vengono registrati con la sottoscrizione scelta nell'appliance. La registrazione di un provider di risorse configura la sottoscrizione per l'utilizzo del provider di risorse. Per registrare i provider di risorse, è necessario il ruolo di proprietario o collaboratore della sottoscrizione.<br/><br/> Nell'ambito dell'onboarding, Azure Migrate crea un'app Azure Active Directory (Azure AD):<br/> L'app Azure AD viene usata per la comunicazione (autenticazione e autorizzazione) tra gli agenti in esecuzione nell'appliance con i rispettivi servizi in esecuzione in Azure. Questa app non dispone dei privilegi necessari per effettuare chiamate ARM né dell'accesso basato sul controllo degli accessi in base al ruolo su alcuna risorsa.



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


## <a name="prepare-hyper-v-for-assessment"></a>Preparare Hyper-V per la valutazione

È possibile preparare Hyper-V per la valutazione delle VM manualmente o usando uno script di configurazione. Ecco cosa è necessario preparare, con lo script o [manualmente](#prepare-hyper-v-manually).

- [Verificare](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) le impostazioni dell'host Hyper-V e assicurarsi che le [porte richieste](migrate-support-matrix-hyper-v.md#port-access) siano aperte negli host Hyper-V.
- Configurare la comunicazione remota di PowerShell in ogni host, in modo che l'appliance di Azure Migrate possa eseguire i comandi di PowerShell nell'host, tramite una connessione WinRM.
- Delegare le credenziali se i dischi delle macchine virtuali si trovano in condivisioni SMB remote.
- Configurare un account che verrà usato dall'appliance per individuare le VM negli host Hyper-V.
- Configurare Integration Services Hyper-V in ogni macchina virtuale che si vuole individuare e valutare.



## <a name="prepare-with-a-script"></a>Eseguire la preparazione con uno script

Lo script effettua le operazioni seguenti:

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

### <a name="hashtag-values"></a>Valori hashtag

I valori degli hash sono:

| **Hash** | **Valore** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |


## <a name="prepare-hyper-v-manually"></a>Preparare Hyper-V manualmente

Seguire le procedure descritte in questa sezione per preparare Hyper-V manualmente, invece di usare lo script.

### <a name="verify-powershell-version"></a>Verificare la versione di PowerShell

Assicurarsi che PowerShell 4.0 o versioni successive sia installato nell'host Hyper-V.



### <a name="set-up-an-account-for-vm-discovery"></a>Configurare un account per l'individuazione di macchine virtuali

Azure Migrate necessita delle autorizzazioni per individuare le macchine virtuali locali.

- Configurare un account utente locale o di dominio con autorizzazioni di amministratore per gli host o i cluster Hyper-V.

    - È necessario un singolo account per tutti gli host e i cluster da includere nell'individuazione.
    - L'account può essere locale o di dominio. È consigliabile che abbia autorizzazioni di amministratore per gli host o i cluster Hyper-V.
    - In alternativa, se non si vogliono assegnare autorizzazioni di amministratore, sono necessarie le autorizzazioni seguenti:
        - Utenti gestione remota
        - Amministratori di Hyper-V
        - Performance Monitor Users

### <a name="verify-hyper-v-host-settings"></a>Verificare le impostazioni degli host Hyper-V

1. Verificare i [requisiti degli host Hyper-V](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) per la valutazione dei server.
2. Assicurarsi che le [porte necessarie](migrate-support-matrix-hyper-v.md#port-access) siano aperte negli host Hyper-V.

### <a name="enable-powershell-remoting-on-hosts"></a>Abilitare la comunicazione remota di PowerShell negli host

Configurare la comunicazione remota di PowerShell in ogni host, come indicato di seguito:

1. In ogni host aprire una console di PowerShell come amministratore.
2. Eseguire questo comando:

    ```
    Enable-PSRemoting -force
    ```
### <a name="enable-integration-services-on-vms"></a>Abilitare i servizi di integrazione nelle VM

I servizi di integrazione devono essere abilitati in ogni VM, in modo che Azure Migrate possa acquisire informazioni sul relativo sistema operativo.

In ogni VM da individuare e valutare abilitare [Servizi di integrazione Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).


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


## <a name="prepare-for-appliance-deployment"></a>Preparare la distribuzione dell'appliance

Prima di configurare l'appliance di Azure Migrate e iniziare la valutazione nella prossima esercitazione, preparare la distribuzione dell'appliance.

1. [Verificare](migrate-appliance.md#appliance---hyper-v) i requisiti dell'appliance.
2. [Rivedere](migrate-appliance.md#url-access) gli URL di Azure a cui l'appliance dovrà accedere.
3. Esaminare i dati che l'appliance raccoglierà durante l'individuazione e la valutazione.
4. [Prendere nota](migrate-appliance.md#collected-data---hyper-v) dei requisiti di accesso alle porte per l'appliance.


## <a name="prepare-for-hyper-v-migration"></a>Preparare la migrazione di Hyper-V

1. [Esaminare](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) i requisiti dell'host Hyper-V per la migrazione e gli URL di Azure a cui gli host e i cluster Hyper-V dovranno accedere per la migrazione delle VM.
2. [Esaminare](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) i requisiti delle VM Hyper-V di cui eseguire la migrazione ad Azure.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

> [!div class="checklist"]
> * Sono state configurate le autorizzazioni dell'account Azure.
> * Sono stati preparati gli host e le VM Hyper-V per la valutazione e la migrazione.
> * È stata preparata la distribuzione dell'appliance di Azure Migrate.

Continuare con l'esercitazione successiva per creare un progetto di Azure Migrate, distribuire l'appliance, quindi individuare e valutare le VM Hyper-V per la migrazione ad Azure.

> [!div class="nextstepaction"]
> [Valutare le VM Hyper-V](./tutorial-assess-hyper-v.md)
