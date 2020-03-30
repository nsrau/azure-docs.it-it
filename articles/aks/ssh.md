---
title: Accesso SSH in nodi del cluster del servizio Azure Kubernetes
description: Di seguito viene spiegato come creare una connessione SSH con i nodi del cluster del servizio Azure Kubernetes per la risoluzione dei problemi e le attività di manutenzione.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: dfdcda40a24142f85bbeb360aacf0971d72d181f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593632"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Connessione con SSH ai nodi del cluster del servizio Azure Kubernetes per la risoluzione dei problemi e le attività di manutenzione

Durante l'intero ciclo di vita del cluster del servizio Azure Kubernetes, potrebbe essere necessario accedere a un nodo del servizio Azure Kubernetes. Questo accesso potrebbe servire per la manutenzione, la raccolta dei registri o altre operazioni di risoluzione dei problemi. È possibile accedere ai nodi AKS utilizzando SSH, inclusi i nodi di Windows Server (attualmente in anteprima in AKS). È inoltre possibile [connettersi ai nodi di Windows Server utilizzando connessioni RDP (Remote Desktop Protocol).][aks-windows-rdp] Per motivi di sicurezza, i nodi AKS non sono esposti a Internet. Per SSH ai nodi servizio Azure Kubernetes, si utilizza l'indirizzo IP privato.

Questo articolo mostra come creare una connessione SSH con un nodo servizio Azure Kubernetes utilizzando i loro indirizzi IP privati.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster servizio Azure Kubernetes, vedere la Guida introduttiva su servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

Per impostazione predefinita, le chiavi SSH vengono ottenute o generate, quindi aggiunte ai nodi quando si crea un cluster AKS. In questo articolo viene illustrato come specificare chiavi SSH diverse rispetto alle chiavi SSH utilizzate al momento della creazione del cluster AKS. L'articolo illustra inoltre come determinare l'indirizzo IP privato del nodo e connettersi ad esso utilizzando SSH. Se non è necessario specificare una chiave SSH diversa, è possibile ignorare il passaggio per l'aggiunta della chiave pubblica SSH al nodo.

In questo articolo si presuppone inoltre che si dispone di un tasto SSH. È possibile creare una chiave SSH utilizzando [macOS o Linux][ssh-nix] o [Windows][ssh-windows]. Se si utilizza PuTTY Gen per creare la coppia di chiavi, salvare la coppia di chiavi in un formato OpenSSH anziché il formato di chiave privata PuTTy predefinito (file con estensione ppk).

È inoltre necessaria l'interfaccia della riga di comando di Azure versione 2.0.64 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Configurare i cluster AKS basati su set di scalabilità macchina per l'accesso SSHConfigure virtual machine scale set-based AKS clusters for SSH access

Per configurare il set di scalabilità delle macchine virtuali per l'accesso SSH, trovare il nome del set di scalabilità di macchine virtuali del cluster e aggiungere la chiave pubblica SSH a tale set di scalabilità.

Usare il comando [az aks show][az-aks-show] per ottenere il nome del gruppo di risorse del cluster AKS, quindi il comando [az vmss list][az-vmss-list] per ottenere il nome del set di scalabilità.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

Nell'esempio precedente viene assegnato il nome del gruppo di risorse cluster per *myAKSCluster* in *myResourceGroup* a *CLUSTER_RESOURCE_GROUP*. Nell'esempio *viene* quindi utilizzato CLUSTER_RESOURCE_GROUP per elencare il nome del set di scala e assegnarlo a *SCALE_SET_NAME*.

> [!IMPORTANT]
> A questo punto, è necessario aggiornare solo le chiavi SSH per i cluster AKS basati su set di scalabilità di macchine virtuali usando l'interfaccia della riga di comando di Azure.At this time, you should only update your SSH keys for your virtual machine scale set-based AKS clusters using the Azure CLI.
> 
> Per i nodi Linux, le chiavi SSH attualmente possono essere aggiunte solo usando l'interfaccia della riga di comando di Azure.For Linux nodes, SSH keys currently can only be added using the Azure CLI. Se si desidera connettersi a un nodo di Windows Server utilizzando SSH, utilizzare i tasti SSH forniti quando è stato creato il cluster AKS e ignorare il set successivo di comandi per l'aggiunta della chiave pubblica SSH. Sarà comunque necessario l'indirizzo IP del nodo che si desidera risolvere, come illustrato nel comando finale di questa sezione. In alternativa, è possibile [connettersi ai nodi di Windows Server utilizzando connessioni RDP (Remote Desktop Protocol)][aks-windows-rdp] anziché SSH.

Per aggiungere le chiavi SSH ai nodi in un set di scalabilità di macchine virtuali, usare il set di [estensioni az vmss][az-vmss-extension-set] e i comandi [az vmss update-instances.][az-vmss-update-instances]

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

Nell'esempio precedente vengono utilizzate le variabili *CLUSTER_RESOURCE_GROUP* e *SCALE_SET_NAME* dei comandi precedenti. Nell'esempio precedente *id_rsa* viene utilizzato anche il percorso della chiave pubblica SSH.

> [!NOTE]
> Per impostazione predefinita, il nome utente per i nodi servizio Azure Kubernetes è *azureuser*.

