---
title: Proteggere il traffico pod con i criteri di reteSecure pod traffic with network policy
titleSuffix: Azure Kubernetes Service
description: Informazioni su come proteggere il traffico che entra e esce dai pod usando i criteri di rete di Kubernetes nel servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 01ba9e7353b6783d1b4fd1649291a64405fd9382
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886705"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Proteggere il traffico tra i pod usando criteri di rete nel servizio Azure Kubernetes

Quando si eseguono applicazioni moderne basate su microservizi in Kubernetes, spesso è necessario controllare quali componenti possono comunicare tra loro. Il principio dei privilegi minimi deve essere applicato al flusso del traffico tra i pod in un cluster di servizio Azure Kubernetes Service (AKS). Si supponga che si desidera bloccare il traffico direttamente alle applicazioni back-end. La funzionalità *Criteri* di rete in Kubernetes consente di definire le regole per il traffico in ingresso ed in uscita tra i pod in un cluster.

In questo articolo viene illustrato come installare il motore dei criteri di rete e creare criteri di rete Kubernetes per controllare il flusso di traffico tra i pod in AKS. I criteri di rete devono essere utilizzati solo per i nodi e i pod basati su Linux in AKS.Network policy should be used only for Linux-based nodes and pods in AKS.

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.61 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

> [!TIP]
> Se è stata utilizzata la funzionalità dei criteri di rete durante l'anteprima, è consigliabile [creare un nuovo cluster.](#create-an-aks-cluster-and-enable-network-policy)
> 
> Se si desidera continuare a utilizzare i cluster di test esistenti che utilizzano i criteri di rete durante l'anteprima, aggiornare il cluster a una nuova versione di Kubernetes per la versione GA più recente e quindi distribuire il seguente manifesto YAML per correggere il server delle metriche di arresto anomalo e il dashboard di Kubernetes. Questa correzione è necessaria solo per i cluster che utilizzano il motore dei criteri di rete Calico.
>
> Per una sicurezza ottimale, [esaminare il contenuto di questo manifesto YAML][calico-aks-cleanup] per comprendere cosa viene distribuito nel cluster AKS.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Panoramica dei criteri di rete

Tutti i pod in un cluster AKS possono inviare e ricevere traffico senza limitazioni, per impostazione predefinita. Per migliorare la sicurezza, è possibile definire regole per il controllo del flusso del traffico. Le applicazioni back-end sono spesso esposte solo ai servizi front-end richiesti, ad esempio. In alternativa, i componenti di database sono accessibili solo ai livelli applicazione che si connettono a essi.

Criteri di rete è una specifica Kubernetes che definisce i criteri di accesso per la comunicazione tra Pod. Utilizzando criteri di rete, si definisce un set ordinato di regole per inviare e ricevere traffico e applicarli a una raccolta di pod che corrispondono a uno o più selettori di etichetta.

Queste regole dei criteri di rete sono definite come manifesti YAML. I criteri di rete possono essere inclusi come parte di un manifesto più ampio che crea anche una distribuzione o un servizio.Network policies can be included as part of a wider manifest that also creates a deployment or service.

### <a name="network-policy-options-in-aks"></a>Opzioni dei criteri di rete in AKS

Azure offre due modi per implementare i criteri di rete. È possibile scegliere un'opzione dei criteri di rete quando si crea un cluster AKS. L'opzione dei criteri non può essere modificata dopo la creazione del cluster:The policy option can't be changed after the cluster is created:

* L'implementazione di Azure, denominata *Criteri di rete*di Azure .
* *Calico Network Policies*, una rete open source e soluzione di sicurezza di rete fondata da [Tigera.][tigera]

Entrambe le implementazioni utilizzano *IPTables* Linux per applicare i criteri specificati. I criteri vengono convertiti in set di coppie IP consentite e non consentite. Queste coppie vengono quindi programmate come regole di filtro IPTable.These pairs are then programmed as IPTable filter rules.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Differenze tra i criteri di Azure e Calico e le relative funzionalità

