---
title: Distribuzione continua per Funzioni di Azure | Documentazione Microsoft
description: Usare le funzionalità di distribuzione continua del servizio App di Azure per pubblicare le funzioni.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: d4d2f24a0a7b1f01627ed2cea4a5732ca0e001c9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068471"
---
# <a name="continuous-deployment-for-azure-functions"></a>Distribuzione continua per Funzioni di Azure

Funzioni di Azure consente di distribuire il codice in modo continuo tramito [integrazione del controllo codice sorgente](functions-deployment-technologies.md#source-control). In questo modo un flusso di lavoro in cui codice Aggiorna trigger di distribuzione in Azure. Se si ha familiarità con funzioni di Azure, iniziare a usare il [Panoramica di funzioni di Azure](functions-overview.md).

Distribuzione continua è un'ottima opzione per i progetti in cui si esegue l'integrazione di più e contributi frequenti. Consente inoltre di mantenere un'unica fonte di dati reali per il codice della funzione. È possibile configurare la distribuzione continua in funzioni di Azure dai percorsi del codice sorgente seguente:

* [Repository di Azure](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

L'unità di distribuzione per le funzioni di Azure è l'app per le funzioni. Ciò significa che tutte le funzioni in un'app per le funzioni vengono distribuite nello stesso momento. Dopo aver abilitata la distribuzione continua, l'accesso al codice di funzione nel portale di Azure è configurato come *sola lettura*, dal momento che source of truth viene impostata come un' posizione.

## <a name="requirements-for-continuous-deployment"></a>Requisiti per la distribuzione continua

Per la distribuzione continua abbia esito positivo, la struttura di directory deve essere compatibile con la seguente struttura di cartelle di base che prevede che le funzioni di Azure:

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>Impostare la distribuzione continua

Usare la procedura seguente per configurare la distribuzione continua per un'app per le funzioni esistente. Questa procedura viene illustrata l'integrazione con un repository di GitHub, ma una procedura simile anche per i repository di Azure o altri repository di codice sorgente.

1. Nell'app per le funzioni nel [portale di Azure](https://portal.azure.com), selezionare **funzionalità della piattaforma** > **Deployment center**.

    ![Per aprire il centro di distribuzione](./media/functions-continuous-deployment/platform-features.png)

2. Nel **Deployment Center**, selezionare **GitHub** per **controllo del codice sorgente** > **Authorize**.

    ![Centro di distribuzione](./media/functions-continuous-deployment/github.png)

3. Selezionare **autorizzare AzureAppService** > **continuare**.

    ![Autorizzazione](./media/functions-continuous-deployment/authorize.png)

4. Selezionare uno dei seguenti provider di compilazione:

    * **Servizio app di servizio di compilazione** : migliore quando non è necessaria una compilazione o se è necessaria una compilazione generica.
    * **Le pipeline di Azure (anteprima)** : migliore quando è necessario maggiore controllo sulla compilazione. Questo provider è attualmente in anteprima.

    ![Selezione di un provider di compilazione](./media/functions-continuous-deployment/build.png)

5. Configurare le informazioni specifiche per l'opzione di controllo di origine specificato. Per GitHub, è necessario fornire il **organizzazione**, **Repository**, e **ramo** in cui si trova il codice. Selezionare quindi **Continua**.

    ![Configurazione di GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Infine, esaminare tutti i dettagli e selezionare **fine** per completare la configurazione della distribuzione.

    ![Riepilogo](./media/functions-continuous-deployment/summary.png)

Al termine del processo, tutto il codice dall'origine specificata viene distribuito all'app. A questo punto, le modifiche nell'origine della distribuzione attivano una distribuzione di tali modifiche per l'app per le funzioni in Azure.

## <a name="deployment-scenarios"></a>Scenari di distribuzione

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Trasferire le funzioni esistenti nella distribuzione continua

Se le funzioni già scritto [portale di Azure](https://portal.azure.com) e si desidera scaricare il contenuto dell'app prima di passare alla distribuzione continua, deve passare alle **Panoramica** scheda della funzione App e scegliere il **scarica contenuto dell'app** pulsante.

![Download di contenuto dell'app](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Dopo aver configurato l'integrazione continua, è non possibile modificare non è più i file di origine nel portale funzioni.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
