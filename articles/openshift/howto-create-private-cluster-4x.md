---
title: Creare un cluster privato di Azure Red Hat OpenShift 4
description: Informazioni su come creare un cluster privato di Azure Red Hat OpenShift che esegue OpenShift 4
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: ms-jasondel
ms.author: jasondel
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: 581587382c3bfd03ed329672e5c6ca065554d1c7
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727439"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>Creare un cluster privato di Azure Red Hat OpenShift 4

Questo articolo descrive come preparare l'ambiente per creare cluster privati di Azure Red Hat OpenShift che eseguono OpenShift 4. Si apprenderà come:

> [!div class="checklist"]
> * Configurare i prerequisiti e creare la rete virtuale e la subnet obbligatorie
> * Distribuire un cluster con un endpoint server API privato e un controller di ingresso privato

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure 2.0.75 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Prima di iniziare

### <a name="install-the-az-aro-extension"></a>Installare l'estensione "az aro"
L'estensione `az aro` consente di creare ed eliminare i cluster di Azure Red Hat OpenShift, nonché accedervi direttamente dalla riga di comando usando l'interfaccia della riga di comando di Azure.

Per installare l'estensione `az aro`, eseguire il comando seguente.

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

Se l'estensione è già installata, eseguire il comando seguente per aggiornarla.

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>Registrare il provider di risorse

Successivamente, è necessario registrare il provider di risorse `Microsoft.RedHatOpenShift` nella sottoscrizione.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

Verificare che l'estensione sia stata registrata.

```azurecli-interactive
az -v
```

  L'output dovrebbe essere simile al seguente.

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Ottenere un segreto pull di Red Hat (facoltativo)

Un segreto pull di Red Hat consente al cluster di accedere ai registri contenitori di Red Hat unitamente a contenuto aggiuntivo. Questo passaggio è facoltativo ma consigliato.

1. **[Passare al portale di gestione cluster di Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) ed eseguire l'accesso.**

   È necessario accedere all'account Red Hat o creare un nuovo account Red Hat con l'indirizzo di posta elettronica aziendale e accettare i termini e le condizioni.

2. **Fare clic su Download pull secret (Scarica segreto pull).**

Conservare il file `pull-secret.txt` salvato in una posizione sicura perché verrà usato ogni volta che si crea un cluster.

Quando si esegue il comando `az aro create`, è possibile fare riferimento al segreto di pull usando il parametro `--pull-secret @pull-secret.txt`. Eseguire `az aro create` dalla directory in cui è stato archiviato il file `pull-secret.txt`. In caso contrario, sostituire `@pull-secret.txt` con `@<path-to-my-pull-secret-file`.

Se si copia il segreto pull o vi si fa riferimento in altri script, il segreto pull deve essere formattato come stringa JSON valida.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Creare una rete virtuale contenente due subnet vuote

Successivamente, verrà creata una rete virtuale contenente due subnet vuote.

