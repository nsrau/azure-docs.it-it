---
title: Proteggere i pod con criteri di rete nel servizio Azure Kubernetes
description: Informazioni su come proteggere il traffico che passa da e verso i pod usando i criteri di rete Kubernetes in Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: c9bf2c2c459999813c7fc30f95be653168d270ad
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67613960"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Proteggere il traffico tra i pod usando criteri di rete nel servizio Azure Kubernetes

Quando si eseguono applicazioni moderne basate su microservizi in Kubernetes, spesso è necessario controllare quali componenti possono comunicare tra loro. Il principio del privilegio minimo deve essere applicato al modo in cui il traffico può fluire tra i pod in un cluster di Azure Kubernetes Service (AKS). Supponiamo di voler bloccare il traffico direttamente verso le applicazioni back-end. La funzionalità dei *criteri di rete* in Kubernetes consente di definire le regole per il traffico in ingresso e in uscita tra i pod in un cluster.

Questo articolo illustra come installare il motore dei criteri di rete e creare i criteri di rete Kubernetes per controllare il flusso di traffico tra i pod in AKS. I criteri di rete devono essere usati solo per i nodi basati su Linux e i pod in AKS.

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.61 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia][install-azure-cli]della riga di comando di Azure.

> [!TIP]
> Se è stata usata la funzionalità dei criteri di rete durante l'anteprima, si consiglia di [creare un nuovo cluster](#create-an-aks-cluster-and-enable-network-policy).
> 
> Se si vuole continuare a usare i cluster di test esistenti che hanno usato i criteri di rete durante l'anteprima, aggiornare il cluster a una nuova versione di Kubernetes per la versione GA più recente e quindi distribuire il manifesto YAML seguente per correggere il server delle metriche in arresto anomalo e Kubernetes Dashboard. Questa correzione è necessaria solo per i cluster che usano il motore dei criteri di rete di calice.
>
> Come procedura consigliata di sicurezza, [esaminare il contenuto del manifesto YAML][calico-aks-cleanup] per capire cosa viene distribuito nel cluster AKS.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Panoramica dei criteri di rete

Per impostazione predefinita, tutti i pod in un cluster AKS possono inviare e ricevere traffico senza limitazioni. Per migliorare la sicurezza, è possibile definire regole per il controllo del flusso del traffico. Le applicazioni back-end vengono spesso esposte solo ai servizi front-end richiesti, ad esempio. In alternativa, i componenti del database sono accessibili solo ai livelli applicazione a cui si connettono.

Criteri di rete è una specifica Kubernetes che definisce i criteri di accesso per la comunicazione tra i pod. Usando i criteri di rete, si definisce un set ordinato di regole per inviare e ricevere traffico e applicarle a una raccolta di pod che corrispondono a uno o più selettori di etichette.

Queste regole dei criteri di rete sono definite come manifesti YAML. I criteri di rete possono essere inclusi come parte di un manifesto più ampio che crea anche una distribuzione o un servizio.

### <a name="network-policy-options-in-aks"></a>Opzioni dei criteri di rete in AKS

Azure offre due modi per implementare i criteri di rete. Quando si crea un cluster AKS si sceglie un'opzione di criteri di rete. L'opzione dei criteri non può essere modificata dopo la creazione del cluster:

* Implementazione di Azure, denominata *criteri di rete di Azure*.
* *Calico Network Policies*, una soluzione di sicurezza di rete e di rete open source fondata da [Tigera][tigera].

Entrambe le implementazioni usano *iptables* Linux per applicare i criteri specificati. I criteri vengono convertiti in set di coppie IP consentite e non consentite. Queste coppie vengono quindi programmate come regole di filtro IPTable.

I criteri di rete funzionano solo con l'opzione Azure CNI (Advanced). L'implementazione è diversa per le due opzioni:

* *Criteri di rete di Azure* : Azure CNI configura un Bridge nell'host della macchina virtuale per la rete all'interno di un nodo. Le regole di filtro vengono applicate quando i pacchetti passano attraverso il Bridge.
* *Criteri di rete* di calice: la CNI di Azure configura le route del kernel locale per il traffico all'interno del nodo. I criteri vengono applicati sull'interfaccia di rete del Pod.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Differenze tra i criteri di Azure e di calice e le relative funzionalità

| Capacità                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Piattaforme supportate                      | Linux                      | Linux                       |
| Opzioni di rete supportate             | Azure CNI                  | Azure CNI                   |
| Conformità con la specifica Kubernetes | Tutti i tipi di criteri supportati |  Tutti i tipi di criteri supportati |
| Funzionalità aggiuntive                      | Nessuna                       | Modello di criteri esteso costituito da criteri di rete globali, da un set di reti globale e da un endpoint host. Per ulteriori informazioni sull'utilizzo dell' `calicoctl` interfaccia della riga di comando per la gestione di queste funzionalità estese, vedere [calicoctl User Reference][calicoctl]. |
| Supporto                                  | Supportato dal team di progettazione e supporto tecnico di Azure | Supporto della community di calice. Per altre informazioni sul supporto a pagamento aggiuntivo, vedere [Opzioni di supporto per Project calice][calico-support]. |
| Registrazione                                  | Le regole aggiunte/eliminate in IPTables vengono registrate in ogni host in */var/log/Azure-NPM.log* | Per ulteriori informazioni, vedere [log dei componenti][calico-logs] di calice |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Creare un cluster del servizio Azure Kubernetes e abilitare i criteri di rete

