---
title: RDP nei nodi di Windows Server del cluster di Azure Kubernetes Service (AKS)
description: Informazioni su come creare una connessione RDP con i nodi di Windows Server del cluster di Azure Kubernetes Service (AKS) per la risoluzione dei problemi e le attività di manutenzione.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/04/2019
ms.author: mlearned
ms.openlocfilehash: 0238278b81255d735f8a950ca307d0e05100cfec
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67614570"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Connettersi con RDP ai nodi di Windows Server del cluster di Azure Kubernetes Service (AKS) per la manutenzione o la risoluzione dei problemi

Per tutto il ciclo di vita del cluster Azure Kubernetes Service (AKS), potrebbe essere necessario accedere a un nodo AKS Windows Server. Questo accesso potrebbe servire per la manutenzione, la raccolta dei registri o altre operazioni di risoluzione dei problemi. È possibile accedere ai nodi di Windows Server AKS tramite RDP. In alternativa, se si vuole usare SSH per accedere ai nodi di Windows Server AKS e si ha accesso alla stessa coppia di coppie di due volte che è stata usata durante la creazione del cluster, è possibile seguire la procedura in [SSH nei nodi del cluster di Azure Kubernetes Service (AKS)][ssh-steps]. Per motivi di sicurezza, i nodi di servizio Azure Kubernetes non sono esposti in Internet.

Il supporto per i nodi di Windows Server è attualmente disponibile in anteprima in AKS.

Questo articolo illustra come creare una connessione RDP con un nodo AKS usando i rispettivi indirizzi IP privati.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster AKS esistente con un nodo di Windows Server. Se è necessario un cluster AKS, vedere l'articolo sulla [creazione di un cluster del servizio contenitore di Azure con un contenitore Windows usando l'interfaccia della riga di comando di Azure][aks-windows-cli]. Sono necessari il nome utente e la password dell'amministratore di Windows per il nodo di Windows Server di cui si desidera risolvere i problemi. È necessario anche un client RDP, ad esempio [Desktop remoto Microsoft][rdp-mac].

È necessaria anche l'interfaccia della riga di comando di Azure versione 2.0.61 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia][install-azure-cli]della riga di comando di Azure.

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Distribuire una macchina virtuale nella stessa subnet del cluster

I nodi di Windows Server del cluster AKS non hanno indirizzi IP accessibili esternamente. Per eseguire una connessione RDP, è possibile distribuire una macchina virtuale con un indirizzo IP accessibile pubblicamente nella stessa subnet dei nodi di Windows Server.

L'esempio seguente crea una macchina virtuale denominata *myVM* nel gruppo di risorse *myResourceGroup* .

Per prima cosa, ottenere la subnet usata dal pool di nodi di Windows Server. Per ottenere l'ID subnet, è necessario il nome della subnet. Per ottenere il nome della subnet, è necessario il nome del vnet. Ottenere il nome VNET eseguendo una query sul cluster per l'elenco delle reti. Per eseguire una query sul cluster, è necessario il relativo nome. Per ottenere tutti questi elementi, è possibile eseguire il comando seguente nel Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Ora che si dispone di SUBNET_ID, eseguire il comando seguente nella stessa finestra di Azure Cloud Shell per creare la macchina virtuale:

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

L'output di esempio seguente mostra che la macchina virtuale è stata creata correttamente e visualizza l'indirizzo IP pubblico della macchina virtuale.

```console
13.62.204.18
```

Registrare l'indirizzo IP pubblico della macchina virtuale. Questo indirizzo verrà usato in un passaggio successivo.

## <a name="get-the-node-address"></a>Ottenere l'indirizzo del nodo

Per gestire un cluster Kubernetes, usare [kubectl][kubectl], il client da riga di comando di Kubernetes. Se si usa Azure Cloud Shell, `kubectl` è già installato. Per installare `kubectl` in locale, usare il comando [az aks install-cli][az-aks-install-cli]:
    
```azurecli-interactive
az aks install-cli
```

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials][az-aks-get-credentials]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Elencare l'indirizzo IP interno dei nodi di Windows Server usando il comando [kubectl Get][kubectl-get] :

```console
kubectl get nodes -o wide
```

L'output di esempio seguente Mostra gli indirizzi IP interni di tutti i nodi del cluster, inclusi i nodi di Windows Server.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Registrare l'indirizzo IP interno del nodo di Windows Server di cui si desidera eseguire la risoluzione dei problemi. Questo indirizzo verrà usato in un passaggio successivo.

## <a name="connect-to-the-virtual-machine-and-node"></a>Connettersi alla macchina virtuale e al nodo

Connettersi all'indirizzo IP pubblico della macchina virtuale creata in precedenza usando un client RDP, ad esempio [Desktop remoto Microsoft][rdp-mac].

![Immagine della connessione alla macchina virtuale tramite un client RDP](media/rdp/vm-rdp.png)

Dopo aver eseguito la connessione alla macchina virtuale, connettersi all' *indirizzo IP interno* del nodo di Windows Server per cui si desidera risolvere i problemi utilizzando un client RDP dall'interno della macchina virtuale.

![Immagine della connessione al nodo Windows Server tramite un client RDP](media/rdp/node-rdp.png)

A questo punto si è connessi al nodo Windows Server.

![Immagine della finestra cmd nel nodo Windows Server](media/rdp/node-session.png)

È ora possibile eseguire tutti i comandi di risoluzione dei problemi nella finestra *cmd* . Poiché i nodi di Windows Server usano Windows Server Core, non è disponibile un'interfaccia utente grafica completa o altri strumenti GUI quando ci si connette a un nodo di Windows Server tramite RDP.

## <a name="remove-rdp-access"></a>Rimuovere l'accesso RDP

Al termine, chiudere la connessione RDP al nodo Windows Server, quindi uscire dalla sessione RDP alla macchina virtuale. Dopo aver terminato entrambe le sessioni RDP, eliminare la macchina virtuale con il comando [AZ VM Delete][az-vm-delete] :

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Passaggi successivi

Se sono necessari dati aggiuntivi per la risoluzione dei problemi, è possibile [visualizzare i log del nodo master Kubernetes][view-master-logs] o [monitoraggio di Azure][azure-monitor-containers].

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