1. **Impostare le variabili seguenti.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
   ```

1. **Creare un gruppo di risorse**

    Un gruppo di risorse di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite. Quando si crea un gruppo di risorse, viene chiesto di specificare una posizione. Questo è il percorso in cui vengono archiviati i metadati del gruppo di risorse, ma anche quello in cui vengono eseguite le risorse in Azure se non si specifica un'altra area durante la creazione della risorsa. Creare un gruppo di risorse con il comando [az group create][az-group-create].

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    L'output di esempio seguente mostra il gruppo di risorse creato correttamente:

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **Creare una rete virtuale.**

    I cluster di Azure Red Hat OpenShift che eseguono OpenShift 4 richiedono una rete virtuale con due subnet vuote, per i nodi master e di lavoro.

    Creare una nuova rete virtuale nello stesso gruppo di risorse creato in precedenza.

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    L'output di esempio seguente mostra la rete virtuale creata correttamente:

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **Aggiungere una subnet vuota per i nodi master.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **Aggiungere una subnet vuota per i nodi di lavoro.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[Disabilitare i criteri per gli endpoint privati della subnet](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) nella subnet master**. Questa operazione è necessaria per potersi connettere e gestire il cluster.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Creare il cluster

Eseguire il comando seguente per creare un cluster. Facoltativamente, è possibile passare il [segreto pull di Red Hat](#get-a-red-hat-pull-secret-optional) che consente al cluster di accedere ai registri contenitori di Red Hat unitamente a contenuto aggiuntivo.

>[!NOTE]
> Se si copiano/incollano i comandi e si usa uno dei parametri facoltativi, assicurarsi di eliminare gli hashtag iniziali e il testo del commento finale. Chiudere anche l'argomento nella riga precedente del comando con una barra rovesciata finale.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  --apiserver-visibility Private \
  --ingress-visibility Private
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

Dopo l'esecuzione di `az aro create`, la creazione di un cluster richiede in genere circa 35 minuti.

>[!IMPORTANT]
> Se si sceglie di specificare un dominio personalizzato, ad esempio **foo.example.com**, la console di OpenShift sarà disponibile tramite un URL, ad esempio `https://console-openshift-console.apps.foo.example.com`, invece del dominio predefinito `https://console-openshift-console.apps.<random>.<location>.aroapp.io`.
>
> Per impostazione predefinita, OpenShift usa certificati autofirmati per tutte le route create in `*.apps.<random>.<location>.aroapp.io`.  Se si sceglie un DNS personalizzato dopo la connessione al cluster, è necessario attenersi alla documentazione di OpenShift per [configurare un'autorità di certificazione personalizzata per il controller del traffico in ingresso](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) e un'[autorità di certificazione personalizzata per il server API](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).

## <a name="connect-to-the-private-cluster"></a>Connettersi al cluster privato

È possibile accedere al cluster tramite l'utente `kubeadmin`.  Eseguire il comando seguente per trovare la password per l'utente `kubeadmin`.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

L'output di esempio seguente mostra che la password sarà in `kubeadminPassword`.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

È possibile trovare l'URL della console del cluster eseguendo il comando seguente, simile al seguente: `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

>[!IMPORTANT]
> Per connettersi a un cluster privato di Azure Red Hat OpenShift, è necessario eseguire il passaggio seguente da un host che si trova nella rete virtuale creata o in una rete virtuale [con peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) con la rete virtuale in cui è stato distribuito il cluster.

Avviare l'URL della console in un browser e accedere usando le credenziali `kubeadmin`.

![Schermata di accesso ad Azure Red Hat OpenShift](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>Installare l'interfaccia della riga di comando di OpenShift

Dopo aver eseguito l'accesso alla console Web di OpenShift, fare clic su **?** in alto a destra e quindi su **Strumenti della riga di comando**. Scaricare la versione appropriata per il computer.

![Schermata di accesso ad Azure Red Hat OpenShift](media/aro4-download-cli.png)

È anche possibile scaricare la versione più recente dell'interfaccia della riga di comando appropriata per il computer all'indirizzo <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>.

## <a name="connect-using-the-openshift-cli"></a>Connettersi usando l'interfaccia della riga di comando di OpenShift

Recuperare l'indirizzo del server API.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> Per connettersi a un cluster privato di Azure Red Hat OpenShift, è necessario eseguire il passaggio seguente da un host che si trova nella rete virtuale creata o in una rete virtuale [con peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) con la rete virtuale in cui è stato distribuito il cluster.

Accedere al server API del cluster OpenShift usando il comando seguente. Sostituire **\<kubeadmin password>** con la password appena recuperata.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato distribuito un cluster di Azure Red Hat OpenShift che esegue OpenShift 4. Si è appreso come:

> [!div class="checklist"]
> * Configurare i prerequisiti e creare la rete virtuale e la subnet obbligatorie
> * Distribuire un cluster
> * Connettersi al cluster tramite l'utente `kubeadmin`

Per informazioni su come configurare il cluster per l'autenticazione tramite Azure Active Directory, passare all'articolo successivo.


* [Configurare l'autenticazione con Azure Active Directory tramite la riga di comando](configure-azure-ad-cli.md)


* [Configurare l'autenticazione con Azure Active Directory tramite il portale di Azure e la console Web OpenShift](configure-azure-ad-cli.md)
