---
title: View a knowledge store (preview) with Storage Explorer
titleSuffix: Azure Cognitive Search
description: Visualizzare e analizzare un archivio conoscenze di Ricerca cognitiva di Azure con lo strumento Storage Explorer del portale di Azure. knowledge store is currently in public preview.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2df05cf20ef51b2d5ca866f22bd9450dd6acaf
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406560"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Visualizzare un archivio conoscenze con Storage Explorer

> [!IMPORTANT] 
> L'archivio conoscenze è attualmente disponibile in anteprima pubblica. Preview functionality is provided without a service level agreement, and is not recommended for production workloads. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). The [REST API version 2019-05-06-Preview](search-api-preview.md) provides preview features. Il supporto del portale è attualmente limitato e non è disponibile alcun supporto per .NET SDK.

In this article, you'll learn by example how to connect to and explore a knowledge store using Storage Explorer in the Azure portal.

## <a name="prerequisites"></a>Prerequisiti

+ Follow the steps in [Create a knowledge store in Azure portal](knowledge-store-create-portal.md) or [Create an Azure Cognitive Search knowledge store by using REST](knowledge-store-create-rest.md) to create the sample knowledge store used in this walkthrough.

+ Servirà anche il nome dell'account di archiviazione di Azure usato per creare l'archivio conoscenze, insieme alla relativa chiave di accesso dal portale di Azure.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>View, edit, and query a knowledge store in Storage Explorer

1. In the Azure portal, [open the Storage account](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) that you used to create the knowledge store.

1. Nel riquadro di spostamento sinistro dell'account di archiviazione fare clic su **Storage Explorer**.

1. Espandere l'elenco **TABELLE** per visualizzare un elenco delle proiezioni di tabelle di Azure create quando è stata eseguita la procedura guidata **Importa dati** sui dati di esempio delle recensioni di hotel.

Selezionare una tabella qualsiasi per visualizzare i dati arricchiti, inclusi i punteggi del sentiment delle frasi chiave, i dati relativi alla posizione di latitudine e longitudine e altro ancora.

   ![Visualizzare le tabelle in Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Visualizzare le tabelle in Storage Explorer")

Per cambiare il tipo di dati per qualsiasi valore di tabella o per modificare singoli valori nella tabella, fare clic su **Modifica**. Quando si cambia il tipo di dati di una colonna in una riga della tabella, la modifica viene applicata a tutte le righe.

   ![Modificare la tabella in Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Modificare la tabella in Storage Explorer")

Per eseguire query, fare clic su **Query** sulla barra dei comandi e immettere le condizioni.  

   ![Eseguire query sulla tabella in Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Eseguire query sulla tabella in Storage Explorer")

## <a name="clean-up"></a>Eseguire la pulizia

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale.

## <a name="next-steps"></a>Passaggi successivi

Connect this knowledge store to Power BI for deeper analysis, or move forward with code, using the REST API and Postman to create a different knowledge store.

> [!div class="nextstepaction"]
> [Connect with Power BI](knowledge-store-connect-power-bi.md)
> [Create a knowledge store in REST](knowledge-store-howto.md)
