---
title: Accesso SSH in nodi del cluster del servizio Azure Kubernetes
description: Di seguito viene spiegato come creare una connessione SSH con i nodi del cluster del servizio Azure Kubernetes per la risoluzione dei problemi e le attività di manutenzione.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 6ddd1b160110e7a751f54f89b387a62d94e9308e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "67614489"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Connessione con SSH ai nodi del cluster del servizio Azure Kubernetes per la risoluzione dei problemi e le attività di manutenzione

Durante l'intero ciclo di vita del cluster del servizio Azure Kubernetes, potrebbe essere necessario accedere a un nodo del servizio Azure Kubernetes. Questo accesso potrebbe servire per la manutenzione, la raccolta dei registri o altre operazioni di risoluzione dei problemi. È possibile accedere ai nodi AKS usando SSH, inclusi i nodi di Windows Server (attualmente in anteprima in AKS). È anche possibile [connettersi ai nodi di Windows Server tramite connessioni Remote Desktop Protocol (RDP)][aks-windows-rdp]. Per motivi di sicurezza, i nodi di servizio Azure Kubernetes non sono esposti in Internet.

Questo articolo mostra come creare una connessione SSH con un nodo servizio Azure Kubernetes utilizzando i loro indirizzi IP privati.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster AKS esistente. Se è necessario un cluster AKS, vedere la Guida introduttiva di AKS [usando l'interfaccia della][aks-quickstart-cli] riga di comando di Azure o [l'portale di Azure][aks-quickstart-portal].

È necessaria anche l'interfaccia della riga di comando di Azure versione 2.0.64 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia][install-azure-cli]della riga di comando di Azure.

## <a name="add-your-public-ssh-key"></a>Aggiungere la chiave SSH pubblica

Per impostazione predefinita, le chiavi SSH vengono ottenute o generate, quindi aggiunte ai nodi quando si crea un cluster AKS. Se è necessario specificare chiavi SSH diverse da quelle usate durante la creazione del cluster AKS, aggiungere la chiave SSH pubblica ai nodi di Linux AKS. Se necessario, è possibile creare una chiave SSH usando [MacOS o Linux][ssh-nix] o [Windows][ssh-windows]. Se si usa PuTTy gen per creare la coppia di chiavi, salvare la coppia di chiavi in un formato OpenSSH anziché il formato predefinito di chiave privata PuTTy (file con estensione PPK).

