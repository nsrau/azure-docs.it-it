---
title: RDP nei nodi di Windows Server cluster Azure Kubernetes Service (AKS)
description: Informazioni su come creare una connessione RDP con cluster Azure Kubernetes Service (AKS) nodi del Server di Windows per le attività di manutenzione e risoluzione dei problemi.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/04/2019
ms.author: mlearned
ms.openlocfilehash: 0238278b81255d735f8a950ca307d0e05100cfec
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614570"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Connettersi con RDP per Azure Kubernetes Service (AKS) nodi del cluster Windows Server per la manutenzione o risoluzione dei problemi

Nel ciclo di vita del cluster Azure Kubernetes Service (AKS), potrebbe essere necessario accedere a un nodo AKS Windows Server. Questo accesso potrebbe servire per la manutenzione, la raccolta dei registri o altre operazioni di risoluzione dei problemi. È possibile accedere ai nodi AKS Windows Server tramite RDP. In alternativa, se si vuole usare SSH per accedere ai nodi AKS Windows Server ed è possibile utilizzare la stessa coppia di chiavi che è stato usato durante la creazione del cluster, è possibile seguire i passaggi descritti in [SSH in nodi del cluster Azure Kubernetes Service (AKS)][ssh-steps]. Per motivi di sicurezza, i nodi di servizio Azure Kubernetes non sono esposti in Internet.

Supporto del nodo Windows Server è attualmente in anteprima nel servizio contenitore di AZURE.

Questo articolo illustra come creare una connessione RDP a un nodo AKS usando gli indirizzi IP privati.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si dispone di un cluster servizio contenitore di AZURE esistente con un nodo Windows Server. Se è necessario un cluster del servizio contenitore di AZURE, vedere l'articolo sul [creazione di un cluster AKS con un contenitore Windows usando il comando di Azure][aks-windows-cli]. You need the Windows administrator username and password for the Windows Server node you want to troubleshoot. You also need an RDP client such as [Microsoft Remote Desktop][rdp-mac].

Anche necessario la CLI di Azure versione 2.0.61 o versione successiva installato e configurato. Eseguire  `az --version` per trovare la versione. Se è necessario installare o eseguire l'aggiornamento, vedere [installare CLI Azure][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Distribuire una macchina virtuale alla stessa subnet del cluster

I nodi del Server di Windows del cluster servizio contenitore di AZURE non sono accessibile dall'esterno degli indirizzi IP. Per stabilire una connessione RDP, è possibile distribuire una macchina virtuale con un indirizzo IP accessibile pubblicamente alla stessa subnet come nodi di Windows Server.

L'esempio seguente crea una macchina virtuale denominata *myVM* nel *myResourceGroup* gruppo di risorse.

Innanzitutto, ottenere la subnet usata dal pool di nodi di Windows Server. Per ottenere l'id di subnet, è necessario il nome della subnet. Per ottenere il nome della subnet, è necessario il nome della rete virtuale. Ottenere il nome di rete virtuale eseguendo una query del cluster per l'elenco delle reti. Per eseguire una query del cluster, è necessario il relativo nome. È possibile ottenere tutti questi eseguendo le operazioni seguenti in Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Ora che avete il SUBNET_ID, eseguire il comando seguente nella stessa finestra Azure Cloud Shell per creare la macchina virtuale:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

L'output di esempio seguente mostra la macchina virtuale è stata creata correttamente e visualizza l'indirizzo IP pubblico della macchina virtuale.

```console
13.62.204.18
```

Registrare l'indirizzo IP pubblico della macchina virtuale. Si userà questo indirizzo in un passaggio successivo.

## <a name="get-the-node-address"></a>Ottenere l'indirizzo del nodo

Per gestire un cluster Kubernetes, usare [kubectl][kubectl], il client da riga di comando di Kubernetes. Se si usa Azure Cloud Shell, `kubectl` è già installato. Per installare `kubectl` in locale, usare il comando [az aks install-cli][az-aks-install-cli]:
    
```azurecli-interactive
az aks install-cli
```

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials][az-aks-get-credentials]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Elencare l'indirizzo IP interno dei nodi Windows Server usando il [kubectl get][kubectl-get] comando:

```console
kubectl get nodes -o wide
```

L'output di esempio seguente mostra gli indirizzi IP interni di tutti i nodi nel cluster, inclusi i nodi di Windows Server.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Registrare l'indirizzo IP interno del nodo Windows Server che si desidera risolvere i problemi. Si userà questo indirizzo in un passaggio successivo.

## <a name="connect-to-the-virtual-machine-and-node"></a>Connettersi alla macchina virtuale e nodo

Connettersi all'indirizzo IP pubblico della macchina virtuale creata in precedenza tramite, ad esempio un client RDP [Desktop remoto Microsoft][rdp-mac].

![Immagine di connessione alla macchina virtuale usando un client RDP](media/rdp/vm-rdp.png)

Dopo la connessione alla macchina virtuale, la connessione per il *indirizzo IP interno* del nodo Windows Server che si desidera risolvere i problemi con un client RDP all'interno della macchina virtuale.

![Immagine di connessione al nodo Windows Server usando un client RDP](media/rdp/node-rdp.png)

A questo punto si è connessi al nodo Windows Server.

![Immagine della finestra di comando nel nodo Windows Server](media/rdp/node-session.png)

È ora possibile eseguire i comandi sulla risoluzione dei problemi *cmd* finestra. Poiché i nodi di Windows Server usano Windows Server Core, non c'è un'interfaccia utente grafica completa o altri strumenti di interfaccia utente grafica quando ci si connette a un nodo Windows Server tramite RDP.

## <a name="remove-rdp-access"></a>Rimuovere l'accesso RDP

Al termine, chiudere la connessione RDP per il nodo Windows Server, quindi uscire dalla sessione RDP alla macchina virtuale. Dopo avere chiuso entrambe le sessioni RDP, eliminare la macchina virtuale con il [az vm delete][az-vm-delete] comando:

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Passaggi successivi

Se sono necessari dati di risoluzione dei problemi aggiuntivi, è possibile [visualizzare i log del nodo master Kubernetes][view-master-logs] or [Azure Monitor][azure-monitor-containers].

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md
