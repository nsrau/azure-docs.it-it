---
title: "Avvio rapido: Creare un'istanza di Gestione API con un modello di Resource Manager"
description: Informazioni su come creare un'istanza di Gestione API nel livello Developer usando un modello di Azure Resource Manager.
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/09/2020
ms.openlocfilehash: d4baa97794f53e59169d0e9cbbcbf80bc84ad9bb
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91935880"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-an-arm-template"></a>Avvio rapido: Creare una nuova istanza del servizio Gestione API di Azure con un modello di Resource Manager

Questa guida di avvio rapido descrive come usare un modello di Azure Resource Manager per creare un'istanza del servizio Gestione API di Azure. Gestione API di Azure consente alle organizzazioni di pubblicare API in modo che sviluppatori esterni, partner e interni possano sbloccare il potenziale dei loro dati e servizi. Gestione API fornisce le competenze fondamentali per assicurare un programma API di successo attraverso il coinvolgimento degli sviluppatori, informazioni aziendali approfondite, strumenti di analisi, sicurezza e protezione. Gestione API consente di creare e gestire gateway API moderni per servizi back-end esistenti ospitati ovunque. Per altre informazioni, vedere la [panoramica](api-management-key-concepts.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-azure-api-management-create/).


:::code language="json" source="~/quickstart-templates/101-azure-api-management-create/azuredeploy.json":::

Nel modello è definita la risorsa di Azure seguente:

- **[Microsoft.ApiManagement/service](https://docs.microsoft.com/azure/templates/microsoft.apimanagement/service)**

Altri esempi di modelli di Gestione API di Azure sono disponibili nei [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Apimanagement&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea un'istanza del servizio Gestione API con un nome generato automaticamente. 
  
    [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

    In questo esempio l'istanza è configurata nel livello Developer, un'opzione a costi contenuti per la valutazione di Gestione API di Azure. Questo livello non è disponibile per la produzione. Per altre informazioni sulla scalabilità dei livelli di Gestione API, vedere [upgrade and scale](upgrade-and-scale.md) (Aggiornare e ridimensionare).

1. Selezionare o immettere i valori seguenti.
    * **Sottoscrizione**: selezionare una sottoscrizione di Azure.
    * **Gruppo di risorse**: selezionare **Crea nuovo**, immettere un nome univoco per il gruppo di risorse e quindi fare clic su **OK**.
    * **Area**: selezionare una località per il gruppo di risorse. Esempio: **Stati Uniti centrali**.
    * **Publisher Email** (Indirizzo di posta elettronica autore): immettere un indirizzo di posta elettronica in cui ricevere le notifiche.
    * **Nome autore**: immettere un nome scelto per l'autore dell'API.
    * **SKU**: accettare il valore predefinito **Developer**. 
    * **Sku Count** (Numero di SKU): accettare il valore predefinito.
    * **Località**: accettare la località generata per il servizio Gestione API.

    :::image type="content" source="media/quickstart-arm-template/create-instance-template.png" alt-text="Proprietà del modello Gestione API":::


 1. Selezionare **Rivedi e crea**, quindi rivedere i termini e le condizioni. Se si accettano, selezionare **Crea**.
    
    > [!TIP]
    >  Possono essere necessari da 30 a 40 minuti per creare e attivare un servizio Gestione API nel livello Developer. 

 1. Una volta completata la creazione dell'istanza, si riceverà una notifica:
 
    :::image type="content" source="media/quickstart-arm-template/deployment-notification.png" alt-text="Proprietà del modello Gestione API":::

 Per distribuire il modello, si usa il portale di Azure. Oltre al portale di Azure, è anche possibile usare Azure PowerShell, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Usare il portale di Azure per controllare le risorse distribuite oppure usare strumenti come l'interfaccia della riga di comando di Azure o Azure PowerShell per visualizzare un elenco delle risorse distribuite.


1. Nel [portale di Azure](https://portal.azure.com) cercare e selezionare **Servizi Gestione API**, quindi selezionare l'istanza del servizio creata.
1. Esaminare le proprietà del servizio nella pagina **Panoramica**.

:::image type="content" source="media/quickstart-arm-template/service-instance-created.png" alt-text="Proprietà del modello Gestione API":::

Nel momento in cui l'istanza del servizio Gestione API è online, è possibile iniziare a usarla. Cominciare con l'esercitazione per [importare e pubblicare](import-and-publish.md) la prima API.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a eseguire le esercitazioni successive, è possibile lasciare l'istanza di Gestione API impostata. Quando non è più necessario, eliminare il gruppo di risorse per eliminare tutte le risorse contenute al suo interno.

1. Nel [portale di Azure](https://portal.azure.com) cercare e selezionare **Gruppi di risorse**. È anche possibile selezionare **Gruppi di risorse** nella pagina **Home**.
1. Nella pagina **Gruppi di risorse** selezionare il gruppo di risorse desiderato.
1. Nella pagina del gruppo di risorse selezionare **Elimina gruppo di risorse**.

    Eliminare un gruppo di risorse
1. Digitare il nome del gruppo di risorse e quindi selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Importare e pubblicare la prima API](import-and-publish.md)