---
title: Proteggere i pod con criteri di rete nel servizio Azure Kubernetes
description: Informazioni su come proteggere il traffico che passa da e verso i POD usando i criteri di rete Kubernetes in Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/08/2019
ms.author: iainfou
ms.openlocfilehash: 29180d6c1bb5f0991a4f33c3b7c9418f84d8260c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61027973"
---
# <a name="preview---secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Anteprima - proteggere il traffico tra i POD usando i criteri di rete in Azure Kubernetes Service (AKS)

Quando si eseguono applicazioni moderne basate su microservizi in Kubernetes, spesso è necessario controllare quali componenti possono comunicare tra loro. Il principio del privilegio minimo deve essere applicato al modo in cui il traffico tra i POD in un cluster Azure Kubernetes Service (AKS). Si supponga che probabile che si desidera bloccare il traffico direttamente alle applicazioni back-end. Il *criteri di rete* funzionalità in Kubernetes ti permette di definire le regole per il traffico in ingresso e in uscita tra i POD in un cluster.

Questo articolo illustra come installare il modulo criteri di rete e creare i criteri di rete Kubernetes per controllare il flusso del traffico tra i POD nel servizio contenitore di AZURE. Questa funzionalità è attualmente in anteprima.

> [!IMPORTANT]
> Funzionalità di anteprima del servizio contenitore di AZURE sono self-service e fornire il consenso esplicito. Le anteprime sono fornite per raccogliere commenti e suggerimenti e bug dalla community. Tuttavia, non sono supportati dal supporto tecnico di Azure. Se si crea un cluster o aggiungere queste funzionalità in cluster esistenti, tale cluster non è supportato fino a quando la funzionalità non è più disponibile in anteprima e passano a livello generale (GA).
>
> Se si verificano problemi con funzionalità di anteprima [segnalare un problema nel repository GitHub di AKS] [ aks-github] con il nome della funzionalità Anteprima nel titolo del bug.

## <a name="before-you-begin"></a>Prima di iniziare

È necessario la CLI di Azure versione 2.0.61 o versione successiva installato e configurato. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

