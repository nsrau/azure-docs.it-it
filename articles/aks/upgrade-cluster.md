---
title: Aggiornare un cluster del servizio Azure Kubernetes
description: Informazioni su come aggiornare un cluster di Azure Kubernetes Service (AKS) per ottenere le funzionalità più recenti e gli aggiornamenti della sicurezza.
services: container-service
ms.topic: article
ms.date: 05/28/2020
ms.openlocfilehash: da46c44dc9cc16dfa44aacb15b35b652c0c912a9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050616"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Aggiornare un cluster del servizio Azure Kubernetes

Nel corso del ciclo di vita di un cluster del servizio Azure Kubernetes, è spesso necessario eseguire l'aggiornamento alla versione più recente di Kubernetes. È infatti importante applicare gli ultimi aggiornamenti della sicurezza di Kubernetes o eseguire l'aggiornamento per ottenere le funzionalità più nuove. Questo articolo illustra come aggiornare i componenti Master o un singolo pool di nodi predefinito in un cluster AKS.

Per i cluster AKS che usano più pool di nodi o nodi di Windows Server, vedere [aggiornare un pool di nodi in AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede la versione 2.0.65 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

> [!WARNING]
> Un aggiornamento del cluster AKS attiva un cordone e lo svuotamento dei nodi. Se è disponibile una quota di calcolo bassa, l'aggiornamento potrebbe non riuscire. Per altre informazioni, vedere [aumentare le quote](../azure-portal/supportability/resource-manager-core-quotas-request.md) .

## <a name="check-for-available-aks-cluster-upgrades"></a>Verificare la presenza di aggiornamenti disponibili per il cluster del servizio Azure Kubernetes

Per verificare quali versioni di Kubernetes sono disponibili per il cluster, usare il comando [az aks get-upgrades][az-aks-get-upgrades]. L'esempio seguente verifica se sono disponibili aggiornamenti per il cluster denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Quando si aggiorna un cluster AKS supportato, non è possibile ignorare le versioni secondarie di Kubernetes. Ad esempio, gli aggiornamenti compresi tra *1.12. x*  ->  *1.13. x* o *1.13. x*  ->  *1.14.* x sono consentiti, tuttavia *1.12*. x  ->  *1.14. x* non lo è.
>
> Per eseguire l'aggiornamento, da *1.12. x*  ->  *1.14. x*, eseguire prima l'aggiornamento da *1.12.* x  ->  *1.13. x*, quindi eseguire l'aggiornamento da *1.13. x*  ->  *1.14. x*.
>
> È possibile ignorare più versioni solo quando si effettua l'aggiornamento da una versione non supportata di nuovo in una versione supportata. Ad esempio, è possibile eseguire l'aggiornamento da un valore *1,10. x* non supportato--> è possibile completare un valore *1.15. x* supportato.

L'output di esempio seguente mostra che il cluster può essere aggiornato alle versioni *1.13.9* e *1.13.10*:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Se non è disponibile alcun aggiornamento, si otterrà quanto segue:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade-preview"></a>Personalizzare l'aggiornamento del sovraccarico del nodo (anteprima)

> [!Important]
> I picchi di nodo richiedono una quota di sottoscrizione per il numero massimo di picchi richiesti per ogni operazione di aggiornamento. Ad esempio, un cluster con 5 pool di nodi, ognuno con un conteggio di 4 nodi, ha un totale di 20 nodi. Se ogni pool di nodi ha un valore di picco massimo del 50%, per completare l'aggiornamento è necessaria una quota di calcolo e di IP aggiuntiva di 10 nodi (2 nodi * 5 pool).
>
> Se si usa Azure CNI, verificare che siano presenti anche IP disponibili nella subnet per [soddisfare i requisiti IP di Azure CNI](configure-azure-cni.md).

Per impostazione predefinita, AKS configura gli aggiornamenti in modo che si picchino con un altro nodo. Un valore predefinito di uno per l'impostazione del picco massimo consente ad AKS di ridurre al minimo l'interferenza del carico di lavoro creando un nodo aggiuntivo prima del Cordono o dello svuotamento delle applicazioni esistenti per sostituire un nodo con versioni precedenti. Il valore max Surge può essere personalizzato per ogni pool di nodi per consentire un compromesso tra la velocità di aggiornamento e l'interruzione dell'aggiornamento. Aumentando il valore di aumento massimo, il processo di aggiornamento viene completato più velocemente, ma l'impostazione di un valore elevato per il picco massimo può causare interferenze durante il processo di aggiornamento. 

Ad esempio, un valore di picco massimo pari al 100% consente il processo di aggiornamento più rapido (raddoppiando il numero di nodi), ma comporta lo svuotamento simultaneo di tutti i nodi del pool di nodi. È possibile utilizzare un valore più elevato, ad esempio per gli ambienti di testing. Per i pool di nodi di produzione è consigliabile usare un'impostazione di max_surge pari al 33%.

AKS accetta sia valori integer che un valore percentuale per il numero massimo di picchi. Un numero intero, ad esempio "5", indica cinque nodi aggiuntivi per l'incremento. Il valore "50%" indica un valore di incremento della metà del numero di nodi corrente nel pool. Il numero massimo di valori percentuali di aumento può essere minimo dell'1% e un massimo del 100%. Un valore percentuale viene arrotondato per eccesso al numero di nodi più vicino. Se il valore max Surge è inferiore al numero di nodi corrente al momento dell'aggiornamento, il numero di nodi corrente viene usato per il valore max surge.

Durante un aggiornamento, il valore max Surge può essere un minimo di 1 e un valore massimo uguale al numero di nodi nel pool di nodi. È possibile impostare valori più grandi, ma il numero massimo di nodi usati per il picco massimo non sarà superiore al numero di nodi nel pool al momento dell'aggiornamento.

### <a name="set-up-the-preview-feature-for-customizing-node-surge-upgrade"></a>Configurare la funzionalità di anteprima per la personalizzazione dell'aggiornamento del sovraccarico del nodo

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "MaxSurgePreview"
```

La registrazione potrebbe richiedere diversi minuti. Usare il comando seguente per verificare che la funzionalità sia registrata:

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MaxSurgePreview')].{Name:name,State:properties.state}"
```

Durante l'anteprima, è necessaria l'estensione dell'interfaccia della riga di comando *AKS-Preview* per usare il picco massimo. Usare il comando [AZ Extension Add][az-extension-add] , quindi verificare la presenza di eventuali aggiornamenti disponibili usando il comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

> [!Important]
> L'impostazione del picco massimo in un pool di nodi è permanente.  Gli aggiornamenti Kubernetes successivi o gli aggiornamenti della versione del nodo utilizzeranno questa impostazione. È possibile modificare il valore di aumento massimo per i pool di nodi in qualsiasi momento. Per i pool di nodi di produzione, è consigliabile usare un'impostazione di picco massimo del 33%.

Usare i comandi seguenti per impostare i valori di picco massimi per i pool di nodi nuovi o esistenti.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Aggiornare un cluster del servizio Azure Container

Con un elenco di versioni disponibili per il cluster del servizio Azure Kubernetes, usare il comando [az aks upgrade][az-aks-upgrade] per eseguire l'aggiornamento. Durante il processo di aggiornamento, AKS aggiunge un nuovo nodo al cluster che esegue la versione specificata di Kubernetes, quindi controlla attentamente [e svuota][kubernetes-drain] uno dei nodi obsoleti per ridurre al minimo le problematiche di esecuzione delle applicazioni. Quando il nuovo nodo viene confermato come esecuzione di Pod applicazione, il nodo precedente viene eliminato. Questo processo si ripete fino a quando tutti i nodi del cluster non sono stati aggiornati.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Per aggiornare il cluster sono necessari alcuni minuti, a seconda del numero di nodi di cui si dispone.

> [!NOTE]
> È previsto un tempo totale consentito per il completamento dell'aggiornamento di un cluster. Questo tempo viene calcolato prendendo il prodotto di `10 minutes * total number of nodes in the cluster` . Ad esempio, in un cluster a 20 nodi, le operazioni di aggiornamento devono avere esito positivo in 200 minuti oppure AKS avrà esito negativo per evitare uno stato del cluster irreversibile. Per eseguire il ripristino in caso di errore di aggiornamento, ripetere l'operazione di aggiornamento dopo che è stato raggiunto il timeout.

Per verificare che l'aggiornamento sia stato completato correttamente, usare il comando [az aks show][az-aks-show]:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

L'output di esempio seguente mostra che il cluster ora esegue *1.13.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come aggiornare un cluster esistente del servizio Azure Kubernetes. Per altre informazioni sulla distribuzione e sulla gestione dei cluster del servizio Azure Kubernetes, vedere le relative esercitazioni.

> [!div class="nextstepaction"]
> [Esercitazioni sul servizio Azure Kubernetes][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
