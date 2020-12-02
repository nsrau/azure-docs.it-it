---
title: Informazioni su criteri di Azure per Kubernetes
description: Informazioni su come il servizio Criteri di Azure usa Rego e Open Policy Agent per gestire i cluster che eseguono Kubernetes in Azure o in locale.
ms.date: 12/01/2020
ms.topic: conceptual
ms.openlocfilehash: e2b9253d8ce60d5dc77d406e3c9d0469539f2c77
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511332"
---
# <a name="understand-azure-policy-for-kubernetes-clusters"></a>Informazioni su Criteri di Azure per i cluster Kubernetes

Il servizio Criteri di Azure estende [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, un _webhook del controller di ammissione_ per [Open Policy Agent](https://www.openpolicyagent.org/) (OPA), per applicare tutele e misure di sicurezza su larga scala per i cluster in modo centralizzato e coerente. Con Criteri di Azure è possibile gestire i cluster Kubernetes e creare report sul loro stato di conformità da un'unica posizione. Il componente aggiuntivo svolge le funzioni seguenti:

- Verifica con il servizio Criteri di Azure le assegnazioni dei criteri al cluster.
- Distribuisce le definizioni dei criteri nel cluster come [modello di vincolo](https://github.com/open-policy-agent/gatekeeper#constraint-templates) e risorse personalizzate di tipo [vincolo](https://github.com/open-policy-agent/gatekeeper#constraints).
- Restituisce i dettagli di controllo e conformità al servizio Criteri di Azure.

Il servizio Criteri di Azure per Kubernetes supporta gli ambienti cluster seguenti:

- [Servizio Azure Kubernetes](../../../aks/intro-kubernetes.md)
- [Kubernetes con abilitazione di Azure Arc](../../../azure-arc/kubernetes/overview.md)
- [Motore del servizio Azure Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> I componenti aggiuntivi per il motore AKS e l'arco abilitato Kubernetes sono disponibili in **Anteprima**. Criteri di Azure per Kubernetes supporta solo i pool di nodi Linux e le definizioni dei criteri predefiniti. Le definizioni dei criteri predefinite sono nella categoria **Kubernetes**. Le definizioni dei criteri di anteprima limitati con effetto **EnforceOPAConstraint** e **EnforceRegoPolicy** e la categoria del **servizio Kubernetes** correlata sono _deprecate_. Usare invece la modalità di _controllo_ degli effetti e di _negazione_ con il provider di risorse `Microsoft.Kubernetes.Data` .

## <a name="overview"></a>Panoramica

Per abilitare e usare Criteri di Azure con il cluster Kubernetes, eseguire le operazioni seguenti:

1. Configurare il cluster Kubernetes e installare il componente aggiuntivo:
   - [Servizio Azure Kubernetes](#install-azure-policy-add-on-for-aks)
   - [Kubernetes con abilitazione di Azure Arc](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [Motore del servizio Azure Kubernetes](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > Per problemi comuni relativi all'installazione, vedere [risoluzione dei problemi-componente aggiuntivo criteri di Azure](../troubleshoot/general.md#add-on-for-kubernetes-installation-errors).

1. [Familiarizzare con il linguaggio di Criteri di Azure per Kubernetes](#policy-language)

1. [Assegnare una definizione predefinita al cluster Kubernetes](#assign-a-built-in-policy-definition)

1. [Attendere la convalida](#policy-evaluation)

## <a name="limitations"></a>Limitazioni

Le limitazioni generali seguenti si applicano al componente aggiuntivo di criteri di Azure per i cluster Kubernetes:

- Il componente aggiuntivo criteri di Azure per Kubernetes è supportato in Kubernetes versione **1,14** o successiva.
- Il componente aggiuntivo criteri di Azure per Kubernetes può essere distribuito solo nei pool di nodi Linux
- Sono supportate solo le definizioni di criteri predefinite
- Numero massimo di record non conformi per ogni criterio per cluster: **500**
- Numero massimo di record non conformi per ogni sottoscrizione: **1 milione**
- Le installazioni di Gatekeeper all'esterno del componente aggiuntivo criteri di Azure non sono supportate. Disinstallare i componenti installati da un'installazione precedente di gatekeeper prima di abilitare il componente aggiuntivo criteri di Azure.
- I [motivi della mancata conformità](../how-to/determine-non-compliance.md#compliance-reasons) non sono disponibili per la `Microsoft.Kubernetes.Data` 
   [modalità del provider di risorse](./definition-structure.md#resource-provider-modes). Usare [i dettagli del componente](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).
- Le [esenzioni](./exemption-structure.md) non sono supportate per le [modalità del provider di risorse](./definition-structure.md#resource-provider-modes).

Le limitazioni seguenti si applicano solo al componente aggiuntivo criteri di Azure per AKS:

- Non è possibile abilitare entrambi i [criteri di sicurezza AKS Pod](../../../aks/use-pod-security-policies.md) e il componente aggiuntivo criteri di Azure per AKS. Per altre informazioni, vedere la pagina relativa al [limite di sicurezza di AKS Pod](../../../aks/use-pod-security-on-azure-policy.md#limitations).
- Spazi dei nomi esclusi automaticamente dal componente aggiuntivo criteri di Azure per la valutazione: _Kube-System_, _Gatekeeper-System_ e _AKS-periscopio_.

## <a name="recommendations"></a>Consigli

Di seguito sono riportate indicazioni generali per l'uso del componente aggiuntivo criteri di Azure:

- Il componente aggiuntivo di criteri di Azure richiede tre componenti Gatekeeper per eseguire: 1 audit pod e 2 repliche Pod del webhook. Questi componenti utilizzano un numero maggiore di risorse, in quanto il numero di risorse Kubernetes e le assegnazioni di criteri aumentano nel cluster, che richiede operazioni di controllo e imposizione.

  - Per meno di 500 pod in un singolo cluster con un massimo di 20 vincoli: 2 vCPU e 350 MB di memoria per ogni componente.
  - Per più di 500 pod in un singolo cluster con un massimo di 40 vincoli: 3 vCPU e 600 MB di memoria per ogni componente.

- I pod Windows [non supportano i contesti di sicurezza](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods).
  Pertanto, alcune delle definizioni di criteri di Azure, ad esempio la disattivazione dei privilegi radice, non possono essere Escalate nei Pod Windows e si applicano solo ai pod Linux.

La raccomandazione seguente si applica solo ad AKS e al componente aggiuntivo criteri di Azure:

- Usare il pool di nodi `CriticalAddonsOnly` di sistema con Tain per pianificare i pod Gatekeeper. Per ulteriori informazioni, vedere [utilizzo dei pool di nodi di sistema](../../../aks/use-system-pools.md#system-and-user-node-pools).
- Proteggere il traffico in uscita dai cluster AKS. Per altre informazioni, vedere [controllare il traffico in uscita per i nodi del cluster](../../../aks/limit-egress-traffic.md).
- Se il cluster ha `aad-pod-identity` abilitato, i pod di identità gestita del nodo modificano i iptables dei nodi per intercettare le chiamate all'endpoint dei metadati dell'istanza di Azure. Questa configurazione significa che tutte le richieste effettuate all'endpoint dei metadati vengono intercettate da NMI anche se il Pod non lo usa `aad-pod-identity` . AzurePodIdentityException CRD può essere configurato per informare `aad-pod-identity` che qualsiasi richiesta all'endpoint di metadati originata da un pod che corrisponde alle etichette definite in CRD deve essere inoltrata senza alcuna elaborazione in NMI. I pod di sistema con `kubernetes.azure.com/managedby: aks` etichetta nello spazio dei nomi _Kube-System_ devono essere esclusi in `aad-pod-identity` tramite la configurazione di AzurePodIdentityException CRD. Per altre informazioni, vedere [disabilitare AAD-Pod-Identity per un pod o un'applicazione specifica](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  Per configurare un'eccezione, installare la [YAML di eccezione MIC](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="install-azure-policy-add-on-for-aks"></a>Installare il componente aggiuntivo Criteri di Azure per il servizio Azure Kubernetes

Prima di installare il componente aggiuntivo Criteri di Azure o di abilitare le funzionalità del servizio, la sottoscrizione deve abilitare i provider di risorse **Microsoft.ContainerService** e **Microsoft.PolicyInsights**.

1. È necessaria l'interfaccia della riga di comando di Azure versione 2.12.0 o successiva installata e configurata. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

1. Registrare i provider di risorse e le funzionalità di anteprima.

   - Portale di Azure:

     Registrare i provider di risorse **Microsoft.ContainerService** e **Microsoft.PolicyInsights**. Per le procedure, vedere [Provider e tipi di risorse](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

   - Interfaccia della riga di comando di Azure:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Kubernetes Service provider
     az provider register --namespace Microsoft.ContainerService

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
     ```

1. Se sono state installate definizioni dei criteri di anteprima limitate, rimuovere il componente aggiuntivo con il pulsante **Disabilita** nel cluster AKS nella pagina **criteri** .

1. La versione del cluster del servizio Azure Kubernetes deve essere la _1.14_ o successiva. Usare lo script seguente per verificare la versione del cluster del servizio Azure Kubernetes:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Installare la versione _2.12.0_ o successiva dell'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Una volta completati i passaggi dei prerequisiti, installare il componente aggiuntivo Criteri di Azure nel cluster del servizio Azure Kubernetes che si vuole gestire.

- Portale di Azure

  1. Avviare il servizio AKS nel portale di Azure selezionando **tutti i servizi**, quindi cercando e selezionando **Servizi Kubernetes**.

  1. Selezionare uno dei cluster del servizio Azure Kubernetes.

  1. Selezionare **criteri** sul lato sinistro della pagina del servizio Kubernetes.

  1. Nella pagina principale selezionare il pulsante **Abilita componente aggiuntivo**.

     <a name="migrate-from-v1"></a>
     > [!NOTE]
     > Se il pulsante **Disabilita componente aggiuntivo** è abilitato e viene visualizzato un messaggio di avviso di migrazione V2, il componente aggiuntivo V1 è installato e deve essere rimosso prima di assegnare le definizioni dei criteri V2. Il componente aggiuntivo _deprecato_ V1 verrà automaticamente sostituito con il componente aggiuntivo V2 a partire dal 24 agosto,
     > 2020. Sarà quindi necessario assegnare le nuove versioni V2 delle definizioni dei criteri. Per eseguire l'aggiornamento ora, attenersi alla seguente procedura:
     >
     > 1. Verificare che nel cluster del servizio contenitore di Azure sia installato il componente aggiuntivo V1 visitando la pagina **criteri** del cluster AKS e che il cluster corrente usi il componente aggiuntivo criteri di Azure V1... Messaggio.
     > 1. [Rimuovere il componente aggiuntivo](#remove-the-add-on-from-aks).
     > 1. Selezionare il pulsante **Abilita componente** aggiuntivo per installare la versione V2 del componente aggiuntivo.
     > 1. [Assegnare le versioni V2 delle definizioni dei criteri predefiniti V1](#assign-a-built-in-policy-definition)

- Interfaccia della riga di comando di Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

Per verificare che l'installazione del componente aggiuntivo sia stata completata correttamente e che i pod _azure-policy_ e _gatekeeper_ siano in esecuzione, eseguire il comando seguente:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Infine, verificare che sia installato il componente aggiuntivo più recente eseguendo questo comando dell'interfaccia della riga di comando di Azure, sostituendo `<rg>` con il nome del gruppo di risorse e `<cluster-name>` con il nome del cluster del servizio Azure Kubernetes: `az aks show --query addonProfiles.azurepolicy -g <rg> -n <cluster-name>`. Il risultato dovrebbe essere simile all'output seguente e **config.version** dovrebbe essere `v2`:

```output
"addonProfiles": {
    "azurepolicy": {
        "config": {
            "version": "v2"
        },
        "enabled": true,
        "identity": null
    },
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes-preview"></a><a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Installare il componente aggiuntivo criteri di Azure per Azure Arc Enabled Kubernetes (anteprima)

Prima di installare il componente aggiuntivo Criteri di Azure o di abilitare le funzionalità del servizio, la sottoscrizione deve abilitare il provider di risorse **Microsoft.PolicyInsights** e creare un'assegnazione di ruolo per l'entità servizio del cluster.

1. È necessaria l'interfaccia della riga di comando di Azure versione 2.12.0 o successiva installata e configurata. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

1. Per abilitare il provider di risorse, seguire la procedura descritta in [Provider e tipi di risorse](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) oppure eseguire l'interfaccia della riga di comando di Azure o il comando Azure PowerShell:

   - Interfaccia della riga di comando di Azure

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. La versione del cluster Kubernetes deve essere la _1.14_ o successiva.

1. Installare [Helm 3](https://v3.helm.sh/docs/intro/install/).

1. Connettere il cluster Kubernetes con abilitazione di Azure Arc. Per altre informazioni, vedere [Connettere un cluster Kubernetes abilitato per Azure Arc (anteprima)](../../../azure-arc/kubernetes/connect-cluster.md).

1. Ottenere l'ID risorsa di Azure completo del cluster Kubernetes con abilitazione di Azure Arc.

1. Aprire le porte per il componente aggiuntivo. Il componente aggiuntivo Criteri di Azure usa questi domini e queste porte per recuperare le definizioni e le assegnazioni dei criteri e segnalare la conformità del cluster a Criteri di Azure.

   |Dominio |Porta |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Assegnare "Ruolo con autorizzazioni di scrittura per i dati di Policy Insights (anteprima)" al cluster Kubernetes con abilitazione di Azure Arc. Sostituire `<subscriptionId>` con l'ID sottoscrizione, `<rg>` con il gruppo di risorse del cluster Kubernetes con abilitazione di Azure Arc e `<clusterName>` con il nome del cluster Kubernetes con abilitazione di Azure Arc. Tenere traccia dei valori restituiti per _appId_, _password_ e _tenant_ per i passaggi di installazione.

   - Interfaccia della riga di comando di Azure

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   Ecco un output di esempio dei comandi:

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

Una volta completati i passaggi dei prerequisiti, installare il componente aggiuntivo Criteri di Azure nel cluster Kubernetes con abilitazione di Azure Arc:

1. Aggiungere il repository del componente aggiuntivo Criteri di Azure a Helm:

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Installare il componente aggiuntivo Criteri di Azure usando il grafico Helm:

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   Per altre informazioni sugli elementi installati dal grafico Helm del componente aggiuntivo, vedere la [definizione del grafico Helm del componente aggiuntivo Criteri di Azure](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters) su GitHub.

Per verificare che l'installazione del componente aggiuntivo sia stata completata correttamente e che i pod _azure-policy_ e _gatekeeper_ siano in esecuzione, eseguire il comando seguente:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine-preview"></a><a name="install-azure-policy-add-on-for-aks-engine"></a>Installare il componente aggiuntivo criteri di Azure per il motore AKS (anteprima)

Prima di installare il componente aggiuntivo Criteri di Azure o di abilitare le funzionalità del servizio, la sottoscrizione deve abilitare il provider di risorse **Microsoft.PolicyInsights** e creare un'assegnazione di ruolo per l'entità servizio del cluster.

1. L'interfaccia della riga di comando di Azure 2.0.62 o versioni successive deve essere installata e configurata. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

1. Per abilitare il provider di risorse, seguire la procedura descritta in [Provider e tipi di risorse](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) oppure eseguire l'interfaccia della riga di comando di Azure o il comando Azure PowerShell:

   - Interfaccia della riga di comando di Azure

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Creare un'assegnazione di ruolo per l'entità servizio del cluster.

   - Se non si conosce l'ID app dell'entità servizio del cluster, cercarlo con il comando seguente.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Assegnare l'assegnazione di ruolo "Ruolo con autorizzazioni di scrittura per i dati di Policy Insights (anteprima)" all'ID app dell'entità servizio del cluster (valore _aadClientID_ dal passaggio precedente) con l'interfaccia della riga di comando di Azure. Sostituire `<subscriptionId>` con l'ID della sottoscrizione e `<aks engine cluster resource group>` con il gruppo di risorse in cui si trova il cluster Kubernetes autogestito del motore del servizio Azure Kubernetes.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

Una volta completati i passaggi dei prerequisiti, installare il componente aggiuntivo Criteri di Azure. L'installazione può avvenire durante il ciclo di creazione o aggiornamento di un motore del servizio Azure Kubernetes oppure come azione indipendente su un cluster esistente.

- Installazione durante un ciclo di creazione o aggiornamento

  Per abilitare il componente aggiuntivo Criteri di Azure durante la creazione di un nuovo cluster autogestito o come aggiornamento di un cluster esistente, includere la definizione del cluster della proprietà [addons](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) per il motore del servizio Azure Kubernetes.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  Per altre informazioni, vedere la guida esterna sulla [definizione del cluster del motore del servizio Azure Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Installazione nel cluster esistente con i grafici Helm

  Usare la procedura seguente per preparare il cluster e installare il componente aggiuntivo:

  1. Installare [Helm 3](https://v3.helm.sh/docs/intro/install/).

  1. Aggiungere il repository di Criteri di Azure a Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Per altre informazioni, vedere la [guida di avvio rapido al grafico Helm](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Installare il componente aggiuntivo con un grafico Helm. Sostituire `<subscriptionId>` con l'ID della sottoscrizione e `<aks engine cluster resource group>` con il gruppo di risorse in cui si trova il cluster Kubernetes autogestito del motore del servizio Azure Kubernetes.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Per altre informazioni sugli elementi installati dal grafico Helm del componente aggiuntivo, vedere la [definizione del grafico Helm del componente aggiuntivo Criteri di Azure](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine) su GitHub.

     > [!NOTE]
     > A causa della relazione tra il componente aggiuntivo Criteri di Azure e l'ID del gruppo di risorse, Criteri di Azure supporta un solo cluster del motore del servizio Azure Kubernetes per ogni gruppo di risorse.

Per verificare che l'installazione del componente aggiuntivo sia stata completata correttamente e che i pod _azure-policy_ e _gatekeeper_ siano in esecuzione, eseguire il comando seguente:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Linguaggio dei criteri

La struttura del linguaggio di Criteri di Azure per la gestione di Kubernetes segue quella delle definizioni dei criteri esistenti. Con la [modalità del provider di risorse](./definition-structure.md#resource-provider-modes) `Microsoft.Kubernetes.Data` , vengono usati gli effetti [Audit](./effects.md#audit) e [Deny](./effects.md#deny) per gestire i cluster Kubernetes. _Audit_ e _Deny_ devono fornire **informazioni dettagliate** sulle proprietà specifiche per l'uso del [Framework di vincolo OPA](https://github.com/open-policy-agent/frameworks/tree/master/constraint) e di Gatekeeper V3.

Come parte delle proprietà _details.constraintTemplate_ e _details.constraint_ nella definizione dei criteri, Criteri di Azure passa gli URI di queste [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) al componente aggiuntivo. Rego è il linguaggio supportato da OPA e Gatekeeper per convalidare una richiesta al cluster Kubernetes. Grazie al supporto di uno standard esistente per la gestione di Kubernetes, Criteri di Azure consente di riutilizzare le regole esistenti e di associarle a Criteri di Azure per un'esperienza di creazione di report di conformità cloud unificata. Per altre informazioni, vedere [Informazioni su Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="assign-a-built-in-policy-definition"></a>Assegnare una definizione di criteri predefinita

Per assegnare una definizione di criteri al cluster Kubernetes, è necessario disporre delle operazioni di assegnazione dei criteri appropriate per il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). I ruoli predefiniti e il **proprietario** dei **criteri delle risorse** di Azure hanno queste operazioni. Per altre informazioni, vedere [autorizzazioni RBAC di Azure in criteri di Azure](../overview.md#azure-rbac-permissions-in-azure-policy).

Trovare le definizioni dei criteri predefinite per la gestione del cluster eseguendo questa procedura nel portale di Azure:

1. Avviare il servizio Criteri di Azure nel portale di Azure. Selezionare **Tutti i servizi** nel riquadro sinistro e quindi cercare e selezionare **Criteri**.

1. Nel riquadro a sinistra della pagina Criteri di Azure selezionare **Definizioni**.

1. Nell'elenco a discesa Categoria usare **Seleziona tutto** per cancellare il filtro e quindi selezionare **Kubernetes**.

1. Selezionare la definizione di criteri e quindi il pulsante **Assegna**.

1. Impostare **Ambito** sul gruppo di gestione, sulla sottoscrizione o sul gruppo di risorse del cluster Kubernetes in cui si applicherà l'assegnazione dei criteri.

   > [!NOTE]
   > Quando si assegna la definizione di Criteri di Azure per Kubernetes, l'**ambito** deve includere la risorsa cluster. Per un cluster del motore del servizio Azure Kubernetes, **Ambito** deve essere impostato sul gruppo di risorse del cluster.

1. Compilare i campi **Nome** e **Descrizione** dell'assegnazione dei criteri per identificarla più facilmente.

1. Impostare l' [applicazione dei criteri](./assignment-structure.md#enforcement-mode) su uno dei valori riportati di seguito.

   - **Abilitata**: applica i criteri nel cluster. Le richieste di ammissione Kubernetes con violazioni vengono rifiutate.

   - **Disabilitata**: non applica i criteri nel cluster. Le richieste di ammissione Kubernetes con violazioni non vengono rifiutate. I risultati della valutazione della conformità sono ancora disponibili. Quando si implementano nuove definizioni di criteri nei cluster in esecuzione, l'opzione _Disabilitata_ è utile per il test della definizione dei criteri dato che le richieste di ammissione con violazioni non vengono rifiutate.

1. Selezionare **Avanti**.

1. Impostare i **valori di parametro**

   - Per escludere gli spazi dei nomi Kubernetes dalla valutazione dei criteri, specificare l'elenco di spazi dei nomi nel parametro **Esclusioni per gli spazi dei nomi**. È consigliabile escludere _kube-system_, _gatekeeper-system_ e _azure-arc_.

1. Selezionare **Rivedi e crea**.

In alternativa, usare la guida di avvio rapido [Creare un'assegnazione di criteri per identificare le risorse non conformi](../assign-policy-portal.md) per trovare e assegnare un criterio Kubernetes. Cercare una definizione di criteri Kubernetes al posto del valore "audit vms" di esempio.

> [!IMPORTANT]
> Le definizioni di criteri predefinite sono disponibili per i cluster Kubernetes nella categoria **Kubernetes**. Per un elenco di definizioni di criteri predefinite, vedere gli [esempi di Kubernetes](../samples/built-in-policies.md#kubernetes).

## <a name="policy-evaluation"></a>Valutazione dei criteri

Il componente aggiuntivo sincronizza le modifiche alle assegnazioni dei criteri con il servizio Criteri di Azure ogni 15 minuti.
Durante questo ciclo di aggiornamento, il componente aggiuntivo controlla le modifiche. Queste modifiche attivano la creazione, l'aggiornamento o l'eliminazione dei vincoli e dei modelli di vincolo.

In un cluster Kubernetes, se uno spazio dei nomi ha una delle etichette seguenti, le richieste di ammissione con violazioni non vengono rifiutate. I risultati della valutazione della conformità sono ancora disponibili.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> Anche se un amministratore del cluster può avere l'autorizzazione per creare e aggiornare i modelli di vincolo e le risorse vincolo installati dal componente aggiuntivo Criteri di Azure, questi scenari non sono supportati in quanto gli aggiornamenti manuali vengono sovrascritti. Gatekeeper continua a valutare i criteri che esistevano prima dell'installazione del componente aggiuntivo e dell'assegnazione delle definizioni dei criteri di Criteri di Azure.

Ogni 15 minuti il componente aggiuntivo richiede un'analisi completa del cluster. Dopo aver raccolto tramite Gatekeeper i dettagli dell'analisi completa e di eventuali valutazioni in tempo reale delle modifiche apportate al cluster, il componente aggiuntivo restituisce i risultati a Criteri di Azure in modo che vengano inclusi nei [dettagli di conformità](../how-to/get-compliance-data.md) come qualsiasi assegnazione di Criteri di Azure. Durante il ciclo di controllo vengono restituiti solo i risultati delle assegnazioni di criteri attive. I risultati del controllo possono anche essere considerati come [violazioni](https://github.com/open-policy-agent/gatekeeper#audit) elencate nel campo di stato del vincolo non riuscito. Per informazioni dettagliate sulle risorse _non conformi_ , vedere [Dettagli dei componenti per le modalità del provider di risorse](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).

> [!NOTE]
> Ogni report di conformità in Criteri di Azure per i cluster Kubernetes include tutte le violazioni verificatesi negli ultimi 45 minuti. Il timestamp indica il momento in cui si è verificata una violazione.

Altre considerazioni:

- Se la sottoscrizione del cluster è registrata nel centro sicurezza di Azure, i criteri Kubernetes del Centro sicurezza di Azure vengono applicati automaticamente al cluster.

- Quando un criterio Deny viene applicato al cluster con risorse Kubernetes esistenti, la risorsa preesistente non conforme al nuovo criterio continua a essere eseguita. Quando la risorsa non conforme viene ripianificata in un nodo diverso, il gatekeeper blocca la creazione della risorsa.

- Quando un cluster ha un criterio Deny per la convalida delle risorse, l'utente non visualizzerà un messaggio di rifiuto durante la creazione di una distribuzione. Si consideri, ad esempio, una distribuzione Kubernetes che contiene ReplicaSets e Pod. Quando un utente esegue `kubectl describe deployment $MY_DEPLOYMENT` , non restituisce un messaggio di rifiuto come parte degli eventi. Restituisce tuttavia `kubectl describe replicasets.apps $MY_DEPLOYMENT` gli eventi associati al rifiuto.

## <a name="logging"></a>Registrazione

In quanto controller/contenitori Kubernetes, i pod _azure-policy_ e _gatekeeper_ conservano i log nel cluster Kubernetes. I log possono essere esposti nella pagina **Informazioni dettagliate** del cluster Kubernetes.
Per altre informazioni, vedere [Monitorare le prestazioni del cluster Kubernetes con Monitoraggio di Azure per i contenitori](../../../azure-monitor/insights/container-insights-analyze.md).

Per visualizzare i log del componente aggiuntivo, usare `kubectl`:

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

Per altre informazioni, vedere la sezione [Debugging](https://github.com/open-policy-agent/gatekeeper#debugging) nella documentazione di Gatekeeper.

## <a name="troubleshooting-the-add-on"></a>Risoluzione dei problemi del componente aggiuntivo

Per altre informazioni sulla risoluzione dei problemi del componente aggiuntivo per Kubernetes, vedere la [sezione Kubernetes](/azure/governance/policy/troubleshoot/general#add-on-for-kubernetes-general-errors) dell'articolo sulla risoluzione dei problemi di criteri di Azure.

## <a name="remove-the-add-on"></a>Rimuovere il componente aggiuntivo

### <a name="remove-the-add-on-from-aks"></a>Rimuovere il componente aggiuntivo dal servizio Azure Kubernetes

Per rimuovere il componente aggiuntivo Criteri di Azure dal cluster del servizio Azure Kubernetes, usare il portale di Azure o l'interfaccia della riga di comando di Azure:

- Portale di Azure

  1. Avviare il servizio AKS nel portale di Azure selezionando **tutti i servizi**, quindi cercando e selezionando **Servizi Kubernetes**.

  1. Selezionare il cluster del servizio Azure Kubernetes in cui si vuole disabilitare il componente aggiuntivo Criteri di Azure.

  1. Selezionare **criteri** sul lato sinistro della pagina del servizio Kubernetes.

  1. Nella pagina principale selezionare il pulsante **Disabilita componente aggiuntivo**.

- Interfaccia della riga di comando di Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>Rimuovere il componente aggiuntivo da Kubernetes con abilitazione di Azure Arc

Per rimuovere il componente aggiuntivo Criteri di Azure e Gatekeeper dal cluster Kubernetes con abilitazione di Azure Arc, eseguire il comando Helm seguente:

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>Rimuovere il componente aggiuntivo dal motore del servizio Azure Kubernetes

Per rimuovere il componente aggiuntivo Criteri di Azure e Gatekeeper dal cluster del motore del servizio Azure Kubernetes, usare il metodo appropriato alla modalità di installazione del componente aggiuntivo:

- Se l'installazione è stata eseguita impostando la proprietà **addons** nella definizione del cluster per il motore del servizio Azure Kubernetes:

  Ridistribuire la definizione del cluster nel motore del servizio Azure Kubernetes dopo aver impostato la proprietà **addons** di _azure-policy_ su false:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  Per altre informazioni, vedere [Motore del servizio Azure Kubernetes - Disabilitare il componente aggiuntivo Criteri di Azure](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on).

- Se l'installazione è stata eseguita con i grafici Helm, eseguire il comando Helm seguente:

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dati di diagnostica raccolti dal componente aggiuntivo Criteri di Azure

Il componente aggiuntivo Criteri di Azure per Kubernetes raccoglie dati di diagnostica del cluster limitati. Questi dati diagnostici sono dati tecnici di importanza vitale correlati al software e alle prestazioni. Vengono usati per:

- Mantenere aggiornato il componente aggiuntivo Criteri di Azure
- Mantenere il componente aggiuntivo Criteri di Azure sicuro, affidabile ed efficiente
- Migliorare il componente aggiuntivo Criteri di Azure tramite l'analisi aggregata dell'utilizzo del componente aggiuntivo

Le informazioni raccolte dal componente aggiuntivo non sono dati personali. Attualmente vengono raccolti i dettagli seguenti:

- Versione dell'agente del componente aggiuntivo Criteri di Azure
- Tipo di cluster
- Area del cluster
- Gruppo risorse cluster
- ID della risorsa cluster
- ID sottoscrizione del cluster
- Sistema operativo del cluster (ad esempio: Linux)
- Città del cluster (ad esempio: Seattle)
- Stato o provincia del cluster (ad esempio: Washington)
- Paese o area geografica del cluster (ad esempio: Stati Uniti)
- Eccezioni/errori rilevati dal componente aggiuntivo Criteri di Azure durante l'installazione dell'agente nella valutazione dei criteri
- Numero di definizioni dei criteri Gatekeeper non installate dal componente aggiuntivo Criteri di Azure

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md).
- Vedere [Struttura delle definizioni di criteri di Azure](definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](effects.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Leggere le informazioni su come [ottenere dati sulla conformità](../how-to/get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi](../how-to/remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
