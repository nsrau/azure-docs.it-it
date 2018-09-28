---
title: 'Ricerca personalizzata Bing: Ricerca di una visualizzazione personalizzata | Microsoft Docs'
description: Viene descritto come cercare una visualizzazione personalizzata del Web
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 7a2db7881dfe7efedb1a83637a6281f786652958
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964903"
---
# <a name="call-your-custom-search"></a>Chiamare la ricerca personalizzata

Prima di eseguire la prima chiamata all'API Ricerca personalizzata per ottenere i risultati della ricerca per l'istanza, è necessario ottenere una chiave di sottoscrizione ai Servizi cognitivi. Per ottenere una chiave per l'API Ricerca personalizzata, vedere [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).


## <a name="try-it-out"></a>Provare il servizio

Dopo aver configurato l'esperienza di ricerca personalizzata, è possibile testare la configurazione dall'interno del portale di ricerca personalizzata. 

1. Accedere a [Ricerca personalizzata](https://customsearch.ai).
2. Fare clic su una delle istanze di ricerca personalizzata dall'elenco di istanze.
3. Fare clic sulla scheda **Production**(Produzione). 
4. Sotto la scheda**Endpoint**, selezionare un endpoint (ad esempio, API Web). La sottoscrizione determina quali endpoint vengono mostrati (vedere [listino prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) per le opzioni di sottoscrizione). 
5. Specificare i valori del parametro. 

    Di seguito sono riportati i possibili parametri che è possibile impostare (l'elenco effettivo dipende dall'endpoint selezionato). Per altre informazioni su questi parametri, vedere [API ricerca personalizzata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters).

    - **Query**: il termine di ricerca da ricercare. Disponibile solo per gli endpoint Web, Image e Autosuggest.
    - **ID di configurazione personalizzato**: l'ID di configurazione dell'istanza selezionata di ricerca personalizzata. Questo campo è di sola lettura.
    - **Mercato**: il mercato da dove provengono i risultati. Disponibile solo per gli endpoint Web, Image, Video e Hosted UI.
    - **Chiave di sottoscrizione**: la chiave di sottoscrizione con cui eseguire il test. È possibile selezionare una chiave nell'elenco a discesa o immetterla manualmente.  
      
    Facendo clic su **Parametri aggiuntivi** vengono visualizzati i parametri seguenti:  
      
    - **Ricerca sicura**: un filtro usato per filtrare le pagine Web per il contenuto per adulti. Disponibile solo per gli endpoint Web, Image, Video e Hosted UI.
    - **Lingua dell'interfaccia utente**: lingua utilizzata per le stringhe dell'interfaccia utente. Ad esempio, se si abilitano immagini e video nell'Hosted UI, le schede**Image** e **Video** usano la lingua specificata.
    - **Count** Il numero di risultati della ricerca da restituire nella risposta. Disponibile solo per gli endpoint Web, Image e Video.
    - **Offset**: Numero di risultati da ignorare prima di restituire i risultati. Disponibile solo per gli endpoint Web, Image e Video.

6. Dopo aver specificato tutte le opzioni necessarie, fare clic su **Call** per visualizzare la risposta JSON nel riquadro di destra. 

Se si seleziona l'endpoint Hosted UI, è possibile provare l'esperienza di ricerca nel riquadro inferiore.

## <a name="next-steps"></a>Passaggi successivi

- [Richiamare la visualizzazione personalizzata con C#](./call-endpoint-csharp.md)
- [Richiamare la visualizzazione personalizzata con Java](./call-endpoint-java.md)
- [Richiamare la visualizzazione personalizzata con NodeJs](./call-endpoint-nodejs.md)
- [Richiamare la visualizzazione personalizzata con Python](./call-endpoint-python.md)

- [Richiamare la visualizzazione personalizzata con C#](./sdk-csharp-quick-start.md)