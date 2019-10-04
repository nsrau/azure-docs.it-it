---
title: Visualizzare un archivio conoscenze con Storage Explorer - Ricerca di Azure
description: Visualizzare e analizzare un archivio conoscenze di Ricerca di Azure con lo strumento Storage Explorer del portale di Azure.
author: lisaleib
services: search
ms.service: search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: 9ec93bcd2309bb47b24983260c35726ac24b5e95
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265611"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Visualizzare un archivio conoscenze con Storage Explorer

> [!Note]
> La funzionalità dell'archivio conoscenze è in anteprima e non deve essere usata in ambienti di produzione. È inclusa nell'[API REST di Ricerca di Azure versione 2019-05-06-Preview](search-api-preview.md). Non è attualmente disponibile alcun supporto di .NET SDK.
>
Questo articolo illustra come connettere ed esplorare un archivio conoscenze usando Storage Explorer nel portale di Azure. Per creare l'esempio di archivio conoscenze usato in questa procedura dettagliata, vedere [Creare un archivio conoscenze nel portale di Azure](knowledge-store-create-portal.md).

## <a name="prerequisites"></a>Prerequisiti

+ Per creare l'esempio di archivio conoscenze usato in questa procedura dettagliata, seguire le procedure descritte in [Creare un archivio conoscenze nel portale di Azure](knowledge-store-create-portal.md).

+ Servirà anche il nome dell'account di archiviazione di Azure usato per creare l'archivio conoscenze, insieme alla relativa chiave di accesso dal portale di Azure.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Visualizzare, modificare ed eseguire query su un archivio conoscenze in Storage Explorer

1. Nella portale di Azure aprire l'account di archiviazione usato per creare l'archivio conoscenze.

1. Nel riquadro di spostamento sinistro dell'account di archiviazione fare clic su **Storage Explorer**.

1. Espandere l'elenco **TABELLE** per visualizzare un elenco delle proiezioni di tabelle di Azure create quando è stata eseguita la procedura guidata **Importa dati** sui dati di esempio delle recensioni di hotel.

Selezionare una tabella qualsiasi per visualizzare i dati arricchiti, inclusi i punteggi del sentiment delle frasi chiave, i dati relativi alla posizione di latitudine e longitudine e altro ancora.

   ![Visualizzare le tabelle in Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Visualizzare le tabelle in Storage Explorer")

Per cambiare il tipo di dati per qualsiasi valore di tabella o per modificare singoli valori nella tabella, fare clic su **Modifica**. Quando si cambia il tipo di dati di una colonna in una riga della tabella, la modifica viene applicata a tutte le righe.

   ![Modificare la tabella in Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Modificare la tabella in Storage Explorer")

Per eseguire query, fare clic su **Query** sulla barra dei comandi e immettere le condizioni.  

   ![Eseguire query sulle tabelle in Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Eseguire query sulle tabelle in Storage Explorer")

## <a name="clean-up"></a>Eseguire la pulizia

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come connettere questo archivio conoscenze a Power BI, vedere l'articolo seguente.

> [!div class="nextstepaction"]
> [Stabilire la connessione con Power BI](knowledge-store-connect-power-bi.md)

Per informazioni su come creare un archivio conoscenze usando le API REST e Postman, vedere l'articolo seguente.  

> [!div class="nextstepaction"]
> [Creare un archivio conoscenze in REST](knowledge-store-howto.md)
