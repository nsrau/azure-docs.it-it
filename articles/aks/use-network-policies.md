---
title: Proteggere i pod con criteri di rete nel servizio Azure Kubernetes
description: Informazioni su come proteggere il traffico in ingresso e in uscita dai pod usando i criteri di rete Kubernetes nel servizio Azure Kubernetes
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/12/2019
ms.author: iainfou
ms.openlocfilehash: ade5a39273aa807f6c69f76342a0f715c7a96309
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232175"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Proteggere il traffico tra i pod usando criteri di rete nel servizio Azure Kubernetes

Quando si eseguono applicazioni moderne basate su microservizi in Kubernetes, spesso è necessario controllare quali componenti possono comunicare tra loro. Il principio dei privilegi minimi deve essere applicato al modo in cui il traffico scorre tra i pod in un cluster del servizio Azure Kubernetes. Ad esempio, è probabile che si voglia bloccare il traffico direttamente alle applicazioni back-end. In Kubernetes la funzionalità *Criteri di rete* consente di definire le regole per il traffico in ingresso e in uscita tra i pod in un cluster.

Questo articolo illustra come usare i criteri di rete per controllare il flusso del traffico tra i pod nel servizio Azure Kubernetes.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure 2.0.56 o versioni successive installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="overview-of-network-policy"></a>Panoramica dei criteri di rete

Per impostazione predefinita, tutti i pod in un cluster del servizio Azure Kubernetes possono inviare e ricevere traffico senza limitazioni. Per migliorare la sicurezza, è possibile definire regole per il controllo del flusso del traffico. Ad esempio, le applicazioni back-end vengono spesso esposte solo ai servizi front-end richiesti oppure i componenti dei database sono accessibili solo ai livelli applicazione a cui si connettono.

I criteri di rete sono risorse di Kubernetes che consentono di controllare il flusso del traffico tra i pod. È possibile scegliere di consentire o non consentire il traffico in base a impostazioni come la porta del traffico, lo spazio dei nomi o le etichette assegnate. I criteri di rete vengono definiti come manifesti YAML e possono essere inclusi come parte di un manifesto più ampio che crea anche una distribuzione o un servizio.

Per vedere i criteri di rete in azione, è possibile creare e quindi espandere un criterio che definisce il flusso del traffico nel modo seguente:

* Rifiutare tutto il traffico verso il pod.
* Consentire il traffico in base alle etichette del pod.
* Consentire il traffico in base allo spazio dei nomi.

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Creare un cluster del servizio Azure Kubernetes e abilitare i criteri di rete

I criteri di rete possono essere abilitati solo quando viene creato il cluster. Non è possibile abilitare criteri di rete in un cluster esistente del servizio Azure Kubernetes. Per creare un servizio Azure Kubernetes con criteri di rete, abilitare per prima cosa un flag funzionalità per la sottoscrizione. Per registrare il flag funzionalità *EnableNetworkPolicy*, usare il comando [az feature register][az-feature-register] come mostrato nell'esempio seguente:

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato di registrazione usando il comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft.ContainerService* usando il comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Per usare i criteri di rete con un cluster del servizio Azure Kubernetes, è necessario usare il [plug-in Azure CNI][azure-cni] e definire la propria rete virtuale e le subnet. Per altri dettagli su come pianificare gli intervalli di subnet necessari, vedere [Configurare funzionalità di rete avanzate][use-advanced-networking]. Lo script di esempio seguente:

* Crea una rete virtuale e una subnet.
* Crea un'entità servizio di Azure Active Directory (AD) da usare con il cluster del servizio Azure Kubernetes.
* Assegna autorizzazioni di *Collaboratore* per l'entità servizio del cluster del servizio Azure Kubernetes nella rete virtuale.
* Crea un cluster del servizio Azure Kubernetes nella rete virtuale definita e abilita i criteri di rete.

Specificare la propria *SP_PASSWORD* protetta. Se si vuole, sostituire le variabili *RESOURCE_GROUP_NAME* e *CLUSTER_NAME*:

```azurecli-interactive
SP_PASSWORD=mySecurePassword
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
read SP_ID <<< $(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

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
# Enable network policy using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --kubernetes-version 1.12.4 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy calico
```

La creazione del cluster richiede alcuni minuti. Al termine, configurare `kubectl` per la connessione al cluster Kubernetes usando il comando [az aks get-credentials][az-aks-get-credentials]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarle:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Rifiutare tutto il traffico in ingresso verso un pod

Prima di definire le regole per consentire il traffico di rete specifico, creare innanzitutto un criterio di rete per rifiutare tutto il traffico. Questo criterio fornisce un punto di partenza per iniziare a creare un elenco elementi consentiti solo per il traffico desiderato. Si vede anche chiaramente che il traffico viene eliminato quando si applica il criterio di rete.

Per l'ambiente dell'applicazione di esempio e le regole del traffico, è necessario creare per prima cosa uno spazio dei nomi chiamato *development* per eseguire i pod di esempio:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Creare ora un pod back-end di esempio che esegue NGINX. Questo pod back-end può essere usato per simulare un'applicazione back-end di esempio basata sul Web. Creare il pod nello spazio dei nomi *development* e aprire la porta *80* per gestire il traffico Web. Assegnare al pod l'etichetta *app=webapp,role=backend* in modo che sia possibile usarlo come destinazione con un criterio di rete nella sezione successiva:

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

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Uscire dalla sessione del terminale collegata. Il pod di test viene eliminato automaticamente:

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Creare e applicare i criteri di rete