Per creare un cluster AKS che è possibile usare i criteri di rete, è necessario attivare un flag funzionalità per la sottoscrizione. Per registrare il flag funzionalità *EnableNetworkPolicy*, usare il comando [az feature register][az-feature-register] come mostrato nell'esempio seguente:

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato di registrazione usando il [elenco delle funzionalità az] [ az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione dei *containerservice* provider di risorse usando la [register di az provider] [ az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-network-policy"></a>Panoramica dei criteri di rete

Tutti i POD in un cluster AKS possono inviare e ricevere traffico senza limitazioni, per impostazione predefinita. Per migliorare la sicurezza, è possibile definire regole per il controllo del flusso del traffico. Applicazioni back-end vengono esposte spesso solo per i servizi front-end necessari, ad esempio. In alternativa, i componenti database accessibili solo per i livelli di applicazione che si connettono a essi.

Criteri di rete sono una specifica di Kubernetes che definisce i criteri di accesso per la comunicazione tra i POD. Usando i criteri di rete, si definisce un set ordinato di regole per inviare e ricevere il traffico e applicarli a una raccolta di POD che corrispondono a uno o più selettori di etichetta.

Queste regole di criteri di rete siano definite come YAML manifesti. I criteri di rete possono essere inclusi come parte di un manifesto più ampio che crea anche un servizio o distribuzione.

### <a name="network-policy-options-in-aks"></a>Opzioni dei criteri di rete nel servizio contenitore di AZURE

Azure offre due modi per implementare criteri di rete. Scegliere un'opzione di criteri di rete quando si crea un cluster AKS. L'opzione dei criteri non può essere modificato dopo la creazione del cluster:

* Implementazione di Azure, chiamato *i criteri di rete di Azure*.
* *I criteri di rete Tigrato*, una rete di open source e una soluzione di sicurezza di rete fondata da [Tigera][tigera].

Entrambe le implementazioni usano Linux *IPTables* per applicare i criteri specificati. I criteri vengono convertiti in set di coppie IP consentiti e non consentiti. Queste coppie quindi vengono programmate come regole di Iptables filtro.

Criteri di rete funzionano solo con l'opzione Azure CNI (avanzate). Implementazione è diversa per le due opzioni:

* *Criteri di rete di Azure* -CNI Azure consente di impostare un bridge nell'host della macchina virtuale di rete tra nodi. Le regole di filtro vengono applicate quando i pacchetti passano attraverso il bridge.
* *I criteri di rete Tigrato* -CNI Azure Configura le route di kernel locale per il traffico tra nodi. I criteri vengono applicati nell'interfaccia di rete del pod.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Differenze tra i criteri di Azure e Tigrato e le relative funzionalità

| Funzionalità                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Piattaforme supportate                      | Linux                      | Linux                       |
| Opzioni di rete supportate             | Azure CNI                  | Azure CNI                   |
| Conformità con la specifica di Kubernetes | Tutti i tipi di criteri supportati |  Tutti i tipi di criteri supportati |
| Funzionalità aggiuntive                      | Nessuna                       | Esteso al modello dei criteri costituita da criteri di rete globali, impostare rete globale e Host Endpoint. Per altre informazioni sull'uso di `calicoctl` CLI per gestire tali estese le funzionalità, vedere [riferimenti relativi all'utente calicoctl][calicoctl]. |
| Supporto                                  | Supportato dal team di progettazione e supporto tecnico di Azure | Supporto della community Tigrato. Per altre informazioni sul supporto a pagamento aggiuntivo, vedere [opzioni di supporto di progetto Tigrato][calico-support]. |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Creare un cluster del servizio Azure Kubernetes e abilitare i criteri di rete

Per visualizzare i criteri di rete in azione, è possibile creare e quindi espandere un criterio che definisce il flusso del traffico:

* Rifiutare tutto il traffico verso il pod.
* Consentire il traffico in base alle etichette del pod.
* Consentire il traffico in base allo spazio dei nomi.

In primo luogo, è possibile creare un cluster del servizio contenitore di AZURE che supporta i criteri di rete. La funzionalità di criteri di rete può essere abilitata solo quando viene creato il cluster. Non è possibile abilitare criteri di rete in un cluster esistente del servizio Azure Kubernetes.

Per usare i criteri di rete con un cluster AKS, è necessario usare il [plug-in CNI Azure] [ azure-cni] e definire la propria rete virtuale e le subnet. Per altri dettagli su come pianificare gli intervalli di subnet necessari, vedere [Configurare funzionalità di rete avanzate][use-advanced-networking].

Lo script di esempio seguente:

* Crea una rete virtuale e una subnet.
* Crea entità servizio per l'uso di Azure Active Directory (Azure AD) con il cluster AKS.
* Assegna autorizzazioni di *Collaboratore* per l'entità servizio del cluster del servizio Azure Kubernetes nella rete virtuale.
* Crea un cluster AKS nella rete virtuale definita e abilita i criteri di rete.
    * Il *azure* viene utilizzata l'opzione dei criteri di rete. Per usare invece Tigrato come l'opzione dei criteri di rete, usare il `--network-policy calico` parametro.

Specificare la propria *SP_PASSWORD* protetta. È possibile sostituire il *nome_gruppo_di_risorse* e *CLUSTER_NAME* variabili:

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
SP_ID=$(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

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
    --kubernetes-version 1.12.6 \
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

La creazione del cluster richiede alcuni minuti. Quando il cluster è pronto, configurare `kubectl` per connettersi al cluster Kubernetes usando il [az aks get-credentials] [ az-aks-get-credentials] comando. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarle:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Rifiutare tutto il traffico in ingresso verso un pod

Prima di definire le regole per consentire il traffico di rete specifico, creare innanzitutto un criterio di rete per rifiutare tutto il traffico. Questo criterio fornisce un punto di partenza per iniziare a elenco elementi consentiti solo il traffico desiderato. Si vede anche chiaramente che il traffico viene eliminato quando si applica il criterio di rete.

Per l'ambiente dell'applicazione di esempio e le regole del traffico, è necessario prima creare uno spazio dei nomi chiamato *sviluppo* per eseguire i POD di esempio:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Creare un pod di back-end di esempio che esegue NGINX. Questo back-end pod è utilizzabile per simulare un'applicazione di esempio back-end basato sul web. Creare il pod nello spazio dei nomi *development* e aprire la porta *80* per gestire il traffico Web. Assegnare al pod l'etichetta *app=webapp,role=backend* in modo che sia possibile usarlo come destinazione con un criterio di rete nella sezione successiva:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Creare un altro pod e collegarvi una sessione terminal per eseguire il test che è possibile raggiungere correttamente la pagina Web NGINX predefinito:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Al prompt della shell dei comandi utilizzare `wget` per confermare che sia possibile accedere la pagina Web NGINX predefinito:

```console
wget -qO- http://backend
```

L'output di esempio seguente mostra che la pagina Web NGINX predefinito restituito:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Uscire dalla sessione del terminale collegata. Il pod dei test viene eliminato automaticamente.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Creare e applicare i criteri di rete

Ora che è stata verificata che è possibile utilizzare la pagina Web NGINX di base sui pod di back-end di esempio, creare un criterio di rete per negare tutto il traffico. Creare un file denominato `backend-policy.yaml` e incollare il manifesto YAML seguente. Questo manifesto Usa una *podSelector* collegare i criteri per i POD con il *app:webapp, ruolo: back-end* etichetta, come esempio pod NGINX. Non sono definite regole per *ingress*, pertanto viene rifiutato tutto il traffico in ingresso verso il pod:

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

Applicare i criteri di rete usando il [kubectl applicare] [ kubectl-apply] comando e specificare il nome del manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testare i criteri di rete


Vediamo se è possibile usare nuovamente la pagina Web NGINX nel pod back-end. Creare un altro pod di test e collegare una sessione del terminale:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Al prompt della shell dei comandi utilizzare `wget` per vedere se è possibile accedere la pagina Web NGINX predefinito. Questa volta impostare un valore di timeout di *2* secondi. I criteri di rete ora bloccano tutto il traffico in ingresso, in modo che non è possibile caricare la pagina, come illustrato nell'esempio seguente:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Uscire dalla sessione del terminale collegata. Il pod dei test viene eliminato automaticamente.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Consentire il traffico in ingresso in base a un'etichetta del pod

Nella sezione precedente, è stato pianificato un pod NGINX back-end e un criterio di rete è stato creato per negare tutto il traffico. È possibile creare un pod front-end e aggiornare i criteri di rete per consentire il traffico dai POD front-end.

Aggiornare i criteri di rete per consentire il traffico dai pod con le etichette *app:webapp,role:frontend* e in qualsiasi spazio dei nomi. Modificare il precedente *back-end policy.yaml* file, quindi aggiungere *matchLabels* le regole di traffico in ingresso in modo che il manifesto è simile al seguente:

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
> Il criterio di rete usa un *namespaceSelector* e un elemento *podSelector* (elemento) per la regola per il traffico in ingresso. La sintassi YAML è importante per le regole del traffico in ingresso essere additivi. In questo esempio, entrambi gli elementi devono corrispondere perché la regola per il traffico in ingresso venga applicata. Nelle versioni precedenti per le versioni di Kubernetes *1.12* non potrebbe interpretare correttamente questi elementi e limitare il traffico di rete nel modo previsto. Per altre informazioni su questo comportamento, vedere [comportamento da e verso i selettori][policy-rules].

Applicare i criteri di rete aggiornato usando il [kubectl applicare] [ kubectl-apply] comando e specificare il nome del manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Pianificare un pod che viene etichettato come *app = App Web, ruolo = front-end* e collegarvi una sessione terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Al prompt della shell dei comandi utilizzare `wget` per vedere se è possibile accedere la pagina Web NGINX predefinito:

```console
wget -qO- http://backend
```

Dato che la regola in ingresso consente il traffico con i POD con le etichette *app: app Web, ruolo: front-end*, è consentito il traffico dai pod front-end. L'output di esempio seguente mostra la pagina Web NGINX predefinito restituita:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Uscire dalla sessione del terminale collegata. I pod viene eliminato automaticamente.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testare un pod senza un'etichetta corrispondente

I criteri di rete consentono il traffico dai pod etichettati *app: webapp,role: frontend*, ma dovrebbero rifiutare tutto il resto del traffico. È possibile testare per verificare se un altro baccello senza le etichette vengono può accedere il pod NGINX back-end. Creare un altro pod di test e collegare una sessione del terminale:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Al prompt della shell dei comandi utilizzare `wget` per vedere se è possibile accedere la pagina Web NGINX predefinito. I criteri di rete bloccano il traffico in ingresso, in modo che non è possibile caricare la pagina, come illustrato nell'esempio seguente:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Uscire dalla sessione del terminale collegata. Il pod dei test viene eliminato automaticamente.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Consentire solo il traffico da uno spazio dei nomi definito

Negli esempi precedenti, si creano criteri di rete che tutto il traffico negato e quindi aggiornato il criterio per consentire il traffico dai POD con un'etichetta specifica. Un'altra esigenza comune consiste nel limitare il traffico solo all'interno di un determinato spazio dei nomi. Se gli esempi precedenti sono state per il traffico in un *development* dello spazio dei nomi, creare un criterio di rete che impedisce il traffico proveniente da un altro spazio dei nomi, ad esempio *produzione*, di raggiungere il numero di POD.

Per prima cosa, creare un nuovo spazio dei nomi per simulare quello dell'ambiente di produzione:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Pianificare un pod di test nello spazio dei nomi *production* con l'etichetta *app=webapp,role=frontend*. Collegare una sessione del terminale:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Al prompt della shell dei comandi utilizzare `wget` per confermare che sia possibile accedere la pagina Web NGINX predefinito:

```console
wget -qO- http://backend.development
```

Poiché le etichette per i pod corrispondono a ciò che è attualmente consentito nei criteri di rete, il traffico è consentito. I criteri di rete non esaminano gli spazi dei nomi, ma solo le etichette dei pod. L'output di esempio seguente mostra la pagina Web NGINX predefinito restituita:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Uscire dalla sessione del terminale collegata. Il pod dei test viene eliminato automaticamente.

```console
exit
```

### <a name="update-the-network-policy"></a>Aggiornare i criteri di rete

È possibile aggiornare la regola in ingresso *namespaceSelector* sezione per consentire solo il traffico dall'interno di *sviluppo* dello spazio dei nomi. Modificare il file manifesto *backend-policy.yaml* come illustrato nell'esempio seguente:

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

Negli esempi più complessi, è possibile definire più regole di traffico in ingresso, ad esempio un *namespaceSelector* e quindi una *podSelector*.

Applicare i criteri di rete aggiornato usando il [kubectl applicare] [ kubectl-apply] comando e specificare il nome del manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testare i criteri di rete aggiornati

Pianificare un'altra pod nel *produzione* dello spazio dei nomi e collegarvi una sessione terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Al prompt della shell dei comandi utilizzare `wget` per verificare che i criteri di rete ora rifiuta il traffico:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Uscire dal pod di test:

```console
exit
```

Con il traffico negato dal *produzione* spazio dei nomi, pianificazione di un pod test nuovamente il *sviluppo* dello spazio dei nomi e collegarvi una sessione terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Al prompt della shell dei comandi utilizzare `wget` per verificare che i criteri di rete consentono il traffico:

```console
wget -qO- http://backend
```

Il traffico è consentito perché il pod è pianificato nello spazio dei nomi che corrisponde a ciò che è consentito nei criteri di rete. L'output di esempio seguente mostra la pagina Web NGINX predefinito restituita:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Uscire dalla sessione del terminale collegata. Il pod dei test viene eliminato automaticamente.

```console
exit
```

## <a name="clean-up-resources"></a>Pulire le risorse

In questo articolo, abbiamo creato due spazi dei nomi e applicare un criterio di rete. Per pulire le risorse, usare il [kubectl eliminare] [ kubectl-delete] comando e specificare i nomi delle risorse:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle risorse di rete, vedere [concetti per le applicazioni in Azure Kubernetes Service (AKS) di rete][concepts-network].

Per altre informazioni sui criteri, vedere [i criteri di rete Kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues]
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.5/reference/calicoctl/
[calico-support]: https://www.projectcalico.org/support

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