Per visualizzare i criteri di rete in azione, è possibile creare ed espandere i criteri che definiscono il flusso del traffico:

* Rifiutare tutto il traffico verso il pod.
* Consentire il traffico in base alle etichette del pod.
* Consentire il traffico in base allo spazio dei nomi.

Prima di tutto, creare un cluster AKS che supporti i criteri di rete. La funzionalità dei criteri di rete può essere abilitata solo quando viene creato il cluster. Non è possibile abilitare criteri di rete in un cluster esistente del servizio Azure Kubernetes.

Per usare i criteri di rete con un cluster AKS, è necessario usare il [plug-in CNI di Azure][azure-cni] e definire la rete virtuale e le subnet. Per informazioni più dettagliate su come pianificare gli intervalli di subnet necessari, vedere [configurare Advanced Networking][use-advanced-networking].

Lo script di esempio seguente:

* Crea una rete virtuale e una subnet.
* Crea un'entità servizio Azure Active Directory (Azure AD) per l'uso con il cluster AKS.
* Assegna autorizzazioni di *Collaboratore* per l'entità servizio del cluster del servizio Azure Kubernetes nella rete virtuale.
* Crea un cluster AKS nella rete virtuale definita e Abilita i criteri di rete.
    * Viene usata l'opzione dei criteri di rete di *Azure* . Per usare invece l'opzione dei criteri di rete, usare il `--network-policy calico` parametro.

Specificare la propria *SP_PASSWORD* protetta. È possibile sostituire le variabili *RESOURCE_GROUP_NAME* e *CLUSTER_NAME* :

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

La creazione del cluster richiede alcuni minuti. Quando il cluster è pronto, configurare `kubectl` per connettersi al cluster Kubernetes usando il comando [AZ AKS Get-credentials][az-aks-get-credentials] . Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarle:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Rifiutare tutto il traffico in ingresso verso un pod

Prima di definire le regole per consentire il traffico di rete specifico, creare innanzitutto un criterio di rete per rifiutare tutto il traffico. Questo criterio offre un punto di partenza per iniziare a inserire nell'elenco elementi consentiti solo il traffico desiderato. Si vede anche chiaramente che il traffico viene eliminato quando si applica il criterio di rete.

Per l'ambiente dell'applicazione di esempio e le regole del traffico, creare prima di tutto uno spazio dei nomi denominato *sviluppo* per eseguire i pod di esempio:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Creare un pod back-end di esempio che esegue NGINX. Questo pod back-end può essere usato per simulare un'applicazione basata sul Web back-end di esempio. Creare il pod nello spazio dei nomi *development* e aprire la porta *80* per gestire il traffico Web. Assegnare al pod l'etichetta *app=webapp,role=backend* in modo che sia possibile usarlo come destinazione con un criterio di rete nella sezione successiva:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Creare un altro pod e alleghi una sessione terminal per verificare che sia possibile raggiungere correttamente la pagina Web NGINX predefinita:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Al prompt della shell, usare `wget` per confermare che è possibile accedere alla pagina web nginx predefinita:

```console
wget -qO- http://backend
```

L'output di esempio seguente mostra che è stata restituita la pagina Web NGINX predefinita:

