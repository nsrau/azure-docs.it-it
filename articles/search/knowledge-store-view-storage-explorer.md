---
title: Visualizzare un archivio conoscenze con Storage Explorer
titleSuffix: Azure Cognitive Search
description: Visualizzare e analizzare un archivio conoscenze di Ricerca cognitiva di Azure con lo strumento Storage Explorer del portale di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: ba0b02067c032f9038051c169866588ded44af73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85566026"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Visualizzare un archivio conoscenze con Storage Explorer

In questo articolo si apprenderà ad esempio come connettersi ed esplorare un archivio informazioni usando Storage Explorer nel portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

+ Per creare l'esempio di archivio conoscenze usato in questa procedura dettagliata, seguire le procedure descritte in [Creare un archivio conoscenze nel portale di Azure](knowledge-store-create-portal.md).

+ Servirà anche il nome dell'account di archiviazione di Azure usato per creare l'archivio conoscenze, insieme alla relativa chiave di accesso dal portale di Azure.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Visualizzare, modificare ed eseguire query su un archivio informazioni in Storage Explorer

1. Nella portale di Azure [aprire l'account di archiviazione](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) utilizzato per creare l'archivio informazioni.

1. Nel riquadro di spostamento sinistro dell'account di archiviazione fare clic su **Storage Explorer**.

1. Espandere l'elenco **TABELLE** per visualizzare un elenco delle proiezioni di tabelle di Azure create quando è stata eseguita la procedura guidata **Importa dati** sui dati di esempio delle recensioni di hotel.

Selezionare una tabella per visualizzare i dati arricchiti, incluse le frasi chiave e i punteggi dei sentimenti.

   ![Visualizzare le tabelle in Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Visualizzare le tabelle in Storage Explorer")

Per cambiare il tipo di dati per qualsiasi valore di tabella o per modificare singoli valori nella tabella, fare clic su **Modifica**. Quando si cambia il tipo di dati di una colonna in una riga della tabella, la modifica viene applicata a tutte le righe.

   ![Modificare la tabella in Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Modificare la tabella in Storage Explorer")

Per eseguire query, fare clic su **Query** sulla barra dei comandi e immettere le condizioni.  

   ![Eseguire query sulla tabella in Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Eseguire query sulla tabella in Storage Explorer")

## <a name="clean-up"></a>Eseguire la pulizia

Quando si lavora nella propria sottoscrizione, al termine di un progetto è buona norma determinare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale.

## <a name="next-steps"></a>Passaggi successivi

Connettere questo archivio informazioni a Power BI per un'analisi più approfondita o procedere con il codice, usando l'API REST e l'impostore per creare un archivio informazioni diverso.

> [!div class="nextstepaction"]
> [Connetti con Power bi](knowledge-store-connect-power-bi.md) 
>  [Creazione di un archivio informazioni in Rest](knowledge-store-create-rest.md)
