---
title: Proteggere il traffico tra pod con criteri di rete
titleSuffix: Azure Kubernetes Service
description: Informazioni su come proteggere il traffico in ingresso e in uscita dai pod usando i criteri di rete Kubernetes nel servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 7e494c6ac89289a9b271d16b871b8a22e1ca9e6a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683202"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Proteggere il traffico tra i pod usando criteri di rete nel servizio Azure Kubernetes

Quando si eseguono applicazioni moderne basate su microservizi in Kubernetes, spesso è necessario controllare quali componenti possono comunicare tra loro. Il principio dei privilegi minimi deve essere applicato al modo in cui il traffico scorre tra i pod in un cluster del servizio Azure Kubernetes. Si supponga di voler bloccare il traffico direttamente alle applicazioni back-end. In Kubernetes, la funzionalità *Criteri di rete* consente di definire le regole per il traffico in ingresso e in uscita tra i pod in un cluster.

Questo articolo illustra come installare il motore dei criteri di rete e creare criteri di rete Kubernetes per controllare il flusso del traffico tra i pod nel servizio Azure Kubernetes. I criteri di rete devono essere usati solo per pod e nodi basati su Linux nel servizio Azure Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

È necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.61 o versioni successive. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

> [!TIP]
> Nel caso in cui, durante la fase di anteprima, sia stata usata la funzionalità criteri di rete, è consigliabile [creare un nuovo cluster](#create-an-aks-cluster-and-enable-network-policy).
> 
> Per continuare a usare i cluster di test esistenti che hanno usato i criteri di rete durante l'anteprima, aggiornare il cluster a una nuova versione di Kubernetes per la versione di disponibilità generale più recente e quindi distribuire il manifesto YAML seguente per correggere l'arresto anomalo del sistema del server delle metriche e del dashboard di Kubernetes. Questa correzione è necessaria solo per i cluster che usano il motore dei criteri di rete di Calico.
>
> Come procedura di sicurezza consigliata, [esaminare il contenuto di questo manifesto YAML][calico-aks-cleanup] per capire cosa viene distribuito nel cluster del servizio Azure Kubernetes.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Panoramica dei criteri di rete

Per impostazione predefinita, tutti i pod in un cluster del servizio Azure Kubernetes possono inviare e ricevere traffico senza limitazioni. Per migliorare la sicurezza, è possibile definire regole per il controllo del flusso del traffico. Ad esempio, le applicazioni back-end vengono spesso esposte solo ai servizi front-end richiesti. In alternativa, i componenti del database sono accessibili solo ai livelli applicazione a cui si connettono.

Criteri di rete è una specifica Kubernetes che definisce i criteri di accesso per la comunicazione tra pod. Criteri di rete permette di definire un set ordinato di regole per inviare e ricevere traffico e applicarle a una raccolta di pod che corrispondono a uno o più selettori di etichette.

Queste regole dei criteri di rete sono definite come manifesti YAML. I criteri di rete vengono definiti come manifesti YAML e possono essere inclusi come parte di un manifesto più ampio che crea anche una distribuzione o un servizio.

### <a name="network-policy-options-in-aks"></a>Opzioni dei criteri di rete nel servizio Azure Kubernetes

Azure offre due modi per implementare i criteri di rete. Quando si crea un cluster del servizio Azure Kubernetes è possibile scegliere un'opzione per i criteri di rete. L'opzione relativa ai criteri non può essere modificata dopo la creazione del cluster:

* L'implementazione di Azure, denominata *Criteri di rete di Azure*.
* *Criteri di rete Calico*, una soluzione di sicurezza di rete e di rete open source fondata da [Tigera][tigera].

Entrambe le implementazioni usano Linux *IPTables* per applicare i criteri specificati. I criteri vengono convertiti in set di coppie IP consentite e non consentite. Queste coppie vengono quindi programmate come regole del filtro IPTable.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Differenze tra criteri di Azure e Calico e relative funzionalità

| Funzionalità                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Piattaforme supportate                      | Linux                      | Linux                       |
| Opzioni di rete supportate             | Azure CNI                  | Azure CNI e kubenet       |
| Conformità con le specifiche Kubernetes | Tutti i tipi di criteri supportati |  Tutti i tipi di criteri supportati |
| Funzionalità aggiuntive                      | nessuno                       | Modello di criteri esteso, composto da criteri di rete globali, da un set di servizi di rete globale e da un endpoint host. Per altre informazioni sull'uso dell'interfaccia della riga di comando di `calicoctl` per gestire queste funzionalità estese, vedere i [riferimenti utente calicoctl][calicoctl]. |
| Supporto                                  | Supporto fornito dal team di progettazione e supporto tecnico di Azure | Supporto della community di Calico. Per altre informazioni sul supporto a pagamento aggiuntivo, vedere le opzioni di supporto di [Project Calico][calico-support]. |
| Registrazione                                  | Le regole aggiunte/eliminate in IPTables vengono registrate in ogni host in */var/log/azure-npm.log* | Per altre informazioni, vedere [Log dei componenti Calico][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Creare un cluster del servizio Azure Kubernetes e abilitare i criteri di rete

Per vedere i criteri di rete in azione, è possibile creare e quindi espandere un criterio che definisce il flusso del traffico:

* Rifiutare tutto il traffico verso il pod.
* Consentire il traffico in base alle etichette del pod.
* Consentire il traffico in base allo spazio dei nomi.

Prima di tutto, creare un cluster del servizio Azure Kubernetes che supporti i criteri di rete. 

> [!IMPORTANT]
>
> La funzionalità criteri di rete può essere abilitata solo quando viene creato il cluster. Non è possibile abilitare criteri di rete in un cluster esistente del servizio Azure Kubernetes.

Per usare Criteri di rete di Azure, è necessario usare il [plug-in Azure CNI][azure-cni] e definire la propria rete virtuale e le subnet. Per altri dettagli su come pianificare gli intervalli di subnet necessari, vedere [Configurare funzionalità di rete avanzate][use-advanced-networking]. I criteri di rete di Calico possono essere usati con lo stesso plug-in di Azure CNI o con il plug-in di Kubenet CNI.

Lo script di esempio seguente:

* Crea una rete virtuale e una subnet.
* Crea un'entità servizio di Azure Active Directory (Azure AD) da usare con il cluster del servizio Azure Kubernetes.
* Assegna autorizzazioni di *Collaboratore* per l'entità servizio del cluster del servizio Azure Kubernetes nella rete virtuale.
* Crea un cluster del servizio Azure Kubernetes nella rete virtuale definita e abilita i criteri di rete.
    * Viene usata l'opzione _Criteri di rete di Azure_. Per usare invece Calico come opzione dei criteri di rete, usare il parametro `--network-policy calico`. Nota: Calico può essere usato con `--network-plugin azure` o `--network-plugin kubenet`.

Si noti che è possibile usare un'identità gestita anziché un'entità servizio per le autorizzazioni. Per altre informazioni, vedere [Usare le identità gestite](use-managed-identity.md).

Specificare la propria *SP_PASSWORD* protetta. È possibile sostituire le variabili *RESOURCE_GROUP_NAME* e *CLUSTER_NAME*:

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

La creazione del cluster richiede alcuni minuti. Al termine, configurare `kubectl` per la connessione al cluster Kubernetes usando il comando [az aks get-credentials][az-aks-get-credentials]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarle:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Rifiutare tutto il traffico in ingresso verso un pod

Prima di definire le regole per consentire il traffico di rete specifico, creare innanzitutto un criterio di rete per rifiutare tutto il traffico. Questo criterio fornisce un punto iniziale per creare un elenco elementi consentiti solo per il traffico desiderato. Si vede anche chiaramente che il traffico viene eliminato quando si applica il criterio di rete.

Per l'ambiente dell'applicazione di esempio e le regole del traffico, è necessario creare per prima cosa uno spazio dei nomi chiamato *development* per eseguire i pod di esempio:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Creare un pod back-end di esempio che esegue NGINX. Questo pod back-end può essere usato per simulare un'applicazione back-end di esempio basata sul Web. Creare il pod nello spazio dei nomi *development* e aprire la porta *80* per gestire il traffico Web. Assegnare al pod l'etichetta *app=webapp,role=backend* in modo che sia possibile usarlo come destinazione con un criterio di rete nella sezione successiva:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Per verificare che si possa raggiungere correttamente la pagina Web di NGINX predefinita, creare un altro pod e collegare una sessione del terminale:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Nel prompt della shell usare `wget` per confermare che è possibile accedere alla pagina Web di NGINX predefinita:

```console
wget -qO- http://backend
```

L'output di esempio seguente mostra che viene restituita la pagina Web di NGINX predefinita:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Uscire dalla sessione del terminale collegata. Il pod di test viene eliminato automaticamente.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Creare e applicare i criteri di rete

Dopo aver confermato che è possibile usare la pagina Web di NGINX di base sul pod back-end di esempio, creare un criterio di rete per rifiutare tutto il traffico. Creare un file denominato `backend-policy.yaml` e incollare il manifesto YAML seguente. Questo manifesto usa un *podSelector* per collegare il criterio ai pod con l'etichetta *app=webapp,role=backend*, come il pod NGINX di esempio. Non sono definite regole per *ingress*, pertanto viene rifiutato tutto il traffico in ingresso verso il pod:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

Passare a [https://shell.azure.com](https://shell.azure.com) per aprire Azure Cloud Shell nel browser.

Applicare il criterio di rete usando il comando [kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testare i criteri di rete

È il momento di verificare se è possibile usare nuovamente la pagina Web di NGINX nel pod back-end. Creare un altro pod di test e collegare una sessione del terminale:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Nel prompt della shell, usare `wget` per vedere se è possibile accedere alla pagina Web di NGINX predefinita. Questa volta impostare un valore di timeout di *2* secondi. I criteri di rete ora bloccano tutto il traffico in ingresso, pertanto la pagina non può essere caricata, come illustrato nell'esempio seguente:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Uscire dalla sessione del terminale collegata. Il pod di test viene eliminato automaticamente.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Consentire il traffico in ingresso in base a un'etichetta del pod

Nella sezione precedente è stato pianificato un pod NGINX back-end ed è stato creato un criterio di rete per rifiutare tutto il traffico. Creare adesso un pod front-end e aggiornare i criteri di rete per consentire il traffico da tali pod.

Aggiornare i criteri di rete per consentire il traffico dai pod con le etichette *app:webapp,role:frontend* e in qualsiasi spazio dei nomi. Modificare il file *backend-policy.yaml* precedente e aggiungere un regola per il traffico in ingresso *matchLabels* in modo che il manifesto sia simile all'esempio seguente:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> Il criterio di rete usa un *namespaceSelector* e un elemento *podSelector* (elemento) per la regola per il traffico in ingresso. La sintassi YAML è importante perché le regole per il traffico in ingresso siano complementari. In questo esempio, entrambi gli elementi devono corrispondere perché la regola per il traffico in ingresso venga applicata. Le versioni di Kubernetes precedenti alla *1.12* potrebbero non interpretare correttamente questi elementi e limitare il traffico di rete nel modo previsto. Per altre informazioni su questo comportamento, vedere [Comportamento dei selettori di ingresso e uscita][policy-rules].

Applicare il criterio di rete aggiornato usando il comando [kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

Pianificare un pod etichettato *app=webapp,role=frontend* e collegare una sessione del terminale:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Nel prompt della shell, usare `wget` per vedere se è possibile accedere alla pagina Web di NGINX predefinita:

```console
wget -qO- http://backend
```

Dal momento che la regola per il traffico in ingresso consente il traffico con i pod etichettati *app: webapp,role: frontend*, il traffico dal pod front-end è consentito. L'output di esempio seguente mostra la pagina Web di NGINX predefinita restituita:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Uscire dalla sessione del terminale collegata. Il pod viene eliminato automaticamente.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testare un pod senza un'etichetta corrispondente

I criteri di rete consentono il traffico dai pod etichettati *app: webapp,role: frontend*, ma dovrebbero rifiutare tutto il resto del traffico. A questo punto è possibile verificare se un altro pod privo di queste etichette sia in grado di accedere al pod NGINX back-end. Creare un altro pod di test e collegare una sessione del terminale:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Nel prompt della shell, usare `wget` per vedere se è possibile accedere alla pagina Web di NGINX predefinita. I criteri di rete bloccano il traffico in ingresso, pertanto la pagina non può essere caricata, come illustrato nell'esempio seguente:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Uscire dalla sessione del terminale collegata. Il pod di test viene eliminato automaticamente.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Consentire solo il traffico da uno spazio dei nomi definito

Negli esempi precedenti sono stati creati criteri di rete per rifiutare tutto il traffico e successivamente questi criteri sono stati aggiornati per consentire il traffico dai pod con un'etichetta specifica. Un'altra esigenza comune è quella di limitare il traffico solo a un determinato spazio dei nomi. Se gli esempi precedenti si riferivano al traffico in uno spazio dei nomi *development*, creare un criterio di rete per impedire al traffico proveniente da un altro spazio dei nomi, ad esempio *production*, di raggiungere i pod.

Per prima cosa, creare un nuovo spazio dei nomi per simulare quello dell'ambiente di produzione:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Pianificare un pod di test nello spazio dei nomi *production* con l'etichetta *app=webapp,role=frontend*. Collegare una sessione del terminale:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Nel prompt della shell usare `wget` per confermare che è possibile accedere alla pagina Web di NGINX predefinita:

```console
wget -qO- http://backend.development
```

Dal momento che le etichette per il pod corrispondono a quanto attualmente consentito nei criteri di rete, il traffico è consentito. I criteri di rete non esaminano gli spazi dei nomi, ma solo le etichette dei pod. L'output di esempio seguente mostra la pagina Web di NGINX predefinita restituita:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Uscire dalla sessione del terminale collegata. Il pod di test viene eliminato automaticamente.

```console
exit
```

### <a name="update-the-network-policy"></a>Aggiornare i criteri di rete

È ora possibile aggiornare la sezione della regola per il traffico in ingresso *namespaceSelector* per consentire solo il traffico dall'interno dello spazio dei nomi *development*. Modificare il file manifesto *backend-policy.yaml* come illustrato nell'esempio seguente:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

Negli esempi più complessi è possibile definire più regole per il traffico in ingresso, come ad esempio un *namespaceSelector* e quindi un *podSelector*.

Applicare il criterio di rete aggiornato usando il comando [kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testare i criteri di rete aggiornati

Pianificare un altro pod nello spazio dei nomi *production* e collegare una sessione del terminale:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Nel prompt della shell usare `wget` per vedere che i criteri di rete ora rifiutano il traffico:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Uscire dal pod di test:

```console
exit
```

Con il traffico rifiutato dallo spazio dei nomi *production*, pianificare nuovamente un pod di test nello spazio dei nomi *development* e collegare una sessione del terminale:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Nel prompt della shell usare `wget` per vedere che i criteri di rete consentono il traffico:

```console
wget -qO- http://backend
```

Dal momento che il pod viene pianificato nello spazio dei nomi corrispondente a quanto consentito nei criteri di rete, il traffico è consentito. L'output di esempio seguente mostra la pagina Web di NGINX predefinita restituita:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Uscire dalla sessione del terminale collegata. Il pod di test viene eliminato automaticamente.

```console
exit
```

## <a name="clean-up-resources"></a>Pulire le risorse

In questo articolo sono stati creati due spazi dei nomi e sono stati applicati criteri di rete. Per eseguire la pulizia di queste risorse, usare il comando [kubectl delete][kubectl-delete] e specificare i nomi delle risorse:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle risorse di rete, vedere [Concetti relativi alla rete per le applicazioni nel servizio Azure Kubernetes][concepts-network].

Per altre informazioni sui criteri, fare riferimento ai [Criteri di rete Kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/maintenance/troubleshoot/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
