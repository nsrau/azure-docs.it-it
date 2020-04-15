---
title: Gestione dell'agente Azure Arc per server (anteprima)Managing the Azure Arc for servers (preview) agent
description: Questo articolo descrive le diverse attività di gestione che verranno in genere eseguite durante il ciclo di vita dell'agente computer connesso ad Azure Arc per server.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 5ad2127b4cb9da3ca83aa04bd1885908a88dba62
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308964"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Gestione e manutenzione dell'agente di macchine connesse

Dopo la distribuzione iniziale dell'agente di Azure Arc for servers (anteprima) Connected Machine agent for Windows o Linux, potrebbe essere necessario riconfigurare l'agente, aggiornarlo o rimuoverlo dal computer se ha raggiunto la fase di ritiro nel ciclo di vita. È possibile gestire facilmente queste attività di manutenzione di routine manualmente o tramite l'automazione, che riduce sia gli errori operativi che le spese.

## <a name="upgrading-agent"></a>Agente di aggiornamento

L'agente del computer connesso di Azure per Windows e Linux può essere aggiornato alla versione più recente manualmente o automaticamente a seconda delle esigenze. Nella tabella seguente vengono descritti i metodi supportati per eseguire l'aggiornamento dell'agente.

| Sistema operativo | Metodo di aggiornamento |
|------------------|----------------|
| Windows | Manualmente<br> Windows Update |
| Ubuntu | [Appartamento](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SuSE Linux Enterprise Server | [Zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [Yum](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Agente Windows

Per aggiornare l'agente in un computer Windows alla versione più recente, l'agente è disponibile da Microsoft Update e può essere distribuito utilizzando il processo di gestione degli aggiornamenti software esistente. Può anche essere eseguito manualmente dal prompt dei comandi, da uno script o `AzureConnectedMachine.msi`da un'altra soluzione di automazione o dalla procedura guidata dell'interfaccia utente eseguendo . 

> [!NOTE]
> * Per aggiornare l'agente, è necessario disporre delle autorizzazioni *di amministratore.*
> * Per eseguire l'aggiornamento manuale, è innanzitutto necessario scaricare e copiare il pacchetto installer in una cartella sul server di destinazione o da una cartella di rete condivisa. 

Se non si ha familiarità con le opzioni della riga di comando per i pacchetti di Windows Installer, vedere Opzioni della riga di comando standard di [Msiexec](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) e [Opzioni della riga di comando Msiexec](https://docs.microsoft.com/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Per eseguire l'aggiornamento mediante l'Installazione guidata

1. Accedere al computer con un account con diritti amministrativi.

2. Eseguire **AzureConnectedMachineAgent.msi** per avviare l'installazione guidata.

L'Installazione guidata rileva se esiste una versione precedente, quindi esegue automaticamente un aggiornamento dell'agente. Al termine dell'aggiornamento, l'Installazione guidata viene chiusa automaticamente.

#### <a name="to-upgrade-from-the-command-line"></a>Per eseguire l'aggiornamento dalla riga di comando

1. Accedere al computer con un account con diritti amministrativi.

2. Per aggiornare l'agente in modo invisibile all'utente e creare un file di registro dell'installazione nella `C:\Support\Logs` cartella, eseguire il comando seguente.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Agente Linux

Per aggiornare l'agente in un computer Linux alla versione più recente, è necessario due comandi. Un comando per aggiornare l'indice del pacchetto locale con l'elenco dei pacchetti più recenti disponibili dai repository e un comando per aggiornare il pacchetto locale. 

> [!NOTE]
> Per aggiornare l'agente, è necessario disporre delle autorizzazioni di accesso *radice* o con un account con diritti elevati tramite Sudo.

#### <a name="upgrade-ubuntu"></a>Potenzia Ubuntu

1. Per aggiornare l'indice del pacchetto locale con le modifiche più recenti apportate nei repository, eseguire il comando seguente:

    ```bash
    apt update
    ```

2. Per aggiornare il sistema, eseguire il comando seguente:

    ```bash
    apt upgrade
    ```

Le azioni del comando [apt,](https://help.ubuntu.com/lts/serverguide/apt.html) ad esempio l'installazione `/var/log/dpkg.log` e la rimozione dei pacchetti, vengono registrate nel file di registro.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Aggiornamento Red Hat/CentOS/Amazon Linux

1. Per aggiornare l'indice del pacchetto locale con le modifiche più recenti apportate nei repository, eseguire il comando seguente:

    ```bash
    yum check-update
    ```

2. Per aggiornare il sistema, eseguire il comando seguente:

    ```bash
    yum update
    ```

Le azioni del comando [yum,](https://access.redhat.com/articles/yum-cheat-sheet) ad esempio l'installazione `/var/log/yum.log` e la rimozione dei pacchetti, vengono registrate nel file di registro. 

#### <a name="upgrade-suse-linux-enterprise"></a>Aggiornamento di SUSE Linux Enterprise

1. Per aggiornare l'indice del pacchetto locale con le modifiche più recenti apportate nei repository, eseguire il comando seguente:

    ```bash
    zypper refresh
    ```

2. Per aggiornare il sistema, eseguire il comando seguente:

    ```bash
    zypper update
    ```

Le azioni del comando [zypper,](https://en.opensuse.org/Portal:Zypper) ad esempio l'installazione `/var/log/zypper.log` e la rimozione dei pacchetti, vengono registrate nel file di registro. 

## <a name="about-the-azcmagent-tool"></a>Informazioni sullo strumento Azcmagent

Lo strumento Azcmagent (Azcmagent.exe) viene usato per configurare L'agente computer connesso di Azure Arc per i server (anteprima) durante l'installazione o per modificare la configurazione iniziale dell'agente dopo l'installazione. Azcmagent.exe fornisce parametri della riga di comando per personalizzare l'agente e visualizzarne lo stato:

* **Connetti** - Per connettere il computer ad Azure Arc

* **Disconnetti** - Per disconnettere la macchina da Azure Arc

* **Riconnetti** - Per riconnettere un computer disconnesso ad Azure ArcReconnect - To reconnect a disconnected machine to Azure Arc

* **Mostra:** consente di visualizzare lo stato dell'agente e le relative proprietà di configurazione (nome del gruppo di risorse, ID sottoscrizione, versione e così via), che consentono di risolvere un problema con l'agente.

* **-h o --help** - Mostra i parametri disponibili della riga di comando

    Ad esempio, per visualizzare informazioni dettagliate sul `azcmagent reconnect -h`parametro **Reconnect,** digitare . 

* **-v o --verbose** - Abilita registrazione dettagliata

È possibile eseguire un'operazione **Connect**, **Disconnect**e **Reconnect** manualmente durante l'accesso interattivo oppure automatizzare l'utilizzo della stessa entità servizio utilizzata per l'onboarding di più agenti o con un token di [accesso](../../active-directory/develop/access-tokens.md)alla piattaforma di identità Microsoft. Se non è stata usata un'entità servizio per registrare la macchina con Azure Arc per i server (anteprima), vedere [l'articolo](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) seguente per creare un'entità servizio.

### <a name="connect"></a>Connessione

Questo parametro specifica una risorsa in Azure Resource Manager che rappresenta la creazione del computer in Azure.This parameter specifies a resource in Azure Resource Manager representing the machine is created in Azure. La risorsa si trova nella sottoscrizione e nel gruppo di risorse specificato e `--location` i dati relativi al computer vengono archiviati nell'area di Azure specificata dall'impostazione. Il nome della risorsa predefinita è il nome host del computer, se non specificato.

Un certificato corrispondente all'identità assegnata dal sistema del computer viene quindi scaricato e archiviato in locale. Al termine di questo passaggio, il servizio metadati computer connesso di Azure e l'agente di configurazione guest iniziano la sincronizzazione con Azure Arc per i server (anteprima).

Per connettersi usando un'entità servizio, eseguire il comando seguente:To connect using a service principal, run the following command:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Per connettersi utilizzando un token di accesso, eseguire il comando seguente:To connect using an access token, run the following command:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Per connettersi con le credenziali con privilegi elevati di accesso (interattivo), eseguire il comando seguente:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Disconnetti

Questo parametro specifica una risorsa in Azure Resource Manager che rappresenta l'eliminazione del computer in Azure.This parameter specifies a resource in Azure Resource Manager representing the machine is deleted in Azure. Non elimina l'agente dalla macchina, questa operazione deve essere eseguita come passaggio separato. Dopo la disconnessione del computer, se si vuole registrarlo nuovamente `azcmagent connect` con Azure Arc per i server (anteprima), usare pertanto viene creata una nuova risorsa in Azure.After the machine is disconnected, if you want to re-register it with Azure Arc for servers (preview), use so a new resource is created for it in Azure.

Per disconnettersi usando un'entità servizio, eseguire il comando seguente:To disconnect using a service principal, run the following command:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Per disconnettersi utilizzando un token di accesso, eseguire il comando seguente:To disconnect using an access token, run the following command:

`azcmagent disconnect --access-token <accessToken>`

Per disconnettersi con le credenziali con privilegi elevati connessi (interattivo), eseguire il comando seguente:

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>Riconnetti

Questo parametro riconnette il computer già registrato o connesso con Azure Arc per i server (anteprima). Ciò può essere necessario se la macchina è stata spenta, almeno 45 giorni, affinché il suo certificato scada. Questo parametro usa le opzioni di autenticazione fornite per recuperare le nuove credenziali corrispondenti alla risorsa di Azure Resource Manager che rappresenta il computer.

Questo comando richiede privilegi più elevati rispetto al ruolo [di onboarding del computer connesso](overview.md#required-permissions) ad Azure.This command requires higher privileges than the Azure Connected Machine Onboarding role.

Per riconnettersi usando un'entità servizio, eseguire il comando seguente:To reconnect using a service principal, run the following command:

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Per riconnettersi utilizzando un token di accesso, eseguire il comando seguente:To reconnect using an access token, run the following command:

`azcmagent reconnect --access-token <accessToken>`

Per riconnettersi con le credenziali con accesso con privilegi elevati (interattivo), eseguire il comando seguente:

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>Rimuovere l'agente

Eseguire uno dei seguenti metodi per disinstallare l'agente Windows o Linux Connected Machine dalla macchina. La rimozione dell'agente non annulla la registrazione della macchina con Arc per i server (anteprima), si tratta di un processo separato che viene eseguito quando non è più necessario gestire il computer in Azure.Removing the agent does not unregister the machine with Arc for servers (preview), this is a separate process you perform when you no longer need to manage the machine in Azure.

### <a name="windows-agent"></a>Agente Windows

Entrambi i metodi seguenti rimuovono l'agente, ma non rimuovono la cartella *C:.*

#### <a name="uninstall-from-control-panel"></a>Eseguire la disinstallazione dal Pannello di controllo

1. Per disinstallare l'agente di Windows dalla macchina virtuale, eseguire le operazioni seguenti:

    a. Accedere al computer con un account dotato di autorizzazioni di amministratore.  
    b. In **Pannello di controllo** selezionare **Programmi e funzionalità**.  
    c. In **Programmi e funzionalità** selezionare **Agente Azure Connected Machine**, selezionare **Disinstalla** e quindi **Sì**.  

    >[!NOTE]
    > È anche possibile eseguire l'installazione guidata dell'agente facendo doppio clic sul pacchetto del programma di installazione **AzureConnectedMachineAgent.msi**.

#### <a name="uninstall-from-the-command-line"></a>Eseguire la disinstallazione dalla riga di comando

Per disinstallare manualmente l'agente dal prompt dei comandi o per utilizzare un metodo automatizzato, ad esempio uno script, è possibile utilizzare l'esempio seguente. In primo luogo è necessario recuperare il codice del prodotto, che è un GUID che è l'identificatore principale del pacchetto dell'applicazione, dal sistema operativo. La disinstallazione viene eseguita utilizzando la `msiexec /x {Product Code}`riga di comando Msiexec.exe - .
    
1. Aprire l'editor del Registro di sistema.

2. Nella chiave del registro di sistema `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` cercare e copiare il GUID del codice prodotto.

3. È quindi possibile disinstallare l'agente utilizzando Msiexec utilizzando i seguenti esempi:

   * Dalla riga di comando digitare:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * È possibile eseguire la stessa procedura usando PowerShell:You can perform the same steps using PowerShell:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Agente Linux

> [!NOTE]
> Per disinstallare l'agente, è necessario disporre delle autorizzazioni di accesso *root* o con un account con diritti elevati tramite Sudo.

Per disinstallare l'agente Linux, il comando da utilizzare dipende dal sistema operativo Linux.

- Per Ubuntu, eseguire il comando seguente:

    ```bash
    sudo apt purge azcmagent
    ```

- Per RHEL, CentOS e Amazon Linux, eseguire il comando seguente:

    ```bash
    sudo yum remove azcmagent
    ```

- Per SLES, eseguire il comando seguente:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Annulla registrazione macchina

Se si prevede di interrompere la gestione della macchina con i servizi di supporto in Azure, eseguire la procedura seguente per annullare la registrazione del computer con Arc per i server (anteprima). È possibile eseguire questi passaggi prima o dopo aver rimosso l'agente computer connesso dalla macchina.

1. Aprire Azure Arc per server (anteprima) passando al [portale di Azure](https://aka.ms/hybridmachineportal).

2. Selezionare la macchina virtuale nell'elenco, selezionare i puntini di sospensione (**...**) e quindi **Elimina**.
