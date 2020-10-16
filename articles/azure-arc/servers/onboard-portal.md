---
title: Connettere macchine virtuali ibride ad Azure dal portale di Azure
description: Questo articolo illustra come installare l'agente e connettere i computer ad Azure usando i server abilitati per Azure Arc dal portale di Azure.
ms.date: 09/24/2020
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 2ba834d8c55f53792606fffe65d65794e837e9e3
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92101733"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Connettere macchine virtuali ibride ad Azure dal portale di Azure

È possibile abilitare i server abilitati per Azure Arc per uno o un numero ridotto di computer Windows o Linux nell'ambiente eseguendo manualmente un set di passaggi. Si può anche usare un metodo automatico eseguendo uno script di modello fornito da Microsoft. Lo script automatizza il download e l'installazione di entrambi gli agenti.

Con questo metodo sono necessarie le autorizzazioni di amministratore sulla macchina virtuale per installare e configurare l'agente. In Linux, tramite l'account radice, e in Windows, si è membri del gruppo Administrators locale.

Prima di iniziare, esaminare i [prerequisiti](agent-overview.md#prerequisites) e verificare che la sottoscrizione e le risorse soddisfino i requisiti.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Generare lo script di installazione dal portale di Azure

Lo script per automatizzare il download e l'installazione e stabilire la connessione con Azure Arc è disponibile nel portale di Azure. Per completare il processo, seguire questa procedura:

1. Nel browser passare al [portale di Azure](https://aka.ms/hybridmachineportal).

1. Nella pagina **Server-Azure Arc** selezionare **Aggiungi** in alto a sinistra.

1. Nella pagina **Selezione metodo** selezionare il riquadro **Aggiungi server tramite script interattivo** e quindi selezionare **Genera script**.

1. Nella pagina **Genera script** selezionare la sottoscrizione e il gruppo di risorse in cui si vuole che la macchina virtuale venga gestita in Azure. Selezionare una località di Azure in cui verranno archiviati i metadati della macchina virtuale.

    >[!NOTE]
    >I server abilitati per Azure Arc supportano solo le aree seguenti:
    >- EastUS
    >- Stati Uniti occidentali 2
    >- Europa occidentale
    >- SoutheastAsia
    >
    >Esaminare le considerazioni aggiuntive quando si seleziona un'area [qui](overview.md#supported-regions) nell'articolo introduttivo.

1. Nella pagina **prerequisiti** esaminare le informazioni e quindi fare clic su **Avanti: dettagli risorsa**.

1. Nella pagina **Dettagli risorsa** specificare quanto segue:

    1. Nell'elenco a discesa **gruppo di risorse** selezionare il gruppo di risorse da cui verrà gestito il computer.
    1. Nell'elenco a discesa **Region (area** ) selezionare l'area di Azure in cui archiviare i metadati dei server.
    1. Nell'elenco a discesa **sistema operativo** selezionare il sistema operativo in cui si desidera eseguire lo script.
    1. Se il computer sta comunicando tramite un server proxy per la connessione a Internet, specificare l'indirizzo IP del server proxy o il nome e il numero di porta che il computer utilizzerà per comunicare con il server proxy. Immettere il valore nel formato `http://<proxyURL>:<proxyport>`.
    1. Selezionare **Avanti: Tag**.

1. Nella pagina **tag** esaminare i **tag di posizione fisica** predefiniti suggeriti e immettere un valore oppure specificare uno o più **tag personalizzati** per supportare gli standard.

1. Selezionare **Avanti: Scarica ed Esegui script**.

1. Nella pagina **download ed esecuzione dello script** esaminare le informazioni di riepilogo e quindi selezionare **Scarica**. Se occorre ancora apportare modifiche, selezionare **Indietro**.

## <a name="install-and-validate-the-agent-on-windows"></a>Installare e convalidare l'agente in Windows

### <a name="install-manually"></a>Eseguire l'installazione manuale

È possibile installare manualmente l'agente Connected Machine eseguendo il pacchetto di Windows Installer *AzureConnectedMachineAgent.msi*. È possibile scaricare la versione più recente del [Pacchetto Windows Installer dell'agente Windows](https://aka.ms/AzureConnectedMachineAgent) dall'Area download Microsoft.

>[!NOTE]
>* Per installare o disinstallare l'agente, sono necessarie le autorizzazioni di *Amministratore*.
>* Occorre pima di tutto scaricare e copiare il pacchetto di installazione in una cartella nel server di destinazione o da una cartella di rete condivisa. Se si esegue il pacchetto di installazione senza opzioni, si avvia una procedura guidata che consente di completare l'installazione dell'agente in modo interattivo.

Se il computer deve comunicare tramite un server proxy al servizio, dopo l'installazione dell'agente è necessario eseguire un comando descritto nella procedura seguente. Questo comando imposta la variabile di ambiente del sistema del server proxy `https_proxy` .

Se non si ha familiarità con le opzioni della riga di comando per i pacchetti Windows Installer, vedere [Opzioni della riga di comando di Msiexec standard](/windows/win32/msi/standard-installer-command-line-options) e [Opzioni della riga di comando di Msiexec](/windows/win32/msi/command-line-options).

Ad esempio, eseguire il programma di installazione con il parametro `/?` per esaminare la Guida e opzione per l'accesso a informazioni di riferimento rapido.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

1. Per installare l'agente in modo invisibile all'utente e creare un file di log del programma di installazione nella cartella `C:\Support\Logs` esistente, eseguire il comando seguente.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
    ```

    Se l'agente non si avvia dopo l'installazione, controllare i log per vedere le informazioni dettagliate sull'errore. La directory dei log è *%Programfiles%\AzureConnectedMachineAgentAgent\logs*.

2. Se il computer deve comunicare tramite un server proxy, per impostare la variabile di ambiente del server proxy, eseguire il comando seguente:

    ```powershell
    [Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
    $env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
    # For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
    Restart-Service -Name himds
    ```

    >[!NOTE]
    >L'agente non supporta l'impostazione dell'autenticazione proxy in questa versione di anteprima.
    >

3. Dopo aver installato l'agente, è necessario configurarlo per la comunicazione con il servizio Azure Arc eseguendo il comando seguente:

    ```dos
    "%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"
    ```

### <a name="install-with-the-scripted-method"></a>Eseguire l'installazione con il metodo con script

1. Accedere al server.

1. Aprire un prompt dei comandi di PowerShell con privilegi elevati.

    >[!NOTE]
    >Lo script supporta solo l'esecuzione da una versione a 64 bit di Windows PowerShell.
    >

1. Passare alla cartella o alla condivisione in cui è stato copiato lo script ed eseguirlo sul server eseguendo lo script `./OnboardingScript.ps1`.

Se l'agente non si avvia dopo l'installazione, controllare i log per vedere le informazioni dettagliate sull'errore. La directory dei log è *%Programfiles%\AzureConnectedMachineAgentAgent\logs*.

## <a name="install-and-validate-the-agent-on-linux"></a>Installare e convalidare l'agente in Linux

L'agente Connected Machine per Linux viene fornito nel formato di pacchetto preferito per la distribuzione (RPM o DEB) ospitata nel [repository dei pacchetti](https://packages.microsoft.com/) di Microsoft. Il [bundle di script della shell `Install_linux_azcmagent.sh`](https://aka.ms/azcmagent) esegue le azioni seguenti:

- Configura il computer host per scaricare il pacchetto dell'agente da packages.microsoft.com.
- Installa il pacchetto del provider di risorse ibride.
- Registra il computer con Azure Arc

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

## <a name="verify-the-connection-with-azure-arc"></a>Verificare la connessione con Azure Arc

Dopo aver installato l'agente e averlo configurato per la connessione ai server abilitati per Azure Arc, passare al portale di Azure per verificare che il server sia stato connesso correttamente. Visualizzare le proprie macchine virtuali nel [portale di Azure](https://aka.ms/hybridmachineportal).

![Una connessione server riuscita](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Passaggi successivi

* Le informazioni sulla risoluzione dei problemi sono reperibili nella [Guida alla risoluzione dei problemi relativi all'agente del computer connesso](troubleshoot-agent-onboard.md).

* Informazioni su come gestire il computer usando i [criteri di Azure](../../governance/policy/overview.md), ad esempio la configurazione di VM [Guest](../../governance/policy/concepts/guest-configuration.md), verificare che il computer stia segnalando l'area di lavoro Log Analytics prevista, abilitare il monitoraggio con [Monitoraggio di Azure con macchine virtuali](../../azure-monitor/insights/vminsights-enable-policy.md) e molto altro ancora.

* Altre informazioni sull'[agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si desidera raccogliere dati di monitoraggio del carico di lavoro e del sistema operativo, gestirli con manuali operativi di automazione o funzionalità come Gestione aggiornamenti o usare altri servizi di Azure come il [Centro sicurezza di Azure](../../security-center/security-center-introduction.md).