---
title: Informazioni su Criteri di Azure per il motore AKSLearn Azure Policy for AKS Engine
description: Informazioni su come Criteri di Azure usa CustomResourceDefinitions e Open Policy Agent di Gatekeeper v3 per gestire i cluster con AKS Engine.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c41a9d84dfe43e356e9a4a17af523a37209c2933
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436433"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Informazioni sui criteri di Azure per il motore AKSUnderstand Azure Policy for AKS Engine

Criteri di Azure si integra con [AKS Engine,](https://github.com/Azure/aks-engine/blob/master/docs/README.md)un sistema che offre strumenti pratici per eseguire rapidamente il bootstrap di un cluster Kubernetes autogestito in Azure.Azure Policy integrates with AKS Engine , a system that provides convenient tooling to quickly bootstrap a self-managed Kubernetes cluster on Azure. Questa integrazione consente di applicare e proteggere su larga scala i cluster autogestiti del motore AKS in modo centralizzato e coerente. Estendendo l'uso di [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3 (beta), un _webhook_ di controller di ammissione per Kubernetes, Criteri di Azure consente di gestire e creare report sullo stato di conformità delle risorse di Azure e dei cluster autogestiti del motore AKS da un'unica posizione.

> [!NOTE]
> Criteri di Azure per il motore AKS è in anteprima pubblica e non ha alcun servizio di archiviazione. Gatekeeper v3 è in Beta ed è supportato dalla comunità open source. Il servizio supporta solo le definizioni dei criteri predefinite e un singolo cluster del motore AKS per ogni gruppo di risorse configurato con un'entità servizio.

> [!IMPORTANT]
> Per ottenere il supporto per Criteri di Azure per il motore AKS, il motore AKS o gatekeeper v3, creare un [nuovo problema](https://github.com/Azure/aks-engine/issues/new/choose) nel repository GitHub del motore AKS.

## <a name="overview"></a>Panoramica

Per abilitare e usare Criteri di Azure per il motore AKS con il cluster Kubernetes autogestito in Azure, eseguire le azioni seguenti:To enable and use Azure Policy for AKS Engine with your self-managed Kubernetes cluster on Azure, take the following actions:

- [Prerequisiti](#prerequisites)
- [Installare il componente aggiuntivo Criteri di AzureInstall the Azure Policy Add-on](#installing-the-add-on)
- [Assegnare una definizione dei criteri per il motore AKSAssign a policy definition for AKS Engine](#built-in-policies)
- [Attendere la convalida](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Prerequisiti

Prima di installare il componente aggiuntivo Criteri di Azure o abilitare una delle funzionalità del servizio, la sottoscrizione deve abilitare il provider di risorse **Microsoft.PolicyInsights** e creare un'assegnazione di ruolo per l'entità servizio cluster. 

1. Per abilitare il provider di risorse, seguire i passaggi descritti in Provider e tipi di risorse oppure eseguire l'interfaccia della riga di comando di Azure o il comando di Azure PowerShell:To enable the resource provider, follow the steps in [Resource providers and types](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) or run either the Azure CLI or Azure PowerShell command:

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

1. Creare un'assegnazione di ruolo per l'entità servizio clusterCreate a role assignment for the cluster service principal

   - Se non si conosce l'ID applicazione dell'entità del servizio cluster, cercarlo con il comando seguente.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Assegnare l'assegnazione di ruolo 'Policy Insights Data Writer (anteprima)' all'ID applicazione dell'entità servizio cluster (valore aadClientID del passaggio precedente) con l'interfaccia della riga di comando di Azure.Assign 'Policy Insights Data Writer (Preview)' role assignment to the cluster service principal app ID (value _aadClientID_ from previous step) with Azure CLI. Sostituire `<subscriptionId>` con l'ID sottoscrizione e `<aks engine cluster resource group>` con il gruppo di risorse in cui si trova il cluster Kubernetes autogestito del motore AKS.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Componente aggiuntivo Criteri di AzureAzure Policy Add-on

Il componente aggiuntivo Criteri di Azure per Kubernetes connette il servizio Criteri di Azure al controller di ammissione di Gatekeeper.The _Azure Policy Add-on_ for Kubernetes connects the Azure Policy service to the Gatekeeper admission controller. Il componente aggiuntivo, installato nello spazio dei nomi del _sistema kube,_ attua le seguenti funzioni:

- Controlli con criteri di Azure per le assegnazioni al cluster del motore AKSChecks with Azure Policy for assignments to the AKS Engine cluster
- Scarica e installa i dettagli dei criteri, i modelli di vincolo e i vincoli
- Esegue un controllo di conformità dell'analisi completa nel cluster a KS Engine
- Segnala i dettagli di controllo e conformità ai criteri di AzureReports auditing and compliance details back to Azure Policy

### <a name="installing-the-add-on"></a>Installazione del componente aggiuntivo

Una volta completati i prerequisiti, è possibile installare il componente aggiuntivo Criteri di Azure.Once the prerequisites are completed, the Azure Policy Add-on can be installed. L'installazione può essere durante il ciclo di creazione o aggiornamento di un motore AKS o come azione indipendente in un cluster esistente.

- Installazione durante il ciclo di creazione o aggiornamento

  Per abilitare il componente aggiuntivo Criteri di Azure durante la creazione di un nuovo cluster autogestito o come aggiornamento di un cluster esistente, includere la definizione del cluster di proprietà **dei componenti aggiuntivi** per il motore AKS.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true,
      "config": {
          "auditInterval": "30",
          "constraintViolationsLimit": "20"
      }
  }]
  ```

  Per ulteriori informazioni, vedere la guida esterna [Definizione cluster del motore AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Installare in un cluster esistente con Helm Charts

  Utilizzare la procedura seguente per preparare il cluster e installare il componente aggiuntivo:

  1. Installare Gatekeeper nello spazio dei nomi _gatekeeper-system._

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Aggiungete l'etichetta del piano di _controllo_ al _kube-system_. Questa etichetta esclude il controllo dei pod e dei servizi del sistema kube da parte di Gatekeeper e del componente aggiuntivo Criteri di Azure.This label excludes the auditing of _kube-system_ pods and services by Gatekeeper and the Azure Policy Add-on.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Sincronizzare i dati di Kubernetes (Spazio dei nomi, Pod, Ingresso, Servizio) con OPA.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Per ulteriori informazioni, vedere [OPA - Replica dei dati](https://github.com/open-policy-agent/gatekeeper#replicating-data).

  1. Aggiungere il repository dei criteri di Azure a Helm.Add the Azure Policy repo to Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Per ulteriori informazioni, vedere [Helm Chart - Guida introduttiva](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Installare il componente aggiuntivo con un Helm Chart. Sostituire `<subscriptionId>` con l'ID sottoscrizione e `<aks engine cluster resource group>` con il gruppo di risorse in cui si trova il cluster Kubernetes autogestito del motore AKS.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Per altre informazioni su ciò che installa il componente aggiuntivo Helm Chart, vedere la definizione di Azure Policy Add-on Helm Chart in GitHub.For more information about what the add-on Helm Chart installs, see the [Azure Policy Add-on Helm Chart definition](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) on GitHub.

     > [!NOTE]
     > A causa della relazione tra il componente aggiuntivo Criteri di Azure e l'ID del gruppo di risorse, Criteri di Azure supporta un solo cluster del motore AKS per ogni gruppo di risorse.

Per verificare che l'installazione del componente aggiuntivo sia stata eseguita correttamente e che il pod _Azure-policy_ sia in esecuzione, eseguire il comando seguente:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Frequenza di convalida e reporting

Il componente aggiuntivo esegue il check-in con Criteri di Azure per le modifiche nelle assegnazioni dei criteri ogni 5 minuti. Durante questo ciclo di aggiornamento, il componente aggiuntivo verifica la presenza di modifiche. Queste modifiche trigger crea, aggiorna o elimina i modelli di vincolo e vincoli.

> [!NOTE]
> Anche se un _amministratore del cluster_ può disporre dell'autorizzazione per apportare modifiche ai modelli di vincolo e ai vincoli, non è consigliabile o supportato apportare modifiche ai modelli di vincolo o ai vincoli creati da Criteri di Azure.While a cluster admin may have permission to make changes to constraint templates and constraints, it's not recommended or supported to make changes to constraint templates or constraints created by Azure Policy. Tutte le modifiche manuali apportate andranno perse durante il ciclo di aggiornamento.

Ogni 5 minuti, il componente aggiuntivo richiede un'analisi completa del cluster. Dopo aver raccolto i dettagli dell'analisi completa e di tutte le valutazioni in tempo reale da parte di Gatekeeper delle tentativi di modifiche al cluster, il componente aggiuntivo riporta i risultati ai criteri di Azure per l'inclusione nei dettagli di [conformità,](../how-to/get-compliance-data.md) ad esempio qualsiasi assegnazione di Criteri di Azure.After gathering details of the full scan and any real-time evaluations by Gatekeeper of attempted changes to the cluster, the add-on reports the results back to Azure Policy for inclusion in compliance details like any Azure Policy assignment. Solo i risultati per le assegnazioni dei criteri attivi vengono restituiti durante il ciclo di controllo. I risultati del controllo possono anche essere considerati come violazioni elencate nel campo dello stato del vincolo non riuscito.

## <a name="policy-language"></a>Lingua dei criteri

La struttura del linguaggio dei criteri di Azure per la gestione del motore AKS segue quella dei criteri esistenti. L'effetto _EnforceOPAConstraint_ viene utilizzato per gestire i cluster del motore AKS e accetta le proprietà _dei dettagli_ specifiche per l'utilizzo di [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) e Gatekeeper v3. Per informazioni dettagliate ed esempi, vedere l'effetto [EnforceOPAConstraint.For details](effects.md#enforceopaconstraint) and examples, see the EnforceOPAConstraint effect.

Come parte delle proprietà _details.constraintTemplate_ e _details.constraint_ nella definizione dei criteri, Criteri di Azure passa gli URI di questi [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) al componente aggiuntivo. Rego è il linguaggio che OPA e Gatekeeper supportano per convalidare una richiesta al cluster Kubernetes. Supportando uno standard esistente per la gestione di Kubernetes, Criteri di Azure consente di riutilizzare le regole esistenti e associarle a criteri di Azure per un'esperienza di report di conformità cloud unificata. Per ulteriori informazioni, vedere [Che cos'è Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Criteri predefiniti

Per trovare i criteri predefiniti per la gestione del cluster del motore AKS tramite il portale di Azure, eseguire la procedura seguente:To find the built-in policies for managing your AKS Engine cluster using the Azure portal, follow these steps:

1. Avviare il servizio Criteri di Azure nel portale di Azure.Start the Azure Policy service in the Azure portal. Selezionare **Tutti i servizi** nel riquadro sinistro, quindi cercare e selezionare **Criterio**.

1. Nel riquadro sinistro della pagina Criteri di Azure selezionare **Definizioni**.

1. Dalla casella di riepilogo a discesa Categoria , utilizzare **Seleziona tutto** per cancellare il filtro e quindi selezionare **Kubernetes**.

1. Selezionare la definizione dei criteri, quindi selezionare il pulsante **Assegna.Select** the policy definition, then select the Assign button.

> [!NOTE]
> Quando si assegna il criterio di Azure per la definizione del motore AKS, **l'ambito** deve essere il gruppo di risorse del cluster del motore AKS.

In alternativa, usare la guida introduttiva [Assegna un criterio - Portale](../assign-policy-portal.md) per trovare e assegnare un criterio del motore AKS. Cercare una definizione dei criteri del motore AKS anziché l'esempio "audit vms".

> [!IMPORTANT]
> I criteri predefiniti nella categoria **Kubernetes** sono utilizzabili solo con AKS Engine.

## <a name="logging"></a>Registrazione

### <a name="azure-policy-add-on-logs"></a>Log del componente aggiuntivo Criteri di AzureAzure Policy Add-on logs

Come controller/contenitore Kubernetes, il componente aggiuntivo criteri di Azure mantiene i log nel cluster del motore AKS.

Per visualizzare i log del componente `kubectl`aggiuntivo Criteri di Azure, usare:To view the Azure Policy Add-on logs, use:

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Registri gatekeeper

Il contenitore Gatekeeper, _gatekeeper-controller-manager-0_ `gatekeeper-system` , `kube-system` si trova in genere nello spazio dei nomi o , ma può trovarsi in uno spazio dei nomi diverso a seconda della modalità di distribuzione.

Per visualizzare i registri `kubectl`di Gatekeeper, utilizzare:

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Per ulteriori informazioni, vedere [Debug Gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) nella documentazione OPA.

## <a name="remove-the-add-on"></a>Rimuovere il componente aggiuntivo

Per rimuovere il componente aggiuntivo criteri di Azure e Gatekeeper dal cluster del motore AKS, usare il metodo in linea con la modalità di installazione del componente aggiuntivo:To remove the Azure Policy Add-on and Gatekeeper from your AKS Engine cluster, use the method that aligns with how the add-on was installed:

- Se installato impostando la proprietà **addons** nella definizione del cluster per AKS Engine:

  Ridistribuire la definizione del cluster nel motore AKS dopo aver modificato la proprietà **addons** per azure-policy su false:Redeploy the cluster definition to AKS Engine after changing the addons property for _azure-policy_ to false:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Se installato con Helm Charts:

  1. Rimuovere i vecchi vincoli

     Attualmente il meccanismo di disinstallazione rimuove solo il sistema Gatekeeper , non rimuove le risorse _ConstraintTemplate_, _Constraint_o _Config_ create dall'utente, né rimuove _i CRD_ad fianco.

     Quando Gatekeeper è in esecuzione, è possibile rimuovere i vincoli indesiderati:

     - Eliminazione di tutte le istanze della risorsa vincolo
     - Eliminazione della risorsa _ConstraintTemplate,_ che deve pulire automaticamente il _CRDDeleting_ the ConstraintTemplate resource, which should clean up the CRD
     - L'eliminazione della risorsa _config_ rimuove i finalizzatori nelle risorse sincronizzate

  1. Disinstallare il componente aggiuntivo Criteri di AzureUninstall Azure Policy Add-on
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Disinstallare Gatekeeper
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dati di diagnostica raccolti dal componente aggiuntivo Criteri di AzureDiagnostic data collected by Azure Policy Add-on

Il componente aggiuntivo Criteri di Azure per Kubernetes raccoglie dati di diagnostica del cluster limitati. Questi dati diagnostici sono dati tecnici fondamentali relativi al software e alle prestazioni. Viene usata nei modi seguenti:

- Mantenere aggiornato il componente aggiuntivo Criteri di AzureKeep Azure Policy Add-on up-to-date
- Mantenere il componente aggiuntivo Criteri di Azure sicuro, affidabile e performante
- Migliorare il componente aggiuntivo Criteri di Azure tramite l'analisi aggregata dell'uso del componente aggiuntivo

Le informazioni raccolte dal componente aggiuntivo non sono dati personali. Attualmente sono raccolti i seguenti dettagli:

- Versione dell'agente del componente aggiuntivo Criteri di AzureAzure Policy Add-on agent version
- Tipo di cluster
- Area cluster
- Gruppo di risorse cluster
- ID risorsa cluster
- ID sottoscrizione cluster
- Sistema operativo del cluster (esempio: Linux)
- Città del cluster (esempio: Seattle)
- Stato o provincia del cluster (Esempio: Washington)
- Paese o area geografica del cluster (Esempio: Stati Uniti)
- Eccezioni/errori rilevati dal componente aggiuntivo Criteri di Azure durante l'installazione dell'agente durante la valutazione dei criteriExceptions/errors encountered by Azure Policy Add-on during agent installation on policy evaluation
- Numero di criteri Gatekeeper non installati dal componente aggiuntivo Criteri di AzureNumber of Gatekeeper policies not installed by Azure Policy Add-on

## <a name="next-steps"></a>Passaggi successivi

- Esaminare esempi in [Esempi di criteri di Azure](../samples/index.md).Review examples at Azure Policy samples .
- Esaminare la struttura di [definizione dei criteri](definition-structure.md).
- Rivedere [Informazioni sugli effetti dei criteri](effects.md).
- Comprendere come creare criteri a livello di [codice.](../how-to/programmatically-create.md)
- Scopri come ottenere i dati di [conformità](../how-to/get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi.](../how-to/remediate-resources.md)
- Esaminare le informazioni su cui si trova un gruppo di gestione con Organizzare le risorse con i gruppi di gestione di Azure.Review what a management group is with [Organize your resources with Azure management groups](../../management-groups/overview.md).