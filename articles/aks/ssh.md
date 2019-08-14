---
title: Accesso SSH in nodi del cluster del servizio Azure Kubernetes
description: Di seguito viene spiegato come creare una connessione SSH con i nodi del cluster del servizio Azure Kubernetes per la risoluzione dei problemi e le attività di manutenzione.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/31/2019
ms.author: mlearned
ms.openlocfilehash: e0b7154e3c4d6a6f493aac93ffcbcc424a67c300
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932309"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Connessione con SSH ai nodi del cluster del servizio Azure Kubernetes per la risoluzione dei problemi e le attività di manutenzione

Durante l'intero ciclo di vita del cluster del servizio Azure Kubernetes, potrebbe essere necessario accedere a un nodo del servizio Azure Kubernetes. Questo accesso potrebbe servire per la manutenzione, la raccolta dei registri o altre operazioni di risoluzione dei problemi. È possibile accedere ai nodi AKS usando SSH, inclusi i nodi di Windows Server (attualmente in anteprima in AKS). È anche possibile [connettersi ai nodi di Windows Server tramite connessioni Remote Desktop Protocol (RDP)][aks-windows-rdp]. Per motivi di sicurezza, i nodi AKS non sono esposti a Internet. Per SSH ai nodi servizio Azure Kubernetes, si utilizza l'indirizzo IP privato.

Questo articolo mostra come creare una connessione SSH con un nodo servizio Azure Kubernetes utilizzando i loro indirizzi IP privati.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster AKS esistente. Se è necessario un cluster AKS, vedere la Guida introduttiva di AKS [usando l'interfaccia della][aks-quickstart-cli] riga di comando di Azure o [l'portale di Azure][aks-quickstart-portal].

Per impostazione predefinita, le chiavi SSH vengono ottenute o generate, quindi aggiunte ai nodi quando si crea un cluster AKS. Questo articolo illustra come specificare chiavi SSH diverse dalle chiavi SSH usate durante la creazione del cluster AKS. Questo articolo illustra anche come determinare l'indirizzo IP privato del nodo e connetterlo tramite SSH. Se non è necessario specificare una chiave SSH diversa, è possibile ignorare il passaggio per aggiungere la chiave pubblica SSH al nodo.

Questo articolo presuppone anche che sia presente una chiave SSH. È possibile creare una chiave SSH usando [MacOS o Linux][ssh-nix] o [Windows][ssh-windows]. Se si usa PuTTy gen per creare la coppia di chiavi, salvare la coppia di chiavi in un formato OpenSSH anziché il formato predefinito di chiave privata PuTTy (file con estensione PPK).

È necessaria anche l'interfaccia della riga di comando di Azure versione 2.0.64 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia][install-azure-cli]della riga di comando di Azure.

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Configurare cluster AKS basati su set di scalabilità di macchine virtuali per l'accesso SSH

Per configurare il set di scalabilità di macchine virtuali in base all'accesso SSH, trovare il nome del set di scalabilità di macchine virtuali del cluster e aggiungere la chiave pubblica SSH a tale set di scalabilità.

Usare il comando [AZ AKS Show][az-aks-show] per ottenere il nome del gruppo di risorse del cluster AKS, quindi il comando [AZ vmss list][az-vmss-list] per ottenere il nome del set di scalabilità.

