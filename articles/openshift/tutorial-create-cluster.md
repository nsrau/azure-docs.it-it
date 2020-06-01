---
title: Esercitazione - Creare un cluster di Azure Red Hat OpenShift 4
description: Informazioni su come creare un cluster Microsoft Azure Red Hat OpenShift usando l'interfaccia della riga di comando di Azure
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: f8b34f1678d39471a1d0b91756ac93a01cbfedba
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800172"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Esercitazione: Creare un cluster di Azure Red Hat OpenShift 4

In questa esercitazione, la prima di tre su questo argomento, viene preparato l'ambiente per la creazione di un cluster di Azure Red Hat OpenShift che esegue OpenShift 4 e viene creato un cluster. Si apprenderà come:
> [!div class="checklist"]
> * Configurare i prerequisiti e creare la rete virtuale e la subnet obbligatorie
> * Distribuire un cluster

## <a name="before-you-begin"></a>Prima di iniziare

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure 2.0.75 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="verify-your-permissions"></a>Verificare le autorizzazioni

Per creare un cluster Azure Red Hat OpenShift, verificare le autorizzazioni seguenti per l'utente e l'account Azure:

|Autorizzazioni|Gruppo di risorse che contiene la rete virtuale|Utente che esegue `az aro create`|Entità servizio passata come `–client-id`|
|----|:----:|:----:|:----:|
|**Amministratore accessi utente**|X|X| |
|**Collaboratore**|X|X|X|

### <a name="install-the-az-aro-extension"></a>Installare l'estensione `az aro`
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

Quando si esegue il comando `az aro create`, è possibile fare riferimento al segreto di pull usando il parametro `--pull-secret @pull-secret.txt`. Eseguire `az aro create` dalla directory in cui è stato archiviato il file `pull-secret.txt`. In caso contrario, sostituire `@pull-secret.txt` con `@<path-to-my-pull-secret-file>`.

Se si copia il segreto pull o vi si fa riferimento in altri script, il segreto pull deve essere formattato come stringa JSON valida.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Creare una rete virtuale contenente due subnet vuote

Successivamente, verrà creata una rete virtuale contenente due subnet vuote.

1. **Impostare le variabili seguenti.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
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
  --worker-subnet worker-subnet
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

Dopo l'esecuzione di `az aro create`, la creazione di un cluster richiede in genere circa 35 minuti.

>[!IMPORTANT]
> Se si sceglie di specificare un dominio personalizzato, ad esempio **foo.example.com**, la console di OpenShift sarà disponibile tramite un URL, ad esempio `https://console-openshift-console.apps.foo.example.com`, invece del dominio predefinito `https://console-openshift-console.apps.<random>.<location>.aroapp.io`.
>
> Per impostazione predefinita, OpenShift usa certificati autofirmati per tutte le route create in `*.apps.<random>.<location>.aroapp.io`.  Se si sceglie di usare un DNS personalizzato dopo la connessione al cluster, è necessario attenersi alla documentazione di OpenShift per [configurare un'autorità di certificazione personalizzata per il controller del traffico in ingresso](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) e un'[autorità di certificazione personalizzata per il server API](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).
>

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso come:
> [!div class="checklist"]
> * Configurare i prerequisiti e creare la rete virtuale e la subnet obbligatorie
> * Distribuire un cluster

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Connettersi a un cluster di Azure Red Hat OpenShift](tutorial-connect-cluster.md)
