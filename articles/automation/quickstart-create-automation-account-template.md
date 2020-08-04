---
title: 'Avvio rapido: Creare un account di automazione - Modello di Azure'
titleSuffix: Azure Automation
description: Questo argomento di avvio rapido illustra come creare un account di automazione con il modello di Azure Resource Manager.
services: automation
documentationcenter: na
author: mgoedtel
Customer intent: I want to create an Automation account by using an Azure Resource Manager template so that I can automate processes with runbooks.
ms.service: automation
ms.devlang: na
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 07/23/2020
ms.author: magoedte
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 4246d8324eebe15b314393fc4a26adcaf12c9f6f
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87117266"
---
# <a name="quickstart-create-an-automation-account-by-using-arm-template"></a>Avvio rapido: Creare un account di automazione con il modello di Resource Manager

Automazione di Azure offre un servizio di automazione e configurazione basato sul cloud che supporta la gestione coerente di ambienti Azure e non Azure. Questo argomento di avvio rapido illustra come distribuire un modello di Azure Resource Manager che crea un account di automazione. L'uso di un modello di Resource Manager richiede meno passaggi rispetto ad altri metodi di distribuzione.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Questo modello di esempio esegue le azioni seguenti:

* Automatizza la creazione di un'area di lavoro Log Analytics di Monitoraggio di Azure.
* Automatizza la creazione di un account di Automazione di Azure.
* Collega l'account di Automazione all'area di lavoro Log Analytics.
* Aggiunge runbook di automazione di esempio all'account.

>[!NOTE]
>La creazione dell'account RunAs di Automazione non è supportata quando si usa un modello di Resource Manager. Per creare manualmente un account RunAs dal portale o con PowerShell, vedere [Gestire gli account RunAs](manage-runas-account.md).

Dopo aver completato questi passaggi, è necessario [configurare le impostazioni di diagnostica](automation-manage-send-joblogs-log-analytics.md) per l'account di Automazione per inviare lo stato del processo del runbook e i flussi di processo all'area di lavoro Log Analytics collegata.

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-automation/).

:::code language="json" source="~/quickstart-templates/101-automation/azuredeploy.json" range="1-324" highlight="58-122":::

### <a name="api-versions"></a>Versioni dell'API

La tabella seguente elenca la versione dell'API per le risorse usate in questo esempio.

| Risorsa | Tipo di risorsa | Versione dell'API |
|:---|:---|:---|
| [Area di lavoro](/azure/templates/microsoft.operationalinsights/workspaces) | aree di lavoro | 2020-03-01-preview |
| [Account di Automazione](/azure/templates/microsoft.automation/automationaccounts) | automation | 2020-01-13-preview |
| [Servizi collegati dell'area di lavoro](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | aree di lavoro | 2020-03-01-preview |

### <a name="before-you-use-the-template"></a>Prima di iniziare a usare il modello

Il modello di parametri JSON viene configurato automaticamente per specificare:

* Nome dell'area di lavoro.
* Area in cui creare l'area di lavoro.
* Nome dell'account di Automazione.
* Area in cui creare l'account di automazione.

I parametri seguenti nel modello vengono impostati con un valore predefinito per l'area di lavoro Log Analytics:

* Il valore predefinito per *sku* è il nuovo piano tariffario per GB rilasciato nel modello di prezzi di aprile 2018.
* Il valore predefinito per *dataRetention* è 30 giorni.

>[!WARNING]
>Se si vuole creare o configurare un'area di lavoro Log Analytics in una sottoscrizione basata sul modello di prezzi di aprile 2018, l'unico piano tariffario di Log Analytics valido è *PerGB2018*.
>

Il modello JSON specifica un valore predefinito per gli altri parametri che potrebbero essere usati come configurazione standard nell'ambiente in uso. È possibile archiviare il modello in un account di archiviazione di Azure per consentire l'accesso condiviso nell'organizzazione. Per altre informazioni sull'uso dei modelli, vedere [Distribuire le risorse con i modelli di Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md).

Se non si ha familiarità con Automazione di Azure e Monitoraggio di Azure, è importante comprendere i dettagli di configurazione seguenti. Consentono di evitare errori quando si tenta di creare, configurare e usare un'area di lavoro Log Analytics collegata al nuovo account di Automazione.

* Vedere [altri dettagli](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) per comprendere appieno le opzioni di configurazione dell'area di lavoro, ad esempio la modalità di controllo di accesso, il piano tariffario, la conservazione e il livello di prenotazione della capacità.

* Vedere i [mapping dell'area di lavoro](how-to/region-mappings.md) per specificare le aree supportate inline o in un file di parametri. Sono supportate solo determinate aree per il collegamento di un'area di lavoro Log Analytics e un account di Automazione nella sottoscrizione.

* Se non si ha familiarità con i log di Monitoraggio di Azure e non è già stata distribuita un'area di lavoro, è consigliabile vedere le [linee guida per la progettazione delle aree di lavoro](../azure-monitor/platform/design-logs-deployment.md). Queste informazioni saranno utili per comprendere i meccanismi di controllo di accesso e le strategie di implementazione della progettazione consigliate per l'organizzazione.

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea un account di automazione di Azure e un'area di lavoro Log Analytics e collega l'account di automazione all'area di lavoro.

    [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

2. Immettere i valori desiderati.

3. Per il completamento della distribuzione possono essere richiesti alcuni minuti. Al termine, l'output sarà simile all'esempio seguente:

    ![Esempio di risultato al termine della distribuzione](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel portale di Azure aprire l'account di automazione appena creato. 

3. Nel riquadro sinistro selezionare **Runbook**. Nella pagina **Runbook** sono elencati tre runbook di esercitazione creati con l'account di automazione.

    ![Runbook di esercitazione creati con l'account di automazione](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. Nel riquadro sinistro selezionare **Area di lavoro collegata**. Nella pagina **Area di lavoro collegata** viene visualizzata l'area di lavoro Log Analytics specificata in precedenza collegata all'account di automazione.

    ![Account di automazione collegato all'area di lavoro Log Analytics](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, scollegare l'account di automazione dall'area di lavoro Log Analytics e quindi eliminare l'account di automazione e l'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati creati un account di automazione e un'area di lavoro Log Analytics e sono stati collegati tra loro.

Per altre informazioni, passare alle esercitazioni su Automazione di Azure.

> [!div class="nextstepaction"]
> [Esercitazioni su Automazione di Azure](learn/automation-tutorial-runbook-graphical.md)