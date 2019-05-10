---
title: Accesso SSH in nodi del cluster del servizio Azure Kubernetes
description: Di seguito viene spiegato come creare una connessione SSH con i nodi del cluster del servizio Azure Kubernetes per la risoluzione dei problemi e le attività di manutenzione.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/05/2019
ms.author: iainfou
ms.openlocfilehash: d421fad5f574b0d10b24453aca01adf574f493e8
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65407695"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Connessione con SSH ai nodi del cluster del servizio Azure Kubernetes per la risoluzione dei problemi e le attività di manutenzione

Durante l'intero ciclo di vita del cluster del servizio Azure Kubernetes, potrebbe essere necessario accedere a un nodo del servizio Azure Kubernetes. Questo accesso potrebbe servire per la manutenzione, la raccolta dei registri o altre operazioni di risoluzione dei problemi. I nodi servizio Azure Kubernetes sono macchine virtuali Linux, quindi è possibile accedervi utilizzando SSH. Per motivi di sicurezza, i nodi di servizio Azure Kubernetes non sono esposti in Internet.

Questo articolo mostra come creare una connessione SSH con un nodo servizio Azure Kubernetes utilizzando i loro indirizzi IP privati.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster AKS esistente. Se è necessario un cluster servizio Azure Kubernetes, vedere la Guida introduttiva su servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

Anche necessario la CLI di Azure versione 2.0.59 o versione successiva installato e configurato. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="add-your-public-ssh-key"></a>Aggiungere la chiave SSH pubblica

Per impostazione predefinita, le chiavi SSH vengono generate quando si crea un cluster servizio Azure Kubernetes. Se non sono state specificate le proprie chiavi SSH quando si è creato il cluster servizio Azure Kubernetes, aggiungere le chiavi SSH pubbliche ai nodi servizio Azure Kubernetes.

Per aggiungere la chiave SSH a un nodo servizio Azure Kubernetes, attenersi alla seguente procedura:

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

I nodi servizio Azure Kubernetes non sono pubblicamente esposti in Internet. Per SSH ai nodi servizio Azure Kubernetes, si utilizza l'indirizzo IP privato. Nel passaggio successivo è creare un pod helper nel cluster AKS che ti permette di SSH per questo indirizzo IP privato del nodo.

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

## <a name="create-the-ssh-connection"></a>Creare la connessione SSH

Per creare una connessione SSH a un nodo servizio Azure Kubernetes, si esegue un helper pod nel cluster servizio Azure Kubernetes. Questo helper pod fornisce l'accesso SSH nel cluster e quindi l'accesso a un nodo SSH aggiuntivo. Per creare e utilizzare questo helper pod, attenersi alla seguente procedura:

1. Eseguire un'immagine del contenitore `debian` e collegarvi una sessione terminal. Questo contenitore può essere usato per creare una sessione SSH con tutti i nodi del cluster del servizio Azure Container:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

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
