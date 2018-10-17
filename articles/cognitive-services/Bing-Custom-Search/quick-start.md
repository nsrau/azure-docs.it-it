---
title: 'Guida introduttiva: Creare la prima istanza di Ricerca personalizzata Bing'
titlesuffix: Azure Cognitive Services
description: Per usare Ricerca personalizzata Bing è necessario creare un'istanza di ricerca personalizzata che definisce la visualizzazione o sezione del Web. L'istanza contiene le impostazioni che specificano i domini pubblici, i siti secondari e le pagine Web in cui si vuole eseguire la ricerca con Bing e le modifiche di classificazione.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2017
ms.author: v-brapel
ms.openlocfilehash: 866d32aa4de45076fcbc4e413d8c2e67d5346878
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816239"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Guida introduttiva: Creare la prima istanza di Ricerca personalizzata Bing
Per usare Ricerca personalizzata Bing è necessario creare un'istanza di ricerca personalizzata che definisce la visualizzazione o sezione del Web. L'istanza contiene le impostazioni che specificano i domini pubblici, i siti Web e le pagine Web in cui si vuole eseguire la ricerca con Bing e le modifiche di classificazione. Per creare l'istanza, usare il [portale](https://customsearch.ai) di Ricerca personalizzata Bing. 

## <a name="create-a-custom-search-instance"></a>Creare un'istanza di Ricerca personalizzata

Per creare un'istanza di Ricerca personalizzata Bing:

1.  Ottenere una chiave per l'API Ricerca personalizzata. Vedere [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
2.  Fare clic sul pulsante **Accedi** per accedere al portale usando un account Microsoft (account del servizio gestito). 
    - Se non si ha un account Microsoft, fare clic su **Crea un account Microsoft**. Verranno chieste le autorizzazioni necessarie per accedere ai dati. Fare clic su **Sì**.
    - Accettare le condizioni di Servizi cognitivi. Selezionare **Accetto** e fare clic su **Accetto**.  
3.  Dopo aver eseguito l'accesso, fare clic su **Nuova istanza** e assegnare un nome all'istanza. Usare un nome significativo che descriva il tipo di contenuto restituito dalla ricerca. È possibile modificare il nome in qualsiasi momento. 
4.  Nell'**esperienza di ricerca** fare clic sulla scheda **Elementi attivi** e immettere l'URL di uno o più siti Web da includere nella ricerca.
5.  Per verificare che l'istanza restituisca risultati, immettere una query nel riquadro di anteprima a destra. Se non sono presenti risultati, specificare un nuovo sito Web. Bing restituisce risultati solo per i siti pubblici che ha indicizzato.
6.  Fare clic su **Pubblica** per pubblicare le modifiche di configurazione in produzione. Quando richiesto, fare clic su **Pubblica** per confermare.
7.  Fare clic su **Produzione** > **Endpoint** e copiare l'**ID di configurazione personalizzato**. Questo ID è necessario per chiamare l'API Ricerca personalizzata.

## <a name="next-steps"></a>Passaggi successivi

Continuare a usare l'istanza di ricerca personalizzata creata seguendo le istruzioni contenute in queste guide alla procedure:

- [Configurare un'esperienza di ricerca personalizzata](./define-your-custom-view.md)
- [Chiamare la ricerca personalizzata](./search-your-custom-view.md)
- [Condividere una ricerca personalizzata](./share-your-custom-search.md)
- [Configurare l'esperienza dell'interfaccia utente ospitata](./hosted-ui.md)
- [Usare indicatori di effetto per evidenziare il testo](./hit-highlighting.md)
- [Sfogliare pagine Web](./page-webpages.md)