| Funzionalità                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Piattaforme supportate                      | Linux                      | Linux                       |
| Opzioni di rete supportate             | CNI di AzureAzure CNI                  | Azure CNI e kubenet Azure CNI and kubenet       |
| Conformità con la specifica Kubernetes | Tutti i tipi di criteri supportati |  Tutti i tipi di criteri supportati |
| Funzionalità aggiuntive                      | nessuno                       | Modello di criteri esteso costituito da Criteri di rete globali, Set di rete globale ed Endpoint host. Per ulteriori informazioni `calicoctl` sull'utilizzo dell'interfaccia della riga di comando per gestire queste funzionalità estese, vedere [calicoctl user reference][calicoctl]. |
| Supporto                                  | Supportato dal team di supporto e progettazione di AzureSupported by Azure support and Engineering team | Supporto della comunità Calico. Per ulteriori informazioni sul supporto aggiuntivo a pagamento, consultate Opzioni di [supporto di Project Calico.][calico-support] |
| Registrazione                                  | Le regole aggiunte/eliminate nelle tabelle IPTable vengono registrate in ogni host in */var/log/azure-npm.log* | Per ulteriori informazioni, vedere [Registri dei componenti CalicoFor more information,][calico-logs] see Calico component logs |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Creare un cluster del servizio Azure Kubernetes e abilitare i criteri di rete

Per visualizzare i criteri di rete in azione, creiamo ed espandiamo un criterio che definisce il flusso di traffico:To see network policies in action, let's create and then expand on a policy that defines traffic flow:

* Rifiutare tutto il traffico verso il pod.
* Consentire il traffico in base alle etichette del pod.
* Consentire il traffico in base allo spazio dei nomi.

In primo luogo, creiamo un cluster AKS che supporta i criteri di rete. 

> [!IMPORTANT]
>
> La funzionalità dei criteri di rete può essere abilitata solo quando viene creato il cluster. Non è possibile abilitare criteri di rete in un cluster esistente del servizio Azure Kubernetes.

Per usare Criteri di rete di Azure, è necessario usare il [plug-in CNI][azure-cni] di Azure e definire subnet e rete virtuale personalizzata. Per altri dettagli su come pianificare gli intervalli di subnet necessari, vedere [Configurare funzionalità di rete avanzate][use-advanced-networking]. I criteri di rete Calico possono essere usati con lo stesso plug-in CNI di Azure o con il plug-in CNI DiKbenet.Calco Network Policy could be used with this same Azure CNI plug-in or with the Kubenet CNI plug-in.

Lo script di esempio seguente:

* Crea una rete virtuale e una subnet.
* Crea un'entità servizio di Azure Active Directory (Azure AD) da usare con il cluster AKS.
* Assegna autorizzazioni di *Collaboratore* per l'entità servizio del cluster del servizio Azure Kubernetes nella rete virtuale.
* Crea un cluster AKS nella rete virtuale definita e abilita i criteri di rete.
    * Viene usata l'opzione dei criteri di rete *di Azure.The azure* network policy option is used. Per utilizzare Calico come opzione dei `--network-policy calico` criteri di rete, utilizzare il parametro . Nota: Il Calico `--network-plugin azure` può `--network-plugin kubenet`essere utilizzato con uno o .

Specificare la propria *SP_PASSWORD* protetta. È possibile sostituire *le* RESOURCE_GROUP_NAME e *le* CLUSTER_NAME variabili:

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

La creazione del cluster richiede alcuni minuti. Quando il cluster è `kubectl` pronto, configurare la connessione al cluster Kubernetes utilizzando il comando [az aks get-credentials.][az-aks-get-credentials] Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarle:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Rifiutare tutto il traffico in ingresso verso un pod

Prima di definire le regole per consentire il traffico di rete specifico, creare innanzitutto un criterio di rete per rifiutare tutto il traffico. Questo criterio fornisce un punto di partenza per iniziare a whitelist solo il traffico desiderato. Si vede anche chiaramente che il traffico viene eliminato quando si applica il criterio di rete.

