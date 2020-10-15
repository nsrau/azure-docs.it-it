---
title: Usare watchlists di Sentinel di Azure
description: Questo articolo descrive come usare Azure Sentinel watchlists per esaminare le minacce, importare dati aziendali, creare elenchi Consenti e arricchire i dati degli eventi.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 25252b73f25a96f85d5e2cf1d68b76f9eaa3ca75
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979807"
---
# <a name="use-azure-sentinel-watchlists"></a>Usare watchlists di Sentinel di Azure

Azure Sentinel watchlists consente la raccolta di dati da origini dati esterne per la correlazione con gli eventi nell'ambiente Sentinel di Azure. Una volta creato, è possibile usare watchlists nei PlayBook di ricerca, regole di rilevamento, Hunting delle minacce e risposta. Watchlists vengono archiviati nell'area di lavoro di Azure Sentinel come coppie nome-valore e vengono memorizzati nella cache per ottimizzare le prestazioni delle query e bassa latenza.

Gli scenari comuni per l'uso di watchlists includono:

- **Analisi delle minacce** e risposta tempestiva agli eventi imprevisti con l'importazione rapida di indirizzi IP, hash di file e altri dati da file CSV. Una volta importato, è possibile usare le coppie nome/valore di Watch List per join e filtri in regole di avviso, caccia di minacce, cartelle di lavoro, notebook e query generali.

- **Importazione di dati di business** come watchlists. Importare, ad esempio, gli elenchi di utenti con privilegi di accesso al sistema o i dipendenti terminati, quindi utilizzare l'elenco di controllo per creare elenchi Consenti e nega utilizzati per rilevare o impedire a tali utenti di accedere alla rete.

- **Riduzione della fatica**per gli avvisi. Creare gli elenchi Consenti per eliminare gli avvisi da un gruppo di utenti, ad esempio gli utenti di indirizzi IP autorizzati che eseguono attività che normalmente generano l'avviso e impediscono che eventi benigni diventino avvisi.

- **Arricchimento dei dati degli eventi**. Usare watchlists per arricchire i dati degli eventi con combinazioni nome-valore derivate da origini dati esterne.

## <a name="create-a-new-watchlist"></a>Crea un nuovo controllo

1. Dal portale di Azure passare ad **Azure Sentinel**  >  **Configuration**  >  **Watch** list e quindi selezionare **Aggiungi nuovo**.

    > [!div class="mx-imgBorder"]
    > ![nuovo controllo](./media/watchlists/sentinel-watchlist-new.png)

1. Nella pagina **generale** specificare il nome, la descrizione e l'alias per l'oggetto Watch List e quindi fare clic su **Next (avanti**).

    > [!div class="mx-imgBorder"]
    > ![pagina generale Watch List](./media/watchlists/sentinel-watchlist-general.png)

1. Nella pagina **origine** selezionare il tipo di set di dati, caricare un file e quindi fare clic su **Avanti**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="pagina origine Watch List" lightbox="./media/watchlists/sentinel-watchlist-source.png":::


1. Esaminare le informazioni, verificare che siano corrette e quindi selezionare **Crea**.

    > [!div class="mx-imgBorder"]
    > ![pagina Revisione lista di controllo](./media/watchlists/sentinel-watchlist-review.png)

    Una notifica viene visualizzata dopo la creazione dell'oggetto Watch List.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="pagina origine Watch List" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::


## <a name="use-watchlists-in-queries"></a>Usare watchlists nelle query

1. Dal portale di Azure passare ad **Azure Sentinel**  >  **Configuration**  >  **Watch**list, selezionare il controllo che si vuole usare e quindi selezionare **View in log Analytics**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="pagina origine Watch List" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::


1. Gli elementi nell'oggetto Watch List vengono estratti automaticamente per la query e verranno visualizzati nella scheda **risultati** . Nell'esempio seguente vengono illustrati i risultati dell'estrazione dei campi **ServerName** e **IPAddress** .

    > [!NOTE]
    > Il timestamp sulle query verrà ignorato sia nell'interfaccia utente della query che negli avvisi pianificati.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="pagina origine Watch List" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
## <a name="use-watchlists-in-analytics-rules"></a>Usare watchlists nelle regole di analisi

Per usare watchlists nelle regole di analisi, dal portale di Azure passare ad **Azure Sentinel**  >  **Configuration**  >  **Analytics**e creare una regola usando la `_GetWatchlist('<watchlist>')` funzione nella query.

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule.png" alt-text="pagina origine Watch List" lightbox="./media/watchlists/sentinel-watchlist-analytics-rule.png":::


## <a name="view-list-of-watchlists-aliases"></a>Visualizza l'elenco degli alias watchlists

Per ottenere un elenco di alias di controllo, dalla portale di Azure, passare a **Azure Sentinel**  >  **General**  >  **log**generale ed eseguire la query seguente: `_GetWatchlistAlias` . È possibile visualizzare l'elenco di alias nella scheda **risultati** .

> [!div class="mx-imgBorder"]
> ![elencare watchlists](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come usare watchlists in Sentinel di Azure per arricchire i dati e migliorare le indagini. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.

