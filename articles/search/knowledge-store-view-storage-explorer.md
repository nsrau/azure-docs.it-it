---
title: Visualizzare un archivio informazioni (anteprima) con Storage Explorer
titleSuffix: Azure Cognitive Search
description: Visualizzare e analizzare un archivio informazioni ricerca cognitiva di Azure con il Storage Explorer di portale di Azure. Archivio informazioni è attualmente disponibile in anteprima pubblica.
manager: nitinme
author: lisaleib
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e3ea879a419aa14d3a6693e23f4f120aca8d9d51
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720059"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Visualizzare un archivio conoscenze con Storage Explorer

> [!IMPORTANT] 
> Archivio informazioni è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). L' [API REST versione 2019-05-06-Preview](search-api-preview.md) fornisce funzionalità di anteprima. Il supporto del portale è attualmente limitato e non è disponibile alcun supporto per .NET SDK.

In questo articolo si apprenderà ad esempio come connettersi ed esplorare un archivio informazioni usando Storage Explorer nel portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

+ Per creare l'archivio informazioni di esempio usato in questa procedura dettagliata, seguire i passaggi descritti in [creare un archivio informazioni in portale di Azure](knowledge-store-create-portal.md) o [creare un archivio informazioni ricerca cognitiva Azure con REST](knowledge-store-create-rest.md) .

+ Servirà anche il nome dell'account di archiviazione di Azure usato per creare l'archivio conoscenze, insieme alla relativa chiave di accesso dal portale di Azure.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Visualizzare, modificare ed eseguire query su un archivio informazioni in Storage Explorer

1. Nella portale di Azure [aprire l'account di archiviazione](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) utilizzato per creare l'archivio informazioni.

1. Nel riquadro di spostamento sinistro dell'account di archiviazione fare clic su **Storage Explorer**.

1. Espandere l'elenco **TABELLE** per visualizzare un elenco delle proiezioni di tabelle di Azure create quando è stata eseguita la procedura guidata **Importa dati** sui dati di esempio delle recensioni di hotel.

Selezionare una tabella qualsiasi per visualizzare i dati arricchiti, inclusi i punteggi del sentiment delle frasi chiave, i dati relativi alla posizione di latitudine e longitudine e altro ancora.

   ![Visualizza tabelle in Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Visualizza tabelle in Storage Explorer")

Per cambiare il tipo di dati per qualsiasi valore di tabella o per modificare singoli valori nella tabella, fare clic su **Modifica**. Quando si cambia il tipo di dati di una colonna in una riga della tabella, la modifica viene applicata a tutte le righe.

   ![Modifica tabella in Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Modifica tabella in Storage Explorer")

Per eseguire query, fare clic su **Query** sulla barra dei comandi e immettere le condizioni.  

   ![Tabella query in Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Tabella query in Storage Explorer")

## <a name="clean-up"></a>Eseguire la pulizia

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale.

## <a name="next-steps"></a>Passaggi successivi

Connettere questo archivio informazioni a Power BI per un'analisi più approfondita o procedere con il codice, usando l'API REST e l'impostore per creare un archivio informazioni diverso.

> [!div class="nextstepaction"]
> [Connettersi con Power BI](knowledge-store-connect-power-bi.md)
> [creare un archivio informazioni in Rest](knowledge-store-howto.md)
