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
ms.openlocfilehash: 75f6c8d299c7eed901dda0631fca74b040f72e30
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374697"
---
# <a name="call-your-custom-search"></a>Chiamare la ricerca personalizzata
Prima di eseguire la prima chiamata all'API Ricerca personalizzata per ottenere i risultati della ricerca per l'istanza, è necessario ottenere una chiave di sottoscrizione ai Servizi cognitivi. Per ottenere una chiave per l'API Ricerca personalizzata, vedere [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).

> [!NOTE]
> I clienti di Ricerca personalizzata Bing esistenti che hanno effettuato il provisioning di una chiave di anteprima entro il 15 ottobre 2017 potranno continuare a usare le chiavi fino al 30 novembre 2017 o fino alla scadenza del numero massimo di query consentite. Dopo questa data, dovranno eseguire la migrazione alla versione disponibile a livello generale in Azure.

## <a name="try-it-out"></a>Provare il servizio
Dopo aver configurato l'esperienza di ricerca personalizzata, è possibile testare la configurazione dall'interno del portale di ricerca personalizzata. Accedere a [Ricerca personalizzata](https://customsearch.ai), fare clic su un'istanza di ricerca personalizzata e quindi fare clic sulla scheda **Produzione**. Viene visualizzata la scheda **Endpoint**. La sottoscrizione determina quali endpoint sono disponibili per provare, vedere [le pagine relative ai prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/). Per testare un endpoint, selezionarlo dall'elenco a discesa e impostare le opzioni di configurazione associate. 

Di seguito sono riportate le opzioni disponibili.

- **Query**: il termine di ricerca da ricercare. Disponibile solo per gli endpoint Web, Image e Autosuggest.
- **ID di configurazione personalizzato**: l'ID di configurazione dell'istanza selezionata di ricerca personalizzata. Questo campo è di sola lettura.
- **Mercato**: il mercato da dove provengono i risultati. Disponibile solo per gli endpoint Web, Image e Hosted UI.
- **Chiave di sottoscrizione**: la chiave di sottoscrizione con cui eseguire il test. È possibile selezionare una chiave nell'elenco a discesa o immetterla manualmente.
- **Ricerca sicura**: un filtro usato per filtrare le pagine Web per il contenuto per adulti. Disponibile solo per gli endpoint Web, Image e Hosted UI.
- **Count** Il numero di risultati della ricerca da restituire nella risposta. Disponibile solo per gli endpoint Web e Image.
- **Offset** Il numero di risultati della ricerca da restituire nella risposta. Disponibile solo per gli endpoint Web e Image.

Dopo aver specificato tutte le opzioni necessarie per Web, Image o Autosuggest, fare clic su **Call** (Chiama) per visualizzare la risposta JSON nel riquadro di destra. 

Se si seleziona l'endpoint Hosted UI, è possibile testare l'esperienza di ricerca nel riquadro di destra.

## <a name="next-steps"></a>Passaggi successivi
- [Richiamare la visualizzazione personalizzata con C#](./call-endpoint-csharp.md)
- [Richiamare la visualizzazione personalizzata con Java](./call-endpoint-java.md)
- [Richiamare la visualizzazione personalizzata con NodeJs](./call-endpoint-nodejs.md)
- [Richiamare la visualizzazione personalizzata con Python](./call-endpoint-python.md)