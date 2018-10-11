---
title: Pianificare le applicazioni Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: Definire le finalità e le entità dell'app e quindi creare i relativi piani in Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: diberry
ms.openlocfilehash: e8e6c32eca932803acfe0e7854de885e7ab129fc
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2018
ms.locfileid: "48267665"
---
# <a name="plan-your-luis-app"></a>Pianificare l'app LUIS

È importante pianificare l'app. Identificare il dominio, tra cui possibili finalità ed entità rilevanti per l'applicazione.  

## <a name="identify-your-domain"></a>Identificare il dominio
Un'app LUIS è incentrata su un argomento specifico del dominio.  Ad esempio, si potrebbe avere un'app di viaggi su cui si eseguono prenotazioni di biglietti, voli, hotel e auto a noleggio. Un'altra app potrebbe fornire contenuti relativi ad allenamento fisico, monitoraggio degli sforzi e impostazione degli obiettivi. Identificare il dominio consente di trovare parole o frasi importanti per il dominio.

> [!TIP]
> LUIS offre [domini predefiniti](luis-how-to-use-prebuilt-domains.md) per molti scenari comuni.
> Verificare se è possibile utilizzare un dominio predefinito come punto di partenza per l'app.

## <a name="identify-your-intents"></a>Identificare le finalità
Pensare alle [finalità](luis-concept-intent.md) che sono importanti per l'attività dell'applicazione. Si consideri l'esempio di un'app di viaggi con funzioni per la prenotazione di un volo e il controllo delle previsioni meteo relative alla destinazione dell'utente. Per queste azioni è possibile definire le finalità "BookFlight" e "GetWeather". In un'app più complessa con più funzioni, si avranno più finalità e sarà necessario definirle con attenzione in modo che non siano troppo specifiche. Ad esempio, "BookFlight" e "BookHotel" potrebbero essere finalità separate, ma "BookInternationalFlight" e "BookDomesticFlight" potrebbero essere troppo simili.

> [!NOTE]
> È consigliabile limitare il numero delle finalità solo a quelle necessarie per eseguire le funzioni dell'app. Se si definiscono troppe finalità, per LUIS sarà più difficile classificare correttamente le espressioni. Se invece se ne definiscono troppo poche, potrebbero risultare così generali da sovrapporsi.

## <a name="create-example-utterances-for-each-intent"></a>Creare espressioni di esempio per ogni finalità
Dopo aver determinato le finalità, creare 10 o 15 espressioni di esempio per ciascuna. Per cominciare, il numero non deve essere inferiore a questo e non si devono creare molte espressioni per ogni finalità. Ogni espressione deve essere diversa dall'espressione precedente. Una buona varietà di espressioni comprende il conteggio complessivo delle parole, la scelta delle parole, i tempi verbali e la punteggiatura. 

## <a name="identify-your-entities"></a>Identificare le entità
Nelle espressioni di esempio individuare le entità che si intende estrarre. Per prenotare un volo sono necessarie alcune informazioni come destinazione, data, compagnia aerea, categoria del biglietto e classe di viaggio. Creare entità per questi tipi di dati e quindi contrassegnare le [entità](luis-concept-entity-types.md) nelle espressioni di esempio perché sono importanti per conseguire una finalità. 

Quando si determinano le entità da usare nell'app, tenere presente che esistono diversi tipi di entità per l'acquisizione di relazioni tra i tipi di oggetti. L'articolo [Entità in LUIS](luis-concept-entity-types.md) fornisce maggiori dettagli su questi diversi tipi.

### <a name="simple-entity"></a>Entità semplice
Un'entità semplice descrive un singolo concetto.

![entità semplice](./media/luis-plan-your-app/simple-entity.png)

Per altre informazioni sull'estrazione dell'entità semplice dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#simple-entity-data). Per altre informazioni sull'uso delle entità semplici, seguire questa [guida introduttiva](luis-quickstart-primary-and-secondary-data.md).

### <a name="hierarchical-entity"></a>Entità gerarchica
Un'entità gerarchica è un tipo speciale di entità **semplice** e definisce una categoria e i relativi membri sotto forma di relazione padre-figlio. La relazione è determinata dal contesto all'interno dell'espressione. I figli di un'entità gerarchica sono solo entità semplici.

![entità gerarchica](./media/luis-plan-your-app/hierarchical-entity.png)

Per altre informazioni sull'estrazione dell'entità gerarchica dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#hierarchical-entity-data). Per altre informazioni sull'uso delle entità gerarchiche, seguire questa [guida introduttiva](luis-quickstart-intent-and-hier-entity.md).

### <a name="composite-entity"></a>Entità composita
Un'entità composita è costituita da altre entità che formano le parti di un intero. Un'entità composita contiene un'ampia gamma di tipi di entità.

![entità composita](./media/luis-plan-your-app/composite-entity.png)

Per altre informazioni sull'estrazione dell'entità composita dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#composite-entity-data). Per altre informazioni sull'uso di un'entità composita, seguire la relativa [esercitazione](luis-tutorial-composite-entity.md).

### <a name="prebuilt-entity"></a>Entità predefinita
LUIS fornisce [entità predefinite](luis-prebuilt-entities.md) per i tipi di dati comuni, ad esempio il numero, la data, l'indirizzo di posta elettronica e l'URL. È possibile usare l'entità numerica predefinita per il numero di ticket in un ordine di ticket.

![entità predefinita number](./media/luis-plan-your-app/number-entity.png)

Per altre informazioni sull'estrazione dell'entità predefinita dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#prebuilt-entity-data). 

### <a name="list-entity"></a>Entità elenco 
Un'entità elenco è un elenco di valori specificato in modo esplicito. Ogni valore è costituito da uno o più sinonimi. In un'app di viaggi si potrebbe scegliere di creare un'entità elenco per rappresentare i nomi degli aeroporti.

![entità elenco](./media/luis-plan-your-app/list-entity.png)

Per altre informazioni sull'estrazione di entità elenco dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#list-entity-data). Per altre informazioni sull'uso di un'entità elenco, seguire la [guida introduttiva](luis-quickstart-intent-and-list-entity.md).

### <a name="regular-expression-entity"></a>Entità di espressione regolare
Un'entità di espressione regolare consente a LUIS di estrarre i dati in formato corretto da un'espressione in base a un'espressione regolare.

![Entità di espressione regolare](./media/luis-plan-your-app/regex-entity.png)

Per altre informazioni sull'estrazione di entità di espressione regolare dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#regular-expression-entity-data). Per altre informazioni sull'uso di un'entità di espressione regolare, seguire la [guida introduttiva](luis-quickstart-intents-regex-entity.md).

## <a name="next-steps"></a>Passaggi successivi
Dopo aver effettuato il training e la pubblicazione dell'app e aver ottenuto le espressioni di endpoint per l'app, occorre pianificare l'implementazione di miglioramenti della previsione mediante [apprendimento attivo](luis-how-to-review-endoint-utt.md), [elenchi di frasi](luis-concept-feature.md) e [criteri](luis-concept-patterns.md). 


* Vedere [Creare la prima app Language Understanding Intelligent Service (LUIS)](luis-get-started-create-app.md) per la procedura dettagliata per creare un'app LUIS.