Per l'ambiente dell'applicazione di esempio e le regole del traffico, creiamo innanzitutto uno spazio dei nomi denominato sviluppo per eseguire i pod di esempio:For the sample application environment and traffic rules, let's first create a namespace called *development* to run the example pods:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Creare un pod back-end di esempio che esegue NGINX. Questo pod back-end può essere utilizzato per simulare un'applicazione basata sul Web back-end di esempio. Creare il pod nello spazio dei nomi *development* e aprire la porta *80* per gestire il traffico Web. Assegnare al pod l'etichetta *app=webapp,role=backend* in modo che sia possibile usarlo come destinazione con un criterio di rete nella sezione successiva:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Create un altro contenitore e collegate una sessione terminale per verificare che sia possibile raggiungere correttamente la pagina Web NGINX predefinita:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Al prompt della `wget` shell, utilizzare per confermare che è possibile accedere alla pagina Web NGINX predefinita:

```console
wget -qO- http://backend
```

L'output di esempio seguente mostra che la pagina Web NGINX predefinita ha restituito:

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

Dopo aver confermato che è possibile utilizzare la pagina Web NGINX di base nel pod back-end di esempio, creare un criterio di rete per negare tutto il traffico. Creare un file denominato `backend-policy.yaml` e incollare il manifesto YAML seguente. Questo manifesto usa un *podSelector* per associare i criteri ai pod con *l'etichetta app:webapp,role:backend,* ad esempio il pod NGINX di esempio. Non sono definite regole per *ingress*, pertanto viene rifiutato tutto il traffico in ingresso verso il pod:

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

