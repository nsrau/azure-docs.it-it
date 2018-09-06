---
title: Eseguire Funzioni di Azure da un pacchetto | Microsoft Docs
description: Far eseguire le funzioni dal runtime di Funzioni di Azure tramite il montaggio di un file di pacchetto di distribuzione che contiene i file di progetto dell'app per le funzioni.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
ms.service: functions
ms.devlang: multiple
ms.topic: conceptual
ms.workload: na
ms.date: 08/22/2018
ms.author: glenga
ms.openlocfilehash: 2b9cc3618bf21eac268e3c25f08b80124d52e6af
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669292"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Eseguire Funzioni di Azure da un file di pacchetto

> [!NOTE]
> La funzionalità descritta in questo articolo è attualmente in anteprima. Non è disponibile per le funzioni in Linux.

In Azure, è possibile eseguire le funzioni direttamente da un file di pacchetto di distribuzione nell'app per le funzioni. L'altra opzione consiste nel distribuire i file di progetto della funzione nella directory `d:\home\site\wwwroot` dell'app per le funzioni.

Questo articolo descrive i vantaggi dell'esecuzione delle funzioni da un pacchetto. Viene inoltre illustrato come abilitare questa funzionalità nell'app per le funzioni.

## <a name="benefits-of-running-from-a-package-file"></a>Vantaggi dell'esecuzione da un file di pacchetto
  
Esistono diversi vantaggi nell'esecuzione da un file di pacchetto:

+ Riduce il rischio di problemi di blocco di copia dei file.
+ Possono essere distribuiti in un'app di produzione (con il riavvio).
+ È possibile sapere con sicurezza quali file sono in esecuzione nell'app.
+ Migliora le prestazioni delle [distribuzioni Azure Resource Manager](functions-infrastructure-as-code.md).
+ Può ridurre il tempo di avvio a freddo delle funzioni JavaScript.

Per altre informazioni, vedere [questo annuncio](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Abilitazione di funzioni da eseguire da un pacchetto

Per abilitare l'esecuzione dell'app per le funzioni da un pacchetto, è sufficiente aggiungere un'impostazione `WEBSITE_RUN_FROM_ZIP` alle impostazioni dell'app per le funzioni. L'impostazione `WEBSITE_RUN_FROM_ZIP` deve avere almeno uno dei valori seguenti:

| Valore  | DESCRIZIONE  |
|---------|---------|
|**`<url>`**  | Percorso di un file di pacchetto specifico da eseguire. Quando si usa l'archiviazione BLOB, è consigliabile usare un contenitore privato con una [firma di accesso condiviso (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas) per abilitare il runtime di Funzioni per l'accesso al pacchetto. È possibile usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per caricare i file di pacchetto nell'account di archiviazione BLOB.         |
| **`1`**  | Esecuzione da un file di pacchetto nella cartella `d:\home\data\SitePackages` dell'app per le funzioni. Questa opzione richiede che la cartella abbia anche un file denominato `packagename.txt`. Questo file contiene solo il nome del file di pacchetto nella cartella, senza spazi vuoti. |

Di seguito viene illustrata un'app per le funzioni configurata per l'esecuzione da un file con estensione zip ospitato in archiviazione BLOB di Azure:

![Impostazione dell'app WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Attualmente sono supportati solo i file di pacchetto con estensione zip.

## <a name="integration-with-zip-deployment"></a>Integrazione con distribuzione di file ZIP

[Distribuzione di file ZIP][Zip deployment for Azure Functions] è una funzione del Servizio app di Azure che consente di distribuire il progetto dell'app per le funzioni nella directory `wwwroot`. Il progetto viene impacchettato come un file di distribuzione di file ZIP. La stesse API possono essere usate per distribuire il pacchetto nella cartella `d:\home\data\SitePackages`. Con il valore `1` dell'impostazione dell'app `WEBSITE_RUN_FROM_ZIP`, le API di distribuzione di file ZIP copiano il pacchetto nella cartella `d:\home\data\SitePackages` invece di estrarre i file in `d:\home\site\wwwroot`. Questa operazione crea anche il file `packagename.txt`. L'app per le funzioni viene quindi eseguita dal pacchetto dopo il riavvio e `wwwroot` diventa di sola lettura. Per altre informazioni sulla distribuzione di file ZIP, vedere [Distribuzione di file ZIP nelle Funzioni di Azure](deployment-zip-push.md).

## <a name="adding-the-websiterunfromzip-setting"></a>Aggiunta dell'impostazione WEBSITE_RUN_FROM_ZIP

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
