---
title: Proteggere i pod con criteri di Azure in Azure Kubernetes Service (AKS)
description: Informazioni su come proteggere i pod con criteri di Azure in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 09/22/2020
author: jluk
ms.openlocfilehash: fd4f79e0cae5028e4bbaa8a4f5115d5a767dcf54
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91368856"
---
# <a name="secure-pods-with-azure-policy"></a>Proteggere i pod con criteri di Azure

Per migliorare la sicurezza del cluster AKS, è possibile controllare quali funzioni vengono concesse ai pod e se qualsiasi elemento viene eseguito in base ai criteri aziendali. Questo accesso viene definito tramite criteri predefiniti forniti dal [componente aggiuntivo criteri di Azure per AKS][kubernetes-policy-reference]. Garantendo un ulteriore controllo sugli aspetti di sicurezza della specifica del Pod, ad esempio i privilegi radice, consente una maggiore conformità alla sicurezza e la visibilità di ciò che viene distribuito nel cluster. Se un pod non soddisfa le condizioni specificate nei criteri, i criteri di Azure possono impedire al pod di avviare o contrassegnare una violazione. Questo articolo illustra come usare criteri di Azure per limitare la distribuzione di pod in AKS.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

### <a name="install-the-azure-policy-add-on-for-aks"></a>Installare il componente aggiuntivo criteri di Azure per AKS

Per proteggere il servizio contenitore di Azure tramite criteri di Azure, è necessario installare il componente aggiuntivo criteri di Azure per AKS in un cluster AKS. Seguire questa [procedura per installare il componente aggiuntivo criteri di Azure](../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks).

Questo documento presuppone che siano disponibili gli elementi seguenti, che vengono distribuiti nella procedura dettagliata collegata sopra.

* Registrazione dei `Microsoft.ContainerService` `Microsoft.PolicyInsights` provider di risorse e con `az provider register`
* INTERFACCIA della riga di comando di Azure 2,12 o versione successiva
* Un cluster del servizio contenitore di Azure in una versione 1,15 o successiva installata con il componente aggiuntivo criteri di Azure

## <a name="overview-of-securing-pods-with-azure-policy-for-aks"></a>Panoramica della protezione di Pod con criteri di Azure per AKS

