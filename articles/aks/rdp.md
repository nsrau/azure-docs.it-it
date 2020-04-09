---
title: Nodi da RDP ad AKS di Windows Server
titleSuffix: Azure Kubernetes Service
description: Informazioni su come creare una connessione RDP con i nodi di Windows Server del servizio Azure Kubernetes (AKS) per le attività di risoluzione dei problemi e manutenzione.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 140d59894b38c7f07f16b0ac3cf99316c201d120
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886790"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Connettersi con RDP ai nodi di Windows Server del servizio Azure Kubernetes (AKS) per la manutenzione o la risoluzione dei problemiConnect with RDP to Azure Kubernetes Service (AKS) cluster Windows Server nodes for maintenance or troubleshooting

Per tutto il ciclo di vita del cluster di servizio Kubernetes di Azure, potrebbe essere necessario accedere a un nodo di Windows Server AKS. Questo accesso potrebbe servire per la manutenzione, la raccolta dei registri o altre operazioni di risoluzione dei problemi. È possibile accedere ai nodi AKS di Windows Server utilizzando RDP. In alternativa, se si desidera utilizzare SSH per accedere ai nodi AKS di Windows Server e si ha accesso alla stessa coppia di chiavi utilizzata durante la creazione del cluster, è possibile seguire i passaggi descritti in [SSH nei nodi del cluster di Azure Kubernetes Service (AKS).][ssh-steps] Per motivi di sicurezza, i nodi di servizio Azure Kubernetes non sono esposti in Internet.

Il supporto dei nodi di Windows Server è attualmente in anteprima in AKS.

In questo articolo viene illustrato come creare una connessione RDP con un nodo AKS utilizzando i relativi indirizzi IP privati.

## <a name="before-you-begin"></a>Prima di iniziare

In questo articolo si presuppone che si dispone di un cluster AKS esistente con un nodo di Windows Server.This article assumes that you have an existing AKS cluster with a Windows Server node. Se è necessario un cluster AKS, vedere l'articolo sulla [creazione di un cluster AKS con un contenitore di Windows usando l'interfaccia della riga di comando][aks-windows-cli]di Azure. Sono necessari il nome utente e la password di amministratore di Windows per il nodo di Windows Server che si desidera risolvere. È inoltre necessario un client RDP, ad esempio [Microsoft Remote Desktop][rdp-mac].

È inoltre necessaria l'interfaccia della riga di comando di Azure versione 2.0.61 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Distribuire una macchina virtuale nella stessa subnet del clusterDeploy a virtual machine to the same subnet as your cluster

I nodi di Windows Server del cluster AKS non dispongono di indirizzi IP accessibili esternamente. Per effettuare una connessione RDP, è possibile distribuire una macchina virtuale con un indirizzo IP accessibile pubblicamente nella stessa subnet dei nodi di Windows Server.To make an RDP connection, you can deploy a virtual machine with a publicly accessible IP address to the same subnet as your Windows Server nodes.

L'esempio seguente crea una macchina virtuale denominata myVM nel gruppo di risorse *myResourceGroup.The following* example creates a virtual machine named *myVM* in the myResourceGroup resource group.

Ottenere innanzitutto la subnet utilizzata dal pool di nodi di Windows Server.First, get the subnet used by your Windows Server node pool. Per ottenere l'ID subnet, è necessario il nome della subnet. Per ottenere il nome della subnet, è necessario il nome della rete virtuale. Ottenere il nome della rete virtuale eseguendo una query nel cluster per l'elenco delle reti. Per eseguire query sul cluster, è necessario il relativo nome. È possibile ottenere tutti questi eseguendo quanto segue in Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Dopo aver creato il SUBNET_ID, eseguire il comando seguente nella stessa finestra di Azure Cloud Shell per creare la macchina virtuale:Now that you have the SUBNET_ID, run the following command in the same Azure Cloud Shell window to create the VM:

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

Registrare l'indirizzo IP pubblico della macchina virtuale. Questo indirizzo verrà utilizzato in un passaggio successivo.

## <a name="allow-access-to-the-virtual-machine"></a>Consentire l'accesso alla macchina virtualeAllow access to the virtual machine

Le subnet del pool di nodi AKS sono protette con i gruppi di sicurezza di rete (gruppi di sicurezza di rete) per impostazione predefinita. Per ottenere l'accesso alla macchina virtuale, è necessario abilitare l'accesso nel gruppo di sicurezza di rete.

> [!NOTE]
> I NSG sono controllati dal servizio AKS. Qualsiasi modifica apportata al gruppo di sicurezza di rete verrà sovrascritta in qualsiasi momento dal piano di controllo.
>

Ottenere innanzitutto il gruppo di risorse e il nome nsg del gruppo di sicurezza del gruppo di sicurezza a cui aggiungere la regola:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Quindi, creare la regola del gruppo di sicurezza di gruppo:Then, create the NSG rule:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>Ottenere l'indirizzo del nodoGet the node address

Per gestire un cluster Kubernetes, usare [kubectl][kubectl], il client da riga di comando di Kubernetes. Se si usa Azure Cloud Shell, `kubectl` è già installato. Per installare `kubectl` in locale, usare il comando [az aks install-cli][az-aks-install-cli]:
    
```azurecli-interactive
az aks install-cli
```

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials][az-aks-get-credentials]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Elencare l'indirizzo IP interno dei nodi di Windows Server utilizzando il comando [kubectl get:][kubectl-get]

```console
kubectl get nodes -o wide
```

L'output di esempio seguente mostra gli indirizzi IP interni di tutti i nodi del cluster, inclusi i nodi di Windows Server.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Registrare l'indirizzo IP interno del nodo di Windows Server che si desidera risolvere. Questo indirizzo verrà utilizzato in un passaggio successivo.

## <a name="connect-to-the-virtual-machine-and-node"></a>Connettersi alla macchina virtuale e al nodo

Connettersi all'indirizzo IP pubblico della macchina virtuale creata in precedenza utilizzando un client RDP, ad esempio [Microsoft Remote Desktop][rdp-mac].

![Immagine della connessione alla macchina virtuale tramite un client RDP](media/rdp/vm-rdp.png)

Dopo aver eseguito la connessione alla macchina virtuale, connettersi *all'indirizzo IP interno* del nodo Windows Server che si desidera risolvere con un client RDP all'interno della macchina virtuale.

![Immagine della connessione al nodo Windows Server tramite un client RDP](media/rdp/node-rdp.png)

A questo punto si è connessi al nodo di Windows Server.

![Immagine della finestra cmd nel nodo Windows Server](media/rdp/node-session.png)

È ora possibile eseguire qualsiasi comando di risoluzione dei problemi nella finestra *cmd.* Poiché i nodi di Windows Server utilizzano Windows Server Core, non esiste una GUI completa o altri strumenti GUI quando ci si connette a un nodo Windows Server tramite RDP.

## <a name="remove-rdp-access"></a>Rimuovere l'accesso RDP

Al termine, chiudere la connessione RDP al nodo Windows Server, quindi chiudere la sessione RDP alla macchina virtuale. Dopo aver chiuso entrambe le sessioni RDP, eliminare la macchina virtuale con il comando [az vm delete:][az-vm-delete]

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

E la regola NSG:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>Passaggi successivi

Se sono necessari dati aggiuntivi per la risoluzione dei problemi, è possibile visualizzare i log dei nodi [master di Kubernetes][view-master-logs] o [Monitoraggio di Azure.][azure-monitor-containers]

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
