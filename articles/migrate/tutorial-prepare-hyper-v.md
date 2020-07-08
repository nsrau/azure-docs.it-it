---
title: Preparare le macchine virtuali Hyper-V per la valutazione e la migrazione con Azure Migrate
description: Informazioni su come preparare la valutazione e la migrazione di macchine virtuali Hyper-V con Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 5f669de6bd8d767ca7b947fca883187dad9fe29d
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109621"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Preparare le VM Hyper-V per la valutazione e la migrazione ad Azure

Questo articolo spiega come preparare la valutazione e la migrazione di macchine virtuali Hyper-V locali in Azure con gli strumenti [Azure Migrate: Valutazione server](migrate-services-overview.md#azure-migrate-server-assessment-tool) e [Azure Migrate: Migrazione del server](migrate-services-overview.md#azure-migrate-server-migration-tool).


Questa esercitazione è la prima di una serie che illustra come valutare ed eseguire la migrazione di VM Hyper-V ad Azure. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Preparare Azure per l'uso con Azure Migrate.
> * Preparare le VM Hyper-V per la valutazione.
> * Preparare le VM Hyper-V per la migrazione 

> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario, per consentire di configurare rapidamente un modello di verifica. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prepare-azure"></a>Preparare Azure

La tabella riepiloga le attività da completare in Azure. Sotto la tabella sono riportate le istruzioni.

**Attività** | **Dettagli** | **Autorizzazioni**
--- | --- | ---
**Creare un progetto di Azure Migrate** | Un progetto Azure Migrate offre una posizione centralizzata per l'orchestrazione e la gestione di valutazioni e migrazioni con strumenti Azure Migrate, strumenti Microsoft e offerte di terze parti. | L'account Azure deve disporre delle autorizzazioni del ruolo Collaboratore o Proprietario nel gruppo di risorse in cui si trova il progetto.
**Registrare l'appliance** | Azure Migrate usa un'appliance di Azure Migrate leggera per individuare e valutare le VM Hyper-V. [Altre informazioni](migrate-appliance-architecture.md#appliance-registration) | Per registrare l'appliance, l'account Azure deve disporre delle autorizzazioni del ruolo Collaboratore o Proprietario nella sottoscrizione di Azure.
**Creare l'app di Azure AD** | Quando si registra l'appliance, Azure Migrate crea un'app di Azure Active Directory (Azure AD) che viene usata per la comunicazione tra gli agenti in esecuzione nell'appliance e Azure Migrate. | L'account di Azure necessita di autorizzazioni per creare app di Azure AD.
**Creare una macchina virtuale** | È necessario disporre delle autorizzazioni per creare una macchina virtuale nel gruppo di risorse e nella rete virtuale e per scrivere in un disco gestito di Azure. | L'account Azure deve avere il ruolo Collaboratore Macchina virtuale.


### <a name="assign-permissions-to-create-project"></a>Assegnare le autorizzazioni per creare il progetto

Verificare di avere le autorizzazioni per creare un progetto di Azure Migrate.

1. Nel portale di Azure aprire la sottoscrizione e selezionare **Controllo di accesso (IAM)** .
2. In **Verifica l'accesso** trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni.
3. È necessario avere le autorizzazioni di **Collaboratore** o **Proprietario**.
    - Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione.
    - Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare il ruolo.


### <a name="assign-permissions-to-create-azure-ad-apps"></a>Assegnare le autorizzazioni per creare app Azure AD

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
> Si tratta di un'impostazione predefinita che non è sensibile. [Altre informazioni](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)



#### <a name="assign-application-developer-role"></a>Assegnare il ruolo Sviluppatore di applicazioni

L'amministratore tenant/globale può assegnare il ruolo Sviluppatore di applicazioni a un account. [Altre informazioni](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

### <a name="assign-azure-account-permissions"></a>Assegnare le autorizzazioni all'account Azure

Assegnare all'account il ruolo Collaboratore Macchina virtuale, in modo da avere le autorizzazioni per:

- Creare una macchina virtuale nel gruppo di risorse selezionato.
- Creare una macchina virtuale nella rete virtuale selezionata.
- Scrivere in un disco gestito di Azure. 


### <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

[Configurare una rete di Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). I computer locali vengono replicati in dischi gestiti di Azure. Quando si effettua il failover in Azure per la migrazione, le macchine virtuali di Azure vengono create da questi dischi gestiti e aggiunte alla rete di Azure configurata.


## <a name="prepare-for-assessment"></a>Preparare la valutazione

È possibile preparare Hyper-V per la valutazione delle VM manualmente o usando uno script di configurazione. I passaggi per la preparazione sono i seguenti. Se si usa uno script, questi passaggi vengono configurati automaticamente.

**Step** | **Script** | **Manuale**
--- | --- | ---
**Verificare i requisiti dell'host Hyper-V** | Lo script verifica che l'host esegua una versione supportata di Hyper-V e il ruolo di Hyper-V.<br/><br/> Abilita il servizio Gestione remota Windows e apre le porte 5985 (HTTP) e 5986 (HTTPS) nell'host (necessario per la raccolta dei metadati). | Verificare i [requisiti degli host Hyper-V](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) per la valutazione dei server.<br/><br/> Assicurarsi che le [porte necessarie](migrate-support-matrix-hyper-v.md#port-access) siano aperte negli host Hyper-V.
**Verificare la versione di PowerShell** | Verifica che lo script sia in esecuzione in una versione di PowerShell supportata. | Verificare di eseguire PowerShell versione 4.0 o successiva nell'host Hyper-V.
**Creare un account** | Verifica che l'utente che esegue lo script disponga dei privilegi amministrativi per l'host Hyper-V.<br/><br/>  Consente di creare un account utente locale (non amministratore) usato per la comunicazione tra il servizio Azure Migrate e l'host Hyper-V. Questo account utente viene aggiunto ai gruppi seguenti nell'host:<br/><br/> - Utenti gestione remota<br/><br/> - Amministratori di Hyper-V<br/><br/>- Performance Monitor Users | Configurare un account utente locale o di dominio con autorizzazioni di amministratore per gli host o i cluster Hyper-V.<br/><br/> - È necessario un singolo account per tutti gli host e i cluster da includere nell'individuazione.<br/><br/> - L'account può essere locale o di dominio. È consigliabile che abbia autorizzazioni di amministratore per gli host o i cluster Hyper-V.<br/><br/> In alternativa, se non si vogliono assegnare autorizzazioni di amministratore, sono necessarie le autorizzazioni seguenti: Utenti gestione remota, Amministratori di Hyper-V, Performance Monitor Users.
**Abilitare la comunicazione remota di PowerShell** | Abilita la comunicazione remota di PowerShell nell'host, in modo che l'appliance di Azure Migrate possa eseguire i comandi di PowerShell nell'host, tramite una connessione WinRM.| Per eseguire la configurazione, in ogni host aprire una console di PowerShell come amministratore ed eseguire questo comando:<br/><br/>``` Enable-PSRemoting -force ```
**Configurare i servizi di integrazione di Hyper-V** | Verifica che i servizi di integrazione Hyper-V siano abilitati in tutte le macchine virtuali gestite dall'host. |  [Abilitare i servizi di integrazione di Hyper-V](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) su ogni VM.<br/><br/> Se si esegue Windows Server 2003, [seguire queste istruzioni](prepare-windows-server-2003-migration.md).
**Delegare le credenziali se i dischi delle macchine virtuali si trovano in condivisioni SMB remote** | Lo script delega le credenziali. | [Abilitare CredSSP](#enable-credssp-to-delegate-credentials) per delegare le credenziali.

### <a name="run-the-script"></a>Eseguire lo script

Eseguire lo script nel modo seguente:

1. Assicurarsi che PowerShell 4.0 o versioni successive sia installato nell'host Hyper-V.
2. Scaricare lo script dall'[Area download Microsoft](https://aka.ms/migrate/script/hyperv). Lo script è firmato da Microsoft per la crittografia.
3. Convalidare l'integrità dello script usando i file hash MD5 o SHA256. I valori hashtag sono riportati di seguito. Eseguire questo comando per generare l'hash per lo script:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Esempio di utilizzo:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```

4. Dopo aver convalidato l'integrità dello script, eseguire lo script in ogni host Hyper-V con questo comando di PowerShell:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

#### <a name="hashtag-values"></a>Valori hashtag

I valori degli hash sono:

| **Hash** | **Valore** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |



### <a name="enable-credssp-to-delegate-credentials"></a>Abilitare CredSSP per delegare le credenziali

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
2. Esaminare gli URL di Azure a cui l'appliance dovrà accedere nei cloud [pubblico](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls). Se si usa un firewall o un proxy basato su URL, assicurarsi che consenta l'accesso agli URL necessari.
3. Esaminare i dati che l'appliance raccoglierà durante l'individuazione e la valutazione.
4. [Esaminare](migrate-appliance.md#collected-data---hyper-v) i requisiti di accesso alle porte per l'appliance.


## <a name="prepare-for-hyper-v-migration"></a>Preparare la migrazione di Hyper-V

1. [Esaminare](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) i requisiti dell'host Hyper-V per la migrazione e gli URL di Azure a cui gli host e i cluster Hyper-V dovranno accedere per la migrazione delle VM.
2. [Esaminare](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) i requisiti delle VM Hyper-V di cui eseguire la migrazione ad Azure.
3. Prima di eseguire la migrazione delle macchine virtuali ad Azure, è necessario apportarvi alcune modifiche.
    - È importante apportare queste modifiche prima di avviare la migrazione. Se si esegue la migrazione della VM prima di apportare la modifica, la VM potrebbe non avviarsi in Azure.
    - Esaminare le modifiche che è necessario apportare in [Windows](prepare-for-migration.md#windows-machines) e [Linux](prepare-for-migration.md#linux-machines).



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

> [!div class="checklist"]
> * Sono state configurate le autorizzazioni dell'account Azure.
> * Sono stati preparati gli host e le VM Hyper-V per la valutazione e la migrazione.
> * È stata preparata la distribuzione dell'appliance di Azure Migrate.

Continuare con l'esercitazione successiva per creare un progetto di Azure Migrate, distribuire l'appliance, quindi individuare e valutare le VM Hyper-V per la migrazione ad Azure.

> [!div class="nextstepaction"]
> [Valutare le VM Hyper-V](./tutorial-assess-hyper-v.md)