Passare [https://shell.azure.com](https://shell.azure.com) a per aprire Azure Cloud Shell nel browser.

Applicare i criteri di rete utilizzando il comando [kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testare i criteri di rete

Vediamo se è possibile utilizzare nuovamente la pagina Web NGINX sul pod back-end. Creare un altro pod di test e collegare una sessione del terminale:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Al prompt della `wget` shell, utilizzare per vedere se è possibile accedere alla pagina Web NGINX predefinita. Questa volta impostare un valore di timeout di *2* secondi. Il criterio di rete ora blocca tutto il traffico in ingresso, pertanto la pagina non può essere caricata, come illustrato nell'esempio seguente:The network policy now blocks all inbound traffic, so the page can't be loaded, as shown in the following example:

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

Nella sezione precedente è stato pianificato un pod NGINX back-end ed è stato creato un criterio di rete per negare tutto il traffico. Creiamo un pod front-end e aggiorniamo i criteri di rete per consentire il traffico dai pod front-end.

Aggiornare i criteri di rete per consentire il traffico dai pod con le etichette *app:webapp,role:frontend* e in qualsiasi spazio dei nomi. Modificare il file *back-end-policy.yaml* precedente e aggiungere le regole di ingresso *matchLabels* in modo che il manifesto sia simile all'esempio seguente:

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
> Il criterio di rete usa un *namespaceSelector* e un elemento *podSelector* (elemento) per la regola per il traffico in ingresso. La sintassi YAML è importante per le regole in ingresso per essere additivo. In questo esempio, entrambi gli elementi devono corrispondere perché la regola per il traffico in ingresso venga applicata. Kubernetes versioni precedenti alla *1.12* potrebbero non interpretare correttamente questi elementi e limitare il traffico di rete come previsto. Per ulteriori informazioni su questo comportamento, vedere [Comportamento di da e verso i selettori][policy-rules].

Applicare i criteri di rete aggiornati utilizzando il comando [kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

Pianificare un pod etichettato come *app,webapp,ruolo-frontend* e allegare una sessione terminale:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Al prompt della `wget` shell, utilizzare per vedere se è possibile accedere alla pagina Web NGINX predefinita:

```console
wget -qO- http://backend
```

Poiché la regola di ingresso consente il traffico con pod con etichette *app: webapp,role: frontend*, il traffico dal pod front-end è consentito. L'output di esempio seguente mostra la pagina Web NGINX predefinita restituita:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Uscire dalla sessione del terminale collegata. Il contenitore viene eliminato automaticamente.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testare un pod senza un'etichetta corrispondente

I criteri di rete consentono il traffico dai pod etichettati *app: webapp,role: frontend*, ma dovrebbero rifiutare tutto il resto del traffico. Esaminiamo se un altro pod senza tali etichette può accedere al pod NGINX back-end. Creare un altro pod di test e collegare una sessione del terminale:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Al prompt della `wget` shell, utilizzare per vedere se è possibile accedere alla pagina Web NGINX predefinita. Il criterio di rete blocca il traffico in ingresso, pertanto non è possibile caricare la pagina, come illustrato nell'esempio seguente:The network policy blocks the inbound traffic, so the page can't be loaded, as shown in the following example:

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

Negli esempi precedenti è stato creato un criterio di rete che nega tutto il traffico e quindi ha aggiornato il criterio per consentire il traffico proveniente da pod con un'etichetta specifica. Un'altra esigenza comune consiste nel limitare il traffico solo all'interno di un determinato spazio dei nomi. Se gli esempi precedenti riguardavano il traffico in uno spazio dei nomi di *sviluppo,* creare un criterio di rete che impedisca il raggiungimento del traffico proveniente da un altro spazio dei nomi, ad esempio *production*, di raggiungere i pod.

Per prima cosa, creare un nuovo spazio dei nomi per simulare quello dell'ambiente di produzione:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Pianificare un pod di test nello spazio dei nomi *production* con l'etichetta *app=webapp,role=frontend*. Collegare una sessione del terminale:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Al prompt della `wget` shell, utilizzare per confermare che è possibile accedere alla pagina Web NGINX predefinita:

```console
wget -qO- http://backend.development
```

Poiché le etichette per il pod corrispondono a quelle attualmente consentite nei criteri di rete, il traffico è consentito. I criteri di rete non esaminano gli spazi dei nomi, ma solo le etichette dei pod. L'output di esempio seguente mostra la pagina Web NGINX predefinita restituita:

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

È possibile aggiornare la sezione *namespaceSelector* della regola in ingresso per consentire solo il traffico dall'interno dello spazio dei nomi di *sviluppo.* Modificare il file manifesto *backend-policy.yaml* come illustrato nell'esempio seguente:

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

Negli esempi più complessi, è possibile definire più regole di ingresso, ad esempio *namespaceSelector* e quindi *podSelector*.

Applicare i criteri di rete aggiornati utilizzando il comando [kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testare i criteri di rete aggiornati

Programmate un altro pod nello spazio dei nomi di *produzione* e collegate una sessione terminale:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Al prompt della `wget` shell, utilizzare per vedere che il criterio di rete ora nega il traffico:

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

Con il traffico negato dallo spazio dei nomi di *produzione,* pianificare un pod di test nello spazio dei nomi di sviluppo e collegare una sessione terminale:With traffic denied from the production namespace, schedule a test pod back in the *development* namespace and attach a terminal session:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Al prompt della `wget` shell, utilizzare per vedere che il criterio di rete consente il traffico:

```console
wget -qO- http://backend
```

Il traffico è consentito perché il pod è pianificato nello spazio dei nomi che corrisponde a ciò che è consentito nei criteri di rete. L'output di esempio seguente mostra la pagina Web NGINX predefinita restituita:

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

In questo articolo sono stati creati due spazi dei nomi e sono stati applicati criteri di rete. Per pulire queste risorse, usare il comando [kubectl delete][kubectl-delete] e specificare i nomi delle risorse:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle risorse di rete, vedere Concetti relativi [alla rete per le applicazioni nel servizio Azure Kubernetes (AKS)][concepts-network].

Per ulteriori informazioni sui criteri, vedere [Criteri di rete di Kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.9/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/v3.9/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
