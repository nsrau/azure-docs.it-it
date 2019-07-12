---
title: Distribuzione continua per Funzioni di Azure | Documentazione Microsoft
description: Usare le funzionalità di distribuzione continua del servizio App di Azure per pubblicare funzioni di.
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
ms.openlocfilehash: dd1605aa2f5fc9e0b4bc790bae2a1c20cb3ea048
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594550"
---
# <a name="continuous-deployment-for-azure-functions"></a>Distribuzione continua per Funzioni di Azure

È possibile usare funzioni di Azure per distribuire in modo continuo il codice usando [integrazione del controllo codice sorgente](functions-deployment-technologies.md#source-control). Integrazione del controllo codice sorgente consente un flusso di lavoro in cui un aggiornamento del codice attiva la distribuzione in Azure. Se si ha familiarità con funzioni di Azure, iniziare esaminando le [Panoramica di funzioni di Azure](functions-overview.md).

La distribuzione continua è un'opzione valida per i progetti in cui si integrano più e contributi frequenti. Quando si usa la distribuzione continua, per il codice, che consente ai team di collaborare facilmente è mantenere un'unica fonte di veridicità. È possibile configurare la distribuzione continua in funzioni di Azure dai percorsi del codice sorgente seguente:

* [Repository di Azure](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

L'unità di distribuzione per le funzioni di Azure è l'app per le funzioni. Tutte le funzioni in un'app per le funzioni vengono distribuite nello stesso momento. Dopo aver abilitato la distribuzione continua, l'accesso al codice di funzione nel portale di Azure è configurato come *sola lettura* perché source of truth è impostata per essere un' posizione.

## <a name="requirements-for-continuous-deployment"></a>Requisiti per la distribuzione continua

Per la distribuzione continua abbia esito positivo, la struttura di directory deve essere compatibile con la struttura di cartelle di base che prevede che le funzioni di Azure.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>Impostare la distribuzione continua

Per configurare la distribuzione continua per un'app per le funzioni esistente, completare questi passaggi. La procedura viene illustrata l'integrazione con un repository di GitHub, ma una procedura simile anche per i repository di Azure o altri repository di codice sorgente.

1. Nell'app per le funzioni nel [portale di Azure](https://portal.azure.com), selezionare **funzionalità della piattaforma** > **Deployment Center**.

    ![Aprire Centro di distribuzione](./media/functions-continuous-deployment/platform-features.png)

2. Nelle **Deployment Center**, selezionare **GitHub**, quindi selezionare **Authorize**. Se già autorizzato GitHub, selezionare **continuazione**. 

    ![Centro di distribuzione di Azure App Service](./media/functions-continuous-deployment/github.png)

3. In GitHub, selezionare la **autorizzare AzureAppService** pulsante. 

    ![Autorizzare il servizio App di Azure](./media/functions-continuous-deployment/authorize.png)
    
    Nelle **Deployment Center** nel portale di Azure, selezionare **continua**.

4. Selezionare uno dei seguenti provider di compilazione:

    * **Servizio app di servizio di compilazione**: Migliore quando non è necessaria una compilazione o se è necessaria una compilazione generica.
    * **Le pipeline di Azure (anteprima)** : Topologia migliore quando è necessario un maggior controllo tramite la compilazione. Questo provider è attualmente in anteprima.

    ![Selezionare un provider di compilazione](./media/functions-continuous-deployment/build.png)

5. Configurare le informazioni specifiche per l'opzione di controllo di origine specificato. Per GitHub, è necessario immettere o selezionare i valori per **organizzazione**, **Repository**, e **ramo**. I valori sono basati sulla posizione del codice. Selezionare quindi **Continua**.

    ![Configurare GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Esaminare tutti i dettagli e quindi selezionare **fine** per completare la configurazione della distribuzione.

    ![Riepilogo](./media/functions-continuous-deployment/summary.png)

Al termine del processo, tutto il codice dall'origine specificata viene distribuito all'app. A questo punto, le modifiche nell'origine della distribuzione attivano una distribuzione di tali modifiche per l'app per le funzioni in Azure.

## <a name="deployment-scenarios"></a>Scenari di distribuzione

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Trasferire le funzioni esistenti nella distribuzione continua

Se le funzioni già scritto [portale di Azure](https://portal.azure.com) e si vuole scaricare il contenuto dell'app prima di passare alla distribuzione continua, andare alla **Panoramica** scheda dell'app per le funzioni. Selezionare il **scarica contenuto dell'app** pulsante.

![Scarica contenuto dell'app](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Dopo aver configurato l'integrazione continua, è non possibile modificare non è più i file di origine nel portale funzioni.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
