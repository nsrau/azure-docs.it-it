---
title: Frontdoor di Azure | Microsoft Docs
description: Questo articolo offre una panoramica di Azure Frontdoor. È possibile scoprire se è la scelta giusta per bilanciare il carico del traffico degli utenti per la propria applicazione.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: 19deb763c8e750490854892c90d0293d3e209c09
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515796"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Che cos'è il motore regole per Frontdoor di Azure? 

Il motore regole consente di personalizzare il modo in cui vengono gestite le richieste HTTP al perimetro e fornisce un maggior controllo sul comportamento delle applicazioni Web. Il motore regole per Frontdoor di Azure include diverse funzionalità chiave, tra cui:

- Routing basato su intestazione: instrada le richieste in base ai modelli nel contenuto delle intestazioni, dei cookie e delle stringhe di query.
- Routing basato su parametri: sfrutta una serie di condizioni di corrispondenza, tra cui argomenti POST, stringhe di query, cookie e metodi di richiesta, per instradare le richieste in base ai parametri delle richieste HTTP. 
- Sostituzione delle configurazioni della route, che consente di: 
    - Usare le funzionalità di reindirizzamento per restituire i codici 301/302/307/308 al client per il reindirizzamento a nuovi nomi host, percorsi e protocolli. 
    - Usare le funzionalità di inoltro per riscrivere il percorso dell'URL della richiesta senza eseguire un tradizionale reindirizzamento e inoltrare la richiesta al back-end appropriato nel pool back-end configurato. 
    - Personalizzare la configurazione della memorizzazione nella cache e cambiare dinamicamente una route dall'inoltro alla memorizzazione nella cache in base alle condizioni di corrispondenza. 

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="architecture"></a>Architecture 

Il motore regole gestisce le richieste al perimetro. Dopo aver configurato il motore regole, quando una richiesta raggiunge l'endpoint Frontdoor, verrà prima eseguito WAF, seguito dalla configurazione del motore regole associata al front-end/dominio. Quando viene eseguita una configurazione del motore regole, significa che la regola di routing padre è già una corrispondenza. Tutte le azioni di ogni regola all'interno della configurazione del motore regole vengono eseguite solo quando vengono soddisfatte tutte le condizioni di corrispondenza all'interno di tale regola. Se una richiesta non corrisponde a nessuna delle condizioni della configurazione del motore regole, viene eseguita la regola di routing predefinita. 

Nella configurazione seguente, ad esempio, un motore regole è configurato per aggiungere un'intestazione di risposta che cambia la validità massima del controllo della cache se viene soddisfatta la condizione di corrispondenza. 

![azione dell'intestazione di risposta](./media/front-door-rules-engine/rules-engine-architecture-3.png)

In un altro esempio si può notare che il motore regole è configurato per l'invio di un utente a una versione per dispositivi mobili del sito se la condizione di corrispondenza, ovvero il tipo di dispositivo, è vera. 

![sostituzione delle configurazioni della route](./media/front-door-rules-engine/rules-engine-architecture-1.png)

In entrambi questi esempi, quando nessuna delle condizioni di corrispondenza viene soddisfatta, viene eseguita la regola di route specificata. 

## <a name="terminology"></a>Terminologia 

Con il motore regole del servizio Frontdoor di Azure, è possibile creare una serie di configurazioni, ognuna composta da un set di regole. Di seguito viene illustrata la terminologia utile durante la configurazione del motore regole. 

- *Configurazione del motore regole*: un set di regole applicate a una singola regola di route. Ogni configurazione può essere composta da un massimo di 5 regole. È possibile creare fino a 10 configurazioni. 
- *Regola del motore regole*: una regola composta da un massimo di 10 condizioni di corrispondenza e 5 azioni.
- *Condizione di corrispondenza*: esistono numerose condizioni di corrispondenza che è possibile usare per analizzare le risposte in ingresso. Una regola può contenere fino a 10 condizioni di corrispondenza. Le condizioni di corrispondenza vengono valutate con un operatore **AND**. L'elenco completo delle condizioni di corrispondenza è disponibile [qui](front-door-rules-engine-match-conditions.md). 
- *Azione*: le azioni stabiliscono cosa succede alle richieste in ingresso. Le azioni delle intestazioni di richieste/risposte, l'inoltro, i reindirizzamenti e le riscritture sono già disponibili. Una regola può contenere fino a 5 azioni, ma solo una sostituzione delle configurazioni della route.  L'elenco completo di azioni è disponibile [qui](front-door-rules-engine-actions.md).


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come configurare la prima [configurazione del motore regole](front-door-tutorial-rules-engine.md). 
- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
