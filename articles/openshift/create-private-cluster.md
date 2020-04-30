---
title: Creare un cluster privato Azure Red Hat OpenShift 4
description: Informazioni su come creare un cluster privato Azure Red Hat OpenShift che esegue OpenShift 4
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: ms-jasondel
ms.author: jasondel
keywords: Aro, OpenShift, AZ Aro, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: a0f726d32f2f63cf85101254fded005fc0b5a1db
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233551"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>Creare un cluster privato Azure Red Hat OpenShift 4

Questo articolo descrive come preparare l'ambiente per creare cluster privati Azure Red Hat OpenShift che eseguono OpenShift 4. Si apprenderà come:

> [!div class="checklist"]
> * Configurare i prerequisiti e creare la rete virtuale e le subnet necessarie
> * Distribuire un cluster con un endpoint server API privato e un controller di ingresso privato

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.75 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Prima di iniziare

### <a name="install-the-az-aro-extension"></a>Installare l'estensione ' AZ Aro '
L' `az aro` estensione consente di creare, accedere ed eliminare i cluster OpenShift di Azure Red Hat direttamente dalla riga di comando usando l'interfaccia della riga di comando di Azure.

Eseguire il comando seguente per installare l' `az aro` estensione.

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

Se è già installata l'estensione, è possibile eseguire l'aggiornamento eseguendo il comando seguente.

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>Registrare il provider di risorse

Successivamente, è necessario registrare il `Microsoft.RedHatOpenShift` provider di risorse nella sottoscrizione.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

Verificare che l'estensione sia registrata.

```azurecli-interactive
az -v
```

  Si otterrà un output simile al seguente.

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="obtain-a-red-hat-pull-secret-optional"></a>Ottenere un segreto di pull Red Hat (facoltativo)

Un Red Hat pull Secret consente al cluster di accedere ai registri contenitori di Red Hat insieme a contenuti aggiuntivi. Questo passaggio è facoltativo ma consigliato.

Per ottenere il segreto di pull, passare https://cloud.redhat.com/openshift/install/azure/aro-provisioned a e fare clic su *Scarica segreto di pull*.

Sarà necessario accedere all'account Red Hat o creare un nuovo account Red Hat con la posta elettronica aziendale e accettare i termini e le condizioni.

Conservare il file `pull-secret.txt` salvato in un luogo sicuro, che verrà usato in ogni creazione di cluster.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Creare una rete virtuale contenente due subnet vuote

Successivamente, verrà creata una rete virtuale contenente due subnet vuote.

1. **Impostare le variabili seguenti.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
   ```

1. **Creare un gruppo di risorse**

    Un gruppo di risorse di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite. Quando si crea un gruppo di risorse, viene chiesto di specificare una posizione. Questo è il percorso in cui vengono archiviati i metadati del gruppo di risorse, ma anche quello in cui vengono eseguite le risorse in Azure se non si specifica un'altra area durante la creazione della risorsa. Creare un gruppo di risorse usando il comando [AZ Group create] [AZ-Group-create].

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

    I cluster OpenShift di Azure Red Hat che eseguono OpenShift 4 richiedono una rete virtuale con due subnet vuote, per i nodi master e worker.

    Creare una nuova rete virtuale nello stesso gruppo di risorse creato in precedenza.

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    L'output di esempio seguente mostra che la rete virtuale è stata creata correttamente:

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

4. **Aggiungere una subnet vuota per i nodi del ruolo di lavoro.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[Disabilitare i criteri dell'endpoint privato della subnet](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) nella subnet master.** Questa operazione è necessaria per potersi connettere e gestire il cluster.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Creare il cluster

Eseguire il comando seguente per creare un cluster. Si notino `apiserver-visibility` i `ingress-visibility` parametri e. Facoltativamente, è possibile passare un segreto pull che consente al cluster di accedere ai registri contenitori di Red Hat insieme a contenuti aggiuntivi. Accedere al segreto di pull passando a [Red Hat OpenShift cluster Manager](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) e facendo clic su Copy Secret pull.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  --apiserver-visibility Private \
  --ingress-visibility Private
  # --domain aro.example.com # [OPTIONAL] custom domain
  # --pull-secret 'Pull secret from https://cloud.redhat.com/openshift/install/azure/installer-provisioned/' # [OPTIONAL]
```

>[!NOTE]
> La creazione di un cluster richiede in genere circa 35 minuti.

>[!IMPORTANT]
> Se si sceglie di specificare un dominio personalizzato, ad esempio **foo.example.com**, la console di OpenShift sarà disponibile in un URL quale `https://console-openshift-console.apps.foo.example.com`, invece del dominio `https://console-openshift-console.apps.<random>.<location>.aroapp.io`predefinito.
>
> Per impostazione predefinita, OpenShift USA certificati autofirmati per tutte le route create `*.apps.<random>.<location>.aroapp.io`in.  Se si sceglie un DNS personalizzato, dopo la connessione al cluster è necessario seguire la documentazione di OpenShift per [configurare un'autorità di certificazione personalizzata per il controller di ingresso](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) e la [CA personalizzata per il server API](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).

## <a name="connect-to-the-private-cluster"></a>Connettersi al cluster privato

È possibile accedere al cluster tramite l' `kubeadmin` utente.  Eseguire il comando seguente per trovare la password per l' `kubeadmin` utente.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

L'output di esempio seguente mostra che la password sarà `kubeadminPassword`in.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

È possibile trovare l'URL della console del cluster eseguendo il comando seguente, che sarà simile al seguente:`https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

>[!IMPORTANT]
> Per connettersi a un cluster privato Azure Red Hat OpenShift, è necessario eseguire il passaggio seguente da un host che si trova nella rete virtuale creata o in una rete virtuale con [peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) con la rete virtuale in cui è stato distribuito il cluster.

Avviare l'URL della console in un browser e accedere usando `kubeadmin` le credenziali.

![Schermata di accesso di Azure Red Hat OpenShift](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>Installare l'interfaccia della riga di comando di OpenShift

Dopo aver eseguito l'accesso alla console Web di OpenShift, fare clic su **?** in alto a destra e quindi su **strumenti da riga di comando**. Scaricare la versione appropriata per il computer.

![Schermata di accesso di Azure Red Hat OpenShift](media/aro4-download-cli.png)

È anche possibile scaricare la versione più recente dell'interfaccia della riga di comando appropriata <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>per il computer in uso da.

## <a name="connect-using-the-openshift-cli"></a>Connettersi usando l'interfaccia della riga di comando di OpenShift

Recuperare l'indirizzo del server API.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> Per connettersi a un cluster privato Azure Red Hat OpenShift, è necessario eseguire il passaggio seguente da un host che si trova nella rete virtuale creata o in una rete virtuale con [peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) con la rete virtuale in cui è stato distribuito il cluster.

Accedere al server API del cluster OpenShift usando il comando seguente. Sostituire ** \<kubeadmin password>** con la password appena recuperata.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato distribuito un cluster Azure Red Hat OpenShift che esegue OpenShift 4. Si è appreso come:

> [!div class="checklist"]
> * Configurare i prerequisiti e creare la rete virtuale e le subnet necessarie
> * Distribuire un cluster
> * Connettersi al cluster con l' `kubeadmin` utente

Passare all'articolo successivo per informazioni su come configurare il cluster per l'autenticazione usando Azure Active Directory.


* [Configurare l'autenticazione con Azure Active Directory tramite la riga di comando](configure-azure-ad-cli.md)


* [Configurare l'autenticazione con Azure Active Directory tramite la console Web portale di Azure e OpenShift](configure-azure-ad-cli.md)
