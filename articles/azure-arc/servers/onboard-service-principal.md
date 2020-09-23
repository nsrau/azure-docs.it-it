---
title: Connettere macchine virtuali ibride ad Azure su larga scala
description: Questo articolo illustra come connettere i computer ad Azure usando i server abilitati per Azure ARC usando un'entità servizio.
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 545d8abd6dd17e1e413852735c096ddc9261b972
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90908323"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Connettere macchine virtuali ibride ad Azure su larga scala

È possibile abilitare i server abilitati per Azure Arc per più computer Windows o Linux nell'ambiente in uso con diverse opzioni flessibili, a seconda dei requisiti. Usando lo script modello fornito è possibile automatizzare tutti i passaggi dell'installazione, inclusa la creazione della connessione ad Azure Arc. Tuttavia, è necessario eseguire questo script in modo interattivo con un account con autorizzazioni elevate, nel computer di destinazione e in Azure. Per connettere i computer ai server abilitati per Azure Arc, è possibile usare un' [entità servizio](../../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory invece di usare l'identità con privilegi per connettere in modo [interattivo il computer](onboard-portal.md). Un'entità servizio è un'identità di gestione limitata speciale a cui viene concessa solo l'autorizzazione minima necessaria per connettere computer ad Azure tramite il comando `azcmagent`. Questa soluzione è più sicura rispetto all'uso di un account con autorizzazioni elevate, come un Amministratore tenant, e segue le procedure consigliate per la sicurezza del controllo di accesso. L'entità servizio viene usata solo durante l'onboarding, non viene usata per altri scopi.  

I metodi di installazione per installare e configurare l'agente di Connected Machine richiedono che il metodo automatizzato usato disponga delle autorizzazioni di amministratore per i computer. In Linux, tramite l'account radice, e in Windows, come membri del gruppo Administrators locale.

Prima di iniziare, esaminare i [prerequisiti](agent-overview.md#prerequisites) e verificare che la sottoscrizione e le risorse soddisfino i requisiti.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Al termine di questo processo, le macchine ibride sono state connesse correttamente ai server abilitati per Azure Arc.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Creare un'entità servizio per l'onboarding su larga scala

È possibile usare [Azure PowerShell](/powershell/azure/install-az-ps) per creare un'entità servizio con il cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal). Oppure è possibile seguire la procedura descritta in [Creare un'entità servizio usando il portale di Azure](../../active-directory/develop/howto-create-service-principal-portal.md) per completare questa attività.

> [!NOTE]
> Quando si crea un'entità servizio, è necessario essere un proprietario o un amministratore Accesso utente nella sottoscrizione che si vuole usare per l'onboarding. Se non si hanno autorizzazioni sufficienti per creare assegnazioni di ruolo, l'entità servizio potrebbe essere creata, ma non sarà in grado di eseguire l'onboarding dei computer.
>

Per creare l'entità servizio tramite PowerShell, eseguire l'operazione seguente.

1. Eseguire il comando seguente. È necessario archiviare l'output del cmdlet [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) in una variabile, altrimenti non sarà possibile recuperare la password necessaria in un passaggio successivo.

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

2. Eseguire il comando seguente per recuperare la password archiviata nella variabile `$sp`:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Nell'output, individuare il valore della password nel campo **password** e copiarlo. Individuare inoltre il valore nel campo **ApplicationId** e copiarlo. Salvarli in una posizione sicura per usarli in seguito. Se si dimentica o si perde la password dell'entità servizio, è possibile reimpostarla usando il cmdlet [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential).

I valori delle proprietà seguenti vengono usati con i parametri passati al `azcmagent`:

* Il valore dalla proprietà **ApplicationId** viene usata per il valore del parametro `--service-principal-id`
* Il valore dalla proprietà **password** viene usato per il parametro `--service-principal-secret` per la connessione dell'agente.

> [!NOTE]
> Usare la proprietà **ApplicationId** dell'entità servizio e non la proprietà **Id**.
>

