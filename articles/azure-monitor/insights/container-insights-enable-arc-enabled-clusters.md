---
title: Configurare il cluster Kubernetes abilitato per Azure Arc con monitoraggio di Azure per i contenitori | Microsoft Docs
description: Questo articolo descrive come configurare il monitoraggio con monitoraggio di Azure per i contenitori nei cluster Kubernetes abilitati per Azure Arc.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 44512acbd09df449dbba2177bb10f22f480b82d6
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977525"
---
# <a name="enable-monitoring-of-azure-arc-enabled-kubernetes-cluster"></a>Abilita il monitoraggio del cluster Kubernetes abilitato per Azure Arc

Monitoraggio di Azure per i contenitori offre un'esperienza di monitoraggio avanzata per i cluster di Azure Kubernetes Service (AKS) e del motore AKS. Questo articolo descrive come abilitare il monitoraggio dei cluster Kubernetes ospitati all'esterno di Azure abilitati con Azure Arc, per ottenere un'esperienza di monitoraggio simile.

Il monitoraggio di Azure per i contenitori può essere abilitato per una o più distribuzioni esistenti di Kubernetes usando uno script di PowerShell o bash.

## <a name="supported-configurations"></a>Configurazioni supportate

Monitoraggio di Azure per i contenitori supporta il monitoraggio di Azure Arc abilitato Kubernetes (anteprima), come descritto nell'articolo [introduttivo](container-insights-overview.md) , ad eccezione delle funzionalità seguenti:

- Dati in tempo reale (anteprima)

- [Raccogliere le metriche](container-insights-update-metrics.md) dai nodi e dai pod del cluster e archiviarle nel database di metriche di monitoraggio di Azure

Il codice seguente è ufficialmente supportato con monitoraggio di Azure per i contenitori:

- Le versioni di Kubernetes e i criteri di supporto corrispondono alle versioni di [AKS supportate](../../aks/supported-kubernetes-versions.md).

- Sono supportati i seguenti runtime di contenitori: i Runtime compatibili con Docker, Moby e CRI, ad esempio CRI-O e contenitori.

- La versione del sistema operativo Linux per i nodi master e di lavoro supportati sono: Ubuntu (18,04 LTS e 16,04 LTS).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di disporre degli elementi seguenti:

