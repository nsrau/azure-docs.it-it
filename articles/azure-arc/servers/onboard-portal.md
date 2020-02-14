---
title: Connetti le macchine ibride ad Azure dalla portale di Azure
description: Questo articolo illustra come installare l'agente e connettere i computer ad Azure usando Azure Arc per i server (anteprima) dal portale di Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/12/2020
ms.topic: conceptual
ms.openlocfilehash: 12fc29cf12fba6325af3197e727d94b3073ef2ff
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192311"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Connetti le macchine ibride ad Azure dalla portale di Azure

È possibile abilitare Azure Arc per i server (anteprima) per uno o un numero ridotto di computer Windows o Linux nell'ambiente eseguendo manualmente un set di passaggi. In alternativa, è possibile usare un metodo automatico eseguendo uno script modello fornito. Questo script automatizza il download e l'installazione di entrambi gli agenti.

Per questo metodo è necessario disporre delle autorizzazioni di amministratore sul computer per installare e configurare l'agente. In Linux, usando l'account radice e in Windows, si è membri del gruppo Administrators locale.

Prima di iniziare, assicurarsi di esaminare i [prerequisiti](overview.md#prerequisites) e verificare che la sottoscrizione e le risorse soddisfino i requisiti.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Genera lo script di installazione dal portale di Azure

Lo script per automatizzare il download e l'installazione e stabilire la connessione con Azure Arc è disponibile dal portale di Azure. Per completare il processo, effettuare le operazioni seguenti:

1. Dal browser passare al [portale di Azure](https://aka.ms/hybridmachineportal).

1. Nella pagina **macchine-Azure Arc** selezionare **Aggiungi**, in alto a sinistra, oppure l'opzione **crea computer-Azure Arc** nella parte inferiore del riquadro centrale. 

1. Nella pagina **Selezione metodo** selezionare il riquadro **Aggiungi computer con script interattivo** e quindi selezionare **Genera script**.

1. Nella pagina **Genera script** selezionare la sottoscrizione e il gruppo di risorse in cui si vuole che il computer venga gestito in Azure. Selezionare una località di Azure in cui verranno archiviati i metadati del computer.

    >[!NOTE]
    >Azure Arc for Servers (anteprima) supporta solo le aree seguenti:
    >- WestUS2
    >- Europa occidentale
    >- WestAsia
    >
    >Per altre considerazioni sulla [selezione di un'area,](overview.md#supported-regions) vedere l'articolo introduttivo.

1. Nell'elenco a discesa **sistema operativo** della pagina **Genera script** selezionare il sistema operativo in cui verrà eseguito lo script.

1. Se il computer sta comunicando tramite un server proxy per la connessione a Internet, selezionare **Avanti: server proxy**. 
1. Nella scheda **server proxy** , specificare l'indirizzo IP del server proxy o il nome e il numero di porta che il computer utilizzerà per comunicare con il server proxy. Immettere il valore nel formato `http://<proxyURL>:<proxyport>`. 
1. Selezionare **Verifica e genera**.

1. Nella scheda **Verifica e genera** esaminare le informazioni di riepilogo e quindi selezionare **Scarica**. Se è ancora necessario apportare modifiche, selezionare **indietro**.

## <a name="install-and-validate-the-agent-on-windows"></a>Installare e convalidare l'agente in Windows

### <a name="install-manually"></a>Eseguire l'installazione manuale
È possibile installare manualmente l'agente del computer connesso eseguendo il pacchetto di Windows Installer *AzureConnectedMachineAgent. msi*. 

> [!NOTE]
> * Per installare o disinstallare l'agente, è necessario disporre delle autorizzazioni di *amministratore* .
> * È innanzitutto necessario scaricare e copiare il pacchetto di installazione in una cartella nel server di destinazione o da una cartella di rete condivisa. Se si esegue il pacchetto di installazione senza opzioni, viene avviata un'installazione guidata che è possibile seguire per installare l'agente in modo interattivo.

Se il computer deve comunicare tramite un server proxy al servizio, dopo l'installazione dell'agente è necessario eseguire un comando descritto più avanti in questo articolo. Imposta la variabile di ambiente del sistema del server proxy `https_proxy`.

La tabella seguente illustra i parametri supportati dal programma di installazione per l'agente dalla riga di comando.

| Parametro | Descrizione |
|:--|:--|
| /? | Restituisce un elenco delle opzioni della riga di comando. |
| /S | Esegue un'installazione invisibile all'utente, senza interazione da parte dell'utente. |

Ad esempio, per eseguire il programma di installazione con il parametro `/?`, immettere `msiexec.exe /i AzureConnectedMachineAgent.msi /?`.

Per impostazione predefinita, i file per l'agente del computer connesso sono installati in *C:\Program Files\AzureConnectedMachineAgent* . Se l'avvio dell'agente non riesce al termine dell'installazione, controllare i registri per informazioni dettagliate sull'errore. La directory di log è *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Eseguire l'installazione con il metodo con script

1. Accedere al server.

1. Aprire un prompt dei comandi di PowerShell con privilegi elevati.

1. Passare alla cartella o alla condivisione in cui è stato copiato lo script ed eseguirlo nel server eseguendo lo script `./OnboardingScript.ps1`.

### <a name="configure-the-agent-proxy-setting"></a>Configurare l'impostazione proxy agente

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

Dopo aver installato l'agente, è necessario configurare l'agente per comunicare con il servizio Azure Arc eseguendo il comando seguente:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Installare e convalidare l'agente in Linux

L'agente computer connesso per Linux viene fornito nel formato di pacchetto preferito per la distribuzione (. RPM o. DEB) ospitata nel [repository dei pacchetti](https://packages.microsoft.com/)Microsoft. Il [bundle di script della shell `Install_linux_azcmagent.sh`](https://aka.ms/azcmagent) esegue le azioni seguenti:

- Configura il computer host per scaricare il pacchetto dell'agente da packages.microsoft.com.
- Installa il pacchetto del provider di risorse ibride.

Facoltativamente, è possibile configurare l'agente con le informazioni sul proxy includendo il parametro `--proxy "{proxy-url}:{proxy-port}"`.

Lo script contiene inoltre la logica per identificare le distribuzioni supportate e non supportate e verifica le autorizzazioni necessarie per eseguire l'installazione. 

Nell'esempio seguente viene scaricato e installato l'agente:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Per scaricare e installare l'agente, incluso il parametro `--proxy` per la configurazione dell'agente per la comunicazione tramite il server proxy, eseguire i comandi seguenti:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>Configurare la comunicazione dell'agente

Dopo aver installato l'agente, configurarlo per la comunicazione con il servizio Azure Arc eseguendo il comando seguente:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>Verificare la connessione con Azure Arc

Dopo aver installato l'agente e configurato per la connessione ad Azure Arc for Servers (anteprima), passare alla portale di Azure per verificare che il server sia stato connesso correttamente. Visualizzare i computer nell' [portale di Azure](https://aka.ms/hybridmachineportal).

![Una connessione server riuscita](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Eseguire la pulizia

Per disconnettere un computer da Azure Arc for Servers (anteprima), seguire questa procedura:

1. Aprire Azure Arc per i server (anteprima) passando alla [portale di Azure](https://aka.ms/hybridmachineportal).

1. Selezionare il computer nell'elenco, selezionare i puntini di sospensione ( **...** ) e quindi selezionare **Elimina**.

1. Per disinstallare l'agente Windows dal computer, eseguire le operazioni seguenti:

    a. Accedere al computer con un account che disponga di autorizzazioni di amministratore.  
    b. Nel **Pannello di controllo**selezionare **programmi e funzionalità**.  
    c. In **programmi e funzionalità**selezionare **Agente computer connesso di Azure**, selezionare **Disinstalla**e quindi selezionare **Sì**.  

    >[!NOTE]
    > È inoltre possibile eseguire l'installazione guidata agente facendo doppio clic sul pacchetto del programma di installazione **AzureConnectedMachineAgent. msi** .

    Se si desidera generare uno script per la rimozione dell'agente, è possibile utilizzare l'esempio seguente, che recupera il codice prodotto e disinstalla l'agente utilizzando la riga di comando msiexec. exe-`msiexec /x {Product Code}`. A tale scopo, procedere come indicato di seguito:  
    
    a. Aprire l'Editor del Registro di sistema.  
    b. In chiave del registro di sistema `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`cercare e copiare il GUID del codice prodotto.  
    c. È quindi possibile disinstallare l'agente usando msiexec.

    Nell'esempio seguente viene illustrato come disinstallare l'agente:

    ```powershell
    Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
    Get-ItemProperty | `
    Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
    ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
    ```

1. Per disinstallare l'agente Linux, eseguire il comando seguente:

      ```bash
      sudo apt purge hybridagent
      ```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come gestire il computer usando [criteri di Azure](../../governance/policy/overview.md), ad esempio la [configurazione Guest](../../governance/policy/concepts/guest-configuration.md)della VM, verificando che il computer stia segnalando l'area di lavoro log Analytics prevista, abilitare il monitoraggio con [monitoraggio di Azure con le macchine virtuali](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)e molto altro ancora.

- Altre informazioni sull' [agente log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è obbligatorio quando si vuole monitorare in modo proattivo il sistema operativo e i carichi di lavoro in esecuzione nel computer, gestirli con manuali operativi di automazione o soluzioni come Gestione aggiornamenti o usare altri servizi di Azure come il [Centro sicurezza di Azure](../../security-center/security-center-intro.md).