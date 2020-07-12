---
title: Trasferire gli artefatti
description: Trasferire raccolte di immagini o altri artefatti da un registro contenitori a un altro registro di sistema creando una pipeline di trasferimento usando gli account di archiviazione di Azure
ms.topic: article
ms.date: 05/08/2020
ms.custom: ''
ms.openlocfilehash: 7f63936ad8f2a97bae6ff63e783e38c15db35e13
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259452"
---
# <a name="transfer-artifacts-to-another-registry"></a>Trasferire gli artefatti in un altro registro

Questo articolo illustra come trasferire raccolte di immagini o altri elementi del registro di sistema da un registro contenitori di Azure a un altro registro di sistema. I registri di origine e di destinazione possono trovarsi nella stessa sottoscrizione o in sottoscrizioni diverse, Active Directory tenant, cloud di Azure o cloud fisicamente disconnessi. 

Per trasferire gli elementi, si crea una *pipeline di trasferimento* che replica gli artefatti tra due registri usando l' [archiviazione BLOB](../storage/blobs/storage-blobs-introduction.md):

* Gli artefatti di un registro di origine vengono esportati in un BLOB in un account di archiviazione di origine 
* Il BLOB viene copiato dall'account di archiviazione di origine a un account di archiviazione di destinazione
* Il BLOB nell'account di archiviazione di destinazione viene importato come artefatti nel registro di sistema di destinazione. È possibile configurare la pipeline di importazione per attivare ogni volta che il BLOB di elementi viene aggiornato nell'archiviazione di destinazione.

Il trasferimento è ideale per la copia di contenuto tra due registri contenitori di Azure in cloud fisicamente disconnessi, mediati dagli account di archiviazione in ogni cloud. Per la copia di immagini da registri contenitori in cloud connessi, tra cui Hub Docker e altri fornitori di cloud, è consigliabile usare l' [importazione di immagini](container-registry-import-images.md) .

In questo articolo si usano Azure Resource Manager le distribuzioni di modelli per creare ed eseguire la pipeline di trasferimento. L'interfaccia della riga di comando di Azure viene usata per il provisioning delle risorse associate, ad esempio i segreti È consigliabile usare l'interfaccia della riga di comando di Azure versione 2.2.0 o successiva. Se è necessario installare o aggiornare l'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

Questa funzionalità è disponibile per il livello di servizio **Premium** del registro contenitori. Per informazioni sui livelli di servizio del registro e sui limiti, vedere [livelli di Registro Azure Container](container-registry-skus.md).

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="prerequisites"></a>Prerequisiti

