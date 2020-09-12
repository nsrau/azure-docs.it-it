---
title: Installare l'agente del computer connesso usando Windows PowerShell DSC
description: Questo articolo illustra come connettere i computer ad Azure usando i server abilitati per Azure Arc (anteprima) con Windows PowerShell DSC.
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 675258ff95829c2dc9922571db5014b2ba93d336
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89565821"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Come installare l'agente del computer connesso usando Windows PowerShell DSC

Utilizzando [Windows PowerShell DSC (Desired state Configuration](/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) ), è possibile automatizzare l'installazione e la configurazione del software per un computer Windows. Questo articolo descrive come usare DSC per installare l'agente computer connesso di Azure Arc (anteprima) in computer Windows ibridi.

## <a name="requirements"></a>Requisiti

- Windows PowerShell versione 4,0 o successiva

- Modulo DSC [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0)

- Un'entità servizio per connettere i computer ai server abilitati per Azure Arc (anteprima) in modo non interattivo. Seguire i passaggi descritti nella sezione [creare un'entità servizio per l'onboarding su larga scala](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) se non è già stata creata un'entità servizio per i server abilitati per Arc (anteprima).

## <a name="install-the-connectedmachine-dsc-module"></a>Installare il modulo ConnectedMachine DSC

1. Per installare manualmente il modulo, scaricare il codice sorgente e decomprimere il contenuto della directory del progetto in `$env:ProgramFiles\WindowsPowerShell\Modules folder` . In alternativa, eseguire il comando seguente per installare da PowerShell Gallery usando PowerShellGet (in PowerShell 5,0):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Per confermare l'installazione, eseguire il comando seguente e assicurarsi di visualizzare le risorse DSC del computer connesso di Azure disponibili.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   Nell'output dovrebbe essere visualizzata una schermata simile alla seguente:

   ![Conferma dell'esempio di installazione del modulo DSC connesso del computer](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Installare l'agente e connettersi ad Azure

Le risorse in questo modulo sono progettate per gestire la configurazione dell'agente del computer connesso di Azure. È incluso anche uno script `AzureConnectedMachineAgent.ps1` di PowerShell, disponibile nella `AzureConnectedMachineDsc\examples` cartella. USA risorse della community per automatizzare il download e l'installazione e stabilire una connessione con Azure Arc. Questo script esegue passaggi simili descritti in [connettere macchine ibride ad Azure dall'articolo portale di Azure](onboard-portal.md) .

Se il computer deve comunicare tramite un server proxy al servizio, dopo l'installazione dell'agente è necessario eseguire un comando descritto [qui](manage-agent.md#update-or-remove-proxy-settings). Il comando imposta la variabile di ambiente di sistema del server proxy `https_proxy`. Anziché eseguire il comando manualmente, è possibile eseguire questo passaggio con DSC usando il modulo [ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0) .

>[!NOTE]
>Per consentire l'esecuzione di DSC, è necessario configurare Windows per la ricezione di comandi remoti di PowerShell anche quando si esegue una configurazione localhost. Per configurare correttamente l'ambiente, è sufficiente eseguire `Set-WsManQuickConfig -Force` in un terminale di PowerShell con privilegi elevati.
>

È possibile applicare i documenti di configurazione (file MOF) al computer usando il `Start-DscConfiguration` cmdlet.

Di seguito sono riportati i parametri passati allo script di PowerShell da usare.

- `TenantId`: Identificatore univoco (GUID) che rappresenta l'istanza dedicata di Azure AD.

- `SubscriptionId`: ID sottoscrizione (GUID) della sottoscrizione di Azure in cui si desiderano le macchine virtuali.

- `ResourceGroup`: Nome del gruppo di risorse a cui si desidera che appartengano i computer connessi.

- `Location`: Vedere le [aree di Azure supportate](overview.md#supported-regions). La località può essere uguale o diversa da quella del gruppo di risorse.

- `Tags`: Matrice di stringhe di tag da applicare alla risorsa del computer connesso.

- `Credential`: Un oggetto credenziale di PowerShell con il **ApplicationID** e la **password** usati per registrare i computer su larga scala usando un' [entità servizio](onboard-service-principal.md). 

1. In una console di PowerShell passare alla cartella in cui è stato salvato il `.ps1` file.

2. Eseguire i comandi PowerShell seguenti per compilare il documento MOF. Per informazioni sulla compilazione delle configurazioni DSC, vedere [Configurazioni DSC](/powershell/scripting/dsc/configurations/configurations?view=powershell-7):

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Verrà creato un oggetto `localhost.mof file` in una nuova cartella denominata `C:\dsc` .

Dopo aver installato l'agente e configurato per la connessione ai server abilitati per Azure Arc (anteprima), passare alla portale di Azure per verificare che il server sia stato connesso correttamente. Visualizzare i computer nel [portale di Azure](https://aka.ms/hybridmachineportal).

## <a name="adding-to-existing-configurations"></a>Aggiunta a configurazioni esistenti

Questa risorsa può essere aggiunta alle configurazioni DSC esistenti per rappresentare una configurazione end-to-end per un computer. Ad esempio, potrebbe essere necessario aggiungere questa risorsa a una configurazione che imposta impostazioni del sistema operativo sicure.

Il modulo [CompositeResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0) del PowerShell Gallery può essere usato per creare una [risorsa composita](/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) della configurazione di esempio, per semplificare ulteriormente la combinazione delle configurazioni.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come gestire il computer usando i [criteri di Azure](../../governance/policy/overview.md), ad esempio la configurazione di VM [Guest](../../governance/policy/concepts/guest-configuration.md), verificare che il computer stia segnalando l'area di lavoro Log Analytics prevista, abilitare il monitoraggio con [Monitoraggio di Azure con macchine virtuali](../../azure-monitor/insights/vminsights-enable-policy.md) e molto altro ancora.

- Altre informazioni sull'[agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si vuole monitorare in modo proattivo il sistema operativo e i carichi di lavoro in esecuzione nella macchina virtuale, gestirla con runbook di automazione o soluzioni come Gestione aggiornamenti o usare altri servizi di Azure, come il [Centro sicurezza di Azure](../../security-center/security-center-intro.md).
