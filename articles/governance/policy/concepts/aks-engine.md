---
title: Informazioni su criteri di Azure per il motore AKS
description: Informazioni su come i criteri di Azure usano CustomResourceDefinitions e aprono l'agente criteri da Gatekeeper V3 per gestire i cluster con il motore AKS.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 904575680b62233ad5ec7422abbf66cf57dc4e11
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072054"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Informazioni sui criteri di Azure per il motore AKS

Criteri di Azure si integra con il [motore AKS](https://github.com/Azure/aks-engine/blob/master/docs/README.md), un sistema che offre strumenti pratici per eseguire rapidamente il bootstrap di un cluster Kubernetes autogestito in Azure. Questa integrazione consente imposte su larga scala e misure di sicurezza nei cluster autogestiti del motore AKS in modo centralizzato e coerente. Estendendo l'uso di [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) V3 (beta), un _webhook del controller di ammissione_ per Kubernetes, i criteri di Azure consentono di gestire e creare report sullo stato di conformità delle risorse di Azure e del motore AKS cluster autogestiti da un'unica posizione.

> [!NOTE]
> Criteri di Azure per il motore AKS è in versione di anteprima pubblica e non prevede alcun contratto di servizio. Gatekeeper V3 è in versione beta ed è supportato dalla community open source. Il servizio supporta solo le definizioni di criteri predefinite e un singolo cluster del motore AKS per ogni gruppo di risorse configurato con un'entità servizio.

> [!IMPORTANT]
> Per ottenere supporto per criteri di Azure per il motore AKS, il motore AKS o Gatekeeper V3, creare un [nuovo problema](https://github.com/Azure/aks-engine/issues/new/choose) nel repository GitHub del motore AKS.

## <a name="overview"></a>Overview

Per abilitare e usare criteri di Azure per il motore AKS con il cluster Kubernetes autogestito in Azure, eseguire le azioni seguenti:

- [Prerequisiti](#prerequisites)
- [Installare il componente aggiuntivo criteri di Azure](#installing-the-add-on)
- [Assegnare una definizione di criteri per il motore AKS](#built-in-policies)
- [Attendi convalida](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>prerequisiti

Prima di installare il componente aggiuntivo di criteri di Azure o abilitare le funzionalità del servizio, la sottoscrizione deve abilitare il provider di risorse **Microsoft. PolicyInsights** e creare un'assegnazione di ruolo per l'entità servizio del cluster. 

1. Per abilitare il provider di risorse, seguire la procedura descritta in [provider di risorse e tipi](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal) oppure eseguire l'interfaccia della riga di comando di Azure o il comando Azure PowerShell:

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

1. Creare un'assegnazione di ruolo per l'entità servizio cluster

   - Se non si conosce l'ID app dell'entità servizio del cluster, cercarlo con il comando seguente.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Assegnare l'assegnazione di ruolo ' writer di dati Policy Insights (anteprima)' all'ID app dell'entità servizio del cluster (valore _aadClientID_ del passaggio precedente) con l'interfaccia della riga di comando di Azure. Sostituire `<subscriptionId>` con l'ID sottoscrizione e `<aks engine cluster resource group>` con il gruppo di risorse in cui si trova il cluster Kubernetes autogestito del motore AKS.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Componente aggiuntivo criteri di Azure

Il _componente aggiuntivo criteri di Azure_ per Kubernetes connette il servizio criteri di Azure al controller di ammissione Gatekeeper. Il componente aggiuntivo, installato nello spazio dei nomi _Kube-System_ , comporta le funzioni seguenti:

- Verifica con criteri di Azure per le assegnazioni al cluster del motore AKS
- Scarica e installa i dettagli dei criteri, i modelli di vincolo e i vincoli
- Esegue un controllo di conformità dell'analisi completa sul cluster del motore AKS
- Riporta i dettagli di controllo e conformità a criteri di Azure

### <a name="installing-the-add-on"></a>Installazione del componente aggiuntivo

Una volta completati i prerequisiti, è possibile installare il componente aggiuntivo criteri di Azure. L'installazione può essere eseguita durante il ciclo di creazione o aggiornamento di un motore AKS o come azione indipendente su un cluster esistente.

- Installare durante la creazione o il ciclo di aggiornamento

  Per abilitare il componente aggiuntivo di criteri di Azure durante la creazione di un nuovo cluster gestito autonomamente o come aggiornamento a un cluster esistente, includere la definizione del cluster di proprietà **addons** per il motore AKS.

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

  Per ulteriori informazioni su, vedere la definizione del [cluster del motore AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)della guida esterna.

- Installare nel cluster esistente con i grafici Helm

  Usare la procedura seguente per preparare il cluster e installare il componente aggiuntivo:

  1. Installare Gatekeeper nello spazio dei nomi del _sistema Gatekeeper_ .

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Aggiungere l'etichetta del _piano di controllo_ a _Kube-System_. Questa etichetta esclude il controllo di Pod e servizi di _sistema Kube_ da Gatekeeper e dal componente aggiuntivo criteri di Azure.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Sincronizzare i dati di Kubernetes (spazio dei nomi, Pod, ingresso, servizio) con OPA.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Per altre informazioni, vedere [dati di replica OPA](https://github.com/open-policy-agent/gatekeeper#replicating-data).

  1. Aggiungere il repository di criteri di Azure al timone.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Per ulteriori informazioni, vedere la [Guida introduttiva al grafico Helm](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Installare il componente aggiuntivo con un grafico Helm. Sostituire `<subscriptionId>` con l'ID sottoscrizione e `<aks engine cluster resource group>` con il gruppo di risorse in cui si trova il cluster Kubernetes autogestito del motore AKS.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Per altre informazioni sull'installazione del grafico Helm del componente aggiuntivo, vedere la [definizione del grafico Helm aggiuntivo](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) per i criteri di Azure in GitHub.

     > [!NOTE]
     > A causa della relazione tra il componente aggiuntivo di criteri di Azure e l'ID del gruppo di risorse, criteri di Azure supporta un solo cluster del motore AKS per ogni gruppo di risorse.

Per verificare che l'installazione del componente aggiuntivo sia stata completata correttamente e che il Pod _criteri di Azure_ sia in esecuzione, eseguire il comando seguente:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Frequenza di convalida e Reporting

Il componente aggiuntivo archivia i criteri di Azure per le modifiche alle assegnazioni dei criteri ogni 5 minuti. Durante questo ciclo di aggiornamento, il componente aggiuntivo controlla le modifiche. Queste modifiche attivano la creazione, l'aggiornamento o l'eliminazione dei vincoli e dei modelli di vincolo.

> [!NOTE]
> Anche se un _amministratore del cluster_ può disporre delle autorizzazioni per apportare modifiche ai vincoli e ai modelli di vincolo, non è consigliabile né supportato per apportare modifiche ai vincoli o ai modelli di vincolo creati da criteri di Azure. Eventuali modifiche manuali apportate andranno perse durante il ciclo di aggiornamento.

Ogni 5 minuti, il componente aggiuntivo chiama per eseguire un'analisi completa del cluster. Dopo aver raccolto i dettagli dell'analisi completa e di eventuali valutazioni in tempo reale da parte del gatekeeper delle modifiche apportate al cluster, il componente aggiuntivo segnala i risultati ai criteri di Azure per l'inclusione nei [Dettagli di conformità](../how-to/get-compliance-data.md) come qualsiasi assegnazione di criteri di Azure. Durante il ciclo di controllo vengono restituiti solo i risultati per le assegnazioni di criteri attive. I risultati del controllo possono anche essere considerati violazioni elencate nel campo stato del vincolo non riuscito.

## <a name="policy-language"></a>Lingua dei criteri

La struttura del linguaggio dei criteri di Azure per la gestione del motore AKS segue i criteri esistenti. L'effetto _EnforceOPAConstraint_ viene usato per gestire i cluster del motore AKS e accetta le proprietà dei _Dettagli_ specifiche per l'uso del [Framework di vincolo OPA](https://github.com/open-policy-agent/frameworks/tree/master/constraint) e di Gatekeeper V3. Per informazioni dettagliate ed esempi, vedere l'effetto [EnforceOPAConstraint](effects.md#enforceopaconstraint) .

Come parte delle proprietà _Details. constraintTemplate_ e _Details. Constraint_ nella definizione dei criteri, i criteri di Azure passano gli URI di questi [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) al componente aggiuntivo. Rego è il linguaggio supportato da OPA e Gatekeeper per convalidare una richiesta al cluster Kubernetes. Grazie al supporto di uno standard esistente per la gestione Kubernetes, i criteri di Azure consentono di riutilizzare le regole esistenti e di associarle ai criteri di Azure per un'esperienza di creazione di report di conformità cloud unificata. Per ulteriori informazioni, vedere [che cos'è rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Criteri predefiniti

Per trovare i criteri predefiniti per la gestione del cluster del motore AKS usando il portale di Azure, seguire questa procedura:

1. Avviare il servizio criteri di Azure nella portale di Azure. Selezionare **tutti i servizi** nel riquadro a sinistra e quindi cercare e selezionare **criteri**.

1. Nel riquadro sinistro della pagina Criteri di Azure selezionare **definizioni**.

1. Nella casella di riepilogo a discesa Category usare **Select All** per cancellare il filtro e quindi selezionare **Kubernetes**.

1. Selezionare la definizione dei criteri, quindi selezionare il pulsante **assegna** .

> [!NOTE]
> Quando si assegnano i criteri di Azure per la definizione del motore AKS, l' **ambito** deve essere il gruppo di risorse del cluster del motore AKS.

In alternativa, usare la Guida introduttiva [assegnare un criterio al portale](../assign-policy-portal.md) per trovare e assegnare un criterio del motore AKS. Cercare una definizione dei criteri del motore AKS anziché il ' audit VM ' di esempio.

> [!IMPORTANT]
> I criteri predefiniti nella categoria **Kubernetes** sono utilizzabili solo con il motore AKS.

## <a name="logging"></a>Registrazione

### <a name="azure-policy-add-on-logs"></a>Log del componente aggiuntivo di criteri di Azure

Come controller/contenitore Kubernetes, il componente aggiuntivo criteri di Azure mantiene i log nel cluster del motore AKS.

Per visualizzare i log del componente aggiuntivo criteri di Azure, usare `kubectl`:

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Log Gatekeeper

Il Pod Gatekeeper ( _Gatekeeper-Controller-Manager-0_) si trova in genere nello spazio dei nomi `gatekeeper-system` o `kube-system`, ma può trovarsi in uno spazio dei nomi diverso a seconda della modalità di distribuzione.

Per visualizzare i log del gatekeeper, usare `kubectl`:

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Per ulteriori informazioni, vedere [Debugging Gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) nella documentazione di OPA.

## <a name="remove-the-add-on"></a>Rimuovere il componente aggiuntivo

Per rimuovere il componente aggiuntivo di criteri di Azure e il gatekeeper dal cluster del motore AKS, usare il metodo che allinea con la modalità di installazione del componente aggiuntivo:

- Se installato, impostando la proprietà **addons** nella definizione del cluster per il motore AKS:

  Ridistribuire la definizione del cluster nel motore AKS dopo aver modificato la proprietà **addons** per _Azure-Policy_ su false:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Se installato con i grafici Helm:

  1. Rimuovi vincoli obsoleti

     Attualmente, il meccanismo di disinstallazione rimuove solo il sistema gatekeeper, non rimuove le risorse di _ConstraintTemplate_, _vincolo_o _config_ create dall'utente, né rimuove le relative _CRD_ .

     Quando il gatekeeper è in esecuzione, è possibile rimuovere i vincoli indesiderati per:

     - Eliminazione di tutte le istanze della risorsa Constraint
     - Eliminazione della risorsa _ConstraintTemplate_ , che dovrebbe pulire automaticamente il _CRD_
     - L'eliminazione della risorsa di _configurazione_ comporta la rimozione dei finalizzatori sulle risorse sincronizzate

  1. Disinstalla componente aggiuntivo criteri di Azure
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Disinstalla Gatekeeper
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dati di diagnostica raccolti dal componente aggiuntivo criteri di Azure

Il componente aggiuntivo criteri di Azure per Kubernetes raccoglie i dati di diagnostica del cluster limitati. Questi dati diagnostici sono dati tecnici essenziali correlati al software e alle prestazioni. Viene usato nei modi seguenti:

- Mantieni aggiornato il componente aggiuntivo criteri di Azure
- Mantieni il componente aggiuntivo di criteri di Azure protetto, affidabile, efficiente
- Migliorare il componente aggiuntivo criteri di Azure per l'analisi aggregata dell'utilizzo del componente aggiuntivo

Le informazioni raccolte dal componente aggiuntivo non sono dati personali. Sono attualmente raccolti i dettagli seguenti:

- Versione dell'agente componente aggiuntivo criteri di Azure
- Tipo di cluster
- Area del cluster
- Gruppo di risorse cluster
- ID risorsa cluster
- ID sottoscrizione cluster
- Sistema operativo cluster (ad esempio: Linux)
- Città del cluster (ad esempio: Seattle)
- Stato o provincia del cluster (ad esempio: Washington)
- Paese o regione del cluster (esempio: Stati Uniti)
- Eccezioni/errori rilevati dal componente aggiuntivo criteri di Azure durante l'installazione dell'agente durante la valutazione dei criteri
- Numero di criteri Gatekeeper non installati dal componente aggiuntivo criteri di Azure

## <a name="next-steps"></a>Passaggi successivi

- Esaminare gli esempi in [esempi di criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni dei criteri](definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](effects.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Informazioni su come [ottenere i dati di conformità](../how-to/getting-compliance-data.md).
- Informazioni su come monitorare e [aggiornare le risorse non conformi](../how-to/remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).