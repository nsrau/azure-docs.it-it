---
title: Gestione dell'agente server abilitati per Azure Arc
description: Questo articolo descrive le diverse attività di gestione che in genere vengono eseguite durante il ciclo di vita di Azure Arc Enabled Servers Connected computer Agent.
ms.date: 09/09/2020
ms.topic: conceptual
ms.openlocfilehash: 5161bd097809f1feb6f84b07e07c63d06d0a9c94
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254993"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Gestione e manutenzione dell'agente Azure Connected Machine

Dopo la distribuzione iniziale di Azure Arc Enabled Servers Connected Agent per Windows o Linux, potrebbe essere necessario riconfigurare l'agente, aggiornarlo o rimuoverlo dal computer se ha raggiunto la fase di ritiro nel ciclo di vita. È possibile gestire facilmente queste attività di manutenzione di routine manualmente o tramite l'automazione, che riduce sia gli errori operativi che le spese.

## <a name="before-uninstalling-agent"></a>Prima della disinstallazione dell'agente

Prima di rimuovere l'agente del computer connesso dal server abilitato per Arc, tenere presente quanto segue per evitare problemi imprevisti o i costi aggiunti alla fattura di Azure:

* Se le estensioni di VM di Azure sono state distribuite in un server abilitato e si rimuove l'agente del computer connesso o si elimina la risorsa che rappresenta il server con Arc Enabled nel gruppo di risorse, le estensioni continuano a essere eseguite ed eseguono le normali operazioni.

* Se si elimina la risorsa che rappresenta il server abilitato per l'arco nel gruppo di risorse, ma non si disinstalla l'estensione della VM, quando si registra di nuovo il computer, non sarà possibile gestire le estensioni VM installate.

Per i server o i computer che non si vuole più gestire con i server abilitati per Azure Arc, è necessario seguire questi passaggi per arrestare correttamente la gestione:

1. Rimuovere le estensioni della VM dal computer o dal server. Di seguito sono riportati i passaggi.

2. Disconnettere il computer da Azure ARC usando uno dei metodi seguenti:

    * Esecuzione del `azcmagent disconnect` comando sul computer o sul server.

    * Dal server abilitato per l'arco registrato selezionato nel portale di Azure selezionando **Elimina** nella barra superiore.

    * Usare l' [interfaccia](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-cli#delete-resource) della riga di comando di Azure o [Azure PowerShell](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell#delete-resource). Per il `ResourceType` parametro usare `Microsoft.HybridCompute/machines` .

3. Disinstallare l'agente dal computer o dal server. Attenersi alla procedura riportata di seguito.

## <a name="upgrading-agent"></a>Aggiornamento dell'agente

L'agente Azure Connected Machine per Windows e Linux può essere aggiornato alla versione più recente manualmente o automaticamente in base alle esigenze. Nella tabella seguente vengono descritti i metodi supportati per eseguire l'aggiornamento dell'agente.

| Sistema operativo | Metodo di aggiornamento |
|------------------|----------------|
| Windows | Manualmente<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SuSE Linux Enterprise Server | [zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) |

### <a name="windows-agent"></a>Agente Windows

Il pacchetto di aggiornamento per l'agente Azure Connected Machine per Windows è disponibile in:

* Microsoft Update

* [Catalogo di Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx)