* **Registri contenitori** : è necessario un registro di origine esistente con gli elementi da trasferire e un registro di sistema di destinazione. Il trasferimento ACR è progettato per lo spostamento tra cloud fisicamente disconnessi. Per i test, i registri di origine e di destinazione possono trovarsi nella stessa sottoscrizione di Azure o in una sottoscrizione di Azure diversa, Active Directory tenant o cloud. Se è necessario creare un registro, vedere [Guida introduttiva: creare un registro contenitori privato usando l'interfaccia della](container-registry-get-started-azure-cli.md)riga di comando di Azure. 
* **Account di archiviazione** : creare account di archiviazione di origine e di destinazione in una sottoscrizione e in una località di propria scelta. A scopo di test, è possibile usare la stessa sottoscrizione o sottoscrizioni dei registri di origine e di destinazione. Per gli scenari tra cloud, in genere si crea un account di archiviazione separato in ogni cloud. Se necessario, creare gli account di archiviazione con l'interfaccia della riga di comando di [Azure](../storage/common/storage-account-create.md?tabs=azure-cli) o altri strumenti. 

  Creare un contenitore BLOB per il trasferimento di elementi in ogni account. Ad esempio, creare un contenitore denominato *Transfer*. Due o più pipeline di trasferimento possono condividere lo stesso account di archiviazione, ma devono usare ambiti del contenitore di archiviazione diversi.
* Insiemi di credenziali delle **chiavi** : gli insiemi di credenziali delle chiavi sono necessari per archiviare i segreti dei token SAS usati per accedere agli account di archiviazione di origine e destinazione. Creare gli insiemi di credenziali delle chiavi di origine e di destinazione nella stessa sottoscrizione o sottoscrizioni di Azure dei registri di origine e di destinazione. Se necessario, creare insiemi di credenziali delle chiavi con l'interfaccia della riga di comando di [Azure](../key-vault/secrets/quick-create-cli.md) o altri strumenti.
* **Variabili di ambiente** : per i comandi di esempio di questo articolo, impostare le variabili di ambiente seguenti per gli ambienti di origine e di destinazione. Tutti gli esempi sono formattati per la shell bash.
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>Panoramica dello scenario

Per il trasferimento di immagini tra i registri vengono create le seguenti tre risorse della pipeline. Tutti vengono creati usando le operazioni PUT. Queste risorse operano nei registri di *origine* e di *destinazione* e negli account di archiviazione. 

L'autenticazione di archiviazione usa i token SAS, gestiti come segreti in Key Vault. Le pipeline usano identità gestite per leggere i segreti negli insiemi di credenziali.

* **[ExportPipeline](#create-exportpipeline-with-resource-manager)** : risorsa di lunga durata che contiene informazioni di alto livello sul Registro di sistema e sull'account di archiviazione di *origine* . Queste informazioni includono l'URI del contenitore BLOB di archiviazione di origine e l'insieme di credenziali delle chiavi che gestisce il token SAS di origine. 
* **[ImportPipeline](#create-importpipeline-with-resource-manager)** : risorsa di lunga durata che contiene informazioni di alto livello sul Registro di sistema e sull'account di archiviazione di *destinazione* . Queste informazioni includono l'URI del contenitore BLOB di archiviazione di destinazione e l'insieme di credenziali delle chiavi che gestisce il token SAS di destinazione. Un trigger di importazione è abilitato per impostazione predefinita, in modo che la pipeline venga eseguita automaticamente quando un BLOB dell'artefatto si trova nel contenitore di archiviazione di destinazione. 
* **[PipelineRun](#create-pipelinerun-for-export-with-resource-manager)** -Resource usato per richiamare una risorsa ExportPipeline o ImportPipeline.  
  * Eseguire manualmente il ExportPipeline creando una risorsa PipelineRun e specificando gli artefatti da esportare.  
  * Se è abilitato un trigger di importazione, il ImportPipeline viene eseguito automaticamente. Può anche essere eseguito manualmente usando un PipelineRun. 
  * Attualmente è possibile trasferire un massimo di **50 elementi** con ogni PipelineRun.

### <a name="things-to-know"></a>Informazioni importanti
* ExportPipeline e ImportPipeline in genere si troveranno in tenant diversi Active Directory associati ai cloud di origine e di destinazione. Questo scenario richiede identità gestite separate e insiemi di credenziali delle chiavi per le risorse di esportazione e importazione. A scopo di test, queste risorse possono essere inserite nello stesso cloud, condividendo le identità.
* Gli esempi di pipeline creano identità gestite assegnate dal sistema per accedere ai segreti di Key Vault. ExportPipelines e ImportPipelines supportano anche le identità assegnate dall'utente. In questo caso, è necessario configurare gli insiemi di credenziali delle chiavi con criteri di accesso per le identità. 

## <a name="create-and-store-sas-keys"></a>Creare e archiviare chiavi SAS

Il trasferimento usa i token di firma di accesso condiviso per accedere agli account di archiviazione negli ambienti di origine e di destinazione. Generare e archiviare i token come descritto nelle sezioni riportate di seguito.  

### <a name="generate-sas-token-for-export"></a>Genera token SAS per l'esportazione

Eseguire il comando [AZ Storage container generate-SAS][az-storage-container-generate-sas] per generare un token di firma di accesso condiviso per il contenitore nell'account di archiviazione di origine, usato per l'esportazione di artefatti.

*Autorizzazioni token consigliate*: lettura, scrittura, elenco, Aggiungi. 

Nell'esempio seguente, l'output del comando viene assegnato alla variabile di ambiente EXPORT_SAS, con prefisso il carattere '?'. Aggiornare il `--expiry` valore per l'ambiente:

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>Archiviare il token SAS per l'esportazione

Archiviare il token di firma di accesso condiviso nell'insieme di credenziali delle chiavi di Azure con [AZ Key Vault Secret set][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>Genera token SAS per l'importazione

Eseguire il comando [AZ Storage container generate-SAS][az-storage-container-generate-sas] per generare un token di firma di accesso condiviso per il contenitore nell'account di archiviazione di destinazione, usato per l'importazione di artefatti.

*Autorizzazioni token consigliate*: lettura, eliminazione, elenco

Nell'esempio seguente, l'output del comando viene assegnato alla variabile di ambiente IMPORT_SAS, con prefisso il carattere '?'. Aggiornare il `--expiry` valore per l'ambiente:

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>Archiviare il token SAS per l'importazione

Archiviare il token di firma di accesso condiviso nell'insieme di credenziali delle chiavi di Azure di destinazione usando [AZ Keys Vault Secret set][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>Creazione di ExportPipeline con Gestione risorse

Creare una risorsa ExportPipeline per il registro contenitori di origine usando la distribuzione del modello Azure Resource Manager.

Copiare ExportPipeline Gestione risorse [i file modello](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines) in una cartella locale.

Immettere i valori dei parametri seguenti nel file `azuredeploy.parameters.json` :

|Parametro  |Valore  |
|---------|---------|
|registryName     | Nome del registro contenitori di origine      |
|exportPipelineName     |  Nome scelto per la pipeline di esportazione       |
|targetUri     |  URI del contenitore di archiviazione nell'ambiente di origine (la destinazione della pipeline di esportazione).<br/>Esempio: `https://sourcestorage.blob.core.windows.net/transfer`       |
|keyVaultName     |  Nome dell'insieme di credenziali delle chiavi di origine  |
|sasTokenSecretName  | Nome del segreto del token SAS nell'insieme di credenziali delle chiavi di origine <br/>Esempio: acrexportsas

### <a name="export-options"></a>Opzioni di esportazione

La `options` proprietà per le pipeline di esportazione supporta valori booleani facoltativi. Sono consigliati i valori seguenti:

|Parametro  |Valore  |
|---------|---------|
|opzioni | OverwriteBlobs-sovrascrive i BLOB di destinazione esistenti<br/>ContinueOnErrors: continua l'esportazione degli artefatti rimanenti nel registro di sistema di origine se l'esportazione di un artefatto non riesce.

### <a name="create-the-resource"></a>Creare la risorsa

Eseguire [AZ Deployment Group create][az-deployment-group-create] per creare la risorsa. Nell'esempio seguente viene denominata la distribuzione *exportPipeline*.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

Nell'output del comando prendere nota dell'ID risorsa ( `id` ) della pipeline. È possibile archiviare questo valore in una variabile di ambiente per un uso successivo eseguendo il comando [AZ Deployment Group Show][az-deployment-group-show]. ad esempio:

```azurecli
EXPORT_RES_ID=$(az group deployment show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>Creazione di ImportPipeline con Gestione risorse 

Creare una risorsa ImportPipeline nel registro contenitori di destinazione usando Azure Resource Manager distribuzione del modello. Per impostazione predefinita, la pipeline è abilitata per l'importazione automatica quando l'account di archiviazione nell'ambiente di destinazione ha un BLOB di artefatti.

Copiare ImportPipeline Gestione risorse [i file modello](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines) in una cartella locale.

Immettere i valori dei parametri seguenti nel file `azuredeploy.parameters.json` :

Parametro  |Valore  |
|---------|---------|
|registryName     | Nome del registro contenitori di destinazione      |
|importPipelineName     |  Nome scelto per la pipeline di importazione       |
|sourceUri     |  URI del contenitore di archiviazione nell'ambiente di destinazione (origine per la pipeline di importazione).<br/>Esempio: `https://targetstorage.blob.core.windows.net/transfer/`|
|keyVaultName     |  Nome dell'insieme di credenziali delle chiavi di destinazione |
|sasTokenSecretName     |  Nome del segreto del token SAS nell'insieme di credenziali delle chiavi di destinazione<br/>Esempio: ACR Imports |

### <a name="import-options"></a>Opzioni di importazione

La `options` proprietà della pipeline di importazione supporta valori booleani facoltativi. Sono consigliati i valori seguenti:

|Parametro  |Valore  |
|---------|---------|
|opzioni | OverwriteTags-sovrascrive i tag di destinazione esistenti<br/>DeleteSourceBlobOnSuccess-Elimina il BLOB di archiviazione di origine dopo l'importazione corretta nel registro di sistema di destinazione<br/>ContinueOnErrors-continua l'importazione di elementi rimanenti nel registro di sistema di destinazione se l'importazione di un artefatto non riesce.

### <a name="create-the-resource"></a>Creare la risorsa

Eseguire [AZ Deployment Group create][az-deployment-group-create] per creare la risorsa.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json \
  --name importPipeline
```

Se si prevede di eseguire l'importazione manualmente, prendere nota dell'ID risorsa ( `id` ) della pipeline. È possibile archiviare questo valore in una variabile di ambiente per un uso successivo eseguendo il comando [AZ Deployment Group Show][az-deployment-group-show]. ad esempio:

```azurecli
IMPORT_RES_ID=$(az group deployment show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>Creare PipelineRun per l'esportazione con Gestione risorse 

Creare una risorsa PipelineRun per il registro contenitori di origine usando la distribuzione del modello Azure Resource Manager. Questa risorsa esegue la risorsa ExportPipeline creata in precedenza ed Esporta gli elementi specificati dal registro contenitori come BLOB nell'account di archiviazione di origine.

Copiare PipelineRun Gestione risorse [i file modello](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export) in una cartella locale.

Immettere i valori dei parametri seguenti nel file `azuredeploy.parameters.json` :

|Parametro  |Valore  |
|---------|---------|
|registryName     | Nome del registro contenitori di origine      |
|pipelineRunName     |  Nome scelto per l'esecuzione       |
|pipelineResourceId     |  ID risorsa della pipeline di esportazione.<br/>Esempio: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|targetName     |  Nome scelto per il BLOB di artefatti esportato nell'account di archiviazione di origine, ad esempio *BLOB*
|artifacts | Matrice di elementi di origine da trasferire, come tag o digest del manifesto<br/>Esempio: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

Eseguire [AZ Deployment Group create][az-deployment-group-create] per creare la risorsa PipelineRun. Nell'esempio seguente viene denominata la distribuzione *exportPipelineRun*.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

L'esportazione degli artefatti può richiedere diversi minuti. Quando la distribuzione viene completata correttamente, verificare l'esportazione dell'artefatto elencando il BLOB esportato nel contenitore di *trasferimento* dell'account di archiviazione di origine. Ad esempio, eseguire il comando [AZ storage BLOB list][az-storage-blob-list] :

```azurecli
az storage blob list \
  --account-name $SA_SOURCE
  --container transfer
  --output table
```

## <a name="transfer-blob-optional"></a>Transfer BLOB (facoltativo) 

Usare lo strumento AzCopy o altri metodi per [trasferire i dati BLOB](../storage/common/storage-use-azcopy-blobs.md#copy-blobs-between-storage-accounts) dall'account di archiviazione di origine all'account di archiviazione di destinazione.

Ad esempio, il [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md) comando seguente copia il BLOB dal contenitore di *trasferimento* dell'account di origine al contenitore di *trasferimento* nell'account di destinazione. Se il BLOB esiste nell'account di destinazione, viene sovrascritto. L'autenticazione usa i token SAS con le autorizzazioni appropriate per i contenitori di origine e di destinazione. I passaggi per la creazione di token non vengono visualizzati.

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>Attiva risorsa ImportPipeline

Se è stato abilitato il `sourceTriggerStatus` parametro di ImportPipeline (valore predefinito), la pipeline viene attivata dopo la copia del BLOB nell'account di archiviazione di destinazione. L'importazione degli artefatti può richiedere diversi minuti. Quando l'importazione viene completata correttamente, verificare l'importazione dell'artefatto elencando i repository nel registro contenitori di destinazione. Ad esempio, eseguire [AZ ACR repository list][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

Se non è stato abilitato il `sourceTriggerStatus` parametro della pipeline di importazione, eseguire manualmente la risorsa ImportPipeline, come illustrato nella sezione seguente. 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>Creare PipelineRun per l'importazione con Gestione risorse (facoltativo) 
 
È anche possibile usare una risorsa PipelineRun per attivare un ImportPipeline per l'importazione di elementi nel registro contenitori di destinazione.

Copiare PipelineRun Gestione risorse [i file modello](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import) in una cartella locale.

Immettere i valori dei parametri seguenti nel file `azuredeploy.parameters.json` :

|Parametro  |Valore  |
|---------|---------|
|registryName     | Nome del registro contenitori di destinazione      |
|pipelineRunName     |  Nome scelto per l'esecuzione       |
|pipelineResourceId     |  ID risorsa della pipeline di importazione.<br/>Esempio: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|sourceName     |  Nome del BLOB esistente per gli artefatti esportati nell'account di archiviazione, ad esempio *BLOB*

Eseguire [AZ Deployment Group create][az-deployment-group-create] per eseguire la risorsa.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

Quando la distribuzione viene completata correttamente, verificare l'importazione dell'artefatto elencando i repository nel registro contenitori di destinazione. Ad esempio, eseguire [AZ ACR repository list][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="delete-pipeline-resources"></a>Elimina risorse pipeline

Per eliminare una risorsa della pipeline, eliminare la relativa distribuzione Gestione risorse usando il comando [AZ Deployment Group Delete][az-deployment-group-delete] . Gli esempi seguenti eliminano le risorse della pipeline create in questo articolo:

```azurecli
az deployment group delete \
  --resource-group $SOURCE_RG \
  --name exportPipeline

az deployment group delete \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun

az deployment group delete \
  --resource-group $TARGET_RG \
  --name importPipeline  
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

* **Errori Distribuzione modelli o errori**
  * Se l'esecuzione di una pipeline ha esito negativo, esaminare la `pipelineRunErrorMessage` proprietà della risorsa di esecuzione.
  * Per gli errori di distribuzione del modello comuni, vedere [risolvere i problemi delle distribuzioni di modelli ARM](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
* **Problemi con l'esportazione o l'importazione di BLOB di archiviazione**
  * Il token di firma di accesso condiviso può essere scaduto o avere autorizzazioni insufficienti per l'esecuzione dell'esportazione o dell'importazione specificata
  * Il BLOB di archiviazione esistente nell'account di archiviazione di origine potrebbe non essere sovrascritto durante le esecuzioni di più esportazioni. Verificare che l'opzione OverwriteBlob sia impostata nell'esecuzione dell'esportazione e che il token SAS disponga di autorizzazioni sufficienti.
  * Il BLOB di archiviazione nell'account di archiviazione di destinazione potrebbe non essere eliminato dopo l'esecuzione dell'importazione riuscita. Verificare che l'opzione DeleteBlobOnSuccess sia impostata nell'esecuzione dell'importazione e che il token di firma di accesso condiviso disponga di autorizzazioni sufficienti.
  * Il BLOB di archiviazione non è stato creato o eliminato. Verificare che il contenitore specificato nell'esecuzione dell'esportazione o dell'importazione esista oppure che esista un BLOB di archiviazione specificato per l'esecuzione manuale dell'importazione. 
* **Problemi di AzCopy**
  * Vedere [risolvere i problemi relativi a AzCopy](../storage/common/storage-use-azcopy-configure.md#troubleshoot-issues).  
* **Problemi di trasferimento degli artefatti**
  * Non tutti gli artefatti o nessuno vengono trasferiti. Confermare l'ortografia degli elementi nell'esecuzione dell'esportazione e il nome del BLOB nelle esecuzioni di esportazione ed importazione. Confermare che si sta trasferendo un massimo di 50 artefatti.
  * L'esecuzione della pipeline potrebbe non essere stata completata. Un'operazione di esportazione o importazione può richiedere del tempo. 
  * Per altri problemi della pipeline, fornire l' [ID di correlazione](../azure-resource-manager/templates/deployment-history.md) della distribuzione dell'esecuzione dell'esportazione o dell'importazione al team di container Registry di Azure.


## <a name="next-steps"></a>Passaggi successivi

Per importare singole immagini del contenitore in un registro contenitori di Azure da un registro pubblico o da un altro registro privato, vedere le informazioni di riferimento sul comando [AZ ACR Import][az-acr-import] .

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/



<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-login]: /cli/azure/reference-index#az-login
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-secret-show]: /cli/azure/keyvault/secret#az-keyvault-secret-show
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-storage-container-generate-sas]: /cli/azure/storage/container#az-storage-container-generate-sas
[az-storage-blob-list]: /cli/azure/storage/blob#az-storage-blob-list
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-deployment-group-delete]: /cli/azure/deployment/group#az-deployment-group-delete
[az-deployment-group-show]: /cli/azure/deployment/group#az-deployment-group-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-import]: /cli/azure/acr#az-acr-import
