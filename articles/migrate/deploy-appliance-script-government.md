---
title: Configurare un'appliance di Azure Migrate in Azure per enti pubbliciSet up an Azure Migrate appliance in Azure Government
description: Informazioni su come configurare un'appliance di Azure Migrate in Azure per enti pubbliciLearn how to set up an Azure Migrate appliance in Azure Government
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: fd97161ffa075a6165ea963ef80bfabf8904576e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726735"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Configurare un'appliance in Azure per enti pubbliciSet up an appliance in Azure Government 

Seguire questo articolo per distribuire [un'appliance Azure Migrate](deploy-appliance.md) per macchine virtuali VMware, macchine virtuali Hyper-V e server fisici in un cloud di Azure per enti pubblici. Si esegue uno script per creare l'appliance e verificare che possa connettersi ad Azure.You run a script to create the appliance, and verify that it can connect to Azure. Se si desidera configurare un'appliance nel cloud pubblico, seguire [questo articolo](deploy-appliance-script.md).


> [!NOTE]
> L'opzione per distribuire un'appliance usando un modello (per le macchine virtuali VMware e le macchine virtuali Hyper-V) non è supportata in Azure per enti pubblici.


## <a name="prerequisites"></a>Prerequisiti

Lo script configura l'appliance di Azure Migrate in un computer fisico o in una macchina virtuale esistente.

- Il computer che fungerà da appliance deve eseguire Windows Server 2016, con 32 GB di memoria, otto vCPU, circa 80 GB di spazio di archiviazione su disco e un commutatore virtuale esterno. Richiede un indirizzo IP statico o dinamico e l'accesso a Internet.
- Prima di distribuire l'appliance, esaminare i requisiti dettagliati dell'appliance per [le macchine virtuali VMware,](migrate-appliance.md#appliance---vmware)le [macchine virtuali Hyper-V](migrate-appliance.md#appliance---hyper-v)e i [server fisici.](migrate-appliance.md#appliance---physical)
- Non eseguire lo script in un'appliance di Azure Migrate esistente.

## <a name="set-up-the-appliance-for-vmware"></a>Configurare l'appliance per VMware

Per configurare l'appliance per VMware, scaricare un file compresso dal portale di Azure ed estrarre il contenuto. Si esegue lo script di PowerShell per avviare l'applicazione web dell'appliance. Impostare l'appliance e configurarla per la prima volta. Quindi, registrare l'appliance con il progetto di Azure Migrate.Then, you register the appliance with the Azure Migrate project.

### <a name="download-the-script"></a>Scarica lo script

1.  In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** fare clic su **Individua**.
2.  In **Individua macchine virtuali** > **I computer sono virtualizzati?** selezionare **Sì, con VMware vSphere Hypervisor**.
3.  Fare clic su **Download**per scaricare il file compresso. 


### <a name="verify-file-security"></a>Verificare la sicurezza dei file

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare il codice hash per il file compresso
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip MD5```

3. Verificare i valori hash generati. Per l'ultima versione dell'appliance:

    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | 6316bcc8bc932204295bfe33f4be3949
          

### <a name="run-the-script"></a>Eseguire lo script

Ecco cosa fa lo script:

- Installa gli agenti e un'applicazione Web.
- Installa i ruoli di Windows, inclusi il servizio di attivazione di Windows, IIS e PowerShell ISE.
- Scarica e installa un modulo riscrivibile di IIS. [Altre informazioni](https://www.microsoft.com/download/details.aspx?id=7435)
- Aggiorna una chiave del Registro di sistema (HKLM), con impostazioni permanenti per Azure Migrate.
- Crea i file di registro e di configurazione come segue:
    - **File di configurazione**: %ProgramData%\Microsoft Azure\Config
    - **File di log**: %ProgramData%\Microsoft Azure\Logs

Per eseguire lo script:

1. Estrarre il file compresso in una cartella sul computer che ospiterà l'appliance. Assicurarsi di non eseguire lo script in un computer in un'appliance Azure Migrate esistente.
2. Avviare PowerShell nel computer, con privilegi di amministratore (con privilegi elevati).
3. Modificare la directory di PowerShell nella cartella contenente il contenuto estratto dal file compresso scaricato.
4. Eseguire lo script **AzureMigrateInstaller.ps1,** come indicato di seguito:Run the script AzureMigrateInstaller.ps1 , as follows:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
5. Dopo che lo script viene eseguito correttamente, avvia l'applicazione Web dell'appliance in modo che sia possibile configurare l'appliance. Se si verificano problemi, esaminare i log di script in C<em>AzureMigrateScenarioInstaller_:</em>.

### <a name="verify-access"></a>Verificare l'accesso

Assicurarsi che l'appliance possa connettersi agli URL di Azure per [i cloud governativi.](migrate-appliance.md#government-cloud-urls)


## <a name="set-up-the-appliance-for-hyper-v"></a>Configurare l'appliance per Hyper-V

Per configurare l'appliance per Hyper-V, scaricare un file compresso dal portale di Azure ed estrarre il contenuto. Si esegue lo script di PowerShell per avviare l'applicazione web dell'appliance. Impostare l'appliance e configurarla per la prima volta. Quindi, registrare l'appliance con il progetto di Azure Migrate.Then, you register the appliance with the Azure Migrate project.

### <a name="download-the-script"></a>Scarica lo script

1.  In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** fare clic su **Individua**.
2.  In **Individua macchine virtuali** > **I computer sono virtualizzati?** selezionare **Sì, con Hyper-V**.
3.  Fare clic su **Download**per scaricare il file compresso. 


### <a name="verify-file-security"></a>Verificare la sicurezza dei file

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare il codice hash per il file compresso
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip MD5```

3. Verificare i valori hash generati. Per l'ultima versione dell'appliance:

    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | 717f8b9185f565006b5aff0215ecadac
          

### <a name="run-the-script"></a>Eseguire lo script

Ecco cosa fa lo script:

- Installa gli agenti e un'applicazione Web.
- Installa i ruoli di Windows, inclusi il servizio di attivazione di Windows, IIS e PowerShell ISE.
- Scarica e installa un modulo riscrivibile di IIS. [Altre informazioni](https://www.microsoft.com/download/details.aspx?id=7435)
- Aggiorna una chiave del Registro di sistema (HKLM), con impostazioni permanenti per Azure Migrate.
- Crea i file di registro e di configurazione come segue:
    - **File di configurazione**: %ProgramData%\Microsoft Azure\Config
    - **File di log**: %ProgramData%\Microsoft Azure\Logs

Per eseguire lo script:

1. Estrarre il file compresso in una cartella sul computer che ospiterà l'appliance. Assicurarsi di non eseguire lo script in un computer in un'appliance Azure Migrate esistente.
2. Avviare PowerShell nel computer, con privilegi di amministratore (con privilegi elevati).
3. Modificare la directory di PowerShell nella cartella contenente il contenuto estratto dal file compresso scaricato.
4. Eseguire lo script **AzureMigrateInstaller.ps1,** come indicato di seguito:Run the script AzureMigrateInstaller.ps1 , as follows:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ``` 
5. Dopo che lo script viene eseguito correttamente, avvia l'applicazione Web dell'appliance in modo che sia possibile configurare l'appliance. Se si verificano problemi, esaminare i log di script in C<em>AzureMigrateScenarioInstaller_:</em>.

### <a name="verify-access"></a>Verificare l'accesso

Assicurarsi che l'appliance possa connettersi agli URL di Azure per [i cloud governativi.](migrate-appliance.md#government-cloud-urls)


## <a name="set-up-the-appliance-for-physical-servers"></a>Configurare l'appliance per i server fisici

Per configurare l'appliance per VMware, scaricare un file compresso dal portale di Azure ed estrarre il contenuto. Si esegue lo script di PowerShell per avviare l'applicazione web dell'appliance. Impostare l'appliance e configurarla per la prima volta. Quindi, registrare l'appliance con il progetto di Azure Migrate.Then, you register the appliance with the Azure Migrate project.

### <a name="download-the-script"></a>Scarica lo script

1.  In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** fare clic su **Individua**.
2.  In **Discover machines** > **Le macchine sono virtualizzate?**, selezionare **Non virtualizzato/Altro**.
3.  Fare clic su **Download**per scaricare il file compresso. 


### <a name="verify-file-security"></a>Verificare la sicurezza dei file

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare il codice hash per il file compresso
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5```

3. Verificare i valori hash generati. Per l'ultima versione dell'appliance:

    **Algoritmo** | **Valore hash**
    --- | ---
    MD5 | f81c155fc4a1409901caea948713913f
          

### <a name="run-the-script"></a>Eseguire lo script

Ecco cosa fa lo script:

- Installa gli agenti e un'applicazione Web.
- Installa i ruoli di Windows, inclusi il servizio di attivazione di Windows, IIS e PowerShell ISE.
- Scarica e installa un modulo riscrivibile di IIS. [Altre informazioni](https://www.microsoft.com/download/details.aspx?id=7435)
- Aggiorna una chiave del Registro di sistema (HKLM), con impostazioni permanenti per Azure Migrate.
- Crea i file di registro e di configurazione come segue:
    - **File di configurazione**: %ProgramData%\Microsoft Azure\Config
    - **File di log**: %ProgramData%\Microsoft Azure\Logs

Per eseguire lo script:

1. Estrarre il file compresso in una cartella sul computer che ospiterà l'appliance. Assicurarsi di non eseguire lo script in un computer in un'appliance Azure Migrate esistente.
2. Avviare PowerShell nel computer, con privilegi di amministratore (con privilegi elevati).
3. Modificare la directory di PowerShell nella cartella contenente il contenuto estratto dal file compresso scaricato.
4. Eseguire lo script **AzureMigrateInstaller.ps1,** come indicato di seguito:Run the script AzureMigrateInstaller.ps1 , as follows:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```
5. Dopo che lo script viene eseguito correttamente, avvia l'applicazione Web dell'appliance in modo che sia possibile configurare l'appliance. Se si verificano problemi, esaminare i log di script in C<em>AzureMigrateScenarioInstaller_:</em>.

### <a name="verify-access"></a>Verificare l'accesso

Assicurarsi che l'appliance possa connettersi agli URL di Azure per [i cloud governativi.](migrate-appliance.md#government-cloud-urls)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito l'appliance, è necessario configurarla per la prima volta e registrarla con il progetto Azure Migrate.After deploying the appliance, you need to configure it for the first time, and register it with the Azure Migrate project.

- Configurare l'appliance per [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Configurare l'appliance per [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Impostare l'appliance per i [server fisici.](how-to-set-up-appliance-physical.md)