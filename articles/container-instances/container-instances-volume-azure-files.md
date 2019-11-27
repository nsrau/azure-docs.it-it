---
title: Montare File di Azure volume al gruppo di contenitori
description: Informazioni su come montare un volume di File di Azure per rendere persistente lo stato con Istanze di Azure Container
ms.topic: article
ms.date: 07/08/2019
ms.custom: mvc
ms.openlocfilehash: a258a96f5fbc0d54b6a85a780288fb9317cb1a1b
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533251"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Montare una condivisione file di Azure in Istanze di Azure Container

Per impostazione predefinita, Istanze di Azure Container è senza stato. Se il contenitore si blocca o si arresta, lo stato viene perso. Per rendere persistente lo stato oltre la durata del contenitore, è necessario montare un volume da un archivio esterno. Come illustrato in questo articolo, le istanze di contenitore di Azure possono montare una condivisione file di Azure creata con [file di Azure](../storage/files/storage-files-introduction.md). File di Azure offre condivisioni file completamente gestite sul cloud, accessibili tramite il protocollo SMB (Server Message Block) standard di settore. L'uso di una condivisione file di Azure con Istanze di Azure Container offre funzionalità di condivisione di file simili all'uso di una condivisione file di Azure con macchine virtuali di Azure.

> [!NOTE]
> Il montaggio di una condivisione File di Azure è attualmente limitato ai contenitori Linux. Trovare le attuali differenze della piattaforma nella [Panoramica](container-instances-overview.md#linux-and-windows-containers).
>
> Il montaggio di una condivisione di File di Azure in un'istanza di contenitore è simile a quello di un [montaggio di binding](https://docs.docker.com/storage/bind-mounts/)docker. Tenere presente che se si monta una condivisione in una directory del contenitore in cui sono presenti file o directory, questi file o directory sono nascosti dal montaggio e non sono accessibili durante l'esecuzione del contenitore.
>

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

## <a name="deploy-container-and-mount-volume---cli"></a>Distribuire il contenitore e montare il volume-CLI

Per montare una condivisione file di Azure come volume in un contenitore usando l'interfaccia della riga di comando di Azure, specificare la condivisione e il punto di montaggio del volume quando si crea il contenitore con [AZ container create][az-container-create]. Se è stata eseguita la procedura precedente, è possibile montare la condivisione creata in precedenza tramite il comando seguente per creare un contenitore:

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

Il valore di `--dns-name-label` deve essere univoco all'interno dell'area di Azure in cui si crea l'istanza di contenitore. Aggiornare il valore nel comando precedente se viene visualizzato un messaggio di errore relativo all'**etichetta del nome DNS** quando si esegue il comando.

## <a name="manage-files-in-mounted-volume"></a>Gestire i file nel volume montato

Quando il contenitore viene avviato, è possibile usare l'app Web semplice distribuita tramite l'immagine Microsoft [ACI-hellofiles][aci-hellofiles] per creare piccoli file di testo nella condivisione file di Azure nel percorso di montaggio specificato. Ottenere il nome di dominio completo (FQDN) dell'app Web con il comando [AZ container Show][az-container-show] :

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

Dopo aver salvato il testo con l'app, è possibile usare il [portale di Azure][portal] o uno strumento come il [Microsoft Azure Storage Explorer][storage-explorer] per recuperare e ispezionare il file scritto nella condivisione file.

## <a name="deploy-container-and-mount-volume---yaml"></a>Distribuire il contenitore e montare volume-YAML

È anche possibile distribuire un gruppo di contenitori e montare un volume in un contenitore con l'interfaccia della riga di comando di Azure e un [modello YAML](container-instances-multi-container-yaml.md). La distribuzione mediante il modello YAML è il metodo preferito quando si distribuiscono gruppi di contenitori costituiti da più contenitori.

Il modello YAML seguente definisce un gruppo di contenitori con un contenitore creato con l'immagine del `aci-hellofiles`. Il contenitore monta la condivisione file di Azure *acishare nello* creata in precedenza come volume. Dove indicato, immettere il nome e la chiave di archiviazione per l'account di archiviazione che ospita la condivisione file. 

Come nell'esempio dell'interfaccia della riga di comando, il valore `dnsNameLabel` deve essere univoco all'interno dell'area di Azure in cui si crea l'istanza di contenitore. Se necessario, aggiornare il valore nel file YAML.

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

Per eseguire la distribuzione con il modello YAML, salvare la YAML precedente in un file denominato `deploy-aci.yaml`, quindi eseguire il comando [AZ container create][az-container-create] con il parametro `--file`:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Distribuire il contenitore e montare il volume Gestione risorse

Oltre all'interfaccia della riga di comando e alla distribuzione YAML, è possibile distribuire un gruppo di contenitori e montare un volume in un contenitore usando un [modello di gestione risorse](/azure/templates/microsoft.containerinstance/containergroups)di Azure.

Popolare innanzitutto la matrice `volumes` nella sezione `properties` del gruppo di contenitori del modello. 

Quindi, per ogni contenitore in cui si vuole montare il volume, popolare la matrice di `volumeMounts` nella sezione `properties` della definizione del contenitore.

Il modello di Gestione risorse seguente definisce un gruppo di contenitori con un contenitore creato con l'immagine del `aci-hellofiles`. Il contenitore monta la condivisione file di Azure *acishare nello* creata in precedenza come volume. Dove indicato, immettere il nome e la chiave di archiviazione per l'account di archiviazione che ospita la condivisione file. 

Come negli esempi precedenti, il valore di `dnsNameLabel` deve essere univoco all'interno dell'area di Azure in cui si crea l'istanza di contenitore. Se necessario, aggiornare il valore nel modello.

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

Per eseguire la distribuzione con il modello di Gestione risorse, salvare il codice JSON precedente in un file denominato `deploy-aci.json`, quindi eseguire il comando [AZ Group Deployment create][az-group-deployment-create] con il parametro `--template-file`:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Montare più volumi

Per montare più volumi in un'istanza di contenitore, è necessario eseguire la distribuzione tramite un [modello di Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) o un file con estensione yaml. Per usare un modello o un file YAML, fornire i dettagli della condivisione e definire i volumi popolando la matrice di `volumes` nella sezione `properties` del modello. 

Se, ad esempio, sono state create due condivisioni di File di Azure denominate *Share1* e *Share2* nell'account di archiviazione *myStorageAccount*, la matrice di `volumes` in un modello di gestione risorse apparirà come segue:

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