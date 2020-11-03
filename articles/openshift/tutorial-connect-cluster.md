---
title: 'Esercitazione: Connettersi a un cluster di Azure Red Hat OpenShift 4'
description: Informazioni su come connettersi a un cluster di Microsoft Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 132a9b7ad3eef17f4ca27fb76a0c05f94c82fd37
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428283"
---
# <a name="tutorial-connect-to-an-azure-red-hat-openshift-4-cluster"></a>Esercitazione: Connettersi a un cluster di Azure Red Hat OpenShift 4

In questa esercitazione, parte due di tre, si stabilirà una connessione con un cluster Azure Red Hat OpenShift che esegue OpenShift 4 come utente kubeadmin tramite la console Web OpenShift. Si apprenderà come:
> [!div class="checklist"]
> * Ottenere le credenziali `kubeadmin` per il cluster
> * Installare l'interfaccia della riga di comando di OpenShift
> * Connettersi a un cluster di Azure Red Hat OpenShift usando l'interfaccia della riga di comando di OpenShift

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un cluster di Azure Red Hat OpenShift. Se questi passaggi non sono stati eseguiti e si vuole procedere, iniziare con [Esercitazione 1: Creare un cluster di Azure Red Hat OpenShift 4](tutorial-create-cluster.md).

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure 2.6.0 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="connect-to-the-cluster"></a>Stabilire la connessione al cluster

È possibile accedere al cluster tramite l'utente `kubeadmin`.  Eseguire il comando seguente per trovare la password per l'utente `kubeadmin`.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

L'output di esempio seguente mostra quale sarà la password in `kubeadminPassword`.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

È possibile trovare l'URL della console del cluster eseguendo il comando seguente, simile al seguente: `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`.

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

Avviare l'URL della console in un browser e accedere usando le credenziali `kubeadmin`.

![Schermata di accesso ad Azure Red Hat OpenShift](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>Installare l'interfaccia della riga di comando di OpenShift

Dopo aver eseguito l'accesso alla console Web di OpenShift, fare clic su **?** in alto a destra e quindi su **Strumenti della riga di comando**. Scaricare la versione appropriata per il computer.

![Screenshot che evidenzia l'opzione Strumenti della riga di comando nell'elenco quando si seleziona ? hamburger](media/aro4-download-cli.png)

È anche possibile scaricare la versione più recente dell'interfaccia della riga di comando appropriata per il computer all'indirizzo <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>.

Se si eseguono i comandi in Azure Cloud Shell, scaricare la versione più recente dell'interfaccia della riga di comando di OpenShift 4 per Linux.

```azurecli-interactive
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

## <a name="connect-using-the-openshift-cli"></a>Connettersi usando l'interfaccia della riga di comando di OpenShift

Recuperare l'indirizzo del server API.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

Accedere al server API del cluster OpenShift usando il comando seguente. Sostituire **\<kubeadmin password>** con la password appena recuperata.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso come:
> [!div class="checklist"]
> * Ottenere le credenziali `kubeadmin` per il cluster
> * Installare l'interfaccia della riga di comando di OpenShift
> * Connettersi a un cluster di Azure Red Hat OpenShift usando l'interfaccia della riga di comando di OpenShift

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Eliminare un cluster di Azure Red Hat OpenShift](tutorial-delete-cluster.md)