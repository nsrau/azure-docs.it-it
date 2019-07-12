---
title: Esecuzione del montaggio di un volume di File di Azure in Istanze di Azure Container
description: Informazioni su come montare un volume di File di Azure per rendere persistente lo stato con Istanze di Azure Container
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: bc09aa500743d608c0a3a7a379fe9584c9c55e9b
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657632"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Montare una condivisione file di Azure in Istanze di Azure Container

Per impostazione predefinita, Istanze di Azure Container è senza stato. Se il contenitore si blocca o si arresta, lo stato viene perso. Per rendere persistente lo stato oltre la durata del contenitore, è necessario montare un volume da un archivio esterno. Questo articolo illustra come montare una condivisione file di Azure creata con [File di Azure](../storage/files/storage-files-introduction.md) per l'uso con Istanze di Azure Container. File di Azure offre condivisioni file completamente gestite nel cloud, accessibili tramite il protocollo SMB (Server Message Block) standard di settore. L'uso di una condivisione file di Azure con Istanze di Azure Container offre funzionalità di condivisione di file simili all'uso di una condivisione file di Azure con macchine virtuali di Azure.

> [!NOTE]
> Il montaggio di una condivisione File di Azure è attualmente limitato ai contenitori Linux. Microsoft sta lavorando per trasferire tutte le funzionalità in contenitori Windows, è possibile trovare le differenze di piattaforma corrente nel [Panoramica](container-instances-overview.md#linux-and-windows-containers).

## <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure

Prima di usare una condivisione file di Azure con Istanze di Azure Container è necessario creare la condivisione. Eseguire questo script per creare un account di archiviazione per ospitare la condivisione file e la condivisione in sé. Il nome dell'account di archiviazione deve essere globalmente univoco, quindi lo script aggiunge un valore casuale alla stringa di base.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Ottenere le credenziali di archiviazione

Per montare una condivisione file di Azure come volume in Istanze di Azure Container sono necessari tre valori: il nome dell'account di archiviazione, il nome della condivisione e la chiave di accesso alle risorse di archiviazione.

Se si usa lo script precedente, il nome dell'account di archiviazione è stato archiviato nella variabile $ACI_PERS_STORAGE_ACCOUNT_NAME. Per visualizzare il nome dell'account, digitare:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

Il nome della condivisione è già noto (definito come *acishare* nello script precedente), quindi resta da trovare solo la chiave dell'account di archiviazione, che può essere recuperata tramite il comando seguente:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume---cli"></a>Distribuire un contenitore e montare il volume - CLI

Per montare una condivisione file di Azure come volume in un contenitore tramite la CLI di Azure, specificare la condivisione e il volume punto di montaggio quando si crea il contenitore con [contenitore di az creare][az-container-create]. Se è stata eseguita la procedura precedente, è possibile montare la condivisione creata in precedenza tramite il comando seguente per creare un contenitore:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

Il `--dns-name-label` valore deve essere univoco all'interno dell'area di Azure in cui si crea l'istanza di contenitore. Aggiornare il valore nel comando precedente se viene visualizzato un messaggio di errore relativo all'**etichetta del nome DNS** quando si esegue il comando.

## <a name="manage-files-in-mounted-volume"></a>Gestire i file nel volume montato

Una volta avviato il contenitore, è possibile usare la semplice app web distribuita tramite il Microsoft [aci-hellofiles][aci-hellofiles] image to create small text files in the Azure file share at the mount path you specified. Obtain the web app's fully qualified domain name (FQDN) with the [az container show][az-container-show] comando:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

Dopo aver salvato il testo usando l'app, è possibile usare la [portale di Azure][portal] or a tool like the [Microsoft Azure Storage Explorer][storage-explorer] per recuperare e ispezionare il file scritto nella condivisione file.

## <a name="deploy-container-and-mount-volume---yaml"></a>Distribuire un contenitore e montare il volume - YAML

È anche possibile distribuire un gruppo di contenitori e montare un volume in un contenitore con la CLI di Azure e un [modello YAML](container-instances-multi-container-yaml.md). La distribuzione mediante il modello YAML è il metodo preferito quando si distribuiscono gruppi di contenitori costituiti da più contenitori.

Il modello YAML seguente definisce un gruppo di contenitori con un contenitore creato con il `aci-hellofiles` immagine. Il contenitore monta la condivisione file di Azure *acishare* creato in precedenza come un volume. Dove indicato, immettere la chiave di archiviazione e nome dell'account di archiviazione che ospita la condivisione file. 

Come illustrato nell'esempio dell'interfaccia della riga, il `dnsNameLabel` valore deve essere univoco all'interno dell'area di Azure in cui si crea l'istanza di contenitore. Se necessario, aggiornare il valore nel file YAML.

```yaml
apiVersion: '2018-10-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Per distribuire con il modello YAML, salvare il codice YAML precedente in un file denominato `deploy-aci.yaml`, quindi eseguire il [crea contenitore di az][az-container-create] comando con il `--file` parametro:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Distribuire contenitori e montare i volumi - Resource Manager

Oltre alla distribuzione della riga di comando e YAML, è possibile distribuire un gruppo di contenitori e montare un volume in un contenitore usando un'istanza di Azure [modello di Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Popolare innanzitutto la matrice `volumes` nella sezione `properties` del gruppo di contenitori del modello. 

Quindi, per ogni contenitore in cui si desidera montare il volume, popolare la `volumeMounts` matrice il `properties` sezione della definizione del contenitore.

Il modello di Resource Manager seguente definisce un gruppo di contenitori con un contenitore creato con il `aci-hellofiles` immagine. Il contenitore monta la condivisione file di Azure *acishare* creato in precedenza come un volume. Dove indicato, immettere la chiave di archiviazione e nome dell'account di archiviazione che ospita la condivisione file. 

Come negli esempi precedenti, il `dnsNameLabel` valore deve essere univoco all'interno dell'area di Azure in cui si crea l'istanza di contenitore. Se necessario, aggiornare il valore nel modello.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

Per distribuire con il modello di Resource Manager, salvare il codice JSON precedente in un file denominato `deploy-aci.json`, quindi eseguire il [distribuzione gruppo di az creare][az-group-deployment-create] comando con il `--template-file` parametro:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Montare più volumi

Per montare più volumi in un'istanza di contenitore, è necessario eseguire la distribuzione tramite un [modello di Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) o un file con estensione yaml. Per usare un modello o un file YAML, fornire i dettagli di condivisione e definire i volumi popolando la `volumes` matrice il `properties` sezione del modello. 

Ad esempio, se è stato creato due condivisioni file di Azure denominate *share1* e *share2* nell'account di archiviazione *myStorageAccount*, il `volumes` matrice in un gestore di risorse modello sarà simile al seguente:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Successivamente, per ogni contenitore del relativo gruppo in cui si desidera montare i volumi, inserire la matrice `volumeMounts` nella sezione `properties` della definizione del contenitore. Ad esempio, in questo modo vengono montati i due volumi, *myvolume1* e *myvolume2*, definiti in precedenza:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come montare altri tipi di volume in Istanze di Azure Container:

* [Mount an emptyDir volume in Azure Container Instances](container-instances-volume-emptydir.md) (Montare un volume emptyDir in Istanze di Azure Container)
* [Mount a gitRepo volume in Azure Container Instances](container-instances-volume-gitrepo.md) (Montare un volume gitRepo in Istanze di Azure Container)
* [Mount a secret volume in Azure Container Instances](container-instances-volume-secret.md) (Montare un volume segreto in Istanze di Azure Container)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create