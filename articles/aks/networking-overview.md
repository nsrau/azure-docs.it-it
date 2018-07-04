---
title: Configurazione della rete in Azure Kubernetes Service (AKS)
description: Informazioni sulla configurazione di base e avanzata della rete in Azure Kubernetes Service (AKS).
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/15/2018
ms.author: marsma
ms.openlocfilehash: 207accc30e10c4e2bed5b713fc59e2f9ad86a876
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36311095"
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Configurazione della rete in Azure Kubernetes Service (AKS)

Quando si crea un cluster Azure Kubernetes Service (AKS), è possibile scegliere tra due opzioni di rete: **di base** o **avanzata**.

## <a name="basic-networking"></a>Rete di base

L'opzione di rete **di base** è la configurazione predefinita per la creazione del cluster AKS. La configurazione di rete del cluster e dei pod viene gestita completamente da Azure ed è appropriata per le distribuzioni che non richiedono una configurazione personalizzata della rete virtuale. Se si sceglie la rete di base, non si ha il controllo completo sulla configurazione della rete, ad esempio sulle subnet o sugli intervalli di indirizzi IP assegnati al cluster.

I nodi in un cluster AKS configurato per la rete di base usano il plug-in Kubernetes [kubenet][kubenet].

## <a name="advanced-networking"></a>Rete avanzata

La rete **avanzata** inserisce i pod in una rete virtuale di Azure configurata, fornendo la connettività automatica alle risorse della rete virtuale e l'integrazione con il set completo di funzionalità offerto dalle reti virtuali.
La rete avanzata è disponibile quando si distribuiscono i cluster AKS con il [portale di Azure][portal], l'interfaccia della riga di comando di Azure o con un modello di Resource Manager.

I nodi in un cluster AKS configurato per la rete avanzata usano il plug-in Kubernetes [Azure Container Networking Interface (CNI)][cni-networking].

