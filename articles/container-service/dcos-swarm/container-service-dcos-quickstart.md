---
title: Guida rapida al servizio contenitore di Azure - Distribuire un cluster DC/OS
description: Guida rapida al servizio contenitore di Azure - Distribuire un cluster DC/OS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2a0c702f1b1997a35be49c8d94db9ff69e95653f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="deploy-a-dcos-cluster"></a>Distribuire un cluster DC/OS

DC/OS fornisce una piattaforma distribuita per l'esecuzione di applicazioni in contenitori e moderne. Con il servizio contenitore di Azure, il provisioning di un cluster DC/OS pronto per la produzione è semplice e rapido. Questa guida rapida illustra in dettaglio i passaggi di base necessari per distribuire un cluster DC/OS ed eseguire un carico di lavoro di base.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Questa esercitazione richiede l'interfaccia della riga di comando di Azure 2.0.4 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/reference-index#az_login) e seguire le istruzioni visualizzate.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-dcos-cluster"></a>Creare un cluster DC/OS

Creare un cluster DC/OS con il comando [az acs create](/cli/azure/acs#az_acs_create).

L'esempio seguente crea un cluster DC/OS denominato *myDCOSCluster* e crea le chiavi SSH se non esistono già. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`.  

```azurecli
az acs create --orchestrator-type dcos --resource-group myResourceGroup --name myDCOSCluster --generate-ssh-keys
```

In alcuni casi la sottoscrizione di Azure ha accesso limitato alle risorse di Azure, ad esempio con una versione di valutazione gratuita limitata. Se la distribuzione non riesce a causa di core disponibili limitati, ridurre il numero di agenti predefinito aggiungendo `--agent-count 1` al comando [az acs create](/cli/azure/acs#az_acs_create). 

Dopo alcuni minuti, il comando viene completato e restituisce le informazioni sulla distribuzione.

## <a name="connect-to-dcos-cluster"></a>Connettersi al cluster DC/OS

Dopo aver creato un cluster DC/OS, è possibile accedervi tramite un tunnel SSH. Eseguire il comando seguente per restituire l'indirizzo IP pubblico del master DC/OS. L'indirizzo IP viene archiviato in una variabile e usato nel passaggio successivo.

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

Per creare il tunnel SSH, eseguire il comando seguente e seguire le istruzioni visualizzate. Se la porta 80 è già in uso, il comando non riesce. Aggiornare la porta di tunneling selezionandone una non in uso, come `85:localhost:80`. 

```azurecli
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

Il tunnel SSH può essere testato passando a `http://localhost`. Se è stata usata una porta diversa dalla 80, modificare il percorso corrispondentemente. 

Se il tunnel SSH è stato creato correttamente, viene restituito il portale di DC/OS.

![Interfaccia utente di DC/OS](./media/container-service-dcos-quickstart/dcos-ui.png)

## <a name="install-dcos-cli"></a>Installare l'interfaccia della riga di comando di DC/OS

L'interfaccia della riga di comando di DC/OS viene usata per gestire un cluster DC/OS dalla riga di comando. Installare l'interfaccia della riga di comando di DC/OS con il comando [az acs dcos install-cli](/azure/acs/dcos#install-cli). Se si usa Azure CloudShell, l'interfaccia della riga di comando di DC/OS è già installata. 

Se si esegue l'interfaccia della riga di comando di Azure in macOS o Linux, potrebbe essere necessario eseguire il comando con sudo.

```azurecli
az acs dcos install-cli
```

Prima di poter usare l'interfaccia della riga di comando con il cluster, deve essere configurato per usare il tunnel SSH. A tale scopo, eseguire il comando seguente, modificando la porta se necessario.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Eseguire un'applicazione

Il meccanismo di pianificazione predefinito per un cluster DC/OS del servizio contenitore di Azure è Marathon. Marathon viene usato per avviare un'applicazione e gestire lo stato dell'applicazione nel cluster DC/OS. Per pianificare un'applicazione tramite Marathon, creare un file denominato *marathon-app.json* e copiarvi il contenuto seguente. 

```json
{
  "id": "demo-app",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  },
  "acceptedResourceRoles": [
    "slave_public"
  ]
}
```

Eseguire il comando seguente per pianificare l'esecuzione dell'applicazione nel cluster DC/OS.

```azurecli
dcos marathon app add marathon-app.json
```

Per visualizzare lo stato di distribuzione per l'app, eseguire il comando seguente.

```azurecli
dcos marathon app list
```

Quando il valore della colonna **WAITING** (Attesa) passa da *True* a *False*, la distribuzione dell'applicazione è stata completata.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/1    ---       ---      False      DOCKER   None
```

Ottenere l'indirizzo IP pubblico degli agenti del cluster DC/OS.

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Passando a questo indirizzo viene restituito il sito NGINX predefinito.

![NGINX](./media/container-service-dcos-quickstart/nginx.png)

## <a name="delete-dcos-cluster"></a>Eliminare il cluster DC/OS

Quando non serve più, è possibile rimuovere il gruppo di risorse, il cluster DC/OS e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group#az_group_delete).

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida rapida è stato distribuito un cluster DC/OS ed è stato eseguito un contenitore Docker semplice nel cluster. Per altre informazioni sul servizio contenitore di Azure, continuare con le esercitazioni relative a questo servizio.

> [!div class="nextstepaction"]
> [Gestire un cluster DC/OS del servizio contenitore di Azure](container-service-dcos-manage-tutorial.md)