Dopo aver confermato che è possibile accedere alla pagina Web di NGINX di base sul pod back-end di esempio, creare un criterio di rete per rifiutare tutto il traffico. Creare un file denominato `backend-policy.yaml` e incollare il manifesto YAML seguente. Questo manifesto usa un *podSelector* per collegare il criterio ai pod con l'etichetta *app=webapp,role=backend*, come il pod NGINX di esempio. Non sono definite regole per *ingress*, pertanto viene rifiutato tutto il traffico in ingresso verso il pod:

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

Applicare il criterio di rete usando il comando [kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testare i criteri di rete

È il momento di verificare se è possibile accedere nuovamente alla pagina Web di NGINX nel pod back-end. Creare un altro pod di test e collegare una sessione del terminale:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Nel prompt della shell usare `wget` per vedere se è possibile accedere alla pagina Web di NGINX predefinita. Questa volta impostare un valore di timeout di *2* secondi. I criteri di rete ora bloccano tutto il traffico in ingresso, pertanto la pagina non può essere caricata, come illustrato nell'esempio seguente:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Uscire dalla sessione del terminale collegata. Il pod di test viene eliminato automaticamente:

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Consentire il traffico in ingresso in base a un'etichetta del pod

Nella sezione precedente è stato pianificato un pod NGINX back-end ed è stato creato un criterio di rete per rifiutare tutto il traffico. A questo punto è possibile creare un pod front-end e aggiornare i criteri di rete per consentire il traffico da tali pod.

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

Applicare il criterio di rete aggiornato usando il comando [kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Pianificare ora un pod etichettato *app=webapp,role=frontend* e collegare una sessione del terminale:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Nel prompt della shell usare `wget` per vedere se è possibile accedere alla pagina Web di NGINX predefinita:

```console
wget -qO- http://backend
```

Dal momento che la regola per il traffico in ingresso consente il traffico con i pod etichettati *app: webapp,role: frontend*, il traffico dal pod front-end è consentito. L'output di esempio seguente mostra la pagina Web di NGINX predefinita restituita:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Uscire dalla sessione del terminale collegata. Il pod viene eliminato automaticamente:

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testare un pod senza un'etichetta corrispondente

I criteri di rete consentono il traffico dai pod etichettati *app: webapp,role: frontend*, ma dovrebbero rifiutare tutto il resto del traffico. A questo punto è possibile verificare che un altro pod privo di queste etichette non sia in grado di accedere al pod NGINX back-end. Creare un altro pod di test e collegare una sessione del terminale:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Nel prompt della shell usare `wget` per vedere se è possibile accedere alla pagina Web di NGINX predefinita. I criteri di rete bloccano il traffico in ingresso, pertanto la pagina non può essere caricata, come illustrato nell'esempio seguente:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Uscire dalla sessione del terminale collegata. Il pod di test viene eliminato automaticamente:

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Consentire solo il traffico da uno spazio dei nomi definito

Negli esempi precedenti sono stati creati criteri di rete per rifiutare tutto il traffico e successivamente questi criteri sono stati aggiornati per consentire il traffico dai pod con un'etichetta specifica. Un'altra esigenza comune è quella di limitare il traffico solo a un determinato spazio dei nomi. Se gli esempi precedenti si riferivano al traffico in uno spazio dei nomi *development*, ora è possibile creare un criterio di rete per impedire al traffico proveniente da un altro spazio dei nomi, ad esempio *production*, di raggiungere i pod.

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

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Uscire dalla sessione del terminale collegata. Il pod di test viene eliminato automaticamente:

```console
exit
```

### <a name="update-the-network-policy"></a>Aggiornare i criteri di rete

A questo punto è possibile aggiornare la sezione della regola per il traffico in ingresso *namespaceSelector* per consentire solo il traffico dall'interno dello spazio dei nomi *development*. Modificare il file manifesto *backend-policy.yaml* come illustrato nell'esempio seguente:

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

Negli esempi più complessi è possibile definire più regole per il traffico in ingresso, ad esempio per usare un *namespaceSelector* e quindi un *podSelector*.

Applicare il criterio di rete aggiornato usando il comando [kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testare i criteri di rete aggiornati

Pianificare ora un altro pod nello spazio dei nomi *production* e collegare una sessione del terminale:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Nel prompt della shell usare `wget` per vedere che i criteri di rete ora rifiutano il traffico:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Uscire dal pod di test:

```console
exit
```

Con il traffico rifiutato dallo spazio dei nomi *production*, ora pianificare nuovamente un pod di test nello spazio dei nomi *development* e collegare una sessione del terminale:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Nel prompt della shell usare `wget` per vedere che i criteri di rete consentono il traffico:

```console
wget -qO- http://backend
```

Dal momento che il pod viene pianificato nello spazio dei nomi corrispondente a quanto consentito nei criteri di rete, il traffico è consentito. L'output di esempio seguente mostra la pagina Web di NGINX predefinita restituita:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Uscire dalla sessione del terminale collegata. Il pod di test viene eliminato automaticamente:

```console
exit
```

## <a name="clean-up-resources"></a>Pulire le risorse

In questo articolo sono stati creati due spazi dei nomi e sono stati applicati criteri di rete. Per eseguire la pulizia di queste risorse, usare il comando [kubectl delete][kubectl-delete] e specificare i nomi delle risorse come indicato di seguito:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle risorse di rete, vedere [Concetti relativi alla rete per le applicazioni nel servizio Azure Kubernetes][concepts-network].

Per altre informazioni sull'uso dei criteri, fare riferimento ai [criteri di rete Kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