```azurecli-interactive
$CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

Nell'esempio precedente viene assegnato il nome del gruppo di risorse cluster per *myAKSCluster* in *myResourceGroup* a *CLUSTER_RESOURCE_GROUP*. Nell'esempio viene quindi usato *CLUSTER_RESOURCE_GROUP* per elencare il nome del set di scalabilità e assegnarlo a *SCALE_SET_NAME*.  

> [!NOTE]
> Attualmente è possibile aggiungere chiavi SSH solo ai nodi Linux tramite l'interfaccia della riga di comando di Azure. Per connettersi a un nodo di Windows Server tramite SSH, usare le chiavi SSH fornite al momento della creazione del cluster AKS e ignorare il set successivo di comandi per aggiungere la chiave pubblica SSH. Sarà comunque necessario l'indirizzo IP del nodo di cui si desidera risolvere i problemi, come illustrato nel comando finale di questa sezione. In alternativa, è possibile [connettersi ai nodi di Windows Server tramite connessioni Remote Desktop Protocol (RDP)][aks-windows-rdp] invece di usare SSH.

Per aggiungere le chiavi SSH ai nodi in un set di scalabilità di macchine virtuali, usare i comandi [AZ vmss Extension set][az-vmss-extension-set] e [AZ vmss Update-instances][az-vmss-update-instances] .

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

Nell'esempio precedente vengono usate le variabili *CLUSTER_RESOURCE_GROUP* e *SCALE_SET_NAME* dei comandi precedenti. L'esempio precedente usa anche *~/.ssh/id_rsa.pub* come percorso della chiave pubblica SSH.

> [!NOTE]
> Per impostazione predefinita, il nome utente per i nodi servizio Azure Kubernetes è *azureuser*.

Dopo aver aggiunto la chiave pubblica SSH al set di scalabilità, è possibile connettersi tramite SSH a una macchina virtuale del nodo nel set di scalabilità usando il relativo indirizzo IP. Visualizzare gli indirizzi IP privati dei nodi del cluster AKS usando il [comando kubectl Get][kubectl-get].

```console
kubectl get nodes -o wide
```

L'output di esempio seguente Mostra gli indirizzi IP interni di tutti i nodi del cluster, incluso un nodo di Windows Server.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Registrare l'indirizzo IP interno del nodo di cui si desidera eseguire la risoluzione dei problemi.

Per accedere al nodo tramite SSH, seguire la procedura descritta in [creare la connessione SSH](#create-the-ssh-connection).

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Configurare cluster AKS basati su set di disponibilità delle macchine virtuali per l'accesso SSH

Per configurare il cluster AKS basato su set di disponibilità delle macchine virtuali per l'accesso SSH, trovare il nome del nodo Linux del cluster e aggiungere la chiave SSH pubblica al nodo.

Usare il comando [AZ AKS Show][az-aks-show] per ottenere il nome del gruppo di risorse del cluster AKS, quindi il comando [AZ VM list][az-vm-list] per elencare il nome della macchina virtuale del nodo Linux del cluster.

```azurecli-interactive
$CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Nell'esempio precedente viene assegnato il nome del gruppo di risorse cluster per *myAKSCluster* in *myResourceGroup* a *CLUSTER_RESOURCE_GROUP*. Nell'esempio viene quindi usato *CLUSTER_RESOURCE_GROUP* per elencare il nome della macchina virtuale. L'output di esempio mostra il nome della macchina virtuale: 

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Per aggiungere le chiavi SSH al nodo, usare il comando [AZ VM User Update][az-vm-user-update] .

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

L'esempio precedente usa la variabile *CLUSTER_RESOURCE_GROUP* e il nome della macchina virtuale node dei comandi precedenti. L'esempio precedente usa anche *~/.ssh/id_rsa.pub* come percorso della chiave pubblica SSH. È anche possibile usare il contenuto della chiave pubblica SSH invece di specificare un percorso.

> [!NOTE]
> Per impostazione predefinita, il nome utente per i nodi servizio Azure Kubernetes è *azureuser*.

Dopo aver aggiunto la chiave pubblica SSH alla macchina virtuale del nodo, è possibile connettersi tramite SSH a tale macchina virtuale usando il relativo indirizzo IP. Visualizzare l'indirizzo IP privato di un nodo del cluster AKS usando il comando [AZ VM list-IP-][az-vm-list-ip-addresses] addresses.

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Nell'esempio precedente viene usata la variabile *CLUSTER_RESOURCE_GROUP* impostata nei comandi precedenti. Il seguente output di esempio mostra gli indirizzi IP privati dei nodi servizio Azure Kubernetes:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Creare la connessione SSH

Per creare una connessione SSH a un nodo servizio Azure Kubernetes, si esegue un helper pod nel cluster servizio Azure Kubernetes. Questo helper pod fornisce l'accesso SSH nel cluster e quindi l'accesso a un nodo SSH aggiuntivo. Per creare e utilizzare questo helper pod, attenersi alla seguente procedura:

1. Eseguire un'immagine del contenitore `debian` e collegarvi una sessione terminal. Questo contenitore può essere usato per creare una sessione SSH con tutti i nodi del cluster del servizio Azure Container:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Se si usano i nodi di Windows Server (attualmente in anteprima in AKS), aggiungere un selettore di nodo al comando per pianificare il contenitore Debian in un nodo Linux:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. Quando la sessione terminal è connessa al contenitore, installare un client SSH usando `apt-get`:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Aprire una nuova finestra del terminale, non connessa al contenitore, elencare i Pod nel cluster AKS usando il comando [kubectl Get Pod][kubectl-get] . Il pod creato nel passaggio precedente inizia con il nome *aks-ssh*, come mostrato nell'esempio seguente:

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. In un passaggio precedente è stata aggiunta la chiave SSH pubblica al nodo AKS per cui si desidera risolvere i problemi. A questo punto, copiare la chiave SSH privata nel pod helper. Questa chiave privata viene usata per creare SSH nel nodo AKS.

    Fornire il proprio nome pod *aks-ssh* ottenuto nel passaggio precedente. Se necessario, modificare *~/.ssh/id_rsa* alla posizione della chiave SSH privata:

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. Tornare alla sessione terminal nel contenitore, aggiornare le autorizzazioni per la chiave SSH privata `id_rsa` copiata in modo che sia di sola lettura:

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

Se sono necessari dati aggiuntivi per la risoluzione dei problemi, è possibile [visualizzare i log kubelet][view-kubelet-logs] o [visualizzare i log del nodo master di Kubernetes][view-master-logs].

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
