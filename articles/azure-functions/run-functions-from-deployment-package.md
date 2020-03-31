---
title: Eseguire le funzioni di Azure da un pacchettoRun your Azure Functions from a package
description: Far eseguire le funzioni dal runtime di Funzioni di Azure tramite il montaggio di un file di pacchetto di distribuzione che contiene i file di progetto dell'app per le funzioni.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: d40896d6a4659945dbeda9ca965366f0b2ca4bd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365272"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Eseguire Funzioni di Azure da un file di pacchetto

In Azure, è possibile eseguire le funzioni direttamente da un file di pacchetto di distribuzione nell'app per le funzioni. L'altra opzione consiste nel distribuire i file nella directory `d:\home\site\wwwroot` dell'app per le funzioni.

Questo articolo descrive i vantaggi dell'esecuzione delle funzioni da un pacchetto. Viene inoltre illustrato come abilitare questa funzionalità nell'app per le funzioni.

> [!IMPORTANT]
> Quando si distribuiscono le funzioni in un'app per le funzioni Linux in un [piano Premium,](functions-scale.md#premium-plan)è consigliabile eseguire sempre dal file del pacchetto e [pubblicare l'app usando gli](functions-run-local.md#project-file-deployment)strumenti di base di Funzioni di Azure.

## <a name="benefits-of-running-from-a-package-file"></a>Vantaggi dell'esecuzione da un file di pacchetto
  
Esistono diversi vantaggi nell'esecuzione da un file di pacchetto:

+ Riduce il rischio di problemi di blocco di copia dei file.
+ Possono essere distribuiti in un'app di produzione (con il riavvio).
+ È possibile sapere con sicurezza quali file sono in esecuzione nell'app.
+ Migliora le prestazioni delle [distribuzioni Azure Resource Manager](functions-infrastructure-as-code.md).
+ Si possono ridurre i tempi di avvio a freddo, in particolare per le funzioni di JavaScript con grandi alberi del pacchetto npm.

Per ulteriori informazioni, vedere [questo annuncio](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Abilitazione di funzioni da eseguire da un pacchetto

Per abilitare l'esecuzione dell'app per le funzioni da un pacchetto, è sufficiente aggiungere un'impostazione `WEBSITE_RUN_FROM_PACKAGE` alle impostazioni dell'app per le funzioni. L'impostazione `WEBSITE_RUN_FROM_PACKAGE` deve avere almeno uno dei valori seguenti:

| valore  | Descrizione  |
|---------|---------|
| **`1`**  | Consigliato per le app per le funzioni in esecuzione su Windows. Esecuzione da un file di pacchetto nella cartella `d:\home\data\SitePackages` dell'app per le funzioni. Se non si esegue la [distribuzione con zip deploy](#integration-with-zip-deployment), `packagename.txt`questa opzione richiede che la cartella disponga anche di un file denominato . Questo file contiene solo il nome del file di pacchetto nella cartella, senza spazi vuoti. |
|**`<URL>`**  | Percorso di un file di pacchetto specifico da eseguire. Quando si usa l'archiviazione BLOB, è consigliabile usare un contenitore privato con una [firma di accesso condiviso (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) per abilitare il runtime di Funzioni per l'accesso al pacchetto. È possibile usare [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) per caricare i file di pacchetto nell'account di archiviazione BLOB. Quando si specifica un URL, è necessario [sincronizzare](functions-deployment-technologies.md#trigger-syncing) anche i trigger dopo la pubblicazione di un pacchetto aggiornato. |

> [!CAUTION]
> Quando si esegue un'app per le funzioni in Windows, l'opzione URL esterno produce prestazioni di avvio a freddo peggiori. Quando si distribuisce l'app per `WEBSITE_RUN_FROM_PACKAGE` `1` le funzioni in Windows, è necessario impostare su e pubblicare con la distribuzione zip.

Di seguito viene illustrata un'app per le funzioni configurata per l'esecuzione da un file con estensione zip ospitato in archiviazione BLOB di Azure:

![Impostazione dell'app WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Attualmente sono supportati solo i file di pacchetto con estensione zip.

## <a name="integration-with-zip-deployment"></a>Integrazione con distribuzione di file ZIP

[Distribuzione di file ZIP][Zip deployment for Azure Functions] è una funzione del Servizio app di Azure che consente di distribuire il progetto dell'app per le funzioni nella directory `wwwroot`. Il progetto viene impacchettato come un file di distribuzione di file ZIP. La stesse API possono essere usate per distribuire il pacchetto nella cartella `d:\home\data\SitePackages`. Con il valore `1` dell'impostazione dell'app `WEBSITE_RUN_FROM_PACKAGE`, le API di distribuzione di file ZIP copiano il pacchetto nella cartella `d:\home\data\SitePackages` invece di estrarre i file in `d:\home\site\wwwroot`. Questa operazione crea anche il file `packagename.txt`. Dopo un riavvio, il `wwwroot` pacchetto viene montato come file system di sola lettura. Per altre informazioni sulla distribuzione di file ZIP, vedere [Distribuzione di file ZIP nelle Funzioni di Azure](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Aggiungere l'impostazione WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>Risoluzione dei problemi

- Esegui dal `wwwroot` pacchetto rende di sola lettura, pertanto verrà visualizzato un errore durante la scrittura di file in questa directory.
- I formati Tar e gzip non sono supportati.
- Questa funzionalità non viene composta con la cache locale.
- Per migliorare le prestazioni di avvio`WEBSITE_RUN_FROM_PACKAGE`a freddo, utilizzare l'opzione locale zip ( .
- Esegui dal pacchetto non è compatibile`SCM_DO_BUILD_DURING_DEPLOYMENT=true`con l'opzione di personalizzazione della distribuzione ( ), l'istruzione di compilazione verrà ignorata durante la distribuzione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
