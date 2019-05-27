---
title: Accesso SSH in nodi del cluster del servizio Azure Kubernetes
description: Di seguito viene spiegato come creare una connessione SSH con i nodi del cluster del servizio Azure Kubernetes per la risoluzione dei problemi e le attività di manutenzione.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: a85c39fbfbf629e6ba9e668d55dd905c1ce0800c
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956362"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Connessione con SSH ai nodi del cluster del servizio Azure Kubernetes per la risoluzione dei problemi e le attività di manutenzione

Durante l'intero ciclo di vita del cluster del servizio Azure Kubernetes, potrebbe essere necessario accedere a un nodo del servizio Azure Kubernetes. Questo accesso potrebbe servire per la manutenzione, la raccolta dei registri o altre operazioni di risoluzione dei problemi. È possibile accedere tramite SSH, inclusi i nodi di Windows Server, attualmente in anteprima nel servizio contenitore di AZURE, i nodi AKS. È anche possibile [connettersi ai nodi Windows Server tramite connessioni remote desktop protocol (RDP)][aks-windows-rdp]. Per motivi di sicurezza, i nodi di servizio Azure Kubernetes non sono esposti in Internet.

Questo articolo mostra come creare una connessione SSH con un nodo servizio Azure Kubernetes utilizzando i loro indirizzi IP privati.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster AKS esistente. Se è necessario un cluster servizio Azure Kubernetes, vedere la Guida introduttiva su servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

Anche necessario la CLI di Azure versione 2.0.64 o versione successiva installato e configurato. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="add-your-public-ssh-key"></a>Aggiungere la chiave SSH pubblica

Per impostazione predefinita, le chiavi SSH sono ottenute, o generate e quindi aggiunti ai nodi quando si crea un cluster AKS. Se è necessario specificare le chiavi SSH diverse rispetto a quelli utilizzati durante la creazione del cluster AKS, aggiungere la chiave pubblica SSH ai nodi Linux AKS. Se necessario, è possibile creare una chiave di SSH usando [macOS o Linux] [ ssh-nix] oppure [Windows][ssh-windows]. Se si usa PuTTY generazione per creare la coppia di chiavi, salvare la coppia di chiavi in un OpenSSH formato anziché il valore predefinito formato chiave privata PuTTy (file con estensione ppk).

