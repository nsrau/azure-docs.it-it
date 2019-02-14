---
title: Definire i suggerimenti automatici personalizzati - Ricerca personalizzata Bing
titlesuffix: Azure Cognitive Services
description: Descrive come configurare Suggerimenti automatici personalizzati
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: bbad72b41a177bdbafd6cf98bfd2025190d98b16
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237638"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Configurare l'esperienza di Suggerimenti automatici personalizzati

I suggerimenti automatici personalizzati restituiscono un elenco di stringhe di query di ricerca suggerite pertinenti per l'esperienza di ricerca. Le stringhe di query di ricerca sono basate su una stringa di query parziale fornita dall'utente nella casella di ricerca. L'elenco contiene un massimo di 10 suggerimenti. 

È possibile specificare di restituire solo i suggerimenti personalizzati o di includere anche i suggerimenti di Bing. Se si includono i suggerimenti di Bing, questi vengono visualizzati dopo i suggerimenti personalizzati. Se si specifica un numero sufficiente di suggerimenti rilevanti, è possibile che l'elenco di suggerimenti restituito non includa i suggerimenti di Bing. I suggerimenti di Bing sono sempre nel contesto dell'istanza di Ricerca personalizzata. 

Per configurare i suggerimenti per le query di ricerca per l'istanza, fare clic sulla scheda **Suggerimenti automatici**.  

> [!NOTE]
> Per usare questa funzionalità, è necessaria la sottoscrizione di Ricerca personalizzata al livello appropriato (vedere [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)).

Affinché i suggerimenti vengano implementati nell'endpoint che fornisce i risultati (API o interfaccia utente ospitata) possono essere necessarie fino a 24 ore.

## <a name="enable-bing-suggestions"></a>Abilitare i suggerimenti di Bing

Per abilitare i suggerimenti di Bing, spostare il dispositivo di scorrimento **Suggerimenti automatici Bing** su ON. Il dispositivo di scorrimento diventerà blu.

## <a name="add-your-own-suggestions"></a>Aggiungere suggerimenti personalizzati

Per aggiungere suggerimenti personalizzati per le stringhe di query, inserirli nell'elenco in **User-defined suggestions** (Suggerimenti definiti dall'utente). Dopo aver aggiunto un suggerimento all'elenco, premere INVIO o fare clic sull'icona **+**. È possibile specificare il suggerimento in qualsiasi lingua. È possibile aggiungere un massimo di 5.000 suggerimenti per le stringhe di query.

## <a name="upload-suggestions"></a>Caricare suggerimenti

È possibile caricare un elenco di suggerimenti da un file. Il file deve contenere una stringa di query di ricerca per ogni riga. Per caricare il file, fare clic sull'icona di caricamento e selezionare il file da caricare. Il servizio estrae i suggerimenti dal file e li aggiunge all'elenco.

## <a name="remove-suggestions"></a>Rimuovere suggerimenti

Per rimuovere un suggerimento per una stringa di query, fare clic sull'icona di rimozione accanto al suggerimento da rimuovere.

## <a name="block-suggestions"></a>Bloccare i suggerimenti

Se si includono i suggerimenti di Bing, è possibile aggiungere un elenco di stringhe di query di ricerca che non si vuole vengano restituite da Bing. Per aggiungere le stringhe di query bloccate, fare clic su **Show blocked suggestions** (Mostra suggerimenti bloccati). Aggiungere la stringa di query all'elenco e premere INVIO o fare clic sull'icona **+**. È possibile aggiungere un massimo di 50 stringhe di query bloccate.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Possono essere necessarie fino a 24 ore prima che le modifiche alla configurazione di Suggerimenti automatici personalizzati abbiano effetto.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Abilitazione dei suggerimenti automatici nell'interfaccia utente ospitata

Per abilitare i suggerimenti per le stringhe di query per l'interfaccia utente ospitata, fare clic su **Hosted UI** (Interfaccia utente ospitata). Scorrere verso il basso fino alla sezione **Additional Configuration** (Configurazione aggiuntiva). In **Web search** (Ricerca Web) selezionare **On** (Sì) per **Enable autosuggest** (Abilita suggerimenti automatici). Per abilitare i suggerimenti automatici, è necessario selezionare un layout che include una casella di ricerca.


## <a name="calling-the-autosuggest-api"></a>Chiamata dell'API Suggerimenti automatici

Per ottenere le stringhe di query suggerite tramite l'API Ricerca personalizzata Bing, inviare una richiesta `GET` all'endpoint seguente.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

La risposta contiene un elenco di oggetti `SearchAction` contenenti le stringhe di query suggerite.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Ogni suggerimento include i campi `displayText` e `query`. Il campo `displayText` contiene la stringa di query suggerita usata per popolare l'elenco a discesa della casella di ricerca.

Se l'utente seleziona una stringa di query suggerita nell'elenco a discesa, usare la stringa di query nel campo `query` quando si chiama l'[API Ricerca personalizzata Bing](overview.md).


## <a name="next-steps"></a>Passaggi successivi

- [Ottenere suggerimenti personalizzati](./get-custom-suggestions.md)
- [Effettuare ricerche mediante l'istanza personalizzata](./search-your-custom-view.md)
- [Configurare e utilizzare l'interfaccia utente ospitata personalizzata](./hosted-ui.md)
