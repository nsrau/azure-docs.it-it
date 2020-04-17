---
title: Configurare un'appliance di Azure Migrate con uno scriptSet up an Azure Migrate appliance with a script
description: Informazioni su come configurare un'appliance di Azure Migrate con uno script
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: faed7f96ea8c1850af5523d35f9f891011a48df8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537713"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configurare un'appliance con uno script

Questo articolo descrive come configurare [l'appliance Di Azure Migrate](deploy-appliance.md) usando uno script di installazione di PowerShell, per le macchine virtuali VMware e le macchine virtuali Hyper-V.This article describes how to set up the Azure Migrate appliance using a PowerShell installer script, for VMware VMs, and Hyper-V V VMs. Se si desidera configurare l'appliance per i server fisici, [leggere questo articolo](how-to-set-up-appliance-physical.md).


È possibile distribuire l'appliance utilizzando un paio di metodi:


- Utilizzo di un modello per macchine virtuali VMware (OVA) o Hyper-V Vm (VHD).
- Utilizzo di uno script. Questo è il metodo descritto in questo articolo. Lo script fornisce:
    - Un'alternativa alla configurazione dell'appliance tramite un modello OVA, per la valutazione e la migrazione senza agenti delle macchine virtuali VMware.
    - Un'alternativa alla configurazione dell'appliance usando un modello di disco rigido virtuale, per la valutazione e la migrazione delle macchine virtuali Hyper-V.
    - Per la valutazione dei server fisici (o delle macchine virtuali di cui si desidera eseguire la migrazione come server fisici), lo script è l'unico metodo per configurare l'appliance.
    - Un modo per distribuire l'appliance in Azure per enti pubblici.


Dopo aver creato l'appliance, verificare che sia possibile connettersi ad Azure Migrate.After creating the appliance, you verify that it can connect to Azure Migrate. Configurare quindi l'appliance per la prima volta e registrarla con il progetto Azure Migrate.You then configure the appliance for the first time, and register it with the Azure Migrate project.


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
    - Esempio di utilizzo per il cloud pubblico:Example usage for public cloud:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
    - Esempio di utilizzo per il cloud governativo:Example usage for government cloud:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip```

3. Verificare i valori hash generati:

    - Per il cloud pubblico (per l'ultima versione dell'appliance):

        **Algoritmo** | **Valore hash**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - Per gli enti di Azure per enti pubblici (per la versione più recente dell'appliance):For Azure government (for the latest appliance version):

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
4. Eseguire lo script **AzureMigrateInstaller.ps1,** come indicato di seguito:Run the script AzureMigrateInstaller.ps1 , as follows:
    - Per il cloud pubblico:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
    - Per Azure per enti pubblici:For Azure Government:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
   
5. Dopo che lo script viene eseguito correttamente, avvia l'applicazione Web dell'appliance in modo che sia possibile configurare l'appliance. Se si verificano problemi, esaminare i log di script in C<em>AzureMigrateScenarioInstaller_:</em>.

### <a name="verify-access"></a>Verificare l'accesso

Assicurarsi che l'appliance possa connettersi agli URL di Azure per i cloud [pubblici](migrate-appliance.md#public-cloud-urls) e [cloud governativi](migrate-appliance.md-government-cloud-urls.


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
    - Esempio di utilizzo per il cloud pubblico:Example usage for public cloud:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
    - Esempio di utilizzo per il cloud governativo:Example usage for government cloud:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip MD5```

3. Verificare i valori hash generati:

    - Per il cloud pubblico (per l'ultima versione dell'appliance):

        **Algoritmo** | **Valore hash**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - Per gli enti di Azure per enti pubblici (per la versione più recente dell'appliance):For Azure government (for the latest appliance version):

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
4. Eseguire lo script **AzureMigrateInstaller.ps1,** come indicato di seguito:Run the script AzureMigrateInstaller.ps1 , as follows:
    - Per il cloud pubblico:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
    - Per Azure per enti pubblici:For Azure Government:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ```
   
5. Dopo che lo script viene eseguito correttamente, avvia l'applicazione Web dell'appliance in modo che sia possibile configurare l'appliance. Se si verificano problemi, esaminare i log di script in C<em>AzureMigrateScenarioInstaller_:</em>.

### <a name="verify-access"></a>Verificare l'accesso

Assicurarsi che l'appliance possa connettersi agli URL di Azure per i cloud [pubblici](migrate-appliance.md#public-cloud-urls) e [cloud governativi](migrate-appliance.md-government-cloud-urls.



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla configurazione dell'appliance con un modello o per i server fisici, vedere gli articoli seguenti:

- Configurare l'appliance per [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Configurare l'appliance per [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Impostare l'appliance per i [server fisici.](how-to-set-up-appliance-physical.md)