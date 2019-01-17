---
title: Accesso SSH in nodi del cluster del servizio Azure Kubernetes
description: Di seguito viene spiegato come creare una connessione SSH con i nodi del cluster del servizio Azure Kubernetes per la risoluzione dei problemi e le attività di manutenzione.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/21/2018
ms.author: iainfou
ms.openlocfilehash: d687467e6bd64363c78f60064c6a17adbc5e0d1f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52846132"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Connessione con SSH ai nodi del cluster del servizio Azure Kubernetes per la risoluzione dei problemi e le attività di manutenzione

Durante l'intero ciclo di vita del cluster del servizio Azure Kubernetes, potrebbe essere necessario accedere a un nodo del servizio Azure Kubernetes. Questo accesso potrebbe servire per la manutenzione, la raccolta dei registri o altre operazioni di risoluzione dei problemi. I nodi AKS sono macchine virtuali Linux, quindi è possibile accedervi utilizzando SSH. Per motivi di sicurezza, i nodi di Azure Kubernetes Service (AKS) non sono esposti in Internet.

Questo articolo mostra come creare una connessione SSH con un nodo AKS utilizzando i loro indirizzi IP privati.

## <a name="add-your-public-ssh-key"></a>Aggiungere la chiave SSH pubblica

Per impostazione predefinita, le chiavi SSH vengono generate quando si crea un cluster AKS. Se non sono state specificate le proprie chiavi SSH quando si è creato il cluster AKS, aggiungere le chiavi SSH pubbliche ai nodi AKS. 

Per aggiungere la chiave SSH a un nodo AKS, attenersi alla seguente procedura:

1. Ottenere il nome del gruppo di risorse per le risorse del cluster AKS utilizzando [az aks show][az-aks-show]. Fornire il proprio gruppo di risorse di base e il nome del cluster AKS:

    ```azurecli
    az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
    ```

1. Elencare le macchine virtuali nel gruppo di risorse del cluster AKS utilizzando il comando [az vm list][az-vm-list]. Queste macchine virtuali sono i nodi AKS:

    ```azurecli
    az vm list --resource-group MC_myResourceGroup_myAKSCluster_eastus -o table
    ```

    Il seguente output di esempio mostra i nodi AKS:

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. Per aggiungere le chiavi SSH al nodo, utilizzare il comando [az vm user update][az-vm-user-update]. Fornire il nome del gruppo di risorse e quindi uno dei nodi AKS ottenuti nel passaggio precedente. Per impostazione predefinita, il nome utente per i nodi AKS è *azureuser*. Fornire la posizione della propria chiave pubblica SSH, ad esempio *~/.ssh/id_rsa.pub*, oppure incollare il contenuto della chiave pubblica SSH:

    ```azurecli
    az vm user update \
      --resource-group MC_myResourceGroup_myAKSCluster_eastus \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="get-the-aks-node-address"></a>Ottenere l'indirizzo del nodo AKS

I nodi AKS non sono pubblicamente esposti in Internet. Per SSH ai nodi AKS, si utilizza l'indirizzo IP privato.

Visualizzare l'indirizzo IP privato di un nodo del cluster AKS utilizzando il comando [az vm list-ip-addresses][az-vm-list-ip-addresses]. Fornire il nome del gruppo di risorse del cluster AKS ottenuto in un passaggio [az-aks-show][az-aks-show] precedente:

```azurecli
az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table
```

Il seguente output di esempio mostra gli indirizzi IP privati dei nodi AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Creare la connessione SSH

Per creare una connessione SSH a un nodo AKS, si esegue un helper pod nel cluster AKS. Questo helper pod fornisce l'accesso SSH nel cluster e quindi l'accesso a un nodo SSH aggiuntivo. Per creare e utilizzare questo helper pod, attenersi alla seguente procedura:

1. Eseguire un'immagine del contenitore `debian` e collegarvi una sessione terminal. Questo contenitore può essere usato per creare una sessione SSH con tutti i nodi del cluster del servizio contenitore di Azure:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

1. L'immagine Debian di base non include i componenti SSH. Una volta che la sessione del terminale è collegata al contenitore, installare un client SSH usando `apt-get` come segue:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. In una nuova finestra del terminale, non connessa al contenitore, elencare i pod sul cluster AKS utilizzando il comando [kubectl get pods][kubectl-get]. Il pod creato nel passaggio precedente inizia con il nome *aks-ssh*, come mostrato nell'esempio seguente:

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. Nel primo passaggio di questo articolo, è stata aggiunta la chiave pubblica SSH al nodo AKS. A questo punto, copiare la chiave SSH privata nel pod. Questa chiave privata viene utilizzata per creare l'SSH nei nodi AKS.

    Fornire il proprio nome pod *aks-ssh* ottenuto nel passaggio precedente. Se necessario, modificare *~/.ssh/id_rsa* alla posizione della chiave SSH privata:

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. Tornare nella sessione del terminale al contenitore, aggiornare i permessi sulla chiave SSH privata `id_rsa` copiata in modo che sia di sola lettura per l'utente:

    ```console
    chmod 0600 id_rsa
    ```

1. Creare una connessione SSH al proprio nodo AKS. Anche qui il nome utente predefinito per i nodi AKS è *azureuser*. Accettare la richiesta di continuare con la connessione quando viene verificata l’attendibilità della chiave SSH. Viene quindi fornita la richiesta bash del nodo AKS:

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

Al termine, `exit` la sessione SSH e quindi `exit` la sessione interattiva del contenitore. Quando questa sessione del contenitore si chiude, il pod usato per l'accesso SSH dal cluster AKS viene eliminato.

## <a name="next-steps"></a>Passaggi successivi

Se sono necessari ulteriori dati per la risoluzione dei problemi, è possibile [visualizzare i registri kubelet][view-kubelet-logs] o [visualizzare i registri dei nodi master Kubernetes][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
