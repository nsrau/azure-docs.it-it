---
title: Eseguire l'app da un pacchetto ZIP
description: Distribuisci il pacchetto ZIP dell'app con l'atomicità. Migliorare la prevedibilità e l'affidabilità del comportamento dell'app durante il processo di distribuzione ZIP.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 5cc909d79b3f5ea2b4c6a3da12bc7250addbe00c
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945838"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Eseguire l'app nel servizio app Azure direttamente da un pacchetto ZIP

In [app Azure servizio](overview.md)è possibile eseguire le app direttamente da un file di pacchetto zip di distribuzione. Questo articolo illustra come abilitare questa funzionalità nell'app.

Tutti gli altri metodi di distribuzione nel servizio app hanno un elemento comune: i file vengono distribuiti in *D:\home\site\wwwroot* nell'app (o */Home/site/wwwroot* per le app Linux). Poiché la stessa directory viene usata dall'app in fase di esecuzione, è possibile che la distribuzione abbia esito negativo a causa di conflitti di blocco del file e che l'applicazione si comporti in modo imprevedibile perché alcuni file non sono stati ancora aggiornati.

Al contrario, quando si esegue direttamente da un pacchetto, i file nel pacchetto non vengono copiati nella directory *wwwroot* Il pacchetto ZIP stesso viene invece montato direttamente come directory *wwwroot* di sola lettura. L'esecuzione diretta da un pacchetto presenta diversi vantaggi:

- Elimina i conflitti di blocco del file tra distribuzione e Runtime.
- Garantisce che solo le app distribuite in modo completo vengano eseguite in qualsiasi momento.
- Possono essere distribuiti in un'app di produzione (con il riavvio).
- Migliora le prestazioni delle distribuzioni Azure Resource Manager.
- Si possono ridurre i tempi di avvio a freddo, in particolare per le funzioni di JavaScript con grandi alberi del pacchetto npm.

> [!NOTE]
> Attualmente sono supportati solo i file di pacchetto ZIP.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Abilita esecuzione dal pacchetto

L'impostazione dell'app `WEBSITE_RUN_FROM_PACKAGE` Abilita l'esecuzione da un pacchetto. Per impostarlo, eseguire il comando seguente con l'interfaccia della riga di comando di Azure.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` consente di eseguire l'app da un pacchetto locale all'app. È anche possibile [eseguire da un pacchetto remoto](#run-from-external-url-instead).

## <a name="run-the-package"></a>Eseguire il pacchetto

Il modo più semplice per eseguire un pacchetto nel servizio app è con l'interfaccia della riga di comando di Azure [AZ webapp Deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) . Ad esempio:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Poiché l'impostazione dell'app `WEBSITE_RUN_FROM_PACKAGE` è impostata, questo comando non estrae il contenuto del pacchetto nella directory *D:\home\site\wwwroot* dell'app. Al contrario, carica il file ZIP così com'è in *D:\home\data\SitePackages*e crea un file *PackageName. txt* nella stessa directory che contiene il nome del pacchetto zip da caricare in fase di esecuzione. Se si carica il pacchetto ZIP in modo diverso (ad esempio [FTP](deploy-ftp.md)), è necessario creare manualmente la directory *D:\home\data\SitePackages* e il file *PackageName. txt* .

Il comando Riavvia anche l'app. Poiché `WEBSITE_RUN_FROM_PACKAGE` è impostato, il servizio app monta il pacchetto caricato come directory *wwwroot* di sola lettura ed esegue l'app direttamente dalla directory montata.

## <a name="run-from-external-url-instead"></a>Esegui da URL esterno

È anche possibile eseguire un pacchetto da un URL esterno, ad esempio archiviazione BLOB di Azure. È possibile usare [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) per caricare i file di pacchetto nell'account di archiviazione BLOB. È consigliabile usare un contenitore di archiviazione privata con una [firma di accesso condiviso (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) per consentire al runtime del servizio app di accedere in modo sicuro al pacchetto. 

Dopo aver caricato il file nell'archiviazione BLOB e avere un URL di firma di accesso condiviso per il file, impostare l'impostazione dell'app `WEBSITE_RUN_FROM_PACKAGE` sull'URL. L'esempio seguente esegue questa operazione usando l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Se si pubblica un pacchetto aggiornato con lo stesso nome nell'archivio BLOB, è necessario riavviare l'app in modo che il pacchetto aggiornato venga caricato nel servizio app.

## <a name="troubleshooting"></a>Risoluzione dei problemi

- L'esecuzione diretta da un pacchetto rende `wwwroot` di sola lettura. L'app riceverà un errore se tenta di scrivere file in questa directory.
- I formati TAR e GZIP non sono supportati.
- Questa funzionalità non è compatibile con la [cache locale](overview-local-cache.md).
- Per migliorare le prestazioni di avvio a freddo, usare l'opzione zip locale (`WEBSITE_RUN_FROM_PACKAGE`= 1).

## <a name="more-resources"></a>Altre risorse

- [Distribuzione continua per il servizio app Azure](deploy-continuous-deployment.md)
- [Distribuire il codice con un file ZIP o WAR](deploy-zip.md)