> [!NOTE]
> Can le chiavi SSH attualmente essere aggiunti solo ai nodi Linux tramite la CLI di Azure. Se si utilizzano i nodi di Windows Server, usare le chiavi SSH fornite durante la creazione del cluster servizio contenitore di AZURE e andare al passaggio sul [come ottenere l'indirizzo del nodo AKS](#get-the-aks-node-address). In alternativa, [connettersi ai nodi Windows Server tramite connessioni remote desktop protocol (RDP)][aks-windows-rdp].

Per aggiungere la chiave SSH a un nodo Linux AKS, completare i passaggi seguenti:

1. Ottenere il nome del gruppo di risorse per le risorse del cluster servizio Azure Kubernetes utilizzando [az servizio Azure Kubernetes show][az-aks-show]. Fornire il proprio gruppo di risorse di base e il nome del cluster servizio Azure Kubernetes:

    ```azurecli-interactive
    az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
    ```

1. Elencare le macchine virtuali nel gruppo di risorse del cluster servizio Azure Kubernetes utilizzando il comando [az vm list][az-vm-list]. Queste macchine virtuali sono i nodi servizio Azure Kubernetes:

    ```azurecli-interactive
    az vm list --resource-group MC_myResourceGroup_myAKSCluster_eastus -o table
    ```

    Il seguente output di esempio mostra i nodi servizio Azure Kubernetes:

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. Per aggiungere le chiavi SSH al nodo, utilizzare il comando [az vm user update][az-vm-user-update]. Fornire il nome del gruppo di risorse e quindi uno dei nodi servizio Azure Kubernetes ottenuti nel passaggio precedente. Per impostazione predefinita, il nome utente per i nodi servizio Azure Kubernetes è *azureuser*. Fornire la posizione della propria chiave pubblica SSH, ad esempio *~/.ssh/id_rsa.pub*, oppure incollare il contenuto della chiave pubblica SSH:

    ```azurecli-interactive
    az vm user update \
      --resource-group MC_myResourceGroup_myAKSCluster_eastus \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="get-the-aks-node-address"></a>Ottenere l'indirizzo del nodo servizio Azure Kubernetes

I nodi servizio Azure Kubernetes non sono pubblicamente esposti in Internet. Per SSH ai nodi servizio Azure Kubernetes, si utilizza l'indirizzo IP privato. Nel passaggio successivo è creare un pod helper nel cluster AKS che ti permette di SSH per questo indirizzo IP privato del nodo. I passaggi per ottenere l'indirizzo IP privato dei nodi del servizio contenitore di AZURE è diverso in base al tipo del cluster servizio contenitore di AZURE eseguire:

* Per la maggior parte dei cluster servizio contenitore di AZURE, seguire la procedura per [ottenere l'indirizzo IP per i cluster AKS regolari](#regular-aks-clusters).
* Se si usa qualsiasi funzionalità in anteprima nel servizio contenitore di AZURE che usano set di scalabilità di macchine virtuali, ad esempio più pool di nodi o supporto dei contenitori Windows Server, [seguire i passaggi per i cluster servizio contenitore di AZURE basata su set di scalabilità macchina virtuale](#virtual-machine-scale-set-based-aks-clusters).

### <a name="regular-aks-clusters"></a>Regolare i cluster servizio contenitore di AZURE

Visualizzare l'indirizzo IP privato di un nodo del cluster servizio Azure Kubernetes utilizzando il comando [az vm list-ip-addresses][az-vm-list-ip-addresses]. Fornire il nome del gruppo di risorse del cluster servizio Azure Kubernetes ottenuto in un passaggio [az-servizio Azure Kubernetes-show][az-aks-show] precedente:

```azurecli-interactive
az vm list-ip-addresses --resource-group MC_myResourceGroup_myAKSCluster_eastus -o table
```

Il seguente output di esempio mostra gli indirizzi IP privati dei nodi servizio Azure Kubernetes:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

### <a name="virtual-machine-scale-set-based-aks-clusters"></a>Cluster di AKS basata su set di scalabilità macchine virtuali

Elencare l'indirizzo IP interno dei nodi usando il [comando di kubectl get][kubectl-get]:

```console
kubectl get nodes -o wide
```

L'output di esempio seguente mostra gli indirizzi IP interni di tutti i nodi nel cluster, incluso un nodo Windows Server.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Registrare l'indirizzo IP interno del nodo che si desidera risolvere i problemi. Si userà questo indirizzo in un passaggio successivo.

## <a name="create-the-ssh-connection"></a>Creare la connessione SSH

Per creare una connessione SSH a un nodo servizio Azure Kubernetes, si esegue un helper pod nel cluster servizio Azure Kubernetes. Questo helper pod fornisce l'accesso SSH nel cluster e quindi l'accesso a un nodo SSH aggiuntivo. Per creare e utilizzare questo helper pod, attenersi alla seguente procedura:

1. Eseguire un'immagine del contenitore `debian` e collegarvi una sessione terminal. Questo contenitore può essere usato per creare una sessione SSH con tutti i nodi del cluster del servizio Azure Container:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Se si utilizzano i nodi di Windows Server (attualmente in anteprima nel servizio contenitore di AZURE), aggiunta un selettore di nodo per il comando per pianificare il contenitore in un nodo Linux Debian, come indicato di seguito:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. L'immagine Debian di base non include i componenti SSH. Una volta che la sessione del terminale è collegata al contenitore, installare un client SSH usando `apt-get` come segue:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. In una nuova finestra del terminale, non connessa al contenitore, elencare i pod sul cluster servizio Azure Kubernetes utilizzando il comando [kubectl get pods][kubectl-get]. Il pod creato nel passaggio precedente inizia con il nome *aks-ssh*, come mostrato nell'esempio seguente:

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