```
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

A questo punto, dopo aver verificato che è possibile usare la pagina Web NGINX di base sul Pod back-end di esempio, creare un criterio di rete per negare tutto il traffico. Creare un file denominato `backend-policy.yaml` e incollare il manifesto YAML seguente. Questo manifesto usa un *podSelector* per allineare i criteri ai pod con l'etichetta *app: webapp, Role: backend* , come il Pod nginx di esempio. Non sono definite regole per *ingress*, pertanto viene rifiutato tutto il traffico in ingresso verso il pod:

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

Applicare i criteri di rete usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testare i criteri di rete


Vediamo se è possibile usare di nuovo la pagina Web NGINX sul Pod back-end. Creare un altro pod di test e collegare una sessione del terminale:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Al prompt della shell, usare `wget` per verificare se è possibile accedere alla pagina web nginx predefinita. Questa volta impostare un valore di timeout di *2* secondi. Il criterio di rete ora blocca tutto il traffico in ingresso, quindi la pagina non può essere caricata, come illustrato nell'esempio seguente:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Uscire dalla sessione del terminale collegata. Il pod di test viene eliminato automaticamente.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Consentire il traffico in ingresso in base a un'etichetta del pod

Nella sezione precedente è stato pianificato un pod back-end NGINX ed è stato creato un criterio di rete per negare tutto il traffico. Viene ora creato un pod front-end e vengono aggiornati i criteri di rete per consentire il traffico dai pod front-end.

Aggiornare i criteri di rete per consentire il traffico dai pod con le etichette *app:webapp,role:frontend* e in qualsiasi spazio dei nomi. Modificare il file con *estensione YAML del criterio back-end* precedente e aggiungere le regole di ingresso *matchLabels* in modo che il manifesto appaia come nell'esempio seguente:

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
> Il criterio di rete usa un *namespaceSelector* e un elemento *podSelector* (elemento) per la regola per il traffico in ingresso. La sintassi YAML è importante per l'aggiunta delle regole in ingresso. In questo esempio, entrambi gli elementi devono corrispondere perché la regola per il traffico in ingresso venga applicata. Le versioni di Kubernetes precedenti alla *1,12* potrebbero non interpretare correttamente questi elementi e limitare il traffico di rete come previsto. Per ulteriori informazioni su questo comportamento, vedere il [comportamento dei selettori da a e da][policy-rules].

Applicare i criteri di rete aggiornati usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Pianificare un pod con etichetta *app = webapp, Role = frontend* e alleghi una sessione terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Al prompt della shell, usare `wget` per verificare se è possibile accedere alla pagina web nginx predefinita:

```console
wget -qO- http://backend
```

Poiché la regola di ingresso consente il traffico con Pod con l'app labels *: webapp, Role: frontend*, il traffico dal Pod front-end è consentito. L'output di esempio seguente mostra la pagina Web NGINX predefinita restituita:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Uscire dalla sessione del terminale collegata. Il Pod viene eliminato automaticamente.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testare un pod senza un'etichetta corrispondente

I criteri di rete consentono il traffico dai pod etichettati *app: webapp,role: frontend*, ma dovrebbero rifiutare tutto il resto del traffico. Viene ora testato per verificare se un altro Pod senza tali etichette può accedere al pod di NGINX back-end. Creare un altro pod di test e collegare una sessione del terminale:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Al prompt della shell, usare `wget` per verificare se è possibile accedere alla pagina web nginx predefinita. Il criterio di rete blocca il traffico in ingresso, quindi la pagina non può essere caricata, come illustrato nell'esempio seguente:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Uscire dalla sessione del terminale collegata. Il pod di test viene eliminato automaticamente.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Consentire solo il traffico da uno spazio dei nomi definito

Negli esempi precedenti sono stati creati criteri di rete che hanno negato tutto il traffico, quindi sono stati aggiornati i criteri per consentire il traffico da Pod con un'etichetta specifica. Un'altra esigenza comune consiste nel limitare il traffico solo all'interno di uno spazio dei nomi specifico. Se gli esempi precedenti erano relativi al traffico in uno spazio dei nomi di *sviluppo* , creare criteri di rete che impediscono il traffico da un altro spazio dei nomi, ad esempio la *produzione*, di raggiungere i pod.

Per prima cosa, creare un nuovo spazio dei nomi per simulare quello dell'ambiente di produzione:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Pianificare un pod di test nello spazio dei nomi *production* con l'etichetta *app=webapp,role=frontend*. Collegare una sessione del terminale:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Al prompt della shell, usare `wget` per confermare che è possibile accedere alla pagina web nginx predefinita:

```console
wget -qO- http://backend.development
```

Poiché le etichette per il Pod corrispondono a quanto attualmente consentito nei criteri di rete, il traffico è consentito. I criteri di rete non esaminano gli spazi dei nomi, ma solo le etichette dei pod. L'output di esempio seguente mostra la pagina Web NGINX predefinita restituita:

```
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

Viene ora aggiornata la sezione *namespaceSelector* della regola in ingresso per consentire solo il traffico dallo spazio dei nomi di *sviluppo* . Modificare il file manifesto *backend-policy.yaml* come illustrato nell'esempio seguente:

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

In esempi più complessi, è possibile definire più regole di ingresso, ad esempio un *namespaceSelector* e un *podSelector*.

Applicare i criteri di rete aggiornati usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testare i criteri di rete aggiornati

Pianificare un altro Pod nello spazio dei nomi Production e alleghi una sessione terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Al prompt della shell, usare `wget` per verificare che il criterio di rete neghi ora il traffico:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Uscire dal pod di test:

```console
exit
```

Con il traffico negato dallo spazio dei nomi di *produzione* , pianificare un pod di test nello spazio dei nomi di *sviluppo* e associare una sessione terminal:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Al prompt della shell, usare `wget` per verificare che i criteri di rete consentano il traffico:

```console
wget -qO- http://backend
```

Il traffico è consentito perché il Pod è pianificato nello spazio dei nomi che corrisponde a ciò che è consentito nei criteri di rete. L'output di esempio seguente mostra la pagina Web NGINX predefinita restituita:

```
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

In questo articolo sono stati creati due spazi dei nomi e sono stati applicati criteri di rete. Per pulire queste risorse, usare il comando [kubectl Delete][kubectl-delete] e specificare i nomi delle risorse:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle risorse di rete, vedere [concetti di rete per le applicazioni in Azure Kubernetes Service (AKS)][concepts-network].

Per ulteriori informazioni sui criteri, vedere [criteri di rete Kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.6/reference/calicoctl/
[calico-support]: https://www.projectcalico.org/support
[calico-logs]: https://docs.projectcalico.org/v3.6/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
