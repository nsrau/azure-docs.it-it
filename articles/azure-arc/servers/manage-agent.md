---
title: Gestione dell'agente Azure Arc for Servers (anteprima)
description: Questo articolo descrive le diverse attività di gestione che in genere vengono eseguite durante il ciclo di vita di Azure Arc per l'agente computer connesso ai server.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/29/2020
ms.topic: conceptual
ms.openlocfilehash: 685c56c7ef270acb416d4b76c6aceb8553e9a07f
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82581704"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Gestione e manutenzione dell'agente del computer connesso

Dopo la distribuzione iniziale di Azure Arc for Servers (anteprima) Connected Machine Agent per Windows o Linux, potrebbe essere necessario riconfigurare l'agente, aggiornarlo o rimuoverlo dal computer se ha raggiunto la fase di ritiro nel ciclo di vita. È possibile gestire facilmente queste attività di manutenzione di routine manualmente o tramite l'automazione, che riduce sia gli errori operativi che le spese.

## <a name="upgrading-agent"></a>Aggiornamento dell'agente

L'agente Azure Connected Machine per Windows e Linux può essere aggiornato alla versione più recente manualmente o automaticamente in base alle esigenze. Nella tabella seguente vengono descritti i metodi supportati per eseguire l'aggiornamento dell'agente.

