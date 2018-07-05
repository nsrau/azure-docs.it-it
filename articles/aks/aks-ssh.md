---
title: Accesso SSH in nodi del cluster Azure Kubernetes Service (AKS)
description: Creare una connessione SSH con i nodi di un cluster Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/06/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 8f4c70814566a963d86c119044f9ed3ef3238483
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099399"
---
# <a name="ssh-into-azure-kubernetes-service-aks-cluster-nodes"></a>Accesso SSH in nodi del cluster Azure Kubernetes Service (AKS)

A volte potrebbe essere necessario accedere a un nodo di Azure Kubernetes Service (AKS) per operazioni di manutenzione, raccolta di log o risoluzione dei problemi. I nodi AKS non sono esposti in Internet. Usare la procedura descritta in questo documento per creare una connessione SSH a un nodo AKS.

## <a name="reset-ssh-keys"></a>Ripristinare le chiavi SSH

Se è stato eseguito un AKS senza chiavi SSH o se non si ha accesso alle chiavi SSH corrette, è possibile reimpostare queste ultime tramite il portale di Azure.

Passare al cluster AKS, selezionare un nodo AKS (macchina virtuale) e selezionare **Reimposta password** per reimpostare la chiave pubblica SSH.

![Macchina virtuale AKS con pulsante di ripristino della password](media/aks-ssh/reset-password.png)

Selezionare **Reimposta chiave pubblica SSH**, immettere il nome utente cluster AKS, ovvero **azueruser** per impostazione predefinita e copiarlo in una chiave pubblica SSH. Selezionare **Aggiorna** al termine dell’operazione.

![Macchina virtuale del portale AKS con pulsante di ripristino della password](media/aks-ssh/reset-password-2.png)

Una volta reimpostata la chiave SSH, è possibile creare una connessione SSH tramite la chiave privata corrispondente.

## <a name="get-aks-node-address"></a>Ottenere l'indirizzo del nodo del servizio contenitore di Azure

Ottenere l'indirizzo IP di un nodo del cluster del servizio contenitore di Azure usando il comando `az vm list-ip-addresses`. Sostituire il nome del gruppo di risorse con il nome del gruppo di risorse del servizio contenitore di Azure.

```console
$ az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table

VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-42032720-0  10.240.0.6
aks-nodepool1-42032720-1  10.240.0.5
aks-nodepool1-42032720-2  10.240.0.4
```

## <a name="create-ssh-connection"></a>Creare la connessione SSH

Eseguire l'immagine del contenitore `debian` e collegarvi una sessione terminal. Il contenitore può quindi essere usato per creare una sessione SSH con tutti i nodi del cluster del servizio contenitore di Azure.

```console
kubectl run -it --rm aks-ssh --image=debian
```

Installare un client SSH nel contenitore.

```console
apt-get update && apt-get install openssh-client -y
```

Aprire una seconda sessione terminal ed elencare tutti i pod per ottenere il nome del nuovo pod creato.

```console
$ kubectl get pods

NAME                       READY     STATUS    RESTARTS   AGE
aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
```

Copiare la chiave privata SSH nel pod, sostituire il nome del pod con il valore appropriato.

```console
kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
```

Aggiornare il file `id_rsa` in modo che sia di sola lettura per l'utente.

```console
chmod 0600 id_rsa
```

Creare una connessione SSH al nodo del servizio contenitore di Azure. Il nome utente predefinito per un cluster AKS è `azureuser`. Se questo account è stato modificato al momento della creazione del cluster, sostituire il nome utente amministratore appropriato.

```console
$ ssh -i id_rsa azureuser@10.240.0.6

Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

48 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Tue Feb 27 20:10:38 2018 from 167.220.99.8
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@aks-nodepool1-42032720-0:~$
```

## <a name="remove-ssh-access"></a>Rimuovere l'accesso SSH

Al termine, chiudere la sessione SSH e quindi la sessione interattiva del contenitore. Questa azione elimina il pod usato per l'accesso SSH dal cluster del servizio contenitore di Azure.