Il ruolo di **Onboarding di Azure Connected Machine** contiene solo le autorizzazioni necessarie per eseguire l'onboarding di un computer. È possibile assegnare l'autorizzazione dell'entità servizio per consentire al suo ambito di includere un gruppo di risorse o una sottoscrizione. Per aggiungere l'assegnazione di ruolo, vedere [Aggiungere o rimuovere assegnazioni di ruolo di Azure tramite il portale di Azure](../../role-based-access-control/role-assignments-portal.md) oppure [Aggiungere o rimuovere assegnazioni di ruolo in Azure tramite l'interfaccia della riga di comando di Azure](../../role-based-access-control/role-assignments-cli.md).

## <a name="install-the-agent-and-connect-to-azure"></a>Installare l'agente e connettersi ad Azure

I passaggi seguenti consentono di installare e configurare l'agente del computer connesso nei computer ibridi usando il modello dello script, che esegue passaggi simili descritti nell'articolo [Connettere macchine virtuali ibride ad Azure dal portale di Azure](onboard-portal.md). La differenza si trova nel passaggio finale in cui viene stabilita la connessione ad Azure Arc tramite il comando `azcmagent` con l'uso dell'entità servizio.

Le impostazioni seguenti consentono di configurare il comando `azcmagent` da usare per l'entità servizio.

* `tenant-id` : identificatore univoco (GUID), che rappresenta l'istanza dedicata di Azure AD.
* `subscription-id` : ID sottoscrizione (GUID) della sottoscrizione di Azure in cui si desiderano collocare i computer.
* `resource-group` : nome del gruppo di risorse in cui si desidera che appartengano i computer connessi.
* `location` : vedere le [aree di Azure supportate](overview.md#supported-regions). La località può essere uguale o diversa da quella del gruppo di risorse.
* `resource-name` : (*facoltativo*) usato per la rappresentazione delle risorse di Azure del computer locale. Se non si specifica questo valore, viene usato il nome host del computer.

Per altre informazioni sullo strumento della riga di comando `azcmagent`, vedere la pagina relativa al [riferimento Azcmagent](./manage-agent.md).

### <a name="windows-installation-script"></a>Script di installazione in Windows

L'esempio seguente rappresenta un agente di Connected Machine per lo script di installazione Windows modificato per usare l'entità servizio in modo che supporti un'installazione completamente automatizzata e non interattiva dell'agente.

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

>[!NOTE]
>Lo script supporta solo l'esecuzione da una versione a 64 bit di Windows PowerShell.
>

### <a name="linux-installation-script"></a>Script di installazione Linux

L'esempio seguente rappresenta un agente di Connected Machine per lo script di installazione Linux modificato per usare l'entità servizio in modo che supporti un'installazione completamente automatizzata e non interattiva dell'agente.

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

>[!NOTE]
>Per eseguire **azcmagent**, è necessario disporre delle autorizzazioni di accesso alla *radice* nei computer Linux.

Dopo aver installato l'agente e configurato per la connessione ai server abilitati per Azure Arc, passare alla portale di Azure per verificare che il server sia stato connesso correttamente. Visualizzare le proprie macchine virtuali nel [portale di Azure](https://aka.ms/hybridmachineportal).

![Una connessione server riuscita](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come gestire il computer usando i [criteri di Azure](../../governance/policy/overview.md), ad esempio la configurazione di VM [Guest](../../governance/policy/concepts/guest-configuration.md), verificare che il computer stia segnalando l'area di lavoro Log Analytics prevista, abilitare il monitoraggio con [Monitoraggio di Azure con macchine virtuali](../../azure-monitor/insights/vminsights-enable-policy.md) e molto altro ancora.

- Ulteriori informazioni su [[log Analytics Agent]](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si desidera raccogliere dati di monitoraggio del carico di lavoro e del sistema operativo, gestirli con manuali operativi di automazione o funzionalità come Gestione aggiornamenti o usare altri servizi di Azure come il [Centro sicurezza di Azure](../../security-center/security-center-intro.md).