| Sistema operativo | Metodo di aggiornamento |
|------------------|----------------|
| Windows | Manualmente<br> Windows Update |
| Ubuntu | [APT](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SuSE Linux Enterprise Server | [zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Agente Windows

Per aggiornare l'agente in un computer Windows alla versione più recente, l'agente è disponibile da Microsoft Update e può essere distribuito con il processo di gestione degli aggiornamenti software esistente. Può anche essere eseguito manualmente dal prompt dei comandi, da uno script o da un'altra soluzione di automazione o dalla procedura guidata dell'interfaccia utente `AzureConnectedMachine.msi`eseguendo. 

> [!NOTE]
> * Per aggiornare l'agente, è necessario disporre delle autorizzazioni di *amministratore* .
> * Per eseguire l'aggiornamento manualmente, è prima necessario scaricare e copiare il pacchetto di installazione in una cartella nel server di destinazione o da una cartella di rete condivisa. 

Se non si ha familiarità con le opzioni della riga di comando per i pacchetti Windows Installer, vedere le opzioni della riga di [comando msiexec standard](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) e le [Opzioni della riga di comando msiexec](https://docs.microsoft.com/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Per eseguire l'aggiornamento utilizzando l'installazione guidata

1. Accedere al computer con un account con diritti amministrativi.

2. Eseguire **AzureConnectedMachineAgent. msi** per avviare l'installazione guidata.

L'installazione guidata rileva se esiste una versione precedente e quindi esegue automaticamente un aggiornamento dell'agente. Al termine dell'aggiornamento, l'installazione guidata viene chiusa automaticamente.

#### <a name="to-upgrade-from-the-command-line"></a>Per eseguire l'aggiornamento dalla riga di comando

1. Accedere al computer con un account con diritti amministrativi.

2. Per aggiornare l'agente in modo invisibile all'utente e creare un file di `C:\Support\Logs` log del programma di installazione nella cartella, eseguire il comando seguente.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Agente Linux

Per aggiornare l'agente in un computer Linux alla versione più recente, sono necessari due comandi. Un comando per aggiornare l'indice del pacchetto locale con l'elenco dei pacchetti più recenti disponibili nei repository e un comando per aggiornare il pacchetto locale. 

> [!NOTE]
> Per aggiornare l'agente, è necessario disporre delle autorizzazioni di accesso alla *radice* o con un account con diritti elevati usando sudo.

#### <a name="upgrade-ubuntu"></a>Aggiornare Ubuntu

1. Per aggiornare l'indice del pacchetto locale con le modifiche più recenti apportate nei repository, eseguire il comando seguente:

    ```bash
    apt update
    ```

2. Per aggiornare il sistema, eseguire il comando seguente:

    ```bash
    apt upgrade
    ```

Le azioni del comando [apt](https://help.ubuntu.com/lts/serverguide/apt.html) , ad esempio l' `/var/log/dpkg.log` installazione e la rimozione di pacchetti, vengono registrate nel file di log.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Aggiornare Red Hat/CentOS/Amazon Linux

1. Per aggiornare l'indice del pacchetto locale con le modifiche più recenti apportate nei repository, eseguire il comando seguente:

    ```bash
    yum check-update
    ```

2. Per aggiornare il sistema, eseguire il comando seguente:

    ```bash
    yum update
    ```

Le `/var/log/yum.log` azioni del comando [yum](https://access.redhat.com/articles/yum-cheat-sheet) , ad esempio l'installazione e la rimozione di pacchetti, vengono registrate nel file di log. 

#### <a name="upgrade-suse-linux-enterprise"></a>Aggiornare SUSE Linux Enterprise

1. Per aggiornare l'indice del pacchetto locale con le modifiche più recenti apportate nei repository, eseguire il comando seguente:

    ```bash
    zypper refresh
    ```

2. Per aggiornare il sistema, eseguire il comando seguente:

    ```bash
    zypper update
    ```

Le azioni del comando [zypper](https://en.opensuse.org/Portal:Zypper) , ad esempio l' `/var/log/zypper.log` installazione e la rimozione di pacchetti, vengono registrate nel file di log. 

## <a name="about-the-azcmagent-tool"></a>Informazioni sullo strumento Azcmagent

Lo strumento Azcmagent (Azcmagent. exe) viene usato per configurare l'agente del computer connesso di Azure Arc per i server (anteprima) durante l'installazione o per modificare la configurazione iniziale dell'agente dopo l'installazione. Azcmagent. exe fornisce parametri della riga di comando per personalizzare l'agente e visualizzarne lo stato:

* **Connetti** : per connettere il computer ad Azure Arc

* **Disconnetti** : per disconnettere il computer da Azure Arc

* **Riconnettersi** : per riconnettere un computer disconnesso ad Azure Arc

* **Mostra-Visualizza** lo stato dell'agente e le relative proprietà di configurazione (nome del gruppo di risorse, ID sottoscrizione, versione e così via), che può essere utile per la risoluzione di un problema con l'agente.

* **-h o--help** -Mostra i parametri della riga di comando disponibili

    Per visualizzare ad esempio la guida dettagliata per il parametro **Riconnetti** , digitare `azcmagent reconnect -h`. 

* **-v o--verbose** -Abilita registrazione dettagliata

È possibile eseguire manualmente la **connessione**, la **disconnessione**e la **riconnessione** durante l'accesso interattivo oppure automatizzare l'uso della stessa entità servizio usata per caricare più agenti o con un [token di accesso](../../active-directory/develop/access-tokens.md)della piattaforma Microsoft Identity. Se non è stata usata un'entità servizio per registrare il computer con Azure Arc for Servers (anteprima), vedere l' [articolo](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) seguente per creare un'entità servizio.

### <a name="connect"></a>Connessione

Questo parametro specifica una risorsa in Azure Resource Manager che rappresenta il computer creato in Azure. La risorsa si trova nella sottoscrizione e nel gruppo di risorse specificati e i dati relativi al computer vengono archiviati nell'area di Azure specificata `--location` dall'impostazione. Se non è specificato, il nome predefinito della risorsa è il nome host del computer.

Un certificato corrispondente all'identità assegnata dal sistema del computer viene quindi scaricato e archiviato localmente. Una volta completato questo passaggio, il servizio metadati del computer connesso di Azure e l'agente di configurazione Guest iniziano la sincronizzazione con Azure Arc per i server (anteprima).

Per connettersi usando un'entità servizio, eseguire il comando seguente:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Per connettersi usando un token di accesso, eseguire il comando seguente:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Per connettersi con le credenziali di accesso con privilegi elevati (interattivo), eseguire il comando seguente:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Disconnetti

Questo parametro specifica una risorsa in Azure Resource Manager che rappresenta il computer eliminato in Azure. Non elimina l'agente dal computer. questa operazione deve essere eseguita come passaggio separato. Dopo la disconnessione del computer, se si vuole registrarlo di nuovo con Azure Arc per i server (anteprima), `azcmagent connect` usare per creare una nuova risorsa in Azure.

Per disconnettersi usando un'entità servizio, eseguire il comando seguente:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Per disconnettersi usando un token di accesso, eseguire il comando seguente:

`azcmagent disconnect --access-token <accessToken>`

Per disconnettersi con le credenziali di accesso con privilegi elevati (interattivo), eseguire il comando seguente:

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>Riconnetti

Questo parametro riconnette il computer già registrato o connesso con Azure Arc per i server (anteprima). Questa operazione può essere necessaria se il computer è stato disattivato, almeno 45 giorni, per la scadenza del certificato. Questo parametro usa le opzioni di autenticazione fornite per recuperare le nuove credenziali corrispondenti alla risorsa Azure Resource Manager che rappresenta il computer.

Questo comando richiede privilegi più elevati rispetto al ruolo di [onboarding del computer connesso di Azure](overview.md#required-permissions) .

Per riconnettersi usando un'entità servizio, eseguire il comando seguente:

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Per riconnettersi usando un token di accesso, eseguire il comando seguente:

`azcmagent reconnect --access-token <accessToken>`

Per riconnettersi con le credenziali di accesso con privilegi elevati (interattivo), eseguire il comando seguente:

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>Rimuovere l'agente

Eseguire uno dei metodi seguenti per disinstallare l'agente computer connesso Windows o Linux dal computer. La rimozione dell'agente non consente di annullare la registrazione del computer con Arc per i server (anteprima). si tratta di un processo separato eseguito quando non è più necessario gestire il computer in Azure.

### <a name="windows-agent"></a>Agente Windows

Entrambi i metodi seguenti consentono di rimuovere l'agente, ma non di rimuovere la cartella *C:\Program Files\AzureConnectedMachineAgent* nel computer.

#### <a name="uninstall-from-control-panel"></a>Eseguire la disinstallazione dal Pannello di controllo

1. Per disinstallare l'agente di Windows dalla macchina virtuale, eseguire le operazioni seguenti:

    a. Accedere al computer con un account dotato di autorizzazioni di amministratore.  
    b. In **Pannello di controllo** selezionare **Programmi e funzionalità**.  
    c. In **Programmi e funzionalità** selezionare **Agente Azure Connected Machine**, selezionare **Disinstalla** e quindi **Sì**.  

    >[!NOTE]
    > È anche possibile eseguire l'installazione guidata dell'agente facendo doppio clic sul pacchetto del programma di installazione **AzureConnectedMachineAgent.msi**.

#### <a name="uninstall-from-the-command-line"></a>Eseguire la disinstallazione dalla riga di comando

Per disinstallare manualmente l'agente dal prompt dei comandi o per usare un metodo automatizzato, ad esempio uno script, è possibile usare l'esempio seguente. Per prima cosa è necessario recuperare il codice prodotto, ovvero un GUID che rappresenta l'identificatore principale del pacchetto dell'applicazione, dal sistema operativo. La disinstallazione viene eseguita utilizzando la riga di comando msiexec. exe `msiexec /x {Product Code}`-.
    
1. Aprire l'editor del Registro di sistema.

2. Nella chiave del registro di sistema `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` cercare e copiare il GUID del codice prodotto.

3. È quindi possibile disinstallare l'agente usando msiexec usando gli esempi seguenti:

   * Dal tipo di riga di comando:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * È possibile eseguire gli stessi passaggi usando PowerShell:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Agente Linux

> [!NOTE]
> Per disinstallare l'agente, è necessario disporre delle autorizzazioni di accesso alla *radice* o con un account con diritti elevati usando sudo.

Per disinstallare l'agente Linux, il comando da usare dipende dal sistema operativo Linux.

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

## <a name="unregister-machine"></a>Annulla registrazione computer

Se si prevede di arrestare la gestione del computer con servizi di supporto in Azure, seguire questa procedura per annullare la registrazione del computer con Arc for Servers (anteprima). È possibile eseguire questi passaggi prima o dopo la rimozione dell'agente del computer connesso dal computer.

1. Aprire Azure Arc per server (anteprima) passando al [portale di Azure](https://aka.ms/hybridmachineportal).

2. Selezionare la macchina virtuale nell'elenco, selezionare i puntini di sospensione (**...**) e quindi **Elimina**.

## <a name="update-or-remove-proxy-settings"></a>Aggiornare o rimuovere le impostazioni proxy

Per configurare l'agente per la comunicazione con il servizio tramite un server proxy oppure rimuovere la configurazione dopo la distribuzione oppure utilizzare uno dei metodi seguenti per completare questa attività.

### <a name="windows"></a>Windows

Per impostare la variabile di ambiente del server proxy, eseguire il comando seguente:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

Per configurare l'agente in modo che interrompa la comunicazione tramite un server proxy, eseguire il comando seguente per rimuovere la variabile di ambiente del server proxy e riavviare il servizio Agent:

```powershell
[Environment]::SetEnvironmentVariable("https_proxy",$null,"Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable removed.
Restart-Service -Name himds
```

### <a name="linux"></a>Linux

Per impostare il server proxy, eseguire il comando seguente dalla directory in cui è stato scaricato il pacchetto di installazione dell'agente:

```bash
# Reconfigure the connected machine agent and set the proxy server.
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

Per configurare l'agente in modo che interrompa la comunicazione tramite un server proxy, eseguire il comando seguente per rimuovere la configurazione del proxy:

```bash
sudo azcmagent_proxy remove
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come gestire il computer usando [criteri di Azure](../../governance/policy/overview.md), ad esempio la [configurazione Guest](../../governance/policy/concepts/guest-configuration.md)della VM, verificando che il computer stia segnalando l'area di lavoro log Analytics prevista, abilitare il monitoraggio con [monitoraggio di Azure con le macchine virtuali](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)e molto altro ancora.

- Altre informazioni sull' [agente log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è obbligatorio quando si vuole monitorare in modo proattivo il sistema operativo e i carichi di lavoro in esecuzione nel computer, gestirli con manuali operativi di automazione o funzionalità come Gestione aggiornamenti o usare altri servizi di Azure come il [Centro sicurezza di Azure](../../security-center/security-center-intro.md).