* [Pacchetto di installazione dell'agente Connected Machine per Windows](https://aka.ms/AzureConnectedMachineAgent) dall'Area download Microsoft.

L'agente può essere aggiornato mediante diversi metodi per supportare il processo di gestione degli aggiornamenti software. Oltre al recupero da Microsoft Update, è possibile scaricare ed eseguire manualmente l'aggiornamento dal prompt dei comandi, da uno script o da un'altra soluzione di automazione oppure seguire la procedura guidata dell'interfaccia utente eseguendo `AzureConnectedMachine.msi`.

> [!NOTE]
> * Per aggiornare l'agente, sono necessarie le autorizzazioni di *Amministratore*.
> * Per eseguire l'aggiornamento manuale, occorre pima di tutto scaricare e copiare il pacchetto di installazione in una cartella nel server di destinazione o da una cartella di rete condivisa. 

Se non si ha familiarità con le opzioni della riga di comando per i pacchetti Windows Installer, vedere [Opzioni della riga di comando di Msiexec standard](/windows/win32/msi/standard-installer-command-line-options) e [Opzioni della riga di comando di Msiexec](/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Per eseguire l'aggiornamento mediante l'installazione guidata

1. Accedere al computer con un account con diritti amministrativi.

2. Eseguire **AzureConnectedMachineAgent.msi** per avviare l'installazione guidata.

L'installazione guidata rileva se esiste una versione precedente e quindi esegue automaticamente un aggiornamento dell'agente. Al termine dell'aggiornamento, l'installazione guidata viene chiusa automaticamente.

#### <a name="to-upgrade-from-the-command-line"></a>Per eseguire l'aggiornamento dalla riga di comando

1. Accedere al computer con un account con diritti amministrativi.

2. Per aggiornare l'agente in modo invisibile all'utente e creare un file di log del programma di installazione nella cartella `C:\Support\Logs`, eseguire il comando seguente.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Agente Linux

Per aggiornare l'agente in un computer Linux alla versione più recente, sono necessari due comandi. Un comando permette di aggiornare l'indice del pacchetto locale con l'elenco dei pacchetti più recenti disponibili nei repository, mentre l'altro permette di aggiornare il pacchetto locale.

È possibile scaricare il pacchetto dell'agente più recente dal [repository del pacchetto](https://packages.microsoft.com/) di Microsoft.

> [!NOTE]
> Per aggiornare l'agente, è necessario disporre delle autorizzazioni di accesso *radice* o di un account con diritti elevati mediante Sudo.

#### <a name="upgrade-ubuntu"></a>Aggiornare Ubuntu

1. Per aggiornare l'indice del pacchetto locale con le modifiche più recenti apportate nei repository, eseguire il comando seguente:

    ```bash
    apt update
    ```

2. Per aggiornare il sistema, eseguire il comando seguente:

    ```bash
    apt upgrade
    ```

Le azioni del comando [apt](https://help.ubuntu.com/lts/serverguide/apt.html), ad esempio l'installazione e la rimozione di pacchetti, vengono registrate nel file di log `/var/log/dpkg.log`.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Aggiornare Red Hat/CentOS/Amazon Linux

1. Per aggiornare l'indice del pacchetto locale con le modifiche più recenti apportate nei repository, eseguire il comando seguente:

    ```bash
    yum check-update
    ```

2. Per aggiornare il sistema, eseguire il comando seguente:

    ```bash
    yum update
    ```

Le azioni del comando [yum](https://access.redhat.com/articles/yum-cheat-sheet), ad esempio l'installazione e la rimozione di pacchetti, vengono registrate nel file di log `/var/log/yum.log`. 

#### <a name="upgrade-suse-linux-enterprise"></a>Aggiornare SUSE Linux Enterprise

1. Per aggiornare l'indice del pacchetto locale con le modifiche più recenti apportate nei repository, eseguire il comando seguente:

    ```bash
    zypper refresh
    ```

2. Per aggiornare il sistema, eseguire il comando seguente:

    ```bash
    zypper update
    ```

Le azioni del comando [zypper](https://en.opensuse.org/Portal:Zypper), ad esempio l'installazione e la rimozione di pacchetti, vengono registrate nel file di log `/var/log/zypper.log`. 

## <a name="about-the-azcmagent-tool"></a>Informazioni sullo strumento Azcmagent

Lo strumento Azcmagent (Azcmagent.exe) viene usato per configurare l'agente computer connesso dei server abilitati per Azure Arc durante l'installazione o modificare la configurazione iniziale dell'agente dopo l'installazione. Azcmagent.exe fornisce parametri della riga di comando per personalizzare l'agente e visualizzarne lo stato:

* **Connetti**: permette di connettere il computer ad Azure Arc

* **Disconnetti**: permette di disconnettere il computer da Azure Arc

* **Riconnetti**: permette di riconnettere un computer disconnesso ad Azure Arc

* **Mostra**: permette di visualizzare lo stato dell'agente e le relative proprietà di configurazione (nome del gruppo di risorse, ID sottoscrizione, versione e così via), che possono essere utili per la risoluzione di eventuali problemi con l'agente.

* **-h o --help**: mostra i parametri della riga di comando disponibili

    Per visualizzare ad esempio la guida dettagliata per il parametro **Riconnetti**, digitare `azcmagent reconnect -h`. 

* **-v o --verbose**: permette di abilitare la registrazione dettagliata

È possibile eseguire un comando **Connetti**, **Disconnetti** e **Riconnetti** manualmente durante l'accesso in modo interattivo oppure automatizzarlo mediante la stessa entità servizio usata per caricare più agenti o con un [token di accesso](../../active-directory/develop/access-tokens.md) della piattaforma di identità Microsoft. Se non è stata usata un'entità servizio per registrare il computer con i server abilitati per Azure Arc, vedere l' [articolo](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) seguente per creare un'entità servizio.

>[!NOTE]
>Per eseguire **azcmagent**, è necessario disporre delle autorizzazioni di accesso alla *radice* nei computer Linux.

### <a name="connect"></a>Connessione

Questo parametro specifica una risorsa in Azure Resource Manager per indicare che il computer è stato creato in Azure. La risorsa si trova nella sottoscrizione e nel gruppo di risorse specificati e i dati relativi al computer vengono archiviati nell'area di Azure specificata dall'impostazione `--location`. Se non è specificato, il nome predefinito della risorsa è il nome host del computer.

Un certificato corrispondente all'identità assegnata dal sistema del computer viene quindi scaricato e archiviato localmente. Una volta completato questo passaggio, il servizio metadati del computer connesso di Azure e l'agente di configurazione Guest iniziano la sincronizzazione con i server abilitati per Azure Arc.

Per connettersi mediante un'entità servizio, eseguire il comando seguente:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Per connettersi mediante un token di accesso, eseguire il comando seguente:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Per connettersi con le credenziali di accesso con privilegi elevati (interattive), eseguire il comando seguente:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Disconnetti

Questo parametro specifica una risorsa in Azure Resource Manager per indicare che il computer è stato eliminato in Azure. Il parametro non elimina l'agente dal computer: questa operazione deve essere eseguita come passaggio separato. Dopo la disconnessione del computer, se si vuole registrarlo di nuovo con i server abilitati per Azure Arc, usare `azcmagent connect` per creare una nuova risorsa in Azure.

> [!NOTE]
> Se sono state distribuite una o più estensioni di VM di Azure nel server abilitato per Arc e si elimina la registrazione in Azure, le estensioni vengono ancora installate. È importante comprendere che, a seconda dell'estensione installata, sta eseguendo attivamente la relativa funzione. I computer che devono essere ritirati o non più gestiti da server abilitati per Arc devono prima rimuovere le estensioni prima di rimuovere la registrazione da Azure.

Per disconnettersi mediante un'entità servizio, eseguire il comando seguente:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Per disconnettersi mediante un token di accesso, eseguire il comando seguente:

`azcmagent disconnect --access-token <accessToken>`

Per disconnettersi con le credenziali di accesso con privilegi elevati (interattive), eseguire il comando seguente:

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>Riconnetti

> [!WARNING]
> Il `reconnect` comando è deprecato e non deve essere usato. Il comando verrà rimosso in una versione futura dell'agente e gli agenti esistenti non saranno in grado di completare la richiesta di riconnessione. Disconnettere [disconnect](#disconnect) il computer, quindi [riconnetterlo](#connect) .

Questo parametro riconnette il computer già registrato o connesso con i server abilitati per Azure Arc. Questa operazione può essere necessaria, se il computer è stato spento per almeno 45 giorni, per la scadenza del certificato. Questo parametro usa le opzioni di autenticazione fornite per recuperare le nuove credenziali corrispondenti alla risorsa di Azure Resource Manager che rappresenta il computer.

Questo comando richiede privilegi più elevati rispetto al ruolo di [Onboarding di Azure Connected Machine](agent-overview.md#required-permissions).

Per connettersi nuovamente mediante un'entità servizio, eseguire il comando seguente:

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Per connettersi nuovamente mediante un token di accesso, eseguire il comando seguente:

`azcmagent reconnect --access-token <accessToken>`

Per connettersi nuovamente con le credenziali di accesso con privilegi elevati (interattive), eseguire il comando seguente:

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>Rimuovere l'agente

Eseguire uno dei metodi seguenti per disinstallare l'agente Azure Connected Machine di Windows o Linux dal computer. La rimozione dell'agente non annulla la registrazione del computer con i server abilitati per Arc o rimuove le estensioni di VM di Azure installate. È necessario eseguire questi passaggi separatamente quando non è più necessario gestire il computer in Azure e devono essere completati prima di disinstallare l'agente.

### <a name="windows-agent"></a>Agente Windows

Entrambi i metodi seguenti consentono di rimuovere l'agente, ma non di rimuovere la cartella *C:\Program Files\AzureConnectedMachineAgent* sul computer.

#### <a name="uninstall-from-control-panel"></a>Eseguire la disinstallazione dal Pannello di controllo

1. Per disinstallare l'agente di Windows dalla macchina virtuale, eseguire le operazioni seguenti:

    a. Accedere al computer con un account dotato di autorizzazioni di amministratore.  
    b. In **Pannello di controllo** selezionare **Programmi e funzionalità**.  
    c. In **Programmi e funzionalità** selezionare **Agente Azure Connected Machine**, selezionare **Disinstalla** e quindi **Sì**.  

    >[!NOTE]
    > È anche possibile eseguire l'installazione guidata dell'agente facendo doppio clic sul pacchetto del programma di installazione **AzureConnectedMachineAgent.msi**.

#### <a name="uninstall-from-the-command-line"></a>Eseguire la disinstallazione dalla riga di comando

Per disinstallare manualmente l'agente dal prompt dei comandi o usare un metodo automatizzato, ad esempio uno script, è possibile usare l'esempio seguente. Per prima cosa è necessario recuperare il codice prodotto, ovvero un GUID che rappresenta l'identificatore principale del pacchetto dell'applicazione, dal sistema operativo. La disinstallazione viene eseguita tramite la riga di comando Msiexec.exe - `msiexec /x {Product Code}`.

1. Aprire l'editor del Registro di sistema.

2. Nella chiave del registro di sistema `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` cercare e copiare il GUID del codice prodotto.

3. È quindi possibile disinstallare l'agente usando Msiexec, avvalendosi degli esempi seguenti:

   * Dal tipo riga di comando:

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
> Per disinstallare l'agente, è necessario disporre delle autorizzazioni di accesso *radice* o di un account con diritti elevati mediante Sudo.

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

## <a name="unregister-machine"></a>Annullare la registrazione di un computer

Se si prevede di arrestare la gestione del computer con servizi di supporto in Azure, eseguire la procedura seguente per annullare la registrazione del computer con i server abilitati per Arc. È possibile eseguire questi passaggi prima o dopo la rimozione dell'agente Azure Connected Machine dal computer.

1. Aprire i server abilitati per Azure Arc passando al [portale di Azure](https://aka.ms/hybridmachineportal).

2. Selezionare la macchina virtuale nell'elenco, selezionare i puntini di sospensione ( **...** ) e quindi **Elimina**.

## <a name="update-or-remove-proxy-settings"></a>Aggiornare o rimuovere le impostazioni proxy

Per configurare l'agente per comunicare con il servizio tramite un server proxy o rimuovere questa configurazione dopo la distribuzione, usare uno dei modi seguenti per completare questa attività.

### <a name="windows"></a>Windows

Per impostare la variabile di ambiente del server proxy, eseguire il comando seguente:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

Per configurare l'agente in modo che interrompa la comunicazione tramite un server proxy, eseguire il comando seguente per rimuovere la variabile di ambiente del server proxy e riavviare il servizio agente:

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

* Le informazioni sulla risoluzione dei problemi sono reperibili nella [Guida alla risoluzione dei problemi relativi all'agente del computer connesso](troubleshoot-agent-onboard.md).

* Informazioni su come gestire il computer usando i [criteri di Azure](../../governance/policy/overview.md), ad esempio la configurazione di VM [Guest](../../governance/policy/concepts/guest-configuration.md), verificare che il computer stia segnalando l'area di lavoro Log Analytics prevista, abilitare il monitoraggio con [Monitoraggio di Azure con macchine virtuali](../../azure-monitor/insights/vminsights-enable-policy.md) e molto altro ancora.

* Altre informazioni sull'[agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si desidera raccogliere dati di monitoraggio del carico di lavoro e del sistema operativo, gestirli con manuali operativi di automazione o funzionalità come Gestione aggiornamenti o usare altri servizi di Azure come il [Centro sicurezza di Azure](../../security-center/security-center-intro.md).