Dopo aver aggiunto la chiave pubblica SSH al set di scalabilità, è possibile SSH in una macchina virtuale del nodo in tale set di scalabilità usando il relativo indirizzo IP. Visualizzare gli indirizzi IP privati dei nodi del cluster AKS utilizzando il [comando kubectl get][kubectl-get].

```console
kubectl get nodes -o wide
```

L'output di esempio seguente mostra gli indirizzi IP interni di tutti i nodi del cluster, incluso un nodo Windows Server.The follow example output shows the internal IP addresses of all the nodes in the cluster, including a Windows Server node.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Registrare l'indirizzo IP interno del nodo che si desidera risolvere.

Per accedere al nodo utilizzando SSH, attenersi alla procedura descritta in [Creare la connessione SSH](#create-the-ssh-connection).

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Configurare i cluster AKS basati su set di disponibilità della macchina virtuale per l'accesso SSHConfigure virtual machine availability set-based AKS clusters for SSH access

Per configurare il cluster AKS basato sul set di disponibilità della macchina virtuale per l'accesso SSH, trovare il nome del nodo Linux del cluster e aggiungere la chiave pubblica SSH a tale nodo.

Usare il comando [az aks show][az-aks-show] per ottenere il nome del gruppo di risorse del cluster AKS, quindi il comando [az vm list][az-vm-list] per elencare il nome della macchina virtuale del nodo Linux del cluster.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Nell'esempio precedente viene assegnato il nome del gruppo di risorse cluster per *myAKSCluster* in *myResourceGroup* a *CLUSTER_RESOURCE_GROUP*. Nell'esempio *viene* quindi utilizzato CLUSTER_RESOURCE_GROUP per elencare il nome della macchina virtuale. L'output di esempio mostra il nome della macchina virtuale:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Per aggiungere le chiavi SSH al nodo, utilizzare il comando [az vm user update][az-vm-user-update].

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Nell'esempio precedente vengono utilizzati la variabile *CLUSTER_RESOURCE_GROUP* e il nome della macchina virtuale del nodo dai comandi precedenti. Nell'esempio precedente *id_rsa* viene utilizzato anche il percorso della chiave pubblica SSH. È anche possibile usare il contenuto della chiave pubblica SSH anziché specificare un percorso.

> [!NOTE]
> Per impostazione predefinita, il nome utente per i nodi servizio Azure Kubernetes è *azureuser*.

Dopo aver aggiunto la chiave pubblica SSH alla macchina virtuale del nodo, è possibile SSH in tale macchina virtuale usando il relativo indirizzo IP. Visualizzare l'indirizzo IP privato di un nodo del cluster servizio Azure Kubernetes utilizzando il comando [az vm list-ip-addresses][az-vm-list-ip-addresses].

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Nell'esempio precedente viene utilizzata la variabile *CLUSTER_RESOURCE_GROUP* impostata nei comandi precedenti. Il seguente output di esempio mostra gli indirizzi IP privati dei nodi servizio Azure Kubernetes:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Creare la connessione SSH

Per creare una connessione SSH a un nodo servizio Azure Kubernetes, si esegue un helper pod nel cluster servizio Azure Kubernetes. Questo helper pod fornisce l'accesso SSH nel cluster e quindi l'accesso a un nodo SSH aggiuntivo. Per creare e utilizzare questo helper pod, attenersi alla seguente procedura:

1. Eseguire un'immagine del contenitore `debian` e collegarvi una sessione terminal. Questo contenitore può essere usato per creare una sessione SSH con tutti i nodi del cluster del servizio Azure Container:

    ```console
    kubectl run --generator=run-pod/v1 -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Se si utilizzano nodi di Windows Server (attualmente in anteprima in AKS), aggiungere un selettore di nodo al comando per pianificare il contenitore Debian in un nodo Linux:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. Una volta che la sessione terminale è collegata al contenitore, installare un client SSH utilizzando: `apt-get`

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Aprite una nuova finestra del terminale, non collegate al contenitore, copiate la chiave SSH privata nel contenitore helper. Questa chiave privata viene utilizzata per creare il sSH nel nodo AKS. 

   Se necessario, modificare *~/.ssh/id_rsa* alla posizione della chiave SSH privata:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Tornare alla sessione del terminale al contenitore, aggiornare le autorizzazioni per la chiave SSH privata copiata `id_rsa` in modo che sia di sola lettura dell'utente:

    ```console
    chmod 0600 id_rsa
    ```

1. Creare una connessione SSH al nodo AKS. Anche qui il nome utente predefinito per i nodi servizio Azure Kubernetes è *azureuser*. Accettare la richiesta di continuare con la connessione quando viene verificata l’attendibilità della chiave SSH. Viene quindi fornita la richiesta bash del nodo servizio Azure Kubernetes:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4

    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.

    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    [...]

    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>Rimuovere l'accesso SSH

Al termine, `exit` la sessione SSH e quindi `exit` la sessione interattiva del contenitore. Quando questa sessione del contenitore si chiude, il pod usato per l'accesso SSH dal cluster servizio Azure Kubernetes viene eliminato.

## <a name="next-steps"></a>Passaggi successivi

Se sono necessari ulteriori dati per la risoluzione dei problemi, è possibile [visualizzare i log kubelet][view-kubelet-logs] o [visualizzare i log dei nodi master Kubernetes][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
