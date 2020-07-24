---
title: Connettere macchine virtuali ibride ad Azure dal portale di Azure
description: Questo articolo illustra come installare l'agente e connettere macchine virtuali ad Azure usando Azure Arc per server (anteprima) dal portale di Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: fe26731a7f217d0f441a3dea1650f2652677a57f
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133011"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Connettere macchine virtuali ibride ad Azure dal portale di Azure

È possibile abilitare Azure Arc per server (anteprima) per una singola o un numero limitato di macchine virtuali Windows o Linux nell'ambiente eseguendo manualmente un set di passaggi. Si può anche usare un metodo automatico eseguendo uno script di modello fornito da Microsoft. Lo script automatizza il download e l'installazione di entrambi gli agenti.

Con questo metodo sono necessarie le autorizzazioni di amministratore sulla macchina virtuale per installare e configurare l'agente. In Linux, tramite l'account radice, e in Windows, si è membri del gruppo Administrators locale.

Prima di iniziare, esaminare i [prerequisiti](agent-overview.md#prerequisites) e verificare che la sottoscrizione e le risorse soddisfino i requisiti.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Generare lo script di installazione dal portale di Azure

Lo script per automatizzare il download e l'installazione e stabilire la connessione con Azure Arc è disponibile nel portale di Azure. Per completare il processo, seguire questa procedura:

1. Nel browser passare al [portale di Azure](https://aka.ms/hybridmachineportal).

1. Nella pagina **Macchine virtuali - Azure Arc** selezionare **Aggiungi** in alto a sinistra oppure **Create machine - Azure Arc** (Crea macchina virtuale - Azure Arc) nella parte inferiore del riquadro centrale.

1. Nella pagina **Selezionare un metodo** selezionare il riquadro **Aggiungi macchine virtuali tramite script interattivo** e quindi selezionare **Genera script**.

1. Nella pagina **Genera script** selezionare la sottoscrizione e il gruppo di risorse in cui si vuole che la macchina virtuale venga gestita in Azure. Selezionare una località di Azure in cui verranno archiviati i metadati della macchina virtuale.

    >[!NOTE]
    >Azure Arc per server (anteprima) supporta solo le aree seguenti:
    >- EastUS
    >- Stati Uniti occidentali 2
    >- Europa occidentale
    >- SoutheastAsia
    >
    >Esaminare le considerazioni aggiuntive quando si seleziona un'area [qui](overview.md#supported-regions) nell'articolo introduttivo.

1. Nell'elenco a discesa **Sistema operativo** della pagina **Genera script** selezionare il sistema operativo in cui verrà eseguito lo script.

1. Se la macchina virtuale comunica tramite un server proxy per connettersi a Internet, selezionare **Avanti: Server proxy**.
1. Nella scheda **Server proxy** specificare l'indirizzo IP del server proxy o il nome e il numero di porta che la macchina virtuale userà per comunicare con il server proxy. Immettere il valore nel formato `http://<proxyURL>:<proxyport>`.
1. Selezionare **Rivedi e genera**.

1. Nella scheda **Rivedi e genera** esaminare le informazioni di riepilogo e quindi selezionare **Scarica**. Se occorre ancora apportare modifiche, selezionare **Indietro**.

## <a name="install-and-validate-the-agent-on-windows"></a>Installare e convalidare l'agente in Windows

### <a name="install-manually"></a>Eseguire l'installazione manuale

È possibile installare manualmente l'agente Connected Machine eseguendo il pacchetto di Windows Installer *AzureConnectedMachineAgent.msi*. È possibile scaricare la versione più recente del [Pacchetto Windows Installer dell'agente Windows](https://aka.ms/AzureConnectedMachineAgent) dall'Area download Microsoft.

>[!NOTE]
>* Per installare o disinstallare l'agente, sono necessarie le autorizzazioni di *Amministratore*.
>* Occorre pima di tutto scaricare e copiare il pacchetto di installazione in una cartella nel server di destinazione o da una cartella di rete condivisa. Se si esegue il pacchetto di installazione senza opzioni, si avvia una procedura guidata che consente di completare l'installazione dell'agente in modo interattivo.

Se la macchina virtuale deve comunicare tramite un server proxy con il servizio, dopo l'installazione dell'agente è necessario eseguire un comando descritto più avanti in questo articolo. Il comando imposta la variabile di ambiente di sistema del server proxy `https_proxy`.

Se non si ha familiarità con le opzioni della riga di comando per i pacchetti Windows Installer, vedere [Opzioni della riga di comando di Msiexec standard](/windows/win32/msi/standard-installer-command-line-options) e [Opzioni della riga di comando di Msiexec](/windows/win32/msi/command-line-options).

Ad esempio, eseguire il programma di installazione con il parametro `/?` per esaminare la Guida e opzione per l'accesso a informazioni di riferimento rapido.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

Per installare l'agente in modo invisibile all'utente e creare un file di log del programma di installazione nella cartella `C:\Support\Logs` esistente, eseguire il comando seguente.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
```

Se l'agente non si avvia dopo l'installazione, controllare i log per vedere le informazioni dettagliate sull'errore. La directory dei log è *%Programfiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Eseguire l'installazione con il metodo con script

1. Accedere al server.

1. Aprire un prompt dei comandi di PowerShell con privilegi elevati.

    >[!NOTE]
    >Lo script supporta solo l'esecuzione da una versione a 64 bit di Windows PowerShell.
    >

1. Passare alla cartella o alla condivisione in cui è stato copiato lo script ed eseguirlo sul server eseguendo lo script `./OnboardingScript.ps1`.

Se l'agente non si avvia dopo l'installazione, controllare i log per vedere le informazioni dettagliate sull'errore. La directory dei log è *%Programfiles%\AzureConnectedMachineAgentAgent\logs*.

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

`"%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"`

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

`azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"`

>[!NOTE]
>Per eseguire **azcmagent**, è necessario disporre delle autorizzazioni di accesso alla *radice* nei computer Linux.

## <a name="verify-the-connection-with-azure-arc"></a>Verificare la connessione con Azure Arc

Dopo aver installato l'agente e averlo configurato per la connessione ad Azure Arc per server (anteprima), passare al portale di Azure per verificare che il server sia stato connesso correttamente. Visualizzare le proprie macchine virtuali nel [portale di Azure](https://aka.ms/hybridmachineportal).

![Una connessione server riuscita](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come gestire il computer usando i [criteri di Azure](../../governance/policy/overview.md), ad esempio la configurazione di VM [Guest](../../governance/policy/concepts/guest-configuration.md), verificare che il computer stia segnalando l'area di lavoro Log Analytics prevista, abilitare il monitoraggio con [Monitoraggio di Azure con macchine virtuali](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md) e molto altro ancora.

- Altre informazioni sull'[agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si vuole monitorare in modo proattivo il sistema operativo e i carichi di lavoro in esecuzione nella macchina virtuale, gestirla con runbook di automazione o soluzioni come Gestione aggiornamenti o usare altri servizi di Azure, come il [Centro sicurezza di Azure](../../security-center/security-center-intro.md).
