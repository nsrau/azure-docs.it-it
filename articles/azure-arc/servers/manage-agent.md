---
title: Gestione dell'agente Azure Arc per server (anteprima)Managing the Azure Arc for servers (preview) agent
description: Questo articolo descrive le diverse attività di gestione che verranno in genere eseguite durante il ciclo di vita dell'agente computer connesso ad Azure Arc per server.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 758e6123fd09df1e3f8b2e883a729b9fec4328d1
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367291"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Gestione e manutenzione dell'agente di macchine connesse

Dopo la distribuzione iniziale dell'agente di Azure Arc for servers (anteprima) Connected Machine agent for Windows o Linux, potrebbe essere necessario riconfigurare l'agente, aggiornarlo o rimuoverlo dal computer se ha raggiunto la fase di ritiro nel ciclo di vita. È possibile gestire facilmente queste attività di manutenzione di routine manualmente o tramite l'automazione, che riduce sia gli errori operativi che le spese.

## <a name="upgrading-agent"></a>Agente di aggiornamento

L'agente del computer connesso di Azure per Windows e Linux può essere aggiornato alla versione più recente manualmente o automaticamente a seconda delle esigenze. Nella tabella seguente vengono descritti i metodi supportati per eseguire l'aggiornamento dell'agente.

| Sistema operativo | Metodo di aggiornamento |
|------------------|----------------|
| WINDOWS | Manualmente<br> Windows Update |
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

## <a name="remove-the-agent"></a>Rimuovere l'agente

Utilizzare una delle procedure seguenti per disinstallare l'agente Windows o Linux utilizzando la riga di comando o l'installazione guidata descritta in questa sezione. Prima di disinstallare l'agente, disconnettere il computer da Azure Arc per i server (anteprima) completando questi passaggi:Before uninstalling the agent, first disconnect the machine from Azure Arc for servers (preview) by completing these steps: 

1. Aprire Azure Arc per server (anteprima) passando al [portale di Azure](https://aka.ms/hybridmachineportal).

2. Selezionare la macchina virtuale nell'elenco, selezionare i puntini di sospensione (**...**) e quindi **Elimina**.

### <a name="windows-agent"></a>Agente Windows

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
