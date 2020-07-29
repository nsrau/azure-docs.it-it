---
title: Distribuzione continua per Funzioni di Azure
description: Usare le funzionalità di distribuzione continua del servizio app Azure per pubblicare le funzioni.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: e49c235e11eea17fdd1a7ff7751cc0493934d725
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83123677"
---
# <a name="continuous-deployment-for-azure-functions"></a>Distribuzione continua per Funzioni di Azure

È possibile usare funzioni di Azure per distribuire il codice in modo continuo usando l' [integrazione del controllo](functions-deployment-technologies.md#source-control)del codice sorgente. L'integrazione del controllo del codice sorgente consente un flusso di lavoro in cui un aggiornamento del codice attiva la distribuzione in Azure. Se non si ha familiarità con funzioni di Azure, vedere la Panoramica di [funzioni di Azure](functions-overview.md).

La distribuzione continua è una scelta ideale per i progetti in cui si integrano contributi multipli e frequenti. Quando si usa la distribuzione continua, si mantiene un'unica fonte di verità per il codice, che consente ai team di collaborare con facilità. È possibile configurare la distribuzione continua in funzioni di Azure dalle seguenti posizioni del codice sorgente:

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

L'unità di distribuzione per le funzioni in Azure è l'app per le funzioni. Tutte le funzioni in un'app per le funzioni vengono distribuite nello stesso momento. Dopo aver abilitato la distribuzione continua, l'accesso al codice della funzione nella portale di Azure viene configurato come di sola *lettura* , perché l'origine della verità è impostata come altrove.

## <a name="requirements-for-continuous-deployment"></a>Requisiti per la distribuzione continua

Per la riuscita della distribuzione continua, la struttura di directory deve essere compatibile con la struttura di cartelle di base prevista da funzioni di Azure.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> La distribuzione continua non è ancora supportata per le app Linux in esecuzione in un piano a consumo. 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>Configurare la distribuzione continua

Per configurare la distribuzione continua per un'app per le funzioni esistente, completare questi passaggi. I passaggi illustrano l'integrazione con un repository GitHub, ma si applicano passaggi simili per Azure Repos o altri repository del codice sorgente.

1. Nell'app per le funzioni nella [portale di Azure](https://portal.azure.com)selezionare **centro distribuzione**, selezionare **GitHub**e quindi selezionare **autorizza**. Se GitHub è già stato autorizzato, selezionare **continue (continua** ) e ignorare il passaggio successivo. 

    :::image type="content" source="./media/functions-continuous-deployment/github.png" alt-text="Centro distribuzione servizio app Azure":::

3. In GitHub selezionare **autorizza AzureAppService**.

    :::image type="content" source="./media/functions-continuous-deployment/authorize.png" alt-text="Autorizzare il servizio app Azure":::

    Immettere la password di GitHub e quindi selezionare **continue (continua**).

4. Selezionare uno dei provider di compilazione seguenti:

    * **Servizio di compilazione del servizio app**: migliore quando non è necessaria una compilazione o se è necessaria una compilazione generica.
    * **Azure Pipelines (anteprima)**: migliore quando è necessario un maggiore controllo sulla compilazione. Questo provider è attualmente disponibile in anteprima.

    Selezionare **Continua**.

5. Configurare informazioni specifiche per l'opzione di controllo del codice sorgente specificata. Per GitHub è necessario immettere o selezionare i valori per **Organization**, **repository**e **Branch**. I valori sono basati sulla posizione del codice. Quindi selezionare **continue (continua**).

    :::image type="content" source="./media/functions-continuous-deployment/github-specifics.png" alt-text="Configurare GitHub":::

6. Esaminare tutti i dettagli e quindi fare clic su **fine** per completare la configurazione della distribuzione.

Al termine del processo, tutto il codice dall'origine specificata viene distribuito nell'app. A questo punto, le modifiche apportate all'origine della distribuzione attivano una distribuzione di tali modifiche nell'app per le funzioni in Azure.

> [!NOTE]
> Dopo aver configurato l'integrazione continua, non è più possibile modificare i file di origine nel portale delle funzioni.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
