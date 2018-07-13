---
title: 'Ricerca personalizzata Bing: Definire i suggerimenti automatici personalizzati | Microsoft Docs'
description: Descrive come configurare Suggerimenti automatici personalizzati
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: a41b4e5b6c268ec68488c6764d4192cf8d2345a4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374692"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Configurare l'esperienza di Suggerimenti automatici personalizzati
Se si è sottoscritto il livello appropriato di Ricerca personalizzata (vedere le [pagine sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), è possibile personalizzare i suggerimenti di ricerca visualizzati durante l'uso di Ricerca personalizzata. Suggerimenti automatici personalizzati restituisce un elenco di query suggerite in base a una stringa di query parziale fornita dall'utente. Suggerimenti automatici personalizzati consente di fornire suggerimenti di ricerca personalizzati, pertinenti all'esperienza di ricerca dell'utente. È possibile specificare di restituire solo i suggerimenti personalizzati o di includere anche i suggerimenti di Bing. Se si includono i suggerimenti di Bing, questi vengono visualizzati dopo i suggerimenti personalizzati. I suggerimenti di Bing sono limitati al contesto dell'istanza di Ricerca personalizzata.

## <a name="configure-custom-autosuggest"></a>Configurare Suggerimenti automatici personalizzati
Seguire queste istruzioni per configurare Suggerimenti automatici personalizzati per l'istanza di Ricerca personalizzata.

1.  Accedere a [Ricerca personalizzato](https://customsearch.ai).
2.  Fare clic su un'istanza di ricerca personalizzata. Per creare un'istanza, vedere [Creare la prima istanza di Ricerca personalizzata Bing](quick-start.md).
3.  Fare clic sulla scheda **Suggerimenti automatici**.

## <a name="enable-bing-suggestions"></a>Abilitare i suggerimenti di Bing
Per abilitare i suggerimenti di Bing, spostare il dispositivo di scorrimento **Suggerimenti automatici Bing** su ON. Il dispositivo di scorrimento diventerà blu.

## <a name="add-suggestions"></a>Aggiungere suggerimenti
Per aggiungere un suggerimento, immetterlo nella casella di testo. Premere il tasto INVIO oppure fare clic sull'icona **+**. I suggerimenti personalizzati possono essere in qualsiasi lingua e verranno visualizzati prima dei suggerimenti di Bing.

## <a name="upload-suggestions"></a>Caricare suggerimenti
È possibile caricare un elenco di suggerimenti da un file. Inserire ogni suggerimento in una riga separata. Fare clic sull'icona di caricamento e selezionare il file.

## <a name="remove-suggestions"></a>Rimuovere suggerimenti
Per rimuovere un suggerimento, fare clic sull'icona di rimozione accanto al suggerimento da rimuovere.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

  >[!NOTE]  
  >Possono essere necessarie fino a 24 ore prima che le modifiche alla configurazione di Suggerimenti automatici personalizzati abbiano effetto.

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere suggerimenti personalizzati](./get-custom-suggestions.md)
- [Effettuare ricerche mediante l'istanza personalizzata](./search-your-custom-view.md)
- [Configurare e utilizzare l'interfaccia utente ospitata personalizzata](./hosted-ui.md)