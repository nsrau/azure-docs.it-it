---
title: Informazioni sui criteri di Azure per il servizio Azure KubernetesLearn Azure Policy for Azure Kubernetes Service
description: Informazioni su come Criteri di Azure usa Rego e Agente criteri aperti per gestire i cluster nel servizio Azure Kubernetes.Learn how Azure Policy uses Rego and Open Policy Agent to manage clusters on Azure Kubernetes Service.
ms.date: 03/27/2020
ms.topic: conceptual
ms.openlocfilehash: d77c5cf94a8239f4617e563961cbe1cc40e48fe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372650"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Comprendere i criteri di Azure per il servizio Azure Kubernetes

Criteri di Azure si integra con il [servizio Azure Kubernetes](../../../aks/intro-kubernetes.md) (AKS) per applicare le imposizione e le misure di sicurezza su larga scala nei cluster in modo centralizzato e coerente.
Estendendo l'uso di [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, un _webhook_ di controller di ammissione per [Open Policy Agent](https://www.openpolicyagent.org/) (OPA), Criteri di Azure consente di gestire e creare report sullo stato di conformità delle risorse di Azure e dei cluster AKS da un'unica posizione.

> [!IMPORTANT]
> Criteri di Azure per AKS è in anteprima e supporta solo le definizioni dei criteri predefinite. I criteri predefiniti rientrano nella categoria **Kubernetes.** L'effetto **EnforceRegoPolicy** e i relativi criteri di categoria **Kubernetes Service** sono _deprecati._ Utilizzare invece l'effetto [EnforceOPAConstraint](./effects.md#enforceopaconstraint) aggiornato.

> [!WARNING]
> Questa funzionalità non è ancora disponibile in tutte le aree geografiche. Per uno stato di implementazione, vedere [Problemi di AKS - Interruzione della modifica per](https://github.com/Azure/AKS/issues/1529)il componente aggiuntivo per i criteri .

## <a name="overview"></a>Panoramica

Per abilitare e usare Criteri di Azure per AKS con il cluster AKS, eseguire le azioni seguenti:To enable and use Azure Policy for AKS with your AKS cluster, take the following actions:

- [Acconsenti esplicitamente alle funzionalità di anteprima](#opt-in-for-preview)
- [Installare il componente aggiuntivo Criteri di AzureInstall the Azure Policy Add-on](#installation-steps)
- [Assegnare una definizione di criteri per AKSAssign a policy definition for AKS](#built-in-policies)
- [Attendere la convalida](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Opt-in per l'anteprima

Prima di installare il componente aggiuntivo Criteri di Azure o di abilitare una delle funzionalità del servizio, la sottoscrizione deve abilitare il provider di risorse **Microsoft.ContainerService** e **Microsoft.PolicyInsights,** quindi ottenere l'approvazione per partecipare all'anteprima. Per partecipare all'anteprima, seguire questi passaggi nel portale di Azure o con l'interfaccia della riga di comando di Azure:To join the preview, follow these steps in either the Azure portal or with Azure CLI:

- Portale di Azure:

  1. Registrare i provider di risorse **Microsoft.ContainerService** e **Microsoft.PolicyInsights.Register the Microsoft.ContainerService and Microsoft.PolicyInsights** resource providers. Per la procedura, vedere [Tipi e provider di risorse](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

  1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

     ![Cercare Criteri di Azure in Tutti i servizi](../media/rego-for-aks/search-policy.png)

  1. Selezionare Aggiungi anteprima sul lato sinistro della pagina Criteri di Azure.Select **Join Preview** on the left side of the Azure Policy page.

     ![Partecipare all'anteprima di Policy for AKS](../media/rego-for-aks/join-aks-preview.png)

  1. Selezionare la riga della sottoscrizione da aggiungere all'anteprima.

  1. Selezionare il pulsante **Opt-in** nella parte superiore dell'elenco delle sottoscrizioni.

- Interfaccia della riga di comando di Azure:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Service provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  ```

## <a name="azure-policy-add-on"></a>Componente aggiuntivo Criteri di AzureAzure Policy Add-on

Il componente aggiuntivo Criteri di Azure per Kubernetes connette il servizio Criteri di Azure al controller di ammissione di Gatekeeper.The _Azure Policy Add-on_ for Kubernetes connects the Azure Policy service to the Gatekeeper admission controller. Il componente aggiuntivo, installato nello spazio dei nomi del _sistema kube,_ attua le seguenti funzioni:

- Controlli con il servizio Criteri di Azure per le assegnazioni al cluster.
- Distribuisce i criteri nel cluster come modelli di [vincolo](https://github.com/open-policy-agent/gatekeeper#constraint-templates) e risorse personalizzate di [vincolo.](https://github.com/open-policy-agent/gatekeeper#constraints)
- Segnala i dettagli di controllo e conformità al servizio Criteri di Azure.Reports auditing and compliance details back to Azure Policy service.

### <a name="installing-the-add-on"></a>Installazione del componente aggiuntivo

#### <a name="prerequisites"></a>Prerequisiti

Prima di installare il componente aggiuntivo nel cluster AKS, è necessario installare l'estensione di anteprima. Questo passaggio viene eseguito con l'interfaccia della riga di comando di Azure:This step is done with Azure CLI:

1. Se sono stati installati criteri Gatekeeper v2, rimuovere il componente aggiuntivo con il pulsante **Disabilita** nel cluster AKS nella pagina **Criteri (anteprima).**

1. È necessaria l'interfaccia della riga di comando di Azure versione 2.0.62 o successiva installata e configurata. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

1. Il cluster AKS deve essere versione _1.14_ o successiva. Utilizzare lo script seguente per convalidare la versione del cluster AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Installare la versione _0.4.0_ dell'estensione di `aks-preview`anteprima dell'interfaccia della riga di comando di Azure per AKS, :

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Se in precedenza è stata installata l'estensione `az extension update --name aks-preview` _aks-preview,_ installare eventuali aggiornamenti utilizzando il comando.

#### <a name="installation-steps"></a>Procedura di installazione

Una volta completati i prerequisiti, installare il componente aggiuntivo Criteri di Azure nel cluster AKS che si vuole gestire.

- Portale di Azure

  1. Avviare il servizio AKS nel portale di Azure facendo clic su **Tutti i servizi**, quindi cercando e selezionando Servizi **Kubernetes**.

  1. Selezionare uno dei cluster AKS.

  1. Seleziona **Criteri (anteprima)** sul lato sinistro della pagina del servizio Kubernetes.

     ![Criteri dal cluster AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Nella pagina principale, selezionare il **pulsante Abilita componente aggiuntivo.**

     ![Abilitare il componente aggiuntivo Criteri di Azure per AKSEnable the Azure Policy for AKS add-on](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Se il pulsante Abilita componente **aggiuntivo** è disattivato, la sottoscrizione non è ancora stata aggiunta all'anteprima. Vedere [Opt-in per l'anteprima](#opt-in-for-preview) per i passaggi necessari. Se è disponibile un pulsante **Disabilita,** Gatekeeper v2 è ancora installato e deve essere rimosso.

- Interfaccia della riga di comando di Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Frequenza di convalida e reporting

Il componente aggiuntivo esegue il check-in con il servizio Criteri di Azure per le modifiche nelle assegnazioni dei criteri ogni 15 minuti.
Durante questo ciclo di aggiornamento, il componente aggiuntivo verifica la presenza di modifiche. Queste modifiche trigger crea, aggiorna o elimina i modelli di vincolo e vincoli.

> [!NOTE]
> Mentre un amministratore del cluster può disporre dell'autorizzazione per creare e aggiornare i modelli di vincolo e le risorse dei vincoli, questi non sono scenari supportati poiché gli aggiornamenti manuali verranno sovrascritti.

Ogni 15 minuti, il componente aggiuntivo richiede un'analisi completa del cluster. Dopo aver raccolto i dettagli dell'analisi completa e di tutte le valutazioni in tempo reale da parte di Gatekeeper delle tentativi di modifiche al cluster, il componente aggiuntivo riporta i risultati al servizio Criteri di Azure per l'inclusione nei dettagli di [conformità,](../how-to/get-compliance-data.md#portal) ad esempio qualsiasi assegnazione di Criteri di Azure.After gathering details of the full scan and any real-time evaluations by Gatekeeper of attempted changes to the cluster, the add-on reports the results back to Azure Policy service for inclusion in compliance details like any Azure Policy assignment. Solo i risultati per le assegnazioni dei criteri attivi vengono restituiti durante il ciclo di controllo. I risultati del controllo possono anche essere considerati come [violazioni](https://github.com/open-policy-agent/gatekeeper#audit) elencate nel campo dello stato del vincolo non riuscito.

## <a name="policy-language"></a>Lingua dei criteri

La struttura del linguaggio dei criteri di Azure per la gestione di Kubernetes segue quella dei criteri esistenti. L'effetto _EnforceOPAConstraint_ viene utilizzato per gestire i cluster Kubernetes e accetta le proprietà dei dettagli specifiche per l'utilizzo di [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) e Gatekeeper v3. Per informazioni dettagliate ed esempi, vedere l'effetto [EnforceOPAConstraint.For details](./effects.md#enforceopaconstraint) and examples, see the EnforceOPAConstraint effect.
  
Come parte delle proprietà _details.constraintTemplate_ e _details.constraint_ nella definizione dei criteri, Criteri di Azure passa gli URI di questi [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) al componente aggiuntivo. Rego è il linguaggio che OPA e Gatekeeper supportano per convalidare una richiesta al cluster Kubernetes. Supportando uno standard esistente per la gestione di Kubernetes, Criteri di Azure consente di riutilizzare le regole esistenti e associarle a criteri di Azure per un'esperienza di report di conformità cloud unificata. Per ulteriori informazioni, vedere [Che cos'è Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="built-in-policies"></a>Criteri predefiniti

Per trovare i criteri predefiniti per la gestione del cluster tramite il portale di Azure, eseguire la procedura seguente:To find the built-in policies for managing your cluster using the Azure portal, follow these steps:

1. Avviare il servizio Criteri di Azure nel portale di Azure.Start the Azure Policy service in the Azure portal. Selezionare Tutti i servizi nel riquadro sinistro, quindi cercare e selezionare **Criterio**.

1. Nel riquadro sinistro della pagina Criteri di Azure selezionare **Definizioni**.

1. Dalla casella di riepilogo a discesa Categoria , utilizzare Seleziona tutto per cancellare il filtro e quindi selezionare **Kubernetes**.

1. Selezionare la definizione dei criteri, quindi selezionare il pulsante **Assegna.Select** the policy definition, then select the Assign button.

1. Impostare **l'ambito** sul gruppo di gestione, sulla sottoscrizione o sul gruppo di risorse del cluster Kubernetes in cui verrà applicata l'assegnazione dei criteri.

   > [!NOTE]
   > Quando si assegna il criterio di Azure per la definizione AKS, **l'ambito** deve includere la risorsa cluster AKS.

1. Assegnare all'assegnazione dei criteri un **nome** e una **descrizione** che è possibile utilizzare per identificarla facilmente.

1. Impostare [l'applicazione dei criteri](./assignment-structure.md#enforcement-mode) su uno dei valori seguenti.

   - **Abilitato:** consente di applicare il criterio nel cluster. Le richieste di ammissione di Kubernetes con violazioni sono respinte.
   
   - **Disabilitato:** non applicare i criteri nel cluster. Le richieste di ammissione di Kubernetes con violazioni non vengono negate. I risultati della valutazione della conformità sono ancora disponibili. Quando si implementano nuovi criteri per l'esecuzione di cluster, l'opzione _Disabilitato_ è utile per testare i criteri in quanto le richieste di ammissione con violazioni non vengono negate.

1. Fare clic su **Avanti**.

1. Impostare i **valori dei parametri**
   
   - Per escludere gli spazi dei nomi Kubernetes dalla valutazione dei criteri, specificare l'elenco degli spazi dei nomi nel parametro **Namespace exclusions**. Si consiglia di escludere: _kube-system_

1. Selezionare **Revisione e creazione**.

In alternativa, usare la Guida introduttiva [Assegna un criterio - Portale](../assign-policy-portal.md) per trovare e assegnare un criterio AKS. Cercare una definizione dei criteri Kubernetes anziché l'esempio "audit vms".

> [!IMPORTANT]
> I criteri predefiniti nella categoria **Kubernetes** sono solo per l'uso con AKS. Per un elenco dei criteri predefiniti, vedere Esempi di [Kubernetes](../samples/built-in-policies.md#kubernetes).

## <a name="logging"></a>Registrazione

### <a name="azure-policy-add-on-logs"></a>Log del componente aggiuntivo Criteri di AzureAzure Policy Add-on logs

In qualità di controller/contenitore Kubernetes, sia il componente aggiuntivo per i criteri di Azure che gatekeeper conservano i log nel cluster AKS. I log vengono esposti nella pagina **Approfondimenti** del cluster AKS. Per altre informazioni, vedere [Informazioni sulle prestazioni del cluster AKS con Monitoraggio di Azure per i contenitori.](../../../azure-monitor/insights/container-insights-analyze.md)

## <a name="remove-the-add-on"></a>Rimuovere il componente aggiuntivo

Per rimuovere il componente aggiuntivo Criteri di Azure dal cluster AKS, usare il portale di Azure o l'interfaccia della riga di comando di Azure:To remove the Azure Policy Add-on from your AKS cluster, use either the Azure portal or Azure CLI:

- Portale di Azure

  1. Avviare il servizio AKS nel portale di Azure facendo clic su **Tutti i servizi**, quindi cercando e selezionando Servizi **Kubernetes**.

  1. Selezionare il cluster AKS in cui si vuole disabilitare il componente aggiuntivo Criteri di Azure.Select your AKS cluster where you want to disable the Azure Policy Add-on.

  1. Seleziona **Criteri (anteprima)** sul lato sinistro della pagina del servizio Kubernetes.

     ![Criteri dal cluster AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Nella pagina principale, selezionare il **pulsante Disattiva componente aggiuntivo.**

     ![Disabilitare il componente aggiuntivo Criteri di Azure per AKSDisable the Azure Policy for AKS add-on](../media/rego-for-aks/disable-policy-add-on.png)

- Interfaccia della riga di comando di Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dati di diagnostica raccolti dal componente aggiuntivo Criteri di AzureDiagnostic data collected by Azure Policy Add-on

Il componente aggiuntivo Criteri di Azure per Kubernetes raccoglie dati di diagnostica del cluster limitati. Questi dati diagnostici sono dati tecnici fondamentali relativi al software e alle prestazioni. Viene usata nei modi seguenti:

- Mantenere aggiornato il componente aggiuntivo Criteri di AzureKeep Azure Policy Add-on up up
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