![Diagramma che illustra due nodi ognuno dei quali è connesso da bridge a una singola rete virtuale di Azure][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>Funzionalità della rete avanzata

La rete avanzata offre i vantaggi seguenti:

* Distribuire il cluster AKS in una rete virtuale esistente o creare una nuova rete virtuale e una subnet per il cluster.
* Ogni pod del cluster ottiene un indirizzo IP nella rete virtuale e può comunicare direttamente con altri pod nel cluster e con altri nodi della rete virtuale.
* Un pod può connettersi ad altri servizi in una rete virtuale con peering e a reti locali tramite ExpressRoute e connessioni VPN da sito a sito. I pod sono raggiungibili anche da locale.
* Esporre un servizio Kubernetes esternamente o internamente tramite Azure Load Balancer. Questa è anche una funzionalità della rete di base.
* I pod in una subnet con endpoint di servizio abilitati possono connettersi in modo sicuro ai servizi di Azure, ad esempio Archiviazione di Azure e il database SQL.
* Usare route definite dall'utente per instradare il traffico dai pod a un'appliance virtuale di rete.
* I pod possono accedere alle risorse su Internet pubblico. Questa è anche una funzionalità della rete di base.

> [!IMPORTANT]
> Ogni nodo in un cluster AKS configurato per la rete avanzata può ospitare un massimo di **30 pod** se la configurazione è stata eseguita con il portale di Azure.  È possibile cambiare il valore massimo solo modificando la proprietà maxPods quando si distribuisce un cluster con un modello di Resource Manager. Ogni rete virtuale di cui viene effettuato il provisioning per l'uso con il plug-in Azure CNI è limitata a **4096 indirizzi IP** configurati.

## <a name="advanced-networking-prerequisites"></a>Prerequisiti per reti avanzate

* La rete virtuale per il cluster AKS deve consentire la connettività Internet in uscita.
* Non creare più di un cluster AKS nella stessa subnet.
* Le reti avanzate per AKS non supportano le reti virtuali che usano zone DNS private di Azure.
* I cluster AKS non possono usare `169.254.0.0/16`, `172.30.0.0/16` o `172.31.0.0/16` per l'intervallo di indirizzi del servizio Kubernetes.
* L'entità di servizio utilizzata per il cluster AKS deve avere autorizzazioni `Contributor` per il gruppo di risorse contenente la rete virtuale esistente.

## <a name="plan-ip-addressing-for-your-cluster"></a>Pianificare l'indirizzamento IP per il cluster

I cluster configurati con la rete avanzata richiedono una pianificazione aggiuntiva. Le dimensioni della rete virtuale e della subnet devono essere sufficienti sia per il numero di pod che si prevede di eseguire che per il numero di nodi per il cluster.

Gli indirizzi IP per i pod e i nodi del cluster vengono assegnati dalla subnet specificata all'interno della rete virtuale. Ogni nodo è configurato con un IP primario, ovvero l'IP del nodo stesso, e 30 indirizzi IP aggiuntivi preconfigurati da Azure CNI che vengono assegnati ai pod pianificati per il nodo. Quando si aumenta il numero di istanze del cluster, ogni nodo viene configurato in modo simile con gli indirizzi IP della subnet.

Il piano di indirizzo IP per un cluster AKS è costituito da una rete virtuale, almeno una subnet per i nodi e i pod e un intervallo di indirizzi per il servizio Kubernetes.

| Intervallo di indirizzi / Risorsa di Azure | Limiti e dimensioni |
| --------- | ------------- |
| Rete virtuale | La rete virtuale di Azure può arrivare alle dimensioni /8, ma può avere solo 4096 indirizzi IP configurati. |
| Subnet | Deve essere sufficientemente grande da contenere i nodi e i pod. Per calcolare le dimensioni minime della subnet: (numero di nodi) + (numero di nodi * pod per ogni nodo). Per un cluster da 50 nodi: (50) + (50 * 30) = 1.550, la subnet dovrà essere /21 o più grande. |
| Intervallo di indirizzi del servizio Kubernetes | Questo intervallo non deve essere usato da nessun elemento della rete che si trova su questa rete virtuale o è connesso a essa. Il CIDR dell'indirizzo del servizio deve essere più piccolo di /12. |
| Indirizzo IP del servizio DNS Kubernetes | Indirizzo IP compreso nell'intervallo di indirizzi del servizio Kubernetes che verrà usato dall'individuazione del servizio cluster (kube-dns). |
| Indirizzo del bridge Docker | Indirizzo IP (in notazione CIDR) utilizzato come indirizzo IP del bridge Docker nei nodi. Il valore predefinito è 172.17.0.1/16. |

Come accennato in precedenza, ogni rete virtuale di cui viene effettuato il provisioning per l'uso con il plug-in Azure CNI è limitata a **4096 indirizzi IP** configurati. Ogni nodo in un cluster configurato per la rete avanzata può ospitare un massimo di **30 pod**.

## <a name="deployment-parameters"></a>Parametri di distribuzione

Quando si crea un cluster AKS, i parametri seguenti sono configurabili per la rete avanzata:

**Rete virtuale**: rete virtuale in cui si vuole distribuire il cluster Kubernetes. Per creare una nuova rete virtuale per il cluster, selezionare *Crea nuova* e seguire i passaggi della sezione *Creare una rete virtuale*.

**Subnet**: subnet nella rete virtuale in cui si vuole distribuire il cluster. Per creare una nuova subnet nella rete virtuale per il cluster, selezionare *Crea nuova* e seguire i passaggi della sezione *Creare una subnet*.

**Intervallo di indirizzi del servizio Kubernetes**: l'*intervallo di indirizzi del servizio Kubernetes* è l'intervallo IP da cui gli indirizzi vengono assegnati ai servizi Kubernetes nel cluster. Per altre informazioni sui servizi Kubernetes, vedere [Services][services] (Servizi) nella documentazione di Kubernetes.

L'intervallo di indirizzi IP del servizio Kubernetes:

* Non deve essere compreso nell'intervallo di indirizzi IP della rete virtuale del cluster
* Non deve sovrapporsi ad altre reti virtuali con cui la rete virtuale del cluster esegue il peering
* Non deve sovrapporsi ad altri IP locali

Se vengono usati intervalli IP che si sovrappongono, si può verificare un comportamento imprevedibile. Se ad esempio un pod prova ad accedere a un IP esterno al cluster e tale IP è anche un IP di servizio, potrebbero verificarsi comportamenti ed errori imprevedibili.

**Kubernetes DNS service IP address** (Indirizzo IP del servizio DNS Kubernetes): indirizzo IP per il servizio DNS del cluster. Questo indirizzo deve essere compreso nell'*intervallo di indirizzi del servizio Kubernetes*.

**Docker Bridge address** (Indirizzo del bridge Docker): indirizzo IP e netmask da assegnare al bridge Docker. Questo indirizzo IP non deve essere compreso nell'intervallo di indirizzi IP della rete virtuale del cluster.

## <a name="configure-networking---cli"></a>Configurare le impostazioni di rete - interfaccia della riga di comando

Quando si crea un cluster AKS con l'interfaccia della riga di comando di Azure, è anche possibile configurare funzionalità di rete avanzate. Usare i comandi seguenti per creare un nuovo cluster AKS con avanzate funzionalità di rete abilitate.

Ottenere prima di tutto l'ID risorsa della subnet esistente in cui verrà aggiunto il cluster AKS:

```console
$ az network vnet subnet list --resource-group myVnet --vnet-name myVnet --query [].id --output tsv

/subscriptions/d5b9d4b7-6fc1-46c5-bafe-38effaed19b2/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Usare il comando [az aks create][az-aks-create] con l'argomento `--network-plugin azure` per creare un cluster con funzionalità di rete avanzate. Aggiornare il valore `--vnet-subnet-id` con l'ID della subnet raccolto nel passaggio precedente:

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --network-plugin azure --vnet-subnet-id <subnet-id> --docker-bridge-address 172.17.0.1/16 --dns-service-ip 10.2.0.10 --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>Configurare le impostazioni di rete - portale

Lo screenshot seguente dal portale di Azure illustra un esempio di configurazione di queste impostazioni durante la creazione del cluster AKS:

![Configurazione della rete avanzata nel portale di Azure][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Domande frequenti

Le domande e le risposte seguenti si applicano alla configurazione della rete**avanzata**.

* *È possibile configurare la rete avanzata con l'interfaccia della riga di comando di Azure?*

  No. La rete avanzata è attualmente disponibile solo quando si distribuiscono i cluster AKS nel portale di Azure o con un modello di Resource Manager.

* *È possibile distribuire le VM nella subnet del cluster?*

  No. La distribuzione di macchine virtuali nella subnet usata per il cluster Kubernetes non è supportata. Le macchine virtuali possono essere distribuite nella stessa rete virtuale, ma in una subnet diversa.

* *È possibile configurare criteri di rete per pod?*

  No. I criteri di rete per pod non sono attualmente supportati.

* *Il numero massimo di pod distribuibili in un nodo è configurabile?*

  Per impostazione predefinita, ogni nodo può ospitare un massimo di 30 pod. È possibile cambiare il valore massimo solo modificando la proprietà `maxPods` quando si distribuisce un cluster con un modello di Gestione risorse.

* *Come si configurano altre proprietà per la subnet creata durante la creazione del cluster AKS? Ad esempio, gli endpoint di servizio.*

  L'elenco completo delle proprietà per la rete virtuale e le subnet create durante la creazione del cluster AKS può essere configurato nella pagina di configurazione della rete virtuale standard nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

### <a name="networking-in-aks"></a>Rete in AKS

Per altre informazioni sulla rete in AKS, vedere gli articoli seguenti:

[Usare un indirizzo IP statico con il bilanciamento del carico di Azure Kubernetes Service (AKS)](static-ip.md)

[Ingresso HTTPS nel servizio contenitore di Azure (AKS)](ingress.md)

[Usare un servizio di bilanciamento del carico interno con il servizio contenitore di Azure (AKS)](internal-lb.md)

### <a name="acs-engine"></a>ACS Engine

[Azure Container Service Engine (ACS Engine)][acs-engine] è un progetto open source che genera modelli di Azure Resource Manager che è possibile usare per la distribuzione di cluster abilitati per Docker in Azure. Con ACS Engine si possono distribuire agenti di orchestrazione Kubernetes, DC/OS, in modalità Swarm e Swarm.

I cluster Kubernetes creati con ACS Engine supportano entrambi i plug-in [kubenet][kubenet] e [Azure CNI][cni-networking]. Di conseguenza, sono supportati da ACS Engine scenari di rete sia di base che avanzata.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: aks-ssh.md
