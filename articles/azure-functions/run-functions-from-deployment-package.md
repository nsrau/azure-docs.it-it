---
title: Eseguire Funzioni di Azure da un pacchetto | Microsoft Docs
description: Far eseguire le funzioni dal runtime di Funzioni di Azure tramite il montaggio di un file di pacchetto di distribuzione che contiene i file di progetto dell'app per le funzioni.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: glenga
ms.openlocfilehash: 549768473460dcb97b66c3589d71c02039220605
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389955"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Eseguire Funzioni di Azure da un file di pacchetto

In Azure, è possibile eseguire le funzioni direttamente da un file di pacchetto di distribuzione nell'app per le funzioni. L'altra opzione consiste nel distribuire i file nella directory `d:\home\site\wwwroot` dell'app per le funzioni.

Questo articolo descrive i vantaggi dell'esecuzione delle funzioni da un pacchetto. Viene inoltre illustrato come abilitare questa funzionalità nell'app per le funzioni.

> [!IMPORTANT]
> Quando si distribuiscono le funzioni in un'app per le funzioni Linux in un [piano Premium](functions-scale.md#premium-plan), è sempre necessario eseguire dal file del pacchetto e [pubblicare l'app usando il Azure Functions Core Tools](functions-run-local.md#project-file-deployment).

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
| **`1`**  | Consigliato per le app per le funzioni in esecuzione in Windows. Esecuzione da un file di pacchetto nella cartella `d:\home\data\SitePackages` dell'app per le funzioni. Se non si [distribuisce con zip deploy](#integration-with-zip-deployment), questa opzione richiede che anche la cartella disponga di un file denominato `packagename.txt`. Questo file contiene solo il nome del file di pacchetto nella cartella, senza spazi vuoti. |
|**`<url>`**  | Percorso di un file di pacchetto specifico da eseguire. Quando si usa l'archiviazione BLOB, è consigliabile usare un contenitore privato con una [firma di accesso condiviso (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) per abilitare il runtime di Funzioni per l'accesso al pacchetto. È possibile usare [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) per caricare i file di pacchetto nell'account di archiviazione BLOB.         |

> [!CAUTION]
> Quando si esegue un'app per le funzioni in Windows, l'opzione URL esterno produce prestazioni di avvio a freddo inferiori. Quando si distribuisce l'app per le funzioni in Windows, è necessario impostare `WEBSITE_RUN_FROM_PACKAGE` su `1` e pubblicare con la distribuzione zip.

Di seguito viene illustrata un'app per le funzioni configurata per l'esecuzione da un file con estensione zip ospitato in archiviazione BLOB di Azure:

![Impostazione dell'app WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Attualmente sono supportati solo i file di pacchetto con estensione zip.

## <a name="integration-with-zip-deployment"></a>Integrazione con distribuzione di file ZIP

La [distribuzione zip][Zip deployment for Azure Functions] è una funzionalità di app Azure servizio che consente di distribuire il progetto di app per le funzioni nella directory `wwwroot`. Il progetto viene impacchettato come un file di distribuzione di file ZIP. La stesse API possono essere usate per distribuire il pacchetto nella cartella `d:\home\data\SitePackages`. Con il valore `1` dell'impostazione dell'app `WEBSITE_RUN_FROM_PACKAGE`, le API di distribuzione di file ZIP copiano il pacchetto nella cartella `d:\home\data\SitePackages` invece di estrarre i file in `d:\home\site\wwwroot`. Questa operazione crea anche il file `packagename.txt`. Dopo un riavvio, il pacchetto viene montato in `wwwroot` come file System di sola lettura. Per altre informazioni sulla distribuzione di file ZIP, vedere [Distribuzione di file ZIP nelle Funzioni di Azure](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Aggiungere l'impostazione WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi

- Esegui da pacchetto rende di sola lettura `wwwroot`, quindi viene visualizzato un errore durante la scrittura di file in questa directory.
- I formati tar e gzip non sono supportati.
- Questa funzionalità non è composta dalla cache locale.
- Per migliorare le prestazioni di avvio a freddo, usare l'opzione zip locale (`WEBSITE_RUN_FROM_PACKAGE` = 1).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
