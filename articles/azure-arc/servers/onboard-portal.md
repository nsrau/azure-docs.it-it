---
title: Connettere macchine virtuali ibride ad Azure dal portale di Azure
description: Questo articolo illustra come installare l'agente e connettere macchine virtuali ad Azure usando Azure Arc per server (anteprima) dal portale di Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/24/2020
ms.topic: conceptual
ms.openlocfilehash: 8bde9a9e9227f0c8715b38a9a376fad3015c7bf3
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586258"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Connettere macchine virtuali ibride ad Azure dal portale di Azure

È possibile abilitare Azure Arc per server (anteprima) per una singola o un numero limitato di macchine virtuali Windows o Linux nell'ambiente eseguendo manualmente un set di passaggi. Si può anche usare un metodo automatico eseguendo uno script di modello fornito da Microsoft. Lo script automatizza il download e l'installazione di entrambi gli agenti.

Con questo metodo sono necessarie le autorizzazioni di amministratore sulla macchina virtuale per installare e configurare l'agente. In Linux, tramite l'account radice, e in Windows, si è membri del gruppo Administrators locale.

Prima di iniziare, esaminare i [prerequisiti](overview.md#prerequisites) e verificare che la sottoscrizione e le risorse soddisfino i requisiti.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Generare lo script di installazione dal portale di Azure

Lo script per automatizzare il download e l'installazione e stabilire la connessione con Azure Arc è disponibile nel portale di Azure. Per completare il processo, seguire questa procedura:

1. Nel browser passare al [portale di Azure](https://aka.ms/hybridmachineportal).

1. Nella pagina **Macchine virtuali - Azure Arc** selezionare **Aggiungi** in alto a sinistra oppure **Create machine - Azure Arc** (Crea macchina virtuale - Azure Arc) nella parte inferiore del riquadro centrale. 

1. Nella pagina **Selezionare un metodo** selezionare il riquadro **Aggiungi macchine virtuali tramite script interattivo** e quindi selezionare **Genera script**.

1. Nella pagina **Genera script** selezionare la sottoscrizione e il gruppo di risorse in cui si vuole che la macchina virtuale venga gestita in Azure. Selezionare una località di Azure in cui verranno archiviati i metadati della macchina virtuale.

    >[!NOTE]
    >Azure Arc per server (anteprima) supporta solo le aree seguenti:
    >- Stati Uniti occidentali 2
    >- Europa occidentale
    >- Asia occidentale
    >
    >Per altre considerazioni sulla [selezione di un'area,](overview.md#supported-regions) vedere l'articolo introduttivo.

1. Nell'elenco a discesa **Sistema operativo** della pagina **Genera script** selezionare il sistema operativo in cui verrà eseguito lo script.

1. Se il computer sta comunicando tramite un server proxy per la connessione a Internet, selezionare **Avanti: server proxy**. 
1. Nella scheda **Server proxy** specificare l'indirizzo IP del server proxy o il nome e il numero di porta che la macchina virtuale userà per comunicare con il server proxy. Immettere il valore nel formato `http://<proxyURL>:<proxyport>`. 
1. Selezionare **Rivedi e genera**.

1. Nella scheda **Rivedi e genera** esaminare le informazioni di riepilogo e quindi selezionare **Scarica**. Se occorre ancora apportare modifiche, selezionare **Indietro**.

## <a name="install-and-validate-the-agent-on-windows"></a>Installare e convalidare l'agente in Windows

### <a name="install-manually"></a>Eseguire l'installazione manuale
È possibile installare manualmente l'agente Connected Machine eseguendo il pacchetto di Windows Installer *AzureConnectedMachineAgent.msi*. 

> [!NOTE]
> * Per installare o disinstallare l'agente, sono necessarie le autorizzazioni di *Amministratore*.
> * Occorre pima di tutto scaricare e copiare il pacchetto di installazione in una cartella nel server di destinazione o da una cartella di rete condivisa. Se si esegue il pacchetto di installazione senza opzioni, si avvia una procedura guidata che consente di completare l'installazione dell'agente in modo interattivo.

Se la macchina virtuale deve comunicare tramite un server proxy con il servizio, dopo l'installazione dell'agente è necessario eseguire un comando descritto più avanti in questo articolo. Il comando imposta la variabile di ambiente di sistema del server proxy `https_proxy`.

La tabella seguente illustra i parametri supportati dal programma di installazione per l'agente dalla riga di comando.

| Parametro | Descrizione |
|:--|:--|
| /? | Restituisce un elenco delle opzioni della riga di comando. |
| /S | Esegue un'installazione invisibile all'utente, senza interazione da parte dell'utente. |

Per eseguire, ad esempio, il programma di installazione con il parametro `/?`, immettere `msiexec.exe /i AzureConnectedMachineAgent.msi /?`.

Per impostazione predefinita, i file dell'agente Connected Machine vengono installati in *C:\Programmi\AzureConnectedMachineAgent*. Se l'agente non si avvia dopo l'installazione, controllare i log per vedere le informazioni dettagliate sull'errore. La directory dei log è *%Programfiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Eseguire l'installazione con il metodo con script

1. Accedere al server.

1. Aprire un prompt dei comandi di PowerShell con privilegi elevati.

1. Passare alla cartella o alla condivisione in cui è stato copiato lo script ed eseguirlo sul server eseguendo lo script `./OnboardingScript.ps1`.

### <a name="configure-the-agent-proxy-setting"></a>Configurare l'impostazione del proxy agente

Per impostare la variabile di ambiente del server proxy, eseguire il comando seguente:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>L'agente non supporta l'impostazione dell'autenticazione proxy in questa versione di anteprima.
>

### <a name="configure-agent-communication"></a>Configurare la comunicazione dell'agente

Dopo aver installato l'agente, è necessario configurarlo per comunicare con il servizio Azure Arc eseguendo il comando seguente:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Installare e convalidare l'agente in Linux

L'agente Connected Machine per Linux viene fornito nel formato di pacchetto preferito per la distribuzione (RPM o DEB) ospitata nel [repository dei pacchetti](https://packages.microsoft.com/) di Microsoft. Il [bundle di script della shell `Install_linux_azcmagent.sh`](https://aka.ms/azcmagent) esegue le azioni seguenti:

- Configura il computer host per scaricare il pacchetto dell'agente da packages.microsoft.com.
- Installa il pacchetto del provider di risorse ibride.

Facoltativamente, è possibile configurare l'agente con le informazioni del proxy includendo il parametro `--proxy "{proxy-url}:{proxy-port}"`.

Lo script contiene anche la logica per identificare le distribuzioni supportate e non supportate e verifica le autorizzazioni necessarie per eseguire l'installazione. 

L'esempio seguente scarica l'agente e lo installa:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Per scaricare e installare l'agente, incluso il parametro `--proxy` per configurare l'agente per la comunicazione tramite il server proxy, eseguire i comandi seguenti:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>Configurare la comunicazione dell'agente

Dopo aver installato l'agente, configurarlo per comunicare con il servizio Azure Arc eseguendo il comando seguente:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>Verificare la connessione con Azure Arc

Dopo aver installato l'agente e averlo configurato per la connessione ad Azure Arc per server (anteprima), passare al portale di Azure per verificare che il server sia stato connesso correttamente. Visualizzare le proprie macchine virtuali nel [portale di Azure](https://aka.ms/hybridmachineportal).

![Una connessione server riuscita](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Eseguire la pulizia

Per disconnettere una macchina virtuale da Azure Arc per server (anteprima), seguire questa procedura:

1. Aprire Azure Arc per server (anteprima) passando al [portale di Azure](https://aka.ms/hybridmachineportal).

1. Selezionare la macchina virtuale nell'elenco, selezionare i puntini di sospensione ( **...** ) e quindi **Elimina**.

1. Per disinstallare l'agente di Windows dalla macchina virtuale, eseguire le operazioni seguenti:

    a. Accedere al computer con un account dotato di autorizzazioni di amministratore.  
    b. In **Pannello di controllo** selezionare **Programmi e funzionalità**.  
    c. In **Programmi e funzionalità** selezionare **Agente Azure Connected Machine**, selezionare **Disinstalla** e quindi **Sì**.  

    >[!NOTE]
    > È anche possibile eseguire l'installazione guidata dell'agente facendo doppio clic sul pacchetto del programma di installazione **AzureConnectedMachineAgent.msi**.

    Se si desidera generare uno script per la rimozione dell'agente, è possibile utilizzare l'esempio seguente, che recupera il codice prodotto e disinstalla l'agente utilizzando la riga di comando msiexec. exe-`msiexec /x {Product Code}`. A tale scopo, procedere come indicato di seguito:  
    
    a. Aprire l'editor del Registro di sistema.  
    b. Nella chiave del registro di sistema `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` cercare e copiare il GUID del codice prodotto.  
    c. È quindi possibile disinstallare l'agente usando Msiexec.

    L'esempio seguente illustra come disinstallare l'agente:

    ```powershell
    Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
    Get-ItemProperty | `
    Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
    ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
    ```

1. Per disinstallare l'agente Linux, il comando da usare dipende dal sistema operativo Linux.

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

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come gestire il computer usando [criteri di Azure](../../governance/policy/overview.md), ad esempio la [configurazione Guest](../../governance/policy/concepts/guest-configuration.md)della VM, verificando che il computer stia segnalando l'area di lavoro log Analytics prevista, abilitare il monitoraggio con [monitoraggio di Azure con le macchine virtuali](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)e molto altro ancora.

- Altre informazioni sull' [agente log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si vuole monitorare in modo proattivo il sistema operativo e i carichi di lavoro in esecuzione nella macchina virtuale, gestirla con runbook di automazione o soluzioni come Gestione aggiornamenti o usare altri servizi di Azure, come il [Centro sicurezza di Azure](../../security-center/security-center-intro.md).