- Un'area di lavoro Log Analytics.

    Il monitoraggio di Azure per i contenitori supporta un'area di lavoro Log Analytics nelle aree elencate in prodotti Azure in [base all'area](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Per creare un'area di lavoro personalizzata, è possibile crearla tramite [Azure Resource Manager](../platform/template-workspace-configuration.md), tramite [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)o nel [portale di Azure](../learn/quick-create-workspace.md).

- Per abilitare e accedere alle funzionalità di monitoraggio di Azure per i contenitori, è necessario essere almeno un membro del ruolo *collaboratore* di Azure nella sottoscrizione di Azure e un membro del ruolo [*collaboratore log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) dell'area di lavoro log Analytics configurato con monitoraggio di Azure per i contenitori.

- Si è un membro del ruolo [collaboratore](../../role-based-access-control/built-in-roles.md#contributor) per la risorsa cluster di Azure Arc.

- Per visualizzare i dati di monitoraggio, l'utente è membro dell'autorizzazione [*log Analytics Role Reader*](../platform/manage-access.md#manage-access-using-azure-permissions) con l'area di lavoro log Analytics configurata con monitoraggio di Azure per i contenitori.

- [Client Helm](https://helm.sh/docs/using_helm/) per caricare il grafico di monitoraggio di Azure per i contenitori per il cluster Kubernetes specificato.

- Le informazioni di configurazione del proxy e del firewall seguenti sono necessarie per la comunicazione tra la versione in contenitori dell'agente di Log Analytics per Linux e il monitoraggio di Azure:

    |Risorsa agente|Porte |
    |------|---------|
    |`*.ods.opinsights.azure.com` |Porta 443 |
    |`*.oms.opinsights.azure.com` |Porta 443 |
    |`*.dc.services.visualstudio.com` |Porta 443 |

- `cAdvisor secure port: 10250`Per raccogliere le metriche delle prestazioni, l'agente in contenitori richiede l'apertura o l'apertura di Kubelet in `unsecure port :10255` tutti i nodi del cluster. Se non è già configurato, è consigliabile configurare `secure port: 10250` nel cAdvisor di Kubelet.

- Per poter comunicare con il servizio API Kubernetes all'interno del cluster, è necessario specificare le variabili di ambiente seguenti nel contenitore per la raccolta dei dati di inventario, `KUBERNETES_SERVICE_HOST` e `KUBERNETES_PORT_443_TCP_PORT` .

    >[!IMPORTANT]
    >La versione minima dell'agente supportata per il monitoraggio dei cluster Kubernetes abilitati per Arc è ciprod04162020 o versione successiva.

- [PowerShell Core](/powershell/scripting/install/installing-powershell?view=powershell-6&preserve-view=true) è necessario se si Abilita il monitoraggio usando il metodo di script di PowerShell.

- [Bash versione 4](https://www.gnu.org/software/bash/) è necessario se si Abilita il monitoraggio usando il metodo bash con script.

## <a name="identify-workspace-resource-id"></a>Identificare l'ID risorsa dell'area di lavoro

Per abilitare il monitoraggio del cluster usando lo script di PowerShell o bash scaricato in precedenza e l'integrazione con un'area di lavoro Log Analytics esistente, seguire questa procedura per identificare prima di tutto l'ID risorsa completo dell'area di lavoro di Log Analytics. Questa operazione è necessaria per il `workspaceResourceId` parametro quando si esegue il comando per abilitare il componente aggiuntivo di monitoraggio per l'area di lavoro specificata. Se non si dispone di un'area di lavoro da specificare, è possibile ignorare l'inclusione del `workspaceResourceId` parametro e lasciare che lo script crei una nuova area di lavoro.

1. Elencare tutte le sottoscrizioni a cui si ha accesso usando il comando seguente:

    ```azurecli
    az account list --all -o table
    ```

    L'output sarà simile al seguente:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    Copiare il valore per **SubscriptionId**.

2. Passare alla sottoscrizione che ospita l'area di lavoro Log Analytics usando il comando seguente:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Nell'esempio seguente viene visualizzato l'elenco delle aree di lavoro nelle sottoscrizioni nel formato JSON predefinito.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Nell'output trovare il nome dell'area di lavoro, quindi copiare l'ID risorsa completo dell'area di lavoro Log Analytics sotto l' **ID**campo.

## <a name="enable-monitoring-using-powershell"></a>Abilitare il monitoraggio tramite PowerShell

1. Scaricare e salvare lo script in una cartella locale che configuri il cluster con il componente aggiuntivo monitoraggio usando i comandi seguenti:

    ```powershell
    wget https://aka.ms/enable-monitoring-powershell-script -outfile enable-monitoring.ps1
    ```

2. Configurare la `$azureArcClusterResourceId` variabile impostando i valori corrispondenti per `subscriptionId` `resourceGroupName` e `clusterName` rappresentando l'ID risorsa della risorsa cluster Kubernetes abilitata per Azure Arc.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configurare la `$kubeContext` variabile con il **contesto Kube** del cluster eseguendo il comando `kubectl config get-contexts` . Se si desidera utilizzare il contesto corrente, impostare il valore su `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Se si vuole usare l'area di lavoro Log Analytics di monitoraggio di Azure esistente, configurare la variabile `$logAnalyticsWorkspaceResourceId` con il valore corrispondente che rappresenta l'ID risorsa dell'area di lavoro. In caso contrario, impostare la variabile su `""` e lo script crea un'area di lavoro predefinita nel gruppo di risorse predefinito della sottoscrizione del cluster, se non ne esiste già una nell'area. L'area di lavoro predefinita creata è simile al formato di *DefaultWorkspace \<SubscriptionID> - \<Region> -*.

    ```powershell
    $logAnalyticsWorkspaceResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Se il cluster Kubernetes abilitato per l'arco comunica tramite un server proxy, configurare la variabile `$proxyEndpoint` con l'URL del server proxy. Se il cluster non comunica tramite un server proxy, è possibile impostare il valore su `""` .  Per ulteriori informazioni, vedere [configure proxy endpoint](#configure-proxy-endpoint) più avanti in questo articolo.

6. Eseguire il comando seguente per abilitare il monitoraggio.

    ```powershell
    .\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
    ```

Dopo aver abilitato il monitoraggio, possono essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità per il cluster.

### <a name="using-service-principal"></a>Uso dell'entità servizio
Lo script *enable-monitoring.ps1* usa l'accesso interattivo al dispositivo. Se si preferisce un accesso non interattivo, è possibile usare un'entità servizio esistente o crearne una nuova con le autorizzazioni necessarie, come descritto in [prerequisiti](#prerequisites). Per usare l'entità servizio, è necessario passare i parametri $servicePrincipalClientId, $servicePrincipalClientSecret e $tenantId con i valori dell'entità servizio che si intende usare per *enable-monitoring.ps1* script.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"
```

L'assegnazione di ruolo riportata di seguito è applicabile solo se si usa un'area di lavoro di Log Analytics esistente in una sottoscrizione di Azure diversa da quella della risorsa cluster di Arc K8s connessa.

```powershell
$logAnalyticsWorkspaceResourceId = "<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
New-AzRoleAssignment -RoleDefinitionName 'Log Analytics Contributor'  -ObjectId $servicePrincipal.Id -Scope  $logAnalyticsWorkspaceResourceId

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

Ad esempio:

```powershell
.\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
```



## <a name="enable-using-bash-script"></a>Abilitare l'uso di script bash

Eseguire la procedura seguente per abilitare il monitoraggio tramite lo script bash fornito.

1. Scaricare e salvare lo script in una cartella locale che configuri il cluster con il componente aggiuntivo monitoraggio usando i comandi seguenti:

    ```bash
    curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script
    ```

2. Configurare la `azureArcClusterResourceId` variabile impostando i valori corrispondenti per `subscriptionId` `resourceGroupName` e `clusterName` rappresentando l'ID risorsa della risorsa cluster Kubernetes abilitata per Azure Arc.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configurare la `kubeContext` variabile con il **contesto Kube** del cluster eseguendo il comando `kubectl config get-contexts` . Se si desidera utilizzare il contesto corrente, impostare il valore su `""` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Se si vuole usare l'area di lavoro Log Analytics di monitoraggio di Azure esistente, configurare la variabile `logAnalyticsWorkspaceResourceId` con il valore corrispondente che rappresenta l'ID risorsa dell'area di lavoro. In caso contrario, impostare la variabile su `""` e lo script crea un'area di lavoro predefinita nel gruppo di risorse predefinito della sottoscrizione del cluster, se non ne esiste già una nell'area. L'area di lavoro predefinita creata è simile al formato di *DefaultWorkspace \<SubscriptionID> - \<Region> -*.

    ```bash
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Se il cluster Kubernetes abilitato per l'arco comunica tramite un server proxy, configurare la variabile `proxyEndpoint` con l'URL del server proxy. Se il cluster non comunica tramite un server proxy, è possibile impostare il valore su `""` . Per ulteriori informazioni, vedere [configure proxy endpoint](#configure-proxy-endpoint) più avanti in questo articolo.

6. Per abilitare il monitoraggio nel cluster, sono disponibili comandi diversi in base allo scenario di distribuzione.

    Eseguire il comando seguente per abilitare il monitoraggio con le opzioni predefinite, ad esempio usando il contesto Kube corrente, creare un'area di lavoro Log Analytics predefinita e senza specificare un server proxy:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Eseguire il comando seguente per creare un'area di lavoro Log Analytics predefinita e senza specificare un server proxy:

    ```bash
   bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

    Eseguire il comando seguente per usare un'area di lavoro Log Analytics esistente e senza specificare un server proxy:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId
    ```

    Eseguire il comando seguente per usare un'area di lavoro Log Analytics esistente e specificare un server proxy:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
    ```

Dopo aver abilitato il monitoraggio, possono essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità per il cluster.

### <a name="using-service-principal"></a>Uso dell'entità servizio
Lo script bash *Enable-Monitoring.sh* usa l'accesso interattivo al dispositivo. Se si preferisce un accesso non interattivo, è possibile usare un'entità servizio esistente o crearne una nuova con le autorizzazioni necessarie, come descritto in [prerequisiti](#prerequisites). Per usare l'entità servizio, è necessario passare i valori--client-ID,--client-Secret e--tenant-ID dell'entità servizio che si vuole usare per *Enable-Monitoring.sh* script bash.

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')
```

L'assegnazione di ruolo riportata di seguito è applicabile solo se si usa un'area di lavoro di Log Analytics esistente in una sottoscrizione di Azure diversa da quella della risorsa cluster di Arc K8s connessa.

```bash
logAnalyticsWorkspaceResourceId="<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
az role assignment create --role 'Log Analytics Contributor' --assignee $servicePrincipalClientId --scope $logAnalyticsWorkspaceResourceId

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

Ad esempio:

```bash
bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
```

## <a name="configure-proxy-endpoint"></a>Configurare l'endpoint proxy

Con l'agente in contenitori per monitoraggio di Azure per i contenitori, è possibile configurare un endpoint proxy per consentire la comunicazione attraverso il server proxy. La comunicazione tra l'agente in contenitori e monitoraggio di Azure può essere un server proxy HTTP o HTTPS ed è supportata sia l'autenticazione anonima che quella di base (nome utente/password).

Il valore di configurazione proxy ha la sintassi seguente: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Se il server proxy non richiede l'autenticazione, è comunque necessario specificare un nome utente/password di pseudoclasse. Può trattarsi di qualsiasi nome utente o password.

|Proprietà| Descrizione |
|--------|-------------|
|Protocollo | http o https |
|utente | Nome utente facoltativo per l'autenticazione proxy |
|password | Password facoltativa per l'autenticazione proxy |
|proxyhost | Indirizzo o FQDN del server proxy |
|port | Numero di porta facoltativo per il server proxy |

Ad esempio: `http://user01:password@proxy01.contoso.com:3128`

Se si specifica il protocollo come **http**, le richieste HTTP vengono create usando la connessione protetta SSL/TLS. Il server proxy deve supportare i protocolli SSL/TLS.

### <a name="configure-using-powershell"></a>Configurare con PowerShell

Specificare il nome utente e la password, l'indirizzo IP o il nome di dominio completo e il numero di porta per il server proxy. Ad esempio:

```powershell
$proxyEndpoint = https://<user>:<password>@<proxyhost>:<port>
```

### <a name="configure-using-bash"></a>Configurare con bash

Specificare il nome utente e la password, l'indirizzo IP o il nome di dominio completo e il numero di porta per il server proxy. Ad esempio:

```bash
export proxyEndpoint=https://<user>:<password>@<proxyhost>:<port>
```

## <a name="next-steps"></a>Passaggi successivi

- Con il monitoraggio abilitato per raccogliere lo stato e l'utilizzo delle risorse del cluster Kubernetes abilitato per Arc e dei carichi di lavoro in esecuzione su di essi, Scopri [come usare](container-insights-analyze.md) monitoraggio di Azure per i contenitori.

- Per impostazione predefinita, l'agente in contenitori raccoglie i log del contenitore stdout/stderr di tutti i contenitori in esecuzione in tutti gli spazi dei nomi ad eccezione di Kube-System. Per configurare la raccolta di log del contenitore specifica per determinati spazi dei nomi o spazi dei nomi, esaminare la [configurazione dell'agente di container Insights](container-insights-agent-config.md) per configurare le impostazioni di raccolta dati desiderate nel file di configurazione di ConfigMap.

- Per rimuovere e analizzare le metriche di Prometeo dal cluster, vedere [configurare la metrica di Prometeo](container-insights-prometheus-integration.md)

- Per informazioni su come interrompere il monitoraggio del cluster Kubernetes abilitato per Arc con monitoraggio di Azure per i contenitori, vedere [come arrestare il monitoraggio del cluster ibrido](container-insights-optout-hybrid.md#how-to-stop-monitoring-on-arc-enabled-kubernetes).
