---
title: Configurare un'appliance Azure Migrate con uno script
description: Informazioni su come configurare un'appliance Azure Migrate con uno script
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: c4f92d787ea2a72dd534e514e27fa1a5defef39c
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92317321"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configurare un'appliance con uno script

Seguire questo articolo per creare un' [appliance Azure migrate](./migrate-appliance-architecture.md) per la valutazione/migrazione di macchine virtuali VMware e macchine virtuali Hyper-V. Eseguire uno script per creare un'appliance e verificare che sia in grado di connettersi ad Azure. 

È possibile distribuire l'appliance per macchine virtuali VMware e Hyper-V usando uno script o un modello scaricato dal portale di Azure. L'uso di uno script è utile se non si è in grado di creare una macchina virtuale usando il modello scaricato.

- Per usare un modello, seguire le esercitazioni per [VMware](./tutorial-discover-vmware.md) o [Hyper-V](./tutorial-discover-hyper-v.md).
- Per configurare un'appliance per i server fisici, è possibile usare solo uno script. Seguire [questo articolo](how-to-set-up-appliance-physical.md).
- Per configurare un'appliance in un cloud di Azure per enti pubblici, seguire [questo articolo](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Prerequisiti

Lo script configura l'appliance Azure Migrate in un computer fisico o una macchina virtuale esistente.

- Il computer che fungerà da appliance deve soddisfare i requisiti hardware e del sistema operativo seguenti:

Scenario | Requisiti
--- | ---
VMware | Windows Server 2016, con 32 GB di memoria, otto vCPU, circa 80 GB di spazio di archiviazione su disco
Hyper-V | Windows Server 2016, con 16 GB di memoria, otto vCPU, circa 80 GB di spazio di archiviazione su disco
- Il computer necessita anche di un Commuter virtuale esterno. Richiede un indirizzo IP statico o dinamico e l'accesso a Internet.
- Prima di distribuire il dispositivo, esaminare i requisiti dettagliati dell'appliance per le [macchine virtuali VMware](migrate-appliance.md#appliance---vmware)e [Hyper-V](migrate-appliance.md#appliance---hyper-v).
- Non eseguire lo script in un'appliance Azure Migrate esistente.

## <a name="set-up-the-appliance-for-vmware"></a>Configurare l'appliance per VMware

Per configurare l'appliance per VMware, scaricare il file compresso denominato AzureMigrateInstaller-Server-Public.zip dal portale o da [qui](https://go.microsoft.com/fwlink/?linkid=2140334)ed estrarre il contenuto. Eseguire lo script di PowerShell per avviare l'app Web Appliance. Si configura l'appliance e la si configura per la prima volta. Si registra quindi l'appliance con il progetto Azure Migrate.


### <a name="verify-file-security"></a>Verificare la sicurezza del file

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare il codice hash per il file compresso
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-Public.zip SHA256```
3. Verificare la versione più recente del dispositivo e lo script per il cloud pubblico di Azure:

    **Algoritmo** | **Scaricare** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72



### <a name="run-the-script"></a>Eseguire lo script

Ecco cosa fa lo script:

- Installa gli agenti e un'applicazione Web.
- Installa i ruoli di Windows, tra cui il servizio Attivazione Windows, IIS e PowerShell ISE.
- Scarica e installa un modulo riscrivibile di IIS. [Altre informazioni](https://www.microsoft.com/download/details.aspx?id=7435)
- Aggiorna una chiave del registro di sistema (HKLM) con impostazioni permanenti per Azure Migrate.
- Crea i file di log e di configurazione come segue:
    - **File di configurazione**:%ProgramData%\Microsoft Azure\Config
    - **File di log**:%ProgramData%\Microsoft Azure\Logs

Per eseguire lo script:

1. Estrarre il file compresso in una cartella nel computer che ospiterà l'appliance. Assicurarsi di non eseguire lo script in un computer in un'appliance di Azure Migrate esistente.
2. Avviare PowerShell nel computer con privilegi di amministratore (con privilegi elevati).
3. Modificare la directory di PowerShell nella cartella che contiene il contenuto estratto dal file compresso scaricato.
4. Eseguire lo script **AzureMigrateInstaller.ps1**, come indicato di seguito:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. Dopo che lo script è stato eseguito correttamente, viene avviata l'applicazione Web Appliance per poter configurare l'appliance. Se si verificano problemi, esaminare i log di script in C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verificare l'accesso

Assicurarsi che l'appliance possa connettersi agli URL di Azure per il cloud [pubblico](migrate-appliance.md#public-cloud-urls) .

## <a name="set-up-the-appliance-for-hyper-v"></a>Configurare l'appliance per Hyper-V

Per configurare l'appliance per Hyper-V, scaricare il file compresso denominato AzureMigrateInstaller-Server-Public.zip dal portale o da [qui](https://go.microsoft.com/fwlink/?linkid=2105112)ed estrarre il contenuto. Eseguire lo script di PowerShell per avviare l'app Web Appliance. Si configura l'appliance e la si configura per la prima volta. Si registra quindi l'appliance con il progetto Azure Migrate.


### <a name="verify-file-security"></a>Verificare la sicurezza del file

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare il codice hash per il file compresso
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-HyperV.zip SHA256```

3. Verificare la versione più recente del dispositivo e lo script per il cloud pubblico di Azure:

    **Scenario** | **Scaricare** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2116657) |  9bbef62e2e22481eda4b77c7fdf05db98c3767c20f0a873114fb0dcfa6ed682a

### <a name="run-the-script"></a>Eseguire lo script

Ecco cosa fa lo script:

- Installa gli agenti e un'applicazione Web.
- Installa i ruoli di Windows, tra cui il servizio Attivazione Windows, IIS e PowerShell ISE.
- Scarica e installa un modulo riscrivibile di IIS. [Altre informazioni](https://www.microsoft.com/download/details.aspx?id=7435)
- Aggiorna una chiave del registro di sistema (HKLM) con impostazioni permanenti per Azure Migrate.
- Crea i file di log e di configurazione come segue:
    - **File di configurazione**:%ProgramData%\Microsoft Azure\Config
    - **File di log**:%ProgramData%\Microsoft Azure\Logs

Per eseguire lo script:

1. Estrarre il file compresso in una cartella nel computer che ospiterà l'appliance. Assicurarsi di non eseguire lo script in un computer in un'appliance di Azure Migrate esistente.
2. Avviare PowerShell nel computer con privilegi di amministratore (con privilegi elevati).
3. Modificare la directory di PowerShell nella cartella che contiene il contenuto estratto dal file compresso scaricato.
4. Eseguire lo script **AzureMigrateInstaller.ps1**, come indicato di seguito: 

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Dopo che lo script è stato eseguito correttamente, viene avviata l'applicazione Web Appliance per poter configurare l'appliance. Se si verificano problemi, esaminare i log di script in C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verificare l'accesso

Assicurarsi che l'appliance possa connettersi agli URL di Azure per il cloud [pubblico](migrate-appliance.md#public-cloud-urls) .

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito il dispositivo, è necessario configurarlo per la prima volta e registrarlo con il progetto Azure Migrate.

- Configurare l'appliance per [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Configurare l'appliance per [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).