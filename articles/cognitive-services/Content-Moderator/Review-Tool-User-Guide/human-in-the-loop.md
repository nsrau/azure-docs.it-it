---
title: Informazioni sui concetti relativi allo strumento di revisione-Content Moderator
titleSuffix: Azure Cognitive Services
description: Informazioni sullo strumento di revisione del Content Moderator, un sito Web che coordina un lavoro di moderazione combinato e revisione umana.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: ddf4d17cd2631f5bc299e392c46bd9065bb13744
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146622"
---
# <a name="content-moderator-review-tool"></a>Strumento di revisione Content Moderator

Azure Content Moderator fornisce servizi per combinare la moderazione del contenuto di Machine Learning con le revisioni umane. Il sito Web [dello strumento di revisione](https://contentmoderator.cognitive.microsoft.com) è un front-end intuitivo che fornisce un accesso dettagliato a questi servizi.

## <a name="what-it-does"></a>Funzione

Lo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com), se usato insieme alle API di moderazione assistita da computer, consente di eseguire le attività seguenti nel processo di moderazione dei contenuti:

- Usare un set di strumenti per moderare il contenuto in più formati (testo, immagine e video).
- Automatizzare la creazione di [recensioni](../review-api.md#reviews) umane quando si verificano i risultati dell'API di moderazione.
- Assegna o inoltra le revisioni del contenuto a più team di revisione, organizzati per categoria di contenuto o livello di esperienza.
- Usare filtri per la logica predefiniti o personalizzati ([flussi di lavoro](../review-api.md#workflows)) per ordinare e tenere traccia del contenuto, senza scrivere codice.
- Usare i [connettori](./configure.md#connectors) per elaborare il contenuto con i servizi Microsoft PhotoDNA, analisi del testo e Face oltre alle API content moderator.
- Ottenere le metriche di prestazioni chiave relative ai processi di moderazione del contenuto.

## <a name="review-tool-dashboard"></a>Esaminare il Dashboard dello strumento

Nella scheda **Dashboard** è possibile visualizzare le metriche chiave per le revisioni del contenuto eseguite nello strumento. Vedere il numero di revisioni totali, complete e in sospeso per il contenuto di immagini, testo e video. 

La tabella delle verifiche **in sospeso** Mostra la suddivisione degli utenti e dei sottoteam con revisioni in sospeso o completate, nonché il tempo di SLA rimanente. È possibile selezionare gli elementi della tabella per passare alle relative revisioni. La casella di ricerca sopra la tabella consente di filtrare i risultati in base al nome del team e l'icona del **filtro** consente di filtrare in base ad altre metriche.

Passando alla scheda **revisioni completa** viene visualizzato il numero totale di elementi elaborati o completati da utenti e sottoteam. È possibile filtrare questi dati come le revisioni in sospeso.

Facendo clic sul testo nell'angolo superiore destro del dashboard vengono visualizzate le metriche personali giornaliere, che segnala il numero di revisioni completate per ogni tipo di contenuto.

> [!div class="mx-imgBorder"]
> ![Dashboard dello strumento di revisione in un browser](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Verifica credenziali strumento

Quando si esegue l'iscrizione con lo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com), verrà richiesto di selezionare un'area di Azure per l'account. Questo perché lo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com) genera una chiave di valutazione gratuita per i servizi content moderator di Azure. Questa chiave sarà necessaria per accedere ai servizi da una chiamata REST o da un SDK client. È possibile visualizzare la chiave e l'URL dell'endpoint API selezionando le credenziali di **amministratore**  >  **Credentials** .

> [!div class="mx-imgBorder"]
> ![Credenziali di Content Moderator](images/settings-6-credentials.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere [configurare lo strumento di revisione](./configure.md) per informazioni su come accedere alle risorse dello strumento di verifica e modificare le impostazioni.