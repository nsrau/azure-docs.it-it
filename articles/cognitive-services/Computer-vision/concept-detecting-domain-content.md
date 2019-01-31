---
title: Rilevare contenuti specifici di dominio - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Informazioni su come specificare un dominio di classificazione delle immagini per restituire informazioni più dettagliate su un'immagine.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 76484a2340e527dc016f321dbafa29adb7c358b5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157534"
---
# <a name="detecting-domain-specific-content"></a>Rilevamento di contenuti specifici del dominio

Oltre all'assegnazione di tag e alla classificazione di primo livello, Visione artificiale è in grado di rilevare informazioni specializzate (o specifiche di dominio). Le informazioni specializzate possono essere implementate come metodo autonomo o con la classificazione di alto livello. In tal modo è possibile perfezionare ulteriormente la tassonomia basata sulle 86 categorie con l'aggiunta di modelli specifici di dominio.

Per usare i modelli specifici di dominio, sono disponibili due opzioni.

* Analisi con ambito  
  Analizza solo un modello selezionato, con una chiamata POST HTTP. Se si conosce il modello da usare, specificarne il nome. Si ottengono solo informazioni rilevanti per il modello specificato. È possibile ad esempio usare questa opzione per eseguire il riconoscimento solo di persone famose. La risposta contiene un elenco di personaggi potenzialmente corrispondenti, associati ai punteggi di attendibilità.
* Analisi avanzata  
  Esegue l'analisi per indicare dettagli aggiuntivi relativi alle categorie della tassonomia 86. Questa opzione è disponibile per essere usata in applicazioni in cui gli utenti desiderano ottenere l'analisi di immagini generiche oltre ai dettagli di uno o più modelli specifici di dominio. Quando questo metodo viene richiamato, lo strumento di classificazione della tassonomia 86 viene chiamato per primo. Se una delle categorie corrisponde a uno dei modelli noti, viene eseguito un secondo passaggio delle chiamate alla funzione di classificazione. Se ad esempio il parametro `details` della chiamata POST HTTP è impostato su "all" o include "celebrities", il metodo chiama la funzione di classificazione dei personaggi famosi dopo quella della tassonomia 86. Se l'immagine è classificata come `people_` o una delle relative sottocategorie, viene chiamata la funzione di classificazione dei personaggi famosi.

## <a name="listing-domain-specific-models"></a>Definizione dell'elenco di modelli specifici di dominio

È possibile elencare i modelli specifici di dominio supportati da Visione artificiale. La tabella seguente include i modelli attualmente supportati per il rilevamento di contenuti specifici di dominio:

| NOME | DESCRIZIONE |
|------|-------------|
| celebrities | Riconoscimento di personaggi famosi, supportato per le immagini classificate nella categoria `people_` |
| landmarks | Riconoscimento di luoghi di interesse, supportato per le immagini classificate nella categoria `outdoor_` o `building_` |

### <a name="domain-model-list-example"></a>Esempio di elenco di modelli di dominio

La risposta JSON seguente elenca i modelli specifici di dominio supportati da Visione artificiale.

```json
{
    "models": [
        {
            "name": "celebrities",
            "categories": ["people_", "人_", "pessoas_", "gente_"]
        },
        {
            "name": "landmarks",
            "categories": ["outdoor_", "户外_", "屋外_", "aoarlivre_", "alairelibre_",
                "building_", "建筑_", "建物_", "edifício_"]
        }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Concetti relativi alla [classificazione delle immagini](concept-categorizing-images.md).
