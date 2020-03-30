---
title: Connettere macchine ibride ad Azure su larga scalaConnect hybrid machines to Azure at scale
description: In questo articolo viene illustrato come connettere computer ad Azure usando Azure Arc per i server (anteprima) usando un'entità servizio.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192272"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Connettere macchine ibride ad Azure su larga scalaConnect hybrid machines to Azure at scale

È possibile abilitare Azure Arc per i server (anteprima) per più computer Windows o Linux nell'ambiente con diverse opzioni flessibili a seconda dei requisiti. Usando lo script del modello fornito, è possibile automatizzare ogni passaggio dell'installazione, inclusa la definizione della connessione ad Azure Arc.Using the template script we provide, you can automate every step of the installation, including establishing the connection to Azure Arc. Tuttavia, è necessario eseguire in modo interattivo questo script con un account con autorizzazioni elevate nel computer di destinazione e in Azure.However, you are required to interactively execute this script with an account that has elevated permissions on the target machine and in Azure. Per connettere i computer ad Azure Arc per i server, è possibile usare [un'entità servizio](../../active-directory/develop/app-objects-and-service-principals.md) di Azure Active Directory anziché usare l'identità con privilegi per [connettere in modo interattivo il computer.](onboard-portal.md) Un'entità servizio è un'identità di gestione limitata speciale a cui `azcmagent` viene concessa solo l'autorizzazione minima necessaria per connettere i computer ad Azure usando il comando. Questo è più sicuro rispetto all'utilizzo di un account con privilegi più elevati come un Tenant Administrator e segue le nostre procedure consigliate di sicurezza del controllo di accesso. L'entità servizio viene utilizzata solo durante l'onboarding, non viene utilizzata per altri scopi.  

I metodi di installazione per installare e configurare l'agente Computer connesso richiedono che il metodo automatico utilizzato disponga delle autorizzazioni di amministratore sui computer. In Linux, utilizzando l'account radice e in Windows, come membro del gruppo Administrators locale.

Prima di iniziare, esaminare i [prerequisiti](overview.md#prerequisites) e verificare che la sottoscrizione e le risorse soddisfino i requisiti.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Al termine di questo processo, le macchine ibride verranno connesse correttamente ad Azure Arc per i server.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Creare un'entità servizio per l'onboarding su larga scalaCreate a Service Principal for onboarding at scale

È possibile usare [Azure PowerShell](/powershell/azure/install-az-ps) per creare un'entità servizio con il cmdlet New-AzADServicePrincipal.You can use Azure PowerShell to create a service principal with the [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) cmdlet. In alternativa, è possibile seguire i passaggi elencati in [Creare un'entità](../../active-directory/develop/howto-create-service-principal-portal.md) servizio usando il portale di Azure per completare questa attività.

> [!NOTE]
> Quando si crea un'entità servizio, l'account deve essere un proprietario o un amministratore di accesso utente nella sottoscrizione che si vuole usare per l'onboarding. Se non si hanno autorizzazioni sufficienti per creare assegnazioni di ruolo, l'entità servizio potrebbe essere creata, ma non sarà in grado di eseguire l'onboarding dei computer.
>

Per creare l'entità servizio tramite PowerShell, eseguire le operazioni seguenti.

1. Eseguire il comando seguente. È necessario archiviare [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) l'output del cmdlet in una variabile oppure non sarà possibile recuperare la password necessaria in un passaggio successivo.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. Per recuperare la password `$sp` memorizzata nella variabile, eseguire il comando seguente:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Nell'output, trova il valore della password sotto la **password** del campo e copialo. Trovare anche il valore sotto il campo **ApplicationId** e copiarlo anche. Salvarli per dopo in un luogo sicuro. Se si dimentica o si perde la password [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) dell'entità servizio, è possibile reimpostarla utilizzando il cmdlet.

I valori delle seguenti proprietà vengono utilizzati con i parametri passati al `azcmagent`:

* Il valore della proprietà **ApplicationId** `--service-principal-id` viene utilizzato per il valore del parametro
* Il valore della proprietà **password** `--service-principal-secret` viene utilizzato per il parametro utilizzato per connettere l'agente.

> [!NOTE]
> Assicurarsi di utilizzare la proprietà **ApplicationId** dell'entità servizio, non la proprietà **Id.**
>

Il ruolo **Dionboarding del computer connesso ad Azure** contiene solo le autorizzazioni necessarie per l'onboarding di un computer. È possibile assegnare l'autorizzazione dell'entità servizio per consentire al relativo ambito di includere un gruppo di risorse o una sottoscrizione. Per aggiungere l'assegnazione di ruolo, vedere Aggiungere o rimuovere assegnazioni di ruolo usando Controllo degli accessi in base al ruolo di Azure e il portale di Azure o Aggiungere o rimuovere assegnazioni di ruolo usando Controllo degli accessi in base al ruolo [di Azure e l'interfaccia della riga di comando](../../role-based-access-control/role-assignments-cli.md)di Azure.To add role assignment, see Add or remove role [assignments using Azure RBAC and](../../role-based-access-control/role-assignments-portal.md) Azure CLI.

## <a name="install-the-agent-and-connect-to-azure"></a>Installare l'agente e connettersi ad AzureInstall the agent and connect to Azure

I passaggi seguenti installano e configurano l'agente computer connesso nelle macchine ibride usando il modello di script, che esegue passaggi simili descritti nell'articolo [Connettere macchine ibride ad Azure dal portale](onboard-portal.md) di Azure.The following steps install and configure the Connected Machine agent on your hybrid machines by using the script template, which performs similar steps described in the Connect hybrid machines to Azure article. La differenza è nel passaggio finale in cui `azcmagent` si stabilisce la connessione ad Azure Arc usando il comando usando l'entità servizio. 

Di seguito sono riportate `azcmagent` le impostazioni configurate per l'utilizzo per l'entità servizio.

* `tenant-id`: l'identificatore univoco (GUID) che rappresenta l'istanza dedicata di Azure AD.
* `subscription-id`: l'ID sottoscrizione (GUID) della sottoscrizione di Azure in cui si vogliono inserire i computer.
* `resource-group`: il nome del gruppo di risorse a cui si desidera appartenere i computer connessi.
* `location`: vedere Aree di [Azure supportate](overview.md#supported-regions). Questa posizione può essere uguale o diversa, ovvero la posizione del gruppo di risorse.
* `resource-name`: (*Facoltativo*) Utilizzato per la rappresentazione delle risorse di Azure del computer locale. Se non si specifica questo valore, viene utilizzato il nome host del computer.

Per ulteriori informazioni `azcmagent` sullo strumento da riga di comando, vedere [Azcmagent Reference](azcmagent-reference.md).

### <a name="windows-installation-script"></a>Script di installazione di Windows

Di seguito è riportato un esempio dello script di installazione dell'agente computer connesso per Windows modificato per utilizzare l'entità servizio per supportare un'installazione completamente automatizzata e non interattiva dell'agente.

```
 # Download the package
function download() {$ProgressPreference="SilentlyContinue"; Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi}
download

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String

# Run connect command
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
```

### <a name="linux-installation-script"></a>Script di installazione Linux

Di seguito è riportato un esempio dello script di installazione dell'agente di computer connesso per Linux modificato per utilizzare l'entità servizio per supportare un'installazione completamente automatizzata e non interattiva dell'agente.

```
# Download the installation package
wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh

# Install the hybrid agent
bash ~/install_linux_azcmagent.sh

# Run connect command
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
```

Dopo aver installato l'agente e averlo configurato per la connessione ad Azure Arc per server (anteprima), passare al portale di Azure per verificare che il server sia stato connesso correttamente. Visualizzare le proprie macchine virtuali nel [portale di Azure](https://aka.ms/hybridmachineportal).

![Una connessione server riuscita](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come gestire il computer usando [Criteri di Azure,](../../governance/policy/overview.md)ad esempio la [configurazione guest](../../governance/policy/concepts/guest-configuration.md)delle macchine virtuali, la verifica della creazione di report per l'area di lavoro di Log Analytics prevista, l'abilitazione del monitoraggio con Monitoraggio di Azure con le [macchine virtuali](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)e molto altro ancora.

- Ulteriori informazioni [sull'agente di Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si vuole monitorare in modo proattivo il sistema operativo e i carichi di lavoro in esecuzione nella macchina virtuale, gestirla con runbook di automazione o soluzioni come Gestione aggiornamenti o usare altri servizi di Azure, come il [Centro sicurezza di Azure](../../security-center/security-center-intro.md).
