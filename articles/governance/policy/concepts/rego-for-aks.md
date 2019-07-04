---
title: Informazioni su come controllare il contenuto di una macchina virtuale
description: Informazioni su come criteri di Azure Usa Rego e aprire l'agente criteri per gestire i cluster in Azure Kubernetes Service.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: fdb392533e28df1d50e90c842d0117385afb254b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453905"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Comprendere i criteri di Azure per Azure Kubernetes Service

Criteri di Azure si integrano con il [Azure Kubernetes Service](../../../aks/intro-kubernetes.md) (AKS) per applicare si applicano le regole su larga scala e le misure di sicurezza nei cluster in modo centralizzato e coerente.
Mediante l'utilizzo di estensione [GateKeeper](https://github.com/open-policy-agent/gatekeeper), un _ammissione controller webhook_ per [aprire l'agente criteri](https://www.openpolicyagent.org/) (Op), criteri di Azure consente di gestire e creare report sulla conformità stato di risorse di Azure e i cluster servizio contenitore di AZURE da un'unica posizione.

> [!NOTE]
> Criteri di Azure per AKS sono in anteprima limitata e supporta solo definizioni di criteri predefiniti.

## <a name="overview"></a>Panoramica

Per abilitare e usare criteri di Azure per servizio contenitore di AZURE con il cluster AKS, intraprendere le azioni seguenti:

- [Acconsenti esplicitamente alle funzionalità di anteprima](#opt-in-for-preview)
- [Installare il componente aggiuntivo di criteri di Azure](#installation-steps)
- [Assegnare una definizione di criteri per AKS](#built-in-policies)
- [Attesa per la convalida](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Acconsenti esplicitamente di anteprima

Prima di installare il componente aggiuntivo dei criteri di Azure o l'abilitazione delle funzionalità del servizio, è necessario abilitare la sottoscrizione di **containerservice** provider di risorse e il **policyinsights**provider di risorse, quindi essere approvato per partecipare all'anteprima. Per partecipare all'anteprima, seguire questi passaggi nel portale di Azure o con CLI di Azure:

- Portale di Azure:

  1. Registrare il **containerservice** e **policyinsights** i provider di risorse. Per istruzioni, vedere [provider di risorse e i tipi](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

  1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

     ![Cercare Criteri di Azure in Tutti i servizi](../media/rego-for-aks/search-policy.png)

  1. Selezionare **Join anteprima** sul lato sinistro della pagina Criteri di Azure.

     ![Aggiungere i criteri per l'anteprima AKS](../media/rego-for-aks/join-aks-preview.png)

  1. Selezionare la riga della sottoscrizione che si desidera aggiungere all'anteprima.

  1. Selezionare il **acconsenti esplicitamente** nella parte superiore dell'elenco di sottoscrizioni.

- Interfaccia della riga di comando di Azure:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove

  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  ```

## <a name="azure-policy-add-on"></a>Componente aggiuntivo di criteri di Azure

Il _componente aggiuntivo di criteri di Azure_ per Kubernetes si connette il servizio Criteri di Azure per il controller di ricovero di GateKeeper. Il componente aggiuntivo, che viene installato nel _azure-policy_ dello spazio dei nomi, esegue le funzioni seguenti:

- Controlli con criteri di Azure per le assegnazioni a del cluster servizio contenitore di AZURE
- Scarica e memorizza nella cache i dettagli dei criteri, tra cui il _rego_ definizione dei criteri, come **configmaps**
- Esegue un controllo di conformità di analisi completa nel cluster AKS
- Eseguire il report di controllo e i dettagli di conformità a criteri di Azure

### <a name="installing-the-add-on"></a>Installare il componente aggiuntivo

#### <a name="prerequisites"></a>Prerequisiti

Prima di installare il componente aggiuntivo nel cluster AKS, deve essere installato l'estensione di anteprima. Questo passaggio viene eseguito con CLI di Azure:

1. È necessario la CLI di Azure versione 2.0.62 o versione successiva installato e configurato. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

1. Versione deve essere il cluster AKS _1.10_ o versione successiva. Usare lo script seguente per convalidare la versione del cluster servizio contenitore di AZURE:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Installare versione _0.4.0_ della riga di comando di Azure di AKS, anteprima estensione `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Se in precedenza sono stati installati il _aks-preview_ estensione, installare eventuali aggiornamenti usando il `az extension update --name aks-preview` comando.

#### <a name="installation-steps"></a>Procedura di installazione

Dopo aver completato i prerequisiti, è possibile installare il componente aggiuntivo di criteri di Azure nel cluster AKS che si desidera gestire.

- Portale di Azure

  1. Avviare il servizio contenitore di AZURE nel portale di Azure facendo clic **tutti i servizi**, quindi cercare e selezionare **servizi Kubernetes**.

  1. Selezionare uno dei cluster AKS.

  1. Selezionare **criteri (anteprima)** sul lato sinistro della pagina del servizio Kubernetes.

     ![Criteri dal cluster servizio contenitore di AZURE](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Nella pagina principale, selezionare la **componente aggiuntivo di abilitare** pulsante.

     ![Abilitare i criteri di Azure per il componente aggiuntivo servizio contenitore di AZURE](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Se il **componente aggiuntivo di abilitare** pulsante è disattivato, la sottoscrizione non è ancora stato aggiunto all'anteprima. Visualizzare [Opt-in per l'anteprima](#opt-in-for-preview) per i passaggi necessari.

- Interfaccia della riga di comando di Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Convalida e la frequenza di creazione di report

Il componente aggiuntivo può collegarsi criteri di Azure per le modifiche nelle assegnazioni dei criteri ogni 5 minuti. Durante questo ciclo di aggiornamento, il componente aggiuntivo rimuove tutti _configmaps_ nel _criteri di azure_ dello spazio dei nomi quindi ricrea il _configmaps_ per l'uso di GateKeeper.

> [!NOTE]
> Mentre un _amministratore del cluster_ potrebbe disporre dell'autorizzazione per il _criteri di azure_ dello spazio dei nomi, esso non consigliato o supportato per apportare modifiche allo spazio dei nomi. Le modifiche manuali apportate vengono perse durante il ciclo di aggiornamento.

Ogni 5 minuti, viene chiamato il componente aggiuntivo per un'analisi completa del cluster. Dopo aver raccolto i dettagli di tutte le valutazioni in tempo reale e l'analisi completa da GateKeeper delle modifiche tentate al cluster, il componente aggiuntivo segnala i risultati al criterio di Azure per l'inclusione nei [dettagli di conformità](../how-to/get-compliance-data.md) , ad esempio eventuali criteri di Azure assegnazione. Vengono restituiti solo i risultati per le assegnazioni di criteri attivi durante il ciclo di controllo.

## <a name="policy-language"></a>Linguaggio dei criteri

La struttura di lingua di criteri di Azure per la gestione di AKS segue che dei criteri esistenti. L'effetto _EnforceRegoPolicy_ viene usato per gestire i cluster AKS e accetta _dettagli_ proprietà specifiche per l'uso con OPA e GateKeeper. Per informazioni dettagliate ed esempi, vedere la [EnforceRegoPolicy](effects.md#enforceregopolicy) effetto.

Durante la _details.policy_ proprietà nella definizione dei criteri, criteri di Azure passa l'URI di un criterio rego al componente aggiuntivo. Rego è il linguaggio che supportano OPA e GateKeeper per convalidare o modificare una richiesta per il cluster Kubernetes. Grazie al supporto di uno standard esistente per la gestione di Kubernetes, criteri di Azure rende possibile riutilizzare le regole esistenti e associarle a criteri di Azure per una conformità di cloud unificata esperienza di report. Per altre informazioni, vedere [What ' s Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Criteri predefiniti

Per trovare i criteri predefiniti per la gestione di servizio contenitore di AZURE usando il portale di Azure, seguire questa procedura:

1. Avviare il servizio Criteri di Azure nel portale di Azure. Selezionare **tutti i servizi** nel riquadro a sinistra e quindi cercare e selezionare **criterio**.

1. Nel riquadro sinistro della pagina Criteri di Azure, selezionare **definizioni**.

1. Nella casella di riepilogo discesa categoria, usare **Seleziona tutto** per cancellare il filtro e quindi selezionare **servizio Kubernetes**.

1. Selezionare la definizione dei criteri, quindi selezionare il **assegnare** pulsante.

> [!NOTE]
> Quando si assegnano i criteri di Azure per la definizione del servizio contenitore di AZURE, il **ambito** deve includere la risorsa del cluster servizio contenitore di AZURE.

In alternativa, usare il [assegnare un criterio - portale](../assign-policy-portal.md) avvio rapido per trovare e assegnare un criterio del servizio contenitore di AZURE. Ricerca di una definizione di criteri di Kubernetes invece di esempio 'controllo le macchine virtuali'.

## <a name="logging"></a>Registrazione

### <a name="azure-policy-add-on-logs"></a>Log di componenti aggiuntivi dei criteri di Azure

Come Kubernetes controller/contenitore, il componente aggiuntivo di criteri di Azure mantiene i registri nel cluster AKS. I log vengono esposte nel **Insights** pagina del cluster servizio contenitore di AZURE. Per altre informazioni, vedere [prestazioni con monitoraggio di Azure per contenitori del cluster AKS comprendere](../../../azure-monitor/insights/container-insights-analyze.md).

### <a name="gatekeeper-logs"></a>Log di gateKeeper

Per abilitare i log di GateKeeper per le nuove richieste di risorse, seguire i passaggi descritti in [abilitare ed esaminare i log di nodo principale nel servizio contenitore di AZURE Kubernetes](../../../aks/view-master-logs.md).
Ecco un esempio di query per visualizzare gli eventi negati in nuove richieste di risorse:

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Per visualizzare i log dai contenitori GateKeeper, seguire i passaggi descritti in [abilitare ed esaminare i log di nodo principale nel servizio contenitore di AZURE Kubernetes](../../../aks/view-master-logs.md) e controllare le _kube-apiserver_ opzione il **impostazionididiagnostica** riquadro.

## <a name="remove-the-add-on"></a>Rimuovere il componente aggiuntivo

Per rimuovere il componente aggiuntivo di criteri di Azure dal cluster servizio contenitore di AZURE, usare il portale di Azure o Azure CLI:

- Portale di Azure

  1. Avviare il servizio contenitore di AZURE nel portale di Azure facendo clic **tutti i servizi**, quindi cercare e selezionare **servizi Kubernetes**.

  1. Selezionare il cluster servizio contenitore di AZURE in cui si desidera disabilitare il componente aggiuntivo di criteri di Azure.

  1. Selezionare **criteri (anteprima)** sul lato sinistro della pagina del servizio Kubernetes.

     ![Criteri dal cluster servizio contenitore di AZURE](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Nella pagina principale, selezionare la **disabilitare il componente aggiuntivo** pulsante.

     ![Disabilitare i criteri di Azure per il componente aggiuntivo servizio contenitore di AZURE](../media/rego-for-aks/disable-policy-add-on.png)

- Interfaccia della riga di comando di Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="next-steps"></a>Passaggi successivi

- Esaminare gli esempi nella [esempi di criteri di Azure](../samples/index.md).
- Vedere [Struttura delle definizioni di criteri di Azure](definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](effects.md).
- Comprendere come [a livello di codice, creare criteri](../how-to/programmatically-create.md).
- Informazioni su come [ottenere i dati di conformità](../how-to/getting-compliance-data.md).
- Informazioni su come [monitora e aggiorna le risorse non conformi](../how-to/remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/index.md).