>[!NOTE]
> Questo documento illustra come usare i criteri di Azure per proteggere i pod, che è il successore della [funzionalità dei criteri di sicurezza Pod Kubernetes in anteprima](use-pod-security-policies.md).
> **Non è possibile abilitare contemporaneamente i criteri di sicurezza pod (anteprima) e il componente aggiuntivo criteri di Azure per AKS.**
> 
> Se si installa il componente aggiuntivo criteri di Azure in un cluster con criteri di sicurezza Pod abilitati, [attenersi alla procedura seguente per disabilitare i criteri di sicurezza Pod](use-pod-security-policies.md#enable-pod-security-policy-on-an-aks-cluster).

In un cluster AKS viene usato un controller di ammissione per intercettare le richieste al server API quando una risorsa viene creata e aggiornata. Il controller di ammissione può quindi *convalidare* la richiesta di risorse in base a un set di regole sull'eventuale creazione.

In precedenza, i [criteri di sicurezza di Pod funzionalità (anteprima)](use-pod-security-policies.md) venivano abilitati tramite il progetto Kubernetes per limitare i pod che è possibile distribuire.

Con il componente aggiuntivo criteri di Azure un cluster AKS può usare i criteri predefiniti di Azure, che proteggono i pod e altre risorse di Kubernetes in modo analogo ai criteri di sicurezza di Pod. Il componente aggiuntivo criteri di Azure per AKS installa un'istanza gestita di [Gatekeeper](https://github.com/open-policy-agent/gatekeeper), un controller di ammissione per la convalida. Criteri di Azure per Kubernetes si basa sull'agente criteri di apertura open source, che si basa sul [linguaggio dei criteri di rego](../governance/policy/concepts/policy-for-kubernetes.md#policy-language).

Questo documento illustra come usare i criteri di Azure per proteggere i pod in un cluster AKS e come eseguire la migrazione dai criteri di sicurezza pod (anteprima).

## <a name="limitations"></a>Limitazioni

Le limitazioni generali seguenti si applicano al componente aggiuntivo di criteri di Azure per i cluster Kubernetes:

- Il componente aggiuntivo criteri di Azure per Kubernetes è supportato in Kubernetes versione **1,14** o successiva.
- Il componente aggiuntivo criteri di Azure per Kubernetes può essere distribuito solo nei pool di nodi Linux
- Sono supportate solo le definizioni di criteri predefinite
- Numero massimo di record non conformi per ogni criterio per cluster: **500**
- Numero massimo di record non conformi per ogni sottoscrizione: **1 milione**
- Le installazioni di Gatekeeper all'esterno del componente aggiuntivo criteri di Azure non sono supportate. Disinstallare i componenti installati da un'installazione precedente di gatekeeper prima di abilitare il componente aggiuntivo criteri di Azure.
- I [motivi della mancata conformità](../governance/policy/how-to/determine-non-compliance.md#compliance-reasons) non sono disponibili per questa [modalità del provider di risorse](../governance/policy/concepts/definition-structure.md#resource-provider-modes)

Le limitazioni seguenti si applicano solo al componente aggiuntivo criteri di Azure per AKS:

- Non è possibile abilitare entrambi i criteri di sicurezza del servizio contenitore di Azure [(anteprima)](use-pod-security-policies.md) e il componente aggiuntivo criteri di Azure per AKS. 
- Spazi dei nomi esclusi automaticamente dal componente aggiuntivo criteri di Azure per la valutazione: _Kube-System_, _Gatekeeper-System_e _AKS-periscopio_.

### <a name="recommendations"></a>Consigli

Di seguito sono riportate indicazioni generali per l'uso del componente aggiuntivo criteri di Azure:

- Il componente aggiuntivo di criteri di Azure richiede tre componenti Gatekeeper per eseguire: 1 audit pod e 2 repliche Pod del webhook. Questi componenti utilizzano un numero maggiore di risorse, in quanto il numero di risorse Kubernetes e le assegnazioni di criteri aumentano nel cluster, che richiede operazioni di controllo e imposizione.

  - Per meno di 500 pod in un singolo cluster con un massimo di 20 vincoli: 2 vCPU e 350 MB di memoria per ogni componente.
  - Per più di 500 pod in un singolo cluster con un massimo di 40 vincoli: 3 vCPU e 600 MB di memoria per ogni componente.

La raccomandazione seguente si applica solo ad AKS e al componente aggiuntivo criteri di Azure:

- Usare il pool di nodi `CriticalAddonsOnly` di sistema con Tain per pianificare i pod Gatekeeper. Per ulteriori informazioni, vedere [utilizzo dei pool di nodi di sistema](use-system-pools.md#system-and-user-node-pools).
- Proteggere il traffico in uscita dai cluster AKS. Per altre informazioni, vedere [controllare il traffico in uscita per i nodi del cluster](limit-egress-traffic.md).
- Se il cluster ha `aad-pod-identity` abilitato, i pod di identità gestita del nodo modificano i iptables dei nodi per intercettare le chiamate all'endpoint dei metadati dell'istanza di Azure. Questa configurazione significa che tutte le richieste effettuate all'endpoint dei metadati vengono intercettate da NMI anche se il Pod non lo usa `aad-pod-identity` . AzurePodIdentityException CRD può essere configurato per informare `aad-pod-identity` che qualsiasi richiesta all'endpoint di metadati originata da un pod che corrisponde alle etichette definite in CRD deve essere inoltrata senza alcuna elaborazione in NMI. I pod di sistema con `kubernetes.azure.com/managedby: aks` etichetta nello spazio dei nomi _Kube-System_ devono essere esclusi in `aad-pod-identity` tramite la configurazione di AzurePodIdentityException CRD. Per altre informazioni, vedere [disabilitare AAD-Pod-Identity per un pod o un'applicazione specifica](https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md).
  Per configurare un'eccezione, installare la [YAML di eccezione MIC](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

Il componente aggiuntivo criteri di Azure richiede che le risorse di CPU e memoria funzionino. Questi requisiti aumentano con l'aumentare delle dimensioni di un cluster. Per indicazioni generali sull'uso del componente aggiuntivo criteri di Azure, vedere Suggerimenti per i [criteri di Azure][policy-recommendations] .

## <a name="azure-policies-to-secure-kubernetes-pods"></a>Criteri di Azure per proteggere i pod Kubernetes

Dopo l'installazione del componente aggiuntivo criteri di Azure, non vengono applicati criteri per impostazione predefinita.

Sono disponibili 11 criteri di Azure singoli e due iniziative predefinite che proteggono in modo specifico i pod in un cluster AKS.
Ogni criterio può essere personalizzato con un effetto. Di seguito è riportato un elenco completo dei [criteri AKS e degli effetti supportati][policy-samples]. Scopri di più sugli [effetti dei criteri di Azure](../governance/policy/concepts/effects.md).

I criteri di Azure possono essere applicati a livello di gruppo di gestione, sottoscrizione o gruppo di risorse. Quando si assegna un criterio a livello di gruppo di risorse, verificare che il gruppo di risorse del cluster AKS di destinazione sia selezionato nell'ambito dei criteri. Ogni cluster nell'ambito assegnato con il componente aggiuntivo criteri di Azure installato rientra nell'ambito dei criteri.

Se si usano i [criteri di sicurezza pod (anteprima) ](use-pod-security-policies.md), informazioni su come [eseguire la migrazione a criteri di Azure e su altre differenze di comportamento](#migrate-from-kubernetes-pod-security-policy-to-azure-policy).

### <a name="built-in-policy-initiatives"></a>Iniziative predefinite dei criteri

Un'iniziativa in criteri di Azure è una raccolta di definizioni di criteri adattate al raggiungimento di un singolo obiettivo globale. L'uso delle iniziative può semplificare la gestione e l'assegnazione dei criteri nei cluster AKS. Un'iniziativa esiste come singolo oggetto, altre informazioni sulle [iniziative di criteri di Azure](../governance/policy/overview.md#initiative-definition).

Criteri di Azure per Kubernetes offre due iniziative predefinite, che proteggono i pod, la [baseline](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) e le [restrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00).

Entrambe le iniziative predefinite sono compilate dalle definizioni usate nei [criteri di sicurezza pod da Kubernetes](https://github.com/kubernetes/website/blob/master/content/en/examples/policy/baseline-psp.yaml).

|[Controllo dei criteri di sicurezza Pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Collegamento alla definizione di criteri di Azure| [Iniziativa di base](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) | [Iniziativa limitata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) |
|---|---|---|---|
|Non consentire l'esecuzione di contenitori con privilegi|[Cloud pubblico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4)| Sì | Sì
|Non consentire l'utilizzo condiviso degli spazi dei nomi host|[Cloud pubblico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a1ee2f-2a2a-4576-bf2a-e0e36709c2b8)| Sì | Sì
|Limitare l'utilizzo della rete e delle porte dell'host|[Cloud pubblico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe)| Sì | Sì
|Limitare l'utilizzo del file System host|[Cloud pubblico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F098fc59e-46c7-4d99-9b16-64990e543d75)| Sì | Sì
|Limitare le funzionalità di Linux al [set predefinito](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)|[Cloud pubblico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc26596ff-4d70-4e6a-9a30-c2506bd2f80c) | Sì | Sì
|Limitare l'utilizzo dei tipi di volume definiti|[Cloud pubblico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16697877-1118-4fb1-9b65-9898ec2509ec)| - | Sì: i tipi di volume consentiti sono `configMap` , `emptyDir` , `projected` , `downwardAPI` , `persistentVolumeClaim`|
|Escalation dei privilegi alla radice|[Cloud pubblico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c6e92c9-99f0-4e55-9cf2-0c234dc48f99) | - | Sì |
|Limitare gli ID utente e gruppo del contenitore|[Cloud pubblico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Sì|
|Limitare l'allocazione di un FSGroup proprietario dei volumi del Pod|[Cloud pubblico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Sì: le regole consentite sono `runAsUser: mustRunAsNonRoot` ,, `supplementalGroup: mustRunAs 1:65536` `fsGroup: mustRunAs 1:65535` , `runAsGroup: mustRunAs 1:65535` .  |
|Richiede il profilo seccomp|[Cloud pubblico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F975ce327-682c-4f2e-aa46-b9598289b86c) | - | Sì, allowedProfiles sono * `docker/default` o `runtime/default` |

\* Docker/default è deprecato in Kubernetes dalla versione 1.11

### <a name="additional-optional-policies"></a>Criteri facoltativi aggiuntivi

Sono disponibili altri criteri di Azure, che possono essere applicati singolarmente al di fuori dell'applicazione di un'iniziativa. Prendere in considerazione l'aggiunta di questi criteri oltre alle iniziative se i requisiti non sono soddisfatti dalle iniziative predefinite.

|[Controllo dei criteri di sicurezza Pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Collegamento alla definizione di criteri di Azure| Applicare oltre all'iniziativa Baseline | Applica oltre ad iniziativa limitata |
|---|---|---|---|
|Definire il profilo AppArmor usato dai contenitori|[Cloud pubblico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F511f5417-5d12-434d-ab2e-816901e72a5e) | Facoltativo | Facoltativo |
|Consenti montaggi non di sola lettura|[Cloud pubblico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf49d893-a74c-421d-bc95-c663042e5b80) | Facoltativo | Facoltativo |
|Limita a driver FlexVolume specifici|[Cloud pubblico](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4a8fce0-2dd5-4c21-9a36-8f0ec809d663) | Facoltativo: usare se si vuole limitare solo i driver FlexVolume, ma non altri impostati da "limita l'utilizzo dei tipi di volume definiti" | Non applicabile: l'iniziativa limitata include "limita l'utilizzo dei tipi di volume definiti", che non consentono tutti i driver FlexVolume |

### <a name="unsupported-built-in-policies-for-managed-aks-clusters"></a>Criteri predefiniti non supportati per i cluster AKS gestiti

> [!NOTE]
> I tre criteri seguenti **non sono supportati in AKS** a causa della personalizzazione degli aspetti gestiti e protetti da AKS come servizio gestito. Questi criteri vengono creati in modo specifico per i cluster connessi ad Azure Arc con i piani di controllo non gestiti.

|[Controllo dei criteri di sicurezza Pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)|
|---|
|Definire il contesto di SELinux personalizzato di un contenitore|
|Limitare il profilo di sysctl usato dai contenitori|
|Vengono definiti i tipi di montaggio proc predefiniti per ridurre la superficie di attacco|

<!---
# Removing until custom initiatives are supported the week after preview

#### Custom initiative

If the built-in initiatives to address pod security do not match your requirements, you can choose your own policies to exist in a custom initiative. Read more about [building custom initatives in Azure Policy](../governance/policy/tutorials/create-and-manage#create-and-assign-an-initiative-definition).

--->

### <a name="namespace-exclusion"></a>Esclusione dello spazio dei nomi

> [!WARNING]
> I pod negli spazi dei nomi di amministrazione, ad esempio Kube-System, devono essere eseguiti affinché un cluster rimanga integro e la rimozione di uno spazio dei nomi richiesto dall'elenco di spazi dei nomi esclusi predefiniti potrebbe provocare violazioni dei criteri a causa di un pod di sistema obbligatorio.

AKS richiede che i pod di sistema vengano eseguiti in un cluster per fornire servizi critici, ad esempio la risoluzione DNS. I criteri che limitano la funzionalità Pod possono avere un effetto sulla stabilità del pod di sistema. Di conseguenza, gli spazi dei nomi seguenti vengono **esclusi dalla valutazione dei criteri durante le richieste di ammissione durante la creazione, l'aggiornamento e il controllo dei criteri**. In questo modo le nuove distribuzioni a questi spazi dei nomi verranno escluse dai criteri di Azure.

1. Kube-sistema
1. Gatekeeper-sistema
1. Azure-Arc
1. AKS-periscopio

Gli spazi dei nomi personalizzati aggiuntivi possono essere esclusi dalla valutazione durante la creazione, l'aggiornamento e il controllo. Queste esclusioni devono essere usate se si dispone di Pod specializzati che vengono eseguiti in uno spazio dei nomi approvato e si vuole evitare di attivare violazioni di controllo.

## <a name="apply-the-baseline-initiative"></a>Applicare l'iniziativa Baseline

> [!TIP]
> Per impostazione predefinita, tutti i criteri sono un effetto di controllo. Gli effetti possono essere aggiornati per negare in qualsiasi momento tramite criteri di Azure.

Per applicare l'iniziativa Baseline, è possibile eseguire l'assegnazione tramite il portale di Azure.
<!--
1. Navigate to the Policy service in Azure portal
1. In the left pane of the Azure Policy page, select **Definitions**
1. Search for "Baseline Profile" on the search pane to the right of the page
1. Select `Kubernetes Pod Security Standards Baseline Profile for Linux-based workloads` from the `Kubernetes` category
-->
1. Seguire [questo collegamento alla portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) per esaminare l'iniziativa della baseline per la sicurezza Pod
1. Impostare l' **ambito** al livello di sottoscrizione o solo il gruppo di risorse che contiene i cluster AKS con il componente aggiuntivo criteri di Azure abilitato
1. Selezionare la pagina **parametri** e aggiornare l' **effetto** da `audit` a `deny` per bloccare le nuove distribuzioni che violano l'iniziativa di base
1. Aggiungere altri spazi dei nomi da escludere dalla valutazione durante la creazione, l'aggiornamento e il controllo [. alcuni spazi dei nomi vengono esclusi forzatamente dalla valutazione dei criteri.](#namespace-exclusion) 
 ![ effetto aggiornamento](media/use-pod-security-on-azure-policy/update-effect.png)
1. Selezionare **Verifica + crea** per inviare i criteri

Verificare che i criteri vengano applicati al cluster eseguendo `kubectl get constrainttemplates` .

> [!NOTE]
> I criteri possono richiedere [fino a 20 minuti per la sincronizzazione](../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition) in ogni cluster.

L'output dovrebbe essere simile al seguente:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              30m
k8sazureblockhostnamespace               30m
k8sazurecontainernoprivilege             30m
k8sazurehostfilesystem                   30m
k8sazurehostnetworkingports              30m
```

## <a name="validate-rejection-of-a-privileged-pod"></a>Convalidare il rifiuto di un pod con privilegi

Si contesterà innanzitutto cosa accade quando si pianifica un pod con il contesto di sicurezza di `privileged: true` . Questo contesto di sicurezza inoltra i privilegi del Pod. L'iniziativa Baseline non consente i pod con privilegi, quindi la richiesta verrà negata, causando il rifiuto della distribuzione.

Creare un file denominato `nginx-privileged.yaml` e incollare il manifesto YAML seguente:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx
      securityContext:
        privileged: true
```

Creare il pod con [kubectl Apply][kubectl-apply] comando e specificare il nome del manifesto YAML:

```console
kubectl apply -f nginx-privileged.yaml
```

Come previsto, il Pod non viene pianificato, come illustrato nell'output di esempio seguente:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

Il Pod non raggiunge la fase di pianificazione, quindi non sono presenti risorse da eliminare prima di procedere.

## <a name="test-creation-of-an-unprivileged-pod"></a>Creazione di test di un pod senza privilegi

Nell'esempio precedente, l'immagine del contenitore ha provato automaticamente a usare la radice per associare NGINX alla porta 80. Questa richiesta è stata negata dall'iniziativa dei criteri di base, quindi il Pod non viene avviato. Provare ora a eseguire lo stesso pod NGINX senza accesso con privilegi.

Creare un file denominato `nginx-unprivileged.yaml` e incollare il manifesto YAML seguente:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx
```

Creare il pod usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f nginx-unprivileged.yaml
```

La pianificazione del Pod è stata completata. Quando si controlla lo stato del pod usando il comando [kubectl Get][kubectl-get] pods, il Pod è *in esecuzione*:

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

Questo esempio illustra l'iniziativa baseline che influisce solo sulle distribuzioni che violano i criteri della raccolta. Le distribuzioni consentite continuano a funzionare.

Eliminare il Pod senza privilegi NGINX usando il comando [kubectl Delete][kubectl-delete] e specificare il nome del manifesto YAML:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-policies-and-the-azure-policy-add-on"></a>Disabilitare i criteri e il componente aggiuntivo criteri di Azure

Per rimuovere l'iniziativa Baseline:

1. Passare al riquadro criteri sulla portale di Azure
1. Selezionare **assegnazioni** dal riquadro sinistro
1. Fare clic su "..." pulsante accanto al profilo di base
1. Selezionare "Elimina assegnazione"

![Elimina assegnazione](media/use-pod-security-on-azure-policy/delete-assignment.png)

Per disabilitare il componente aggiuntivo criteri di Azure, usare il comando [AZ AKS Disable-addons][az-aks-disable-addons] .

```azurecli
az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
```

Informazioni su come rimuovere il [componente aggiuntivo di criteri di Azure da portale di Azure](../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks).

## <a name="migrate-from-kubernetes-pod-security-policy-to-azure-policy"></a>Eseguire la migrazione da criteri di sicurezza Pod Kubernetes a criteri di Azure

Per eseguire la migrazione dai criteri di sicurezza Pod, è necessario eseguire le azioni seguenti in un cluster.

1. [Disabilitare i criteri di sicurezza Pod](use-pod-security-policies.md#clean-up-resources) nel cluster
1. Abilitare il [componente aggiuntivo criteri di Azure][kubernetes-policy-reference]
1. Abilitare i criteri di Azure desiderati dai [criteri predefiniti disponibili][policy-samples]

Di seguito è riportato un riepilogo delle modifiche del comportamento tra i criteri di sicurezza di Pod e i criteri di Azure.

|Scenario| Criteri di sicurezza Pod | Criteri di Azure |
|---|---|---|
|Installazione|Abilitare la funzionalità dei criteri di sicurezza Pod |Abilita il componente aggiuntivo criteri di Azure
|Distribuisci criteri| Distribuire la risorsa criteri di sicurezza Pod| Assegnare i criteri di Azure all'ambito della sottoscrizione o del gruppo di risorse. Il componente aggiuntivo criteri di Azure è necessario per le applicazioni di risorse Kubernetes.
| Criteri predefiniti | Quando i criteri di sicurezza pod sono abilitati in AKS, vengono applicati i criteri predefiniti con privilegi e senza restrizioni. | Non vengono applicati criteri predefiniti abilitando il componente aggiuntivo criteri di Azure. È necessario abilitare in modo esplicito i criteri in criteri di Azure.
| Utenti che possono creare e assegnare criteri | Amministrazione cluster crea una risorsa criteri di sicurezza Pod | Gli utenti devono avere un ruolo minimo di autorizzazioni ' proprietario ' o ' collaboratore criteri risorse ' nel gruppo di risorse del cluster AKS. -Tramite l'API, gli utenti possono assegnare i criteri nell'ambito delle risorse del cluster AKS. L'utente deve avere almeno le autorizzazioni "proprietario" o "collaboratore criteri risorse" per la risorsa cluster AKS. -Nella portale di Azure i criteri possono essere assegnati a livello di gruppo di gestione/sottoscrizione/gruppo di risorse.
| Autorizzazione di criteri| Gli account utente e del servizio richiedono autorizzazioni esplicite per l'uso di criteri di sicurezza pod. | Per autorizzare i criteri non è necessaria alcuna assegnazione aggiuntiva. Dopo che i criteri sono stati assegnati in Azure, tutti gli utenti del cluster possono usare questi criteri.
| Applicabilità dei criteri | L'utente amministratore ignora l'applicazione dei criteri di sicurezza pod. | Tutti gli utenti (amministratore & non amministratore) vedono gli stessi criteri. Non sono presenti maiuscole e minuscole specifiche in base agli utenti. L'applicazione di criteri può essere esclusa a livello di spazio dei nomi.
| Ambito dei criteri | I criteri di sicurezza Pod non sono spazio dei nomi | I modelli di vincolo usati dai criteri di Azure non sono spazio dei nomi.
| Azione di negazione/controllo/mutazione | I criteri di sicurezza Pod supportano solo le azioni di negazione. La mutazione può essere eseguita con i valori predefiniti per le richieste di creazione. La convalida può essere eseguita durante le richieste di aggiornamento.| Criteri di Azure supporta entrambe le azioni di controllo & negazione. La mutazione non è ancora supportata, ma è pianificata.
| Conformità dei criteri di sicurezza Pod | Non esiste visibilità sulla conformità dei Pod esistenti prima di abilitare i criteri di sicurezza di Pod. I Pod non conformi creati dopo l'abilitazione di criteri di sicurezza pod sono negati. | I Pod non conformi esistenti prima di applicare i criteri di Azure verranno visualizzati nelle violazioni dei criteri. I Pod non conformi creati dopo l'abilitazione di criteri di Azure vengono negati se i criteri vengono impostati con un effetto di negazione.
| Come visualizzare i criteri nel cluster | `kubectl get psp` | `kubectl get constrainttemplate` -Vengono restituiti tutti i criteri.
| Criteri di sicurezza Pod standard-con privilegi | Quando si Abilita la funzionalità, per impostazione predefinita viene creata una risorsa di criteri di sicurezza di Pod con privilegi. | La modalità con privilegi non implica alcuna restrizione, di conseguenza è equivalente all'assenza di un'assegnazione di criteri di Azure.
| [Criteri di sicurezza Pod standard-Baseline/default](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | L'utente installa una risorsa di base dei criteri di sicurezza pod. | Criteri di Azure fornisce un' [iniziativa di base predefinita](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) che esegue il mapping ai criteri di sicurezza pod di base.
| [Criteri di sicurezza Pod standard-con restrizioni](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | L'utente installa una risorsa con restrizioni dei criteri di sicurezza pod. | Criteri di Azure fornisce un' [iniziativa limitata incorporata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) che esegue il mapping ai criteri di sicurezza di Pod limitati.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come applicare criteri di Azure che limitano la distribuzione di Pod con privilegi per impedire l'uso dell'accesso con privilegi. Sono disponibili molti criteri che possono essere applicati, ad esempio i criteri che limitano l'utilizzo dei volumi. Per altre informazioni sulle opzioni disponibili, vedere la [documentazione di riferimento relativa ai criteri di Azure per Kubernetes][kubernetes-policy-reference].

Per altre informazioni sulla limitazione del traffico di rete Pod, vedere [proteggere il traffico tra i pod usando i criteri di rete in AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity
[aad-pod-identity-exception]: https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md

<!-- LINKS - internal -->
[policy-recommendations]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-limitations]: ../governance/policy/concepts/policy-for-kubernetes.md?#limitations
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-samples]: policy-samples.md#microsoftcontainerservice
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-disable-addons]: /cli/azure/aks#az-aks-disable-addons
[az-policy-assignment-delete]: /cli/azure/policy/assignment#az-policy-assignment-delete
