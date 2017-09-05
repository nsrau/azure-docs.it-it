---
title: Creare la prima funzione dall'interfaccia della riga di comando di Azure| Microsoft Docs
description: Informazioni su come creare la prima funzione di Azure per l'esecuzione senza server tramite l'interfaccia della riga di comando di Azure.
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 08/22/2017
ms.topic: hero-article
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 8bd3e4bb7423db44c48b04f25edcf1074e6ea0bd
ms.contentlocale: it-it
ms.lasthandoff: 08/23/2017

---

# <a name="create-your-first-function-using-the-azure-cli"></a>Creare la prima funzione usando l'interfaccia della riga di comando di Azure

Questa esercitazione di avvio rapido illustra come usare Funzioni di Azure per creare la prima funzione. Si userà l'interfaccia della riga di comando di Azure per creare un'app per le funzioni, ovvero l'infrastruttura senza server che ospita la funzione. Il codice della funzione viene distribuito da un repository GitHub di esempio.    

È possibile eseguire queste procedure con un computer Mac, Windows o Linux. 

## <a name="prerequisites"></a>Prerequisiti 

Prima di eseguire questo esempio, è necessario disporre di quanto segue:

+ Un account [GitHub](https://github.com) attivo. 
+ Una sottoscrizione di Azure attiva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui vengono distribuite e gestite risorse di Azure come app per le funzioni, database e account di archiviazione.

Nell'esempio seguente viene creato il gruppo di risorse denominato `myResourceGroup`.  
Se non si usa Cloud Shell, prima è necessario accedere usando `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```


## <a name="create-an-azure-storage-account"></a>Creare un account di Archiviazione di Azure

Funzioni usa un account di archiviazione di Azure per gestire lo stato e altre informazioni sulle funzioni. Creare un account di archiviazione nel gruppo di risorse creato usando il comando [az storage account create](/cli/azure/storage/account#create).

Nel comando seguente sostituire il segnaposto `<storage_name>` con il nome globalmente univoco dell'account di archiviazione. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

Al termine della creazione dell'account di archiviazione, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

Per ospitare l'esecuzione delle funzioni è necessaria un'app per le funzioni. L'app per le funzioni offre un ambiente per l'esecuzione senza server del codice di funzione. Consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione delle risorse. Creare un'app per le funzioni usando il comando [az functionapp create](/cli/azure/functionapp#create). 

Nel comando seguente sostituire il segnaposto `<app_name>` con il nome univoco dell'app per le funzioni e il nome dell'account di archiviazione con `<storage_name>`. Dato che verrà usato come dominio DNS predefinito per l'app per le funzioni, è necessario che `<app_name>` sia univoco tra tutte le app in Azure. 

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--consumption-plan-location westeurope
```
Per impostazione predefinita, viene creata un'app per le funzioni con il piano di hosting a consumo, in base al quale le risorse vengono aggiunte dinamicamente in base alle esigenze delle funzioni e si paga solo quando le funzioni sono in esecuzione. Per altre informazioni, vedere [Scegliere il piano di hosting corretto](functions-scale.md). 

Al termine della creazione dell'app per le funzioni, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

Dopo aver creato l'app per le funzioni, è possibile ora distribuire il codice di funzione effettivo dal repository GitHub di esempio.

## <a name="deploy-your-function-code"></a>Distribuire il codice di funzione  

Esistono diversi modi per creare il codice di funzione nella nuova app per le funzioni. In questo argomento viene effettuata la connessione a un repository di esempio in GitHub. Come in precedenza, nel codice seguente sostituire il segnaposto `<app_name>` con il nome dell'app per le funzioni creata. 

```azurecli-interactive
az functionapp deployment source config --name <app_name> --resource-group myResourceGroup --branch master \
--repo-url https://github.com/Azure-Samples/functions-quickstart \
--manual-integration 
```
Dopo aver impostato l'origine di distribuzione, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente (i valori null sono stati rimossi per una migliore leggibilità):

```json
{
  "branch": "master",
  "deploymentRollbackEnabled": false,
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myResourceGroup/...",
  "isManualIntegration": true,
  "isMercurial": false,
  "location": "West Europe",
  "name": "quickstart",
  "repoUrl": "https://github.com/Azure-Samples/functions-quickstart",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.Web/sites/sourcecontrols"
}
```

## <a name="test-the-function"></a>Testare la funzione

Usare cURL per testare la funzione distribuita in un computer Mac o Linux oppure usando Bash per Windows. Eseguire il comando cURL seguente, sostituendo il segnaposto `<app_name>` con il nome dell'app per le funzioni. Aggiungere la stringa di query `&name=<yourname>` all'URL.

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![Risposta della funzione visualizzata in un browser.](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-curl.png)  

Se nella riga di comando non è disponibile un cURL, immettere lo stesso URL nella barra degli indirizzi del browser Web. Sostituire il segnaposto `<app_name>` con il nome dell'app per le funzioni, aggiungere la stringa di query `&name=<yourname>` all'URL ed eseguire la richiesta. 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![Risposta della funzione visualizzata in un browser.](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-browser.png)  

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide di avvio rapido di questa raccolta si basano sulla presente guida di avvio rapido. Se si prevede di continuare a usare le guide di avvio rapido successive o le esercitazioni, non pulire le risorse create in questa guida di avvio rapido. Se non si prevede di continuare, usare il comando seguente per eliminare tutte le risorse create da questa guida di avvio rapido:

```azurecli-interactive
az group delete --name myResourceGroup
```
Quando richiesto, digitare `y`.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

