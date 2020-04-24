---
title: Connetti macchine ibride ad Azure su larga scala
description: Questo articolo illustra come connettere i computer ad Azure usando Azure Arc per i server (anteprima) usando un'entità servizio.
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
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Connetti macchine ibride ad Azure su larga scala

È possibile abilitare Azure Arc per i server (anteprima) per più computer Windows o Linux nell'ambiente in uso con diverse opzioni flessibili, a seconda dei requisiti. Usando lo script del modello fornito, è possibile automatizzare tutti i passaggi dell'installazione, inclusa la creazione della connessione ad Azure Arc. Tuttavia, è necessario eseguire in modo interattivo questo script con un account con autorizzazioni elevate nel computer di destinazione e in Azure. Per connettere i computer ad Azure Arc per i server, è possibile usare un' [entità servizio](../../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory invece di usare l'identità con privilegi per connettere in modo [interattivo il computer](onboard-portal.md). Un'entità servizio è un'identità di gestione limitata speciale a cui viene concessa solo l'autorizzazione minima necessaria per connettere i computer `azcmagent` ad Azure tramite il comando. Questa soluzione è più sicura rispetto all'uso di un account con privilegi più elevati come un Amministratore tenant e segue le procedure consigliate per la sicurezza del controllo di accesso. L'entità servizio viene utilizzata solo durante l'onboarding, ma non viene utilizzata per altri scopi.  

I metodi di installazione per installare e configurare l'agente del computer connesso richiedono che il metodo automatico utilizzato disponga delle autorizzazioni di amministratore per i computer. In Linux, usando l'account radice e in Windows, come membro del gruppo Administrators locale.

Prima di iniziare, esaminare i [prerequisiti](overview.md#prerequisites) e verificare che la sottoscrizione e le risorse soddisfino i requisiti.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Al termine di questo processo, sarà possibile connettere i computer ibridi ad Azure Arc per i server.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Creare un'entità servizio per l'onboarding su larga scala

È possibile usare [Azure PowerShell](/powershell/azure/install-az-ps) per creare un'entità servizio con il cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) . In alternativa, è possibile seguire la procedura descritta in [creare un'entità servizio usando il portale di Azure](../../active-directory/develop/howto-create-service-principal-portal.md) per completare questa attività.

> [!NOTE]
> Quando si crea un'entità servizio, l'account deve essere un proprietario o un amministratore accesso utenti nella sottoscrizione che si vuole usare per l'onboarding. Se non si hanno autorizzazioni sufficienti per creare assegnazioni di ruolo, l'entità servizio potrebbe essere creata, ma non sarà in grado di eseguire l'onboarding dei computer.
>

Per creare l'entità servizio usando PowerShell, eseguire le operazioni seguenti.

1. Eseguire il comando seguente. È necessario archiviare l'output del [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) cmdlet in una variabile oppure non sarà possibile recuperare la password necessaria in un passaggio successivo.

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

2. Per recuperare la password archiviata nella `$sp` variabile, eseguire il comando seguente:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Nell'output individuare il valore della password sotto la **password** del campo e copiarlo. Trovare anche il valore nel campo **ApplicationID** e copiarlo. Salvarli per un momento successivo in un luogo sicuro. Se si dimentica o si perde la password dell'entità servizio, è possibile reimpostarla utilizzando il [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) cmdlet.

I valori delle proprietà seguenti vengono utilizzati con i parametri passati a `azcmagent`:

* Il valore della proprietà **ApplicationID** viene usato per il valore `--service-principal-id` del parametro
* Il valore della proprietà **password** viene utilizzato per il `--service-principal-secret` parametro utilizzato per la connessione dell'agente.

> [!NOTE]
> Assicurarsi di usare la proprietà **ApplicationID** dell'entità servizio, non la proprietà **ID** .
>

Il ruolo di **onboarding del computer connesso di Azure** contiene solo le autorizzazioni necessarie per caricare un computer. È possibile assegnare l'autorizzazione dell'entità servizio per consentire all'ambito di includere un gruppo di risorse o una sottoscrizione. Per aggiungere l'assegnazione di ruolo, vedere [aggiungere o rimuovere assegnazioni di ruolo usando il controllo degli accessi in base al ruolo di Azure e il portale di Azure](../../role-based-access-control/role-assignments-portal.md) o [aggiungere o rimuovere assegnazioni di ruolo usando RBAC di](../../role-based-access-control/role-assignments-cli.md)Azure

## <a name="install-the-agent-and-connect-to-azure"></a>Installare l'agente e connettersi ad Azure

I passaggi seguenti consentono di installare e configurare l'agente del computer connesso nei computer ibridi usando il modello di script, che esegue passaggi simili descritti in [connettere le macchine ibride ad Azure dall'articolo portale di Azure](onboard-portal.md) . La differenza si trova nel passaggio finale in cui viene stabilita la connessione ad Azure Arc `azcmagent` usando il comando usando l'entità servizio. 

Di seguito sono riportate le impostazioni che `azcmagent` consentono di configurare il comando da utilizzare per l'entità servizio.

* `tenant-id`: Identificatore univoco (GUID) che rappresenta l'istanza dedicata di Azure AD.
* `subscription-id`: ID sottoscrizione (GUID) della sottoscrizione di Azure in cui si desiderano le macchine virtuali.
* `resource-group`: Nome del gruppo di risorse a cui si desidera che appartengano i computer connessi.
* `location`: Vedere le [aree di Azure supportate](overview.md#supported-regions). Questo percorso può essere lo stesso o diverso, come la posizione del gruppo di risorse.
* `resource-name`: (*Facoltativo*) usato per la rappresentazione della risorsa di Azure del computer locale. Se non si specifica questo valore, viene usato il nome host del computer.

Per altre informazioni sullo strumento da `azcmagent` riga di comando, vedere la Guida di [riferimento a Azcmagent](azcmagent-reference.md).

### <a name="windows-installation-script"></a>Script di installazione di Windows

Di seguito è riportato un esempio dello script di installazione dell'agente del computer connesso per Windows che è stato modificato per utilizzare l'entità servizio per supportare un'installazione completamente automatizzata e non interattiva dell'agente.

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

### <a name="linux-installation-script"></a>Script di installazione di Linux

Di seguito è riportato un esempio dello script di installazione dell'agente computer connesso per Linux che è stato modificato per utilizzare l'entità servizio per supportare un'installazione completamente automatizzata e non interattiva dell'agente.

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

- Informazioni su come gestire il computer usando [criteri di Azure](../../governance/policy/overview.md), ad esempio la [configurazione Guest](../../governance/policy/concepts/guest-configuration.md)della VM, verificando che il computer stia segnalando l'area di lavoro log Analytics prevista, abilitare il monitoraggio con [monitoraggio di Azure con le macchine virtuali](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)e molto altro ancora.

- Altre informazioni sull' [agente log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si vuole monitorare in modo proattivo il sistema operativo e i carichi di lavoro in esecuzione nella macchina virtuale, gestirla con runbook di automazione o soluzioni come Gestione aggiornamenti o usare altri servizi di Azure, come il [Centro sicurezza di Azure](../../security-center/security-center-intro.md).
