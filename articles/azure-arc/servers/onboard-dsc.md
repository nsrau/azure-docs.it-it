---
title: Installare l'agente computer connesso tramite Windows PowerShell DSC
description: In this article, you learn how to connect machines to Azure using Azure Arc for servers (preview) using Windows PowerShell DSC.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 1fb64463b0372202adb04c2deb304c389c7773b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164683"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Come installare l'agente computer connesso utilizzando DSC di Windows PowerShell

Utilizzando [DSC (Desired State Configuration) di Windows PowerShell,](https://docs.microsoft.com/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) è possibile automatizzare l'installazione e la configurazione del software per un computer Windows. In questo articolo viene descritto come utilizzare DSC per installare l'agente computer connesso di Azure Arc per server in computer Windows ibridi.

## <a name="requirements"></a>Requisiti

- Windows PowerShell versione 4.0 o successiva

- Modulo [DSC AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0) DSC

- Un'entità servizio per connettere le macchine ad Azure Arc per i server in modo non interattivo. Seguire i passaggi nella sezione [Creare un'entità servizio per l'onboarding su larga scala](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) se non è già stata creata un'entità servizio per Arc per i server.

## <a name="install-the-connectedmachine-dsc-module"></a>Installare il modulo DSC ConnectedMachine

1. Per installare manualmente il modulo, scaricare il codice sorgente e `$env:ProgramFiles\WindowsPowerShell\Modules folder`decomprimere il contenuto della directory del progetto nel file . In alternativa, eseguire il comando seguente per eseguire l'installazione dalla raccolta di PowerShell usando PowerShellGet (in PowerShell 5.0):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Per confermare l'installazione, eseguire il comando seguente e verificare che siano disponibili le risorse DSC del computer connesso di Azure.To confirm installation, run the following command and ensure you see the Azure Connected Machine DSC resources available.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   Nell'output, si dovrebbe vedere qualcosa di simile al seguente:

   ![Esempio di installazione del modulo DSC della macchina connessa](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Installare l'agente e connettersi ad AzureInstall the agent and connect to Azure

Le risorse in questo modulo sono progettate per gestire la configurazione dell'agente computer connesso di Azure.The resources in this module are designed to manage the Azure Connected Machine Agent configuration. È incluso anche `AzureConnectedMachineAgent.ps1`uno script `AzureConnectedMachineDsc\examples` di PowerShell, disponibile nella cartella. Usa le risorse della community per automatizzare il download e l'installazione e stabilire una connessione con Azure Arc.It uses community resources to automate the download and installation, and establish a connection with Azure Arc. Questo script esegue passaggi simili descritti nell'articolo [Connettere le macchine ibride ad Azure dal portale](onboard-portal.md) di Azure.This script performs similar steps described in the Connect hybrid machines to Azure from the Azure portal article.

Se il computer deve comunicare tramite un server proxy al servizio, dopo aver installato l'agente è necessario eseguire un comando descritto [di seguito.](onboard-portal.md#configure-the-agent-proxy-setting) Il comando imposta la variabile di ambiente di sistema del server proxy `https_proxy`. Anziché eseguire il comando manualmente, è possibile eseguire questo passaggio con DSC utilizzando il modulo [ComputeManagementDsc.](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0)

>[!NOTE]
>Per consentire l'esecuzione di DSC, Windows deve essere configurato per ricevere i comandi remoti di PowerShell anche quando si esegue una configurazione localhost. Per configurare correttamente l'ambiente, è sufficiente eseguire `Set-WsManQuickConfig -Force` in un terminale di PowerShell con privilegi elevati.
>

I documenti di configurazione (file MOF) `Start-DscConfiguration` possono essere applicati al computer utilizzando il cmdlet.

Di seguito sono riportati i parametri passati allo script di PowerShell da usare.

- `TenantId`: l'identificatore univoco (GUID) che rappresenta l'istanza dedicata di Azure AD.

- `SubscriptionId`: l'ID sottoscrizione (GUID) della sottoscrizione di Azure in cui si vogliono inserire i computer.

- `ResourceGroup`: il nome del gruppo di risorse a cui si desidera appartenere i computer connessi.

- `Location`: vedere Aree di [Azure supportate](overview.md#supported-regions). Questa posizione può essere uguale o diversa, ovvero la posizione del gruppo di risorse.

- `Tags`: matrice di stringhe di tag da applicare alla risorsa computer collegata.

- `Credential`: oggetto credenziali di PowerShell con **ApplicationId** e **password** utilizzati per registrare i computer su larga scala utilizzando un'entità [servizio](onboard-service-principal.md). 

1. In una console di PowerShell passare alla `.ps1` cartella in cui è stato salvato il file.

2. Eseguire i comandi PowerShell seguenti per compilare il documento MOF. Per informazioni sulla compilazione delle configurazioni DSC, vedere [Configurazioni DSC](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations?view=powershell-7):

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Verrà creata `localhost.mof file` una cartella in `C:\dsc`una nuova cartella denominata .

Dopo aver installato l'agente e averlo configurato per la connessione ad Azure Arc per server (anteprima), passare al portale di Azure per verificare che il server sia stato connesso correttamente. Visualizzare le proprie macchine virtuali nel [portale di Azure](https://aka.ms/hybridmachineportal).

## <a name="adding-to-existing-configurations"></a>Aggiunta a configurazioni esistenti

Questa risorsa può essere aggiunta alle configurazioni DSC esistenti per rappresentare una configurazione end-to-end per una macchina. Ad esempio, è possibile aggiungere questa risorsa a una configurazione che imposta le impostazioni del sistema operativo protetto.

Il modulo [CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0) di PowerShell Gallery può essere usato per creare una [risorsa composita](https://docs.microsoft.com/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) della configurazione di esempio, per semplificare ulteriormente la combinazione di configurazioni.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come gestire il computer usando [Criteri di Azure,](../../governance/policy/overview.md)ad esempio la [configurazione guest](../../governance/policy/concepts/guest-configuration.md)delle macchine virtuali, la verifica della creazione di report per l'area di lavoro di Log Analytics prevista, l'abilitazione del monitoraggio con Monitoraggio di Azure con le [macchine virtuali](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)e molto altro ancora.

- Ulteriori informazioni [sull'agente di Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si vuole monitorare in modo proattivo il sistema operativo e i carichi di lavoro in esecuzione nella macchina virtuale, gestirla con runbook di automazione o soluzioni come Gestione aggiornamenti o usare altri servizi di Azure, come il [Centro sicurezza di Azure](../../security-center/security-center-intro.md).