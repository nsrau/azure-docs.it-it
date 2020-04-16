---
title: Usare il modello di Azure Resource Manager per distribuire il servizio
titleSuffix: Azure Cognitive Search
description: È possibile distribuire rapidamente un'istanza del servizio Ricerca cognitiva di Azure usando il modello di Azure Resource Manager.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/20/2020
ms.openlocfilehash: 6da2c324872a86c2c0ce6f55801bc7b0ee8d713e
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80611815"
---
# <a name="quickstart-deploy-cognitive-search-using-a-resource-manager-template"></a>Guida introduttiva: Distribuire Ricerca cognitiva con un modello di Resource Manager

Questo articolo illustra la procedura per usare un modello di Resource Manager per distribuire una risorsa di Ricerca cognitiva di Azure nel portale di Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-cognitive-search-service"></a>Creare un servizio Ricerca cognitiva

### <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo argomento di avvio rapido proviene dai [modelli di Azure](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchservices).

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json" range="1-86" highlight="4-50":::

La risorsa di Azure definita in questo modello:

- [Microsoft.Search/searchServices](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchServices): creare un servizio Ricerca cognitiva di Azure

### <a name="deploy-the-template"></a>Distribuire il modello

Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea una risorsa di Ricerca cognitiva di Azure.

[![Distribuzione in Azure](./media/search-get-started-arm/arm-deploybuttona.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

Il portale visualizza un modulo che consente di fornire facilmente i valori dei parametri. Alcuni parametri sono precompilati con i valori predefiniti del modello. Sarà necessario specificare la sottoscrizione, il gruppo di risorse, la posizione e il nome del servizio. Se si vuole usare Servizi cognitivi in una pipeline di [arricchimento tramite intelligenza artificiale](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro), ad esempio per analizzare file di immagine binari per verificare la presenza di testo, scegliere una posizione che offra sia Ricerca cognitiva che Servizi cognitivi. È necessario che entrambi i servizi si trovino nella stessa area per i carichi di lavoro di arricchimento tramite intelligenza artificiale. Una volta completato il modulo, sarà necessario accettare i termini e le condizioni, quindi selezionare il pulsante Acquista per completare la distribuzione.

> [!div class="mx-imgBorder"]
> ![Visualizzazione del modello nel portale di Azure](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Al termine della distribuzione, è possibile accedere al nuovo gruppo di risorse e al nuovo servizio di ricerca nel portale.

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide di avvio rapido ed esercitazioni di Ricerca cognitiva si basano su questa guida di avvio rapido. Se si prevede di usare le guide di avvio rapido e le esercitazioni successive, è consigliabile non cancellare la risorsa creata. Quando non è più necessario, è possibile eliminare il gruppo di risorse per eliminare il servizio Ricerca cognitiva e le risorse correlate.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un servizio Ricerca cognitiva usando un modello di Azure Resource Manager ed è stata convalidata la distribuzione. Per altre informazioni su Ricerca cognitiva e Azure Resource Manager, continuare con gli articoli seguenti.

 - Leggere una [panoramica di Ricerca cognitiva di Azure](https://docs.microsoft.com/azure/search/search-what-is-azure-search)
 - [Creare un indice](https://docs.microsoft.com/azure/search/search-get-started-portal) per il servizio di ricerca
 - [Creare un'app di ricerca](https://docs.microsoft.com/azure/search/search-create-app-portal) usando la procedura guidata del portale
 - [Creare un set di competenze](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob) per estrarre informazioni dai dati


