---
title: Run your Azure Functions from a package
description: Far eseguire le funzioni dal runtime di Funzioni di Azure tramite il montaggio di un file di pacchetto di distribuzione che contiene i file di progetto dell'app per le funzioni.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: f5d3465e0899f7e5eab213bdb6234313128b7ec8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230344"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Eseguire Funzioni di Azure da un file di pacchetto

In Azure, è possibile eseguire le funzioni direttamente da un file di pacchetto di distribuzione nell'app per le funzioni. L'altra opzione consiste nel distribuire i file nella directory `d:\home\site\wwwroot` dell'app per le funzioni.

Questo articolo descrive i vantaggi dell'esecuzione delle funzioni da un pacchetto. Viene inoltre illustrato come abilitare questa funzionalità nell'app per le funzioni.

> [!IMPORTANT]
> When deploying your functions to a Linux function app in a [Premium plan](functions-scale.md#premium-plan), you should always run from the package file and [publish your app using the Azure Functions Core Tools](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>Vantaggi dell'esecuzione da un file di pacchetto
  
Esistono diversi vantaggi nell'esecuzione da un file di pacchetto:

+ Riduce il rischio di problemi di blocco di copia dei file.
+ Possono essere distribuiti in un'app di produzione (con il riavvio).
+ È possibile sapere con sicurezza quali file sono in esecuzione nell'app.
+ Migliora le prestazioni delle [distribuzioni Azure Resource Manager](functions-infrastructure-as-code.md).
+ Si possono ridurre i tempi di avvio a freddo, in particolare per le funzioni di JavaScript con grandi alberi del pacchetto npm.

Per altre informazioni, vedere [questo annuncio](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Abilitazione di funzioni da eseguire da un pacchetto

Per abilitare l'esecuzione dell'app per le funzioni da un pacchetto, è sufficiente aggiungere un'impostazione `WEBSITE_RUN_FROM_PACKAGE` alle impostazioni dell'app per le funzioni. L'impostazione `WEBSITE_RUN_FROM_PACKAGE` deve avere almeno uno dei valori seguenti:

| Value  | Description  |
|---------|---------|
| **`1`**  | Recommended for function apps running on Windows. Esecuzione da un file di pacchetto nella cartella `d:\home\data\SitePackages` dell'app per le funzioni. If not [deploying with zip deploy](#integration-with-zip-deployment), this option requires the folder to also have a file named `packagename.txt`. Questo file contiene solo il nome del file di pacchetto nella cartella, senza spazi vuoti. |
|**`<URL>`**  | Percorso di un file di pacchetto specifico da eseguire. Quando si usa l'archiviazione BLOB, è consigliabile usare un contenitore privato con una [firma di accesso condiviso (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) per abilitare il runtime di Funzioni per l'accesso al pacchetto. È possibile usare [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) per caricare i file di pacchetto nell'account di archiviazione BLOB. When you specify a URL, you must also [sync triggers](functions-deployment-technologies.md#trigger-syncing) after you publish an updated package. |

> [!CAUTION]
> When running a function app on Windows, the external URL option yields worse cold-start performance. When deploying your function app to Windows, you should set `WEBSITE_RUN_FROM_PACKAGE` to `1` and publish with zip deployment.

Di seguito viene illustrata un'app per le funzioni configurata per l'esecuzione da un file con estensione zip ospitato in archiviazione BLOB di Azure:

![Impostazione dell'app WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Attualmente sono supportati solo i file di pacchetto con estensione zip.

## <a name="integration-with-zip-deployment"></a>Integrazione con distribuzione di file ZIP

[Zip deployment][Zip deployment for Azure Functions] is a feature of Azure App Service that lets you deploy your function app project to the `wwwroot` directory. Il progetto viene impacchettato come un file di distribuzione di file ZIP. La stesse API possono essere usate per distribuire il pacchetto nella cartella `d:\home\data\SitePackages`. Con il valore `1` dell'impostazione dell'app `WEBSITE_RUN_FROM_PACKAGE`, le API di distribuzione di file ZIP copiano il pacchetto nella cartella `d:\home\data\SitePackages` invece di estrarre i file in `d:\home\site\wwwroot`. Questa operazione crea anche il file `packagename.txt`. After a restart, the package is mounted to `wwwroot` as a read-only filesystem. Per altre informazioni sulla distribuzione di file ZIP, vedere [Distribuzione di file ZIP nelle Funzioni di Azure](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Aggiungere l'impostazione WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi

- Run From Package makes `wwwroot` read-only, so you will receive an error when writing files to this directory.
- Tar and gzip formats are not supported.
- This feature does not compose with local cache.
- For improved cold-start performance, use the local Zip option (`WEBSITE_RUN_FROM_PACKAGE`=1).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
