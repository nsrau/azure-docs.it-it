---
title: Pianificare le applicazioni LUIS | Microsoft Docs
description: Definire le finalità e le entità dell'app e quindi creare i relativi piani in Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: diberry
ms.openlocfilehash: 8cafe2be190988cbcbe8da616fe17c9a35c722f3
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226024"
---
# <a name="plan-your-luis-app"></a>Pianificare l'app LUIS

È importante pianificare l'app prima di iniziarne la creazione in LUIS. Preparare quindi una struttura o uno schema delle possibili finalità ed entità pertinenti all'argomento dell'applicazione specifico del dominio.  

## <a name="identify-your-domain"></a>Identificare il dominio
Un'app LUIS è incentrata su un argomento specifico del dominio.  Ad esempio, si potrebbe avere un'app di viaggi su cui si eseguono prenotazioni di biglietti, voli, hotel e auto a noleggio. Un'altra app potrebbe fornire contenuti relativi ad allenamento fisico, monitoraggio degli sforzi e impostazione degli obiettivi. 

> [!TIP]
> LUIS offre [domini predefiniti](luis-how-to-use-prebuilt-domains.md) per molti scenari comuni.
> Verificare se è possibile utilizzare un dominio predefinito come punto di partenza per l'app.

## <a name="identify-your-intents"></a>Identificare le finalità
Pensare alle [finalità](luis-concept-intent.md) che sono importanti per l'attività dell'applicazione. Si consideri l'esempio di un'app di viaggi con funzioni per la prenotazione di un volo e il controllo delle previsioni meteo relative alla destinazione dell'utente. Per queste azioni è possibile definire le finalità "BookFlight" e "GetWeather". In un'app più complessa con più funzioni, si avranno più finalità e sarà necessario definirle con attenzione in modo che non siano troppo specifiche. Ad esempio, "BookFlight" e "BookHotel" potrebbero essere finalità separate, ma "BookInternationalFlight" e "BookDomesticFlight" potrebbero essere troppo simili.

> [!NOTE]
> È consigliabile limitare il numero delle finalità solo a quelle necessarie per eseguire le funzioni dell'app. Se si definiscono troppe finalità, per LUIS sarà più difficile classificare correttamente le espressioni. Se invece se ne definiscono troppo poche, potrebbero risultare così generali da sovrapporsi.


## <a name="identify-your-entities"></a>Identificare le entità
Per prenotare un volo sono necessarie alcune informazioni come destinazione, data, compagnia aerea, categoria del biglietto e classe di viaggio. È possibile aggiungere queste informazioni come [entità](luis-concept-entity-types.md), in quanto sono importanti per la realizzazione di una finalità. 

Quando si determinano le entità da usare nell'app, tenere presente che esistono diversi tipi di entità per l'acquisizione di relazioni tra i tipi di oggetti. L'articolo [Entità in LUIS](luis-concept-entity-types.md) fornisce maggiori dettagli su questi diversi tipi.

### <a name="simple-entity"></a>Entità semplice
Un'entità semplice descrive un singolo concetto.

![entità semplice](./media/luis-plan-your-app/simple-entity.png)

Per altre informazioni sull'estrazione dell'entità semplice dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#simple-entity-data). Per altre informazioni sull'uso delle entità semplici, seguire la relativa [guida introduttiva](luis-quickstart-primary-and-secondary-data.md).

### <a name="hierarchical-entity"></a>Entità gerarchica
Un'entità gerarchica è un tipo speciale di entità **semplice** e definisce una categoria e i relativi membri sotto forma di relazione padre-figlio.

![entità gerarchica](./media/luis-plan-your-app/hierarchical-entity.png)

Per altre informazioni sull'estrazione dell'entità gerarchica dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#hierarchical-entity-data). Per altre informazioni sull'uso delle entità gerarchiche, seguire la relativa [guida introduttiva](luis-quickstart-intent-and-hier-entity.md).

### <a name="composite-entity"></a>Entità composita
Un'entità composita è costituita da altre entità che formano le parti di un intero. 

![entità composita](./media/luis-plan-your-app/composite-entity.png)

Per altre informazioni sull'estrazione dell'entità composita dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#composite-entity-data). Per altre informazioni sull'uso di un'entità composita, seguire la relativa [esercitazione](luis-tutorial-composite-entity.md).

### <a name="prebuilt-entity"></a>Entità predefinite
LUIS fornisce [entità predefinite](luis-prebuilt-entities.md) per tipi comuni come `Number`, che può ad esempio essere usata per il numero di biglietti in un'ordine.

![entità predefinita number](./media/luis-plan-your-app/number-entity.png)

Per altre informazioni sull'estrazione di entità di espressione regolare dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#prebuilt-entity-data). 

### <a name="list-entity"></a>Entità elenco 
Un'entità elenco è un elenco di valori specificato in modo esplicito. Ogni valore è costituito da uno o più sinonimi. In un'app di viaggi si potrebbe scegliere di creare un'entità elenco per rappresentare i nomi degli aeroporti.

![entità elenco](./media/luis-plan-your-app/list-entity.png)

Per altre informazioni sull'estrazione di entità elenco dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#list-entity-data). Per altre informazioni sull'uso di un'entità elenco, seguire la [guida introduttiva](luis-quickstart-intent-and-list-entity.md).

### <a name="regular-expression-entity"></a>Entità di espressione regolare
Un'entità di espressione regolare consente a LUIS di estrarre i dati da un'espressione in base a un'espressione regex.

![Entità di espressione regolare](./media/luis-plan-your-app/regex-entity.png)

Per altre informazioni sull'estrazione di entità di espressione regolare dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#regular-expression-entity-data). Per altre informazioni sull'uso di un'entità di espressione regolare, seguire la [guida introduttiva](luis-quickstart-intents-regex-entity.md).

## <a name="after-getting-endpoint-utterances"></a>Dopo aver ottenuto le espressioni di endpoint
Dopo aver ottenuto le espressioni, di endpoint per l'app, occorre pianificare l'implementazione di miglioramenti della previsione mediante [apprendimento attivo](luis-how-to-review-endoint-utt.md), [elenchi di frasi](luis-concept-feature.md) e [criteri](luis-concept-patterns.md). 

### <a name="patternany-entity"></a>Entità pattern.any
Patterns.any è un segnaposto di lunghezza variabile usato solo nell'espressione di modello del [criterio](luis-concept-patterns.md) per contrassegnare l'inizio e la fine dell'entità. Le espressioni di modello si conformano alla [corretta sintassi](luis-concept-patterns.md#pattern-syntax) per identificare le entità e il testo ignorabile.


## <a name="next-steps"></a>Passaggi successivi
* Vedere [Creare la prima app Language Understanding Intelligent Service (LUIS)](luis-get-started-create-app.md) per la procedura dettagliata per creare un'app LUIS.