> [!NOTE]
> Attualmente è possibile aggiungere chiavi SSH solo ai nodi Linux tramite l'interfaccia della riga di comando di Azure. Se si usano i nodi di Windows Server, usare le chiavi SSH fornite al momento della creazione del cluster AKS e passare al passaggio su [come ottenere l'indirizzo del nodo AKS](#get-the-aks-node-address). In alternativa, [connettersi ai nodi di Windows Server tramite connessioni Remote Desktop Protocol (RDP)][aks-windows-rdp].

I passaggi per ottenere l'indirizzo IP privato dei nodi AKS variano in base al tipo di cluster AKS eseguito:

* Per la maggior parte dei cluster AKS, seguire la procedura per [ottenere l'indirizzo IP per i cluster AKS normali](#add-ssh-keys-to-regular-aks-clusters).
* Se si usano le funzionalità di anteprima in AKS che usano i set di scalabilità di macchine virtuali, ad esempio i pool di più nodi o il supporto dei contenitori di Windows Server, [seguire i passaggi per i cluster AKS basati sui set di scalabilità di macchine virtuali](#add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="add-ssh-keys-to-regular-aks-clusters"></a>Aggiungere chiavi SSH a cluster AKS normali

Per aggiungere la chiave SSH a un nodo di Linux AKS, seguire questa procedura:

1. Ottenere il nome del gruppo di risorse per le risorse del cluster AKS usando [AZ AKS Show][az-aks-show]. Il nome del cluster viene assegnato alla variabile denominata *CLUSTER_RESOURCE_GROUP*. Sostituire *myResourceGroup* con il nome del gruppo di risorse in cui si trova il cluster AKS:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Elencare le macchine virtuali nel gruppo di risorse del cluster AKS usando il comando [AZ VM list][az-vm-list] . Queste macchine virtuali sono i nodi servizio Azure Kubernetes:

    ```azurecli-interactive
    az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
    ```

    Il seguente output di esempio mostra i nodi servizio Azure Kubernetes:

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. Per aggiungere le chiavi SSH al nodo, usare il comando [AZ VM User Update][az-vm-user-update] . Fornire il nome del gruppo di risorse e quindi uno dei nodi servizio Azure Kubernetes ottenuti nel passaggio precedente. Per impostazione predefinita, il nome utente per i nodi servizio Azure Kubernetes è *azureuser*. Fornire la posizione della propria chiave pubblica SSH, ad esempio *~/.ssh/id_rsa.pub*, oppure incollare il contenuto della chiave pubblica SSH:

    ```azurecli-interactive
    az vm user update \
      --resource-group $CLUSTER_RESOURCE_GROUP \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

### <a name="add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters"></a>Aggiungere chiavi SSH a cluster AKS basati su set di scalabilità di macchine virtuali

Per aggiungere la chiave SSH a un nodo di Linux AKS che fa parte di un set di scalabilità di macchine virtuali, completare i passaggi seguenti:

1. Ottenere il nome del gruppo di risorse per le risorse del cluster AKS usando [AZ AKS Show][az-aks-show]. Il nome del cluster viene assegnato alla variabile denominata *CLUSTER_RESOURCE_GROUP*. Sostituire *myResourceGroup* con il nome del gruppo di risorse in cui si trova il cluster AKS:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Ottenere quindi il set di scalabilità di macchine virtuali per il cluster AKS usando il comando [AZ vmss list][az-vmss-list] . Il nome del set di scalabilità di macchine virtuali viene assegnato alla variabile denominata *SCALE_SET_NAME*:

    ```azurecli-interactive
    SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
    ```

1. Per aggiungere le chiavi SSH ai nodi in un set di scalabilità di macchine virtuali, usare il comando [AZ vmss Extension set][az-vmss-extension-set] . Il gruppo di risorse cluster e il nome del set di scalabilità di macchine virtuali sono forniti dai comandi precedenti. Per impostazione predefinita, il nome utente per i nodi servizio Azure Kubernetes è *azureuser*. Se necessario, aggiornare il percorso del percorso della chiave pubblica SSH, ad esempio *~/.ssh/id_rsa.pub*:

    ```azurecli-interactive
    az vmss extension set  \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --vmss-name $SCALE_SET_NAME \
        --name VMAccessForLinux \
        --publisher Microsoft.OSTCExtensions \
        --version 1.4 \
        --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"
    ```

1. Applicare la chiave SSH ai nodi usando il comando [AZ vmss Update-instances][az-vmss-update-instances] :

    ```azurecli-interactive
    az vmss update-instances --instance-ids '*' \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --name $SCALE_SET_NAME
    ```

## <a name="get-the-aks-node-address"></a>Ottenere l'indirizzo del nodo servizio Azure Kubernetes

I nodi servizio Azure Kubernetes non sono pubblicamente esposti in Internet. Per SSH ai nodi servizio Azure Kubernetes, si utilizza l'indirizzo IP privato. Nel passaggio successivo viene creato un pod helper nel cluster AKS che consente di connettersi tramite SSH a questo indirizzo IP privato del nodo. I passaggi per ottenere l'indirizzo IP privato dei nodi AKS variano in base al tipo di cluster AKS eseguito:

* Per la maggior parte dei cluster AKS, seguire la procedura per [ottenere l'indirizzo IP per i cluster AKS normali](#ssh-to-regular-aks-clusters).
* Se si usano le funzionalità di anteprima in AKS che usano i set di scalabilità di macchine virtuali, ad esempio i pool di più nodi o il supporto dei contenitori di Windows Server, [seguire i passaggi per i cluster AKS basati sui set di scalabilità di macchine virtuali](#ssh-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="ssh-to-regular-aks-clusters"></a>Da SSH a cluster AKS regolari

Visualizzare l'indirizzo IP privato di un nodo del cluster AKS usando il comando [AZ VM list-IP-][az-vm-list-ip-addresses] addresses. Fornire il nome del gruppo di risorse del cluster AKS ottenuto in un passaggio [AZ-AKS-Show][az-aks-show] precedente:

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Il seguente output di esempio mostra gli indirizzi IP privati dei nodi servizio Azure Kubernetes:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

### <a name="ssh-to-virtual-machine-scale-set-based-aks-clusters"></a>Da SSH a cluster AKS basati su set di scalabilità di macchine virtuali

Elencare l'indirizzo IP interno dei nodi usando il [comando kubectl Get][kubectl-get]:

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

Registrare l'indirizzo IP interno del nodo di cui si desidera eseguire la risoluzione dei problemi. Questo indirizzo verrà usato in un passaggio successivo.

## <a name="create-the-ssh-connection"></a>Creare la connessione SSH

Per creare una connessione SSH a un nodo servizio Azure Kubernetes, si esegue un helper pod nel cluster servizio Azure Kubernetes. Questo helper pod fornisce l'accesso SSH nel cluster e quindi l'accesso a un nodo SSH aggiuntivo. Per creare e utilizzare questo helper pod, attenersi alla seguente procedura:

1. Eseguire un'immagine del contenitore `debian` e collegarvi una sessione terminal. Questo contenitore può essere usato per creare una sessione SSH con tutti i nodi del cluster del servizio Azure Container:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Se si usano i nodi di Windows Server (attualmente in anteprima in AKS), aggiungere un selettore di nodo al comando per pianificare il contenitore Debian in un nodo Linux, come indicato di seguito:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. L'immagine Debian di base non include i componenti SSH. Una volta che la sessione del terminale è collegata al contenitore, installare un client SSH usando `apt-get` come segue:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. In una nuova finestra del terminale, non connessa al contenitore, elencare i Pod nel cluster AKS usando il comando [kubectl Get Pod][kubectl-get] . Il pod creato nel passaggio precedente inizia con il nome *aks-ssh*, come mostrato nell'esempio seguente:

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. Nel primo passaggio di questo articolo, è stata aggiunta la chiave pubblica SSH al nodo servizio Azure Kubernetes. A questo punto, copiare la chiave SSH privata nel pod. Questa chiave privata viene utilizzata per creare l'SSH nei nodi servizio Azure Kubernetes.

    Fornire il proprio nome pod *aks-ssh* ottenuto nel passaggio precedente. Se necessario, modificare *~/.ssh/id_rsa* alla posizione della chiave SSH privata:

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. Tornare nella sessione del terminale al contenitore, aggiornare i permessi sulla chiave SSH privata `id_rsa` copiata in modo che sia di sola lettura per l'utente:

    ```console
    chmod 0600 id_rsa
    ```

1. Creare una connessione SSH al proprio nodo servizio Azure Kubernetes. Anche qui il nome utente predefinito per i nodi servizio Azure Kubernetes è *azureuser*. Accettare la richiesta di continuare con la connessione quando viene verificata l’attendibilità della chiave SSH. Viene quindi fornita la richiesta bash del nodo servizio Azure Kubernetes:

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
