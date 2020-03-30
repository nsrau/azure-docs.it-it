---
title: Distribuzione continua per Funzioni di Azure
description: Usare le funzionalità di distribuzione continua del servizio app di Azure per pubblicare le funzioni.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: cc1e100a0c2e652ab081869409fd24dbf88017a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277024"
---
# <a name="continuous-deployment-for-azure-functions"></a>Distribuzione continua per Funzioni di Azure

È possibile usare Funzioni di Azure per distribuire il codice in modo continuo usando l'integrazione del [controllo del codice sorgente.](functions-deployment-technologies.md#source-control) L'integrazione del controllo del codice sorgente consente un flusso di lavoro in cui un aggiornamento del codice attiva la distribuzione in Azure.Source control integration enables a workflow in which a code update triggers deployment to Azure. Se non si ha familiarità con Funzioni di Azure, vedere la [panoramica](functions-overview.md)di Funzioni di Azure.If you're new to Azure Functions, get started by reviewing the Azure Functions overview .

La distribuzione continua è una buona opzione per i progetti in cui si integrano contributi multipli e frequenti. Quando si usa la distribuzione continua, si mantiene un'unica fonte di verità per il codice, che consente ai team di collaborare facilmente. È possibile configurare la distribuzione continua in Funzioni di Azure dai seguenti percorsi del codice sorgente:You can configure continuous deployment in Azure Functions from the following source code locations:

* [Repos di AzureAzure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

L'unità di distribuzione per le funzioni in Azure è l'app per le funzioni. Tutte le funzioni in un'app per le funzioni vengono distribuite contemporaneamente. Dopo aver abilitato la distribuzione continua, l'accesso al codice della funzione nel portale di Azure è configurato come *di sola lettura* perché l'origine della verità è impostata per essere altrove.

## <a name="requirements-for-continuous-deployment"></a>Requisiti per la distribuzione continuaRequirements for continuous deployment

Affinché la distribuzione continua abbia esito positivo, la struttura di directory deve essere compatibile con la struttura di cartelle di base prevista da Funzioni di Azure.For continuous deployment to succeed, your directory structure must be compatible with the basic folder structure that Azure Functions expects.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> La distribuzione continua non è ancora supportata per le app Linux in esecuzione in un piano di consumo. 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>Configurare la distribuzione continua

Per configurare la distribuzione continua per un'app per le funzioni esistente, completare questi passaggi. I passaggi illustrano l'integrazione con un repository GitHub, ma passaggi simili si applicano ai repository di Azure o ad altri repository di codice sorgente.

1. Nell'app per le funzioni nel [portale di Azure](https://portal.azure.com)selezionare Centro**distribuzione**delle funzionalità > della **piattaforma.**

    ![Centro di distribuzione aperto](./media/functions-continuous-deployment/platform-features.png)

2. In **Centro distribuzione**selezionare **GitHub**e quindi **Autorizza**. Se GitHub è già stato autorizzato, selezionare **Continua**. 

    ![Centro di distribuzione del servizio app di AzureAzure App Service Deployment Center](./media/functions-continuous-deployment/github.png)

3. In GitHub selezionare il **pulsante Autorizza AzureAppService.In** GitHub, select the Authorize AzureAppService button. 

    ![Autorizzare il servizio app di AzureAuthorize Azure App Service](./media/functions-continuous-deployment/authorize.png)
    
    In Centro distribuzione nel portale di Azure selezionare Continua .In **Deployment Center** in the Azure portal, select **Continue**.

4. Selezionare uno dei seguenti provider di compilazione:

    * Servizio di compilazione del **servizio app:** ideale quando non è necessaria una compilazione o se è necessaria una compilazione generica.
    * **Pipeline di Azure (anteprima):** è ideale quando è necessario un maggiore controllo sulla compilazione. Questo provider è attualmente in anteprima.

    ![Selezionare un provider di compilazione](./media/functions-continuous-deployment/build.png)

5. Configurare le informazioni specifiche dell'opzione del controllo del codice sorgente specificata. Per GitHub, è necessario immettere o selezionare i valori per **Organizzazione**, **Repository**e **Branch**. I valori sono basati sulla posizione del codice. Selezionare quindi **Continua**.

    ![Configurare GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Esaminare tutti i dettagli e quindi selezionare **Fine** per completare la configurazione di distribuzione.

    ![Riepilogo](./media/functions-continuous-deployment/summary.png)

Al termine del processo, tutto il codice dell'origine specificata viene distribuito all'app. A questo punto, le modifiche nell'origine di distribuzione attivano una distribuzione di tali modifiche all'app per le funzioni in Azure.At that point, changes in the deployment source trigger a deployment of those changes to your function app in Azure.

## <a name="deployment-scenarios"></a>Scenari di distribuzione

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Trasferire le funzioni esistenti nella distribuzione continua

Se sono già state scritte funzioni nel portale di [Azure](https://portal.azure.com) e si vuole scaricare il contenuto dell'app prima di passare alla distribuzione continua, passare alla scheda **Panoramica** dell'app per le funzioni. Seleziona il pulsante **Scarica contenuto dell'app.**

![Scaricare il contenuto dell'app](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Dopo aver configurato l'integrazione continua, non è più possibile modificare i file di origine nel portale Funzioni.After you configure continuous integration, you can no longer edit your source files in the Functions portal.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
