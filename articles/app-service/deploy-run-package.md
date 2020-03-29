---
title: Eseguire l'app da un pacchetto zip
description: Distribuisci il pacchetto zip dell'app con atomicità. Migliora la prevedibilità e l'affidabilità del comportamento dell'tua app durante il processo di distribuzione.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 5cc909d79b3f5ea2b4c6a3da12bc7250addbe00c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920723"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Eseguire l'app nel servizio app di Azure direttamente da un pacchetto zipRun your app in Azure App Service directly from a zip package

In [Servizio app](overview.md)di Azure è possibile eseguire le app direttamente da un file del pacchetto zip di distribuzione. Questo articolo illustra come abilitare questa funzionalità nell'app.

Tutti gli altri metodi di distribuzione nel servizio app hanno qualcosa in comune: i file */home/site/wwwroot* vengono distribuiti in *D:* . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . Poiché la stessa directory viene usata dall'app in fase di esecuzione, è possibile che la distribuzione non riesca a causa di conflitti di blocco dei file e che l'app si comporti in modo imprevedibile perché alcuni file non sono ancora aggiornati.

Al contrario, quando si esegue direttamente da un pacchetto, i file nel pacchetto non vengono copiati nella directory *wwwroot.* Al contrario, il pacchetto zip stesso viene montato direttamente come directory *wwwroot* di sola lettura. L'esecuzione diretta da un pacchetto offre diversi vantaggi:There are several benefits to running directly from a package:

- Elimina i conflitti di blocco dei file tra la distribuzione e il runtime.
- Garantisce che solo le app distribuite a pieno titolo siano in esecuzione in qualsiasi momento.
- Possono essere distribuiti in un'app di produzione (con il riavvio).
- Migliora le prestazioni delle distribuzioni Azure Resource Manager.
- Si possono ridurre i tempi di avvio a freddo, in particolare per le funzioni di JavaScript con grandi alberi del pacchetto npm.

> [!NOTE]
> Attualmente, sono supportati solo i file del pacchetto zip.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Abilitare l'esecuzione dal pacchettoEnable running from package

L'impostazione dell'app `WEBSITE_RUN_FROM_PACKAGE` consente l'esecuzione da un pacchetto. Per impostarlo, eseguire il comando seguente con l'interfaccia della riga di comando di Azure.To set it, run the following command with Azure CLI.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"`consente di eseguire l'app da un pacchetto locale all'app. È anche possibile [eseguire da un pacchetto remoto](#run-from-external-url-instead).

## <a name="run-the-package"></a>Eseguire il pacchetto

Il modo più semplice per eseguire un pacchetto nel servizio app è con il comando [di origine della distribuzione webapp dell'interfaccia](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) della riga di comando dell'interfaccia della riga di comando dell'interfaccia della riga di comando dell'app az di Azure comando config-zip. Ad esempio:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Poiché `WEBSITE_RUN_FROM_PACKAGE` l'impostazione dell'app è impostata, questo comando non estrae il contenuto del pacchetto nella directory *D:* Al contrario, carica il file zip così com'è in *D:* *packagename.txt* Se si carica il pacchetto zip in modo diverso (ad esempio [FTP](deploy-ftp.md)), è necessario creare *packagename.txt* manualmente la directory *D:*

Il comando riavvia anche l'app. Poiché `WEBSITE_RUN_FROM_PACKAGE` è impostato, il servizio app monta il pacchetto caricato come directory *wwwroot* di sola lettura ed esegue l'app direttamente da tale directory montata.

## <a name="run-from-external-url-instead"></a>Esegui invece da URL esterno

È anche possibile eseguire un pacchetto da un URL esterno, ad esempio Archiviazione BLOB di Azure.You can also run a package from an external URL, such as Azure Blob Storage. È possibile usare [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) per caricare i file di pacchetto nell'account di archiviazione BLOB. È consigliabile usare un contenitore di archiviazione privato con una firma di [accesso condiviso per](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) consentire al runtime del servizio app di accedere al pacchetto in modo sicuro. 

Dopo aver caricato il file nell'archivio BLOB e avere `WEBSITE_RUN_FROM_PACKAGE` un URL di sola distribuzione per il file, impostare l'impostazione dell'app sull'URL. L'esempio seguente fa usando l'interfaccia della riga di comando di Azure:The following example does it by using Azure CLI:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Se si pubblica un pacchetto aggiornato con lo stesso nome nell'archivio BLOB, è necessario riavviare l'app in modo che il pacchetto aggiornato venga caricato nel servizio app.

## <a name="troubleshooting"></a>Risoluzione dei problemi

- L'esecuzione diretta `wwwroot` da un pacchetto rende di sola lettura. L'app riceverà un errore se tenta di scrivere file in questa directory.
- I formati TAR e G-IP non sono supportati.
- Questa funzione non è compatibile con la [cache locale.](overview-local-cache.md)
- Per migliorare le prestazioni di avvio`WEBSITE_RUN_FROM_PACKAGE`a freddo, utilizzare l'opzione locale zip ( .

## <a name="more-resources"></a>Altre risorse

- [Distribuzione continua per il servizio app di AzureContinuous deployment for Azure App Service](deploy-continuous-deployment.md)
- [Distribuire codice con un file postale o WAR](deploy-zip.md)
