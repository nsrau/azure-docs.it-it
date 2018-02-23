---
title: Prezzi e fatturazione - App per la logica di Azure | Microsoft Docs
description: Informazioni sul funzionamento dei prezzi e della fatturazione per App per la logica di Azure.
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2017
ms.author: LADocs; klam
ms.openlocfilehash: 49a66c826a98f7160b97b516e6fd541795ae3b0e
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="logic-apps-pricing-model"></a>Modello di determinazione prezzi delle app per la logica
Con App per la logica di Azure è possibile creare ed eseguire flussi di lavoro di integrazione automatizzati e scalabili nel cloud. Di seguito sono riportate informazioni dettagliate sul funzionamento dei prezzi e della fatturazione per App per la logica di Azure.
## <a name="consumption-pricing-model"></a>Modello di prezzi a consumo
Le nuove app per la logica create prevedono che si paghi solo per le risorse effettivamente usate. Le nuove app per la logica usano infatti un modello di determinazione dei prezzi a consumo, in base al quale vengono calcolate tutte le esecuzioni avviate da un'istanza dell'app per la logica.
### <a name="what-are-action-executions"></a>Informazioni sulle esecuzioni di azioni
Ogni passaggio della definizione di un'app per la logica è un'azione che include trigger, passaggi del flusso di controllo, chiamate ad azioni integrate e chiamate a connettori.
### <a name="triggers"></a>Trigger
I trigger sono azioni speciali che creano un'istanza di un'app per la logica ogni volta che si verifica un evento specifico. I trigger possono avere comportamenti diversi, che influiscono sulla misurazione dell'app per la logica.
* **Trigger di poll**: questo trigger controlla continuamente un endpoint fino a quando non riceve un messaggio che soddisfa i criteri per la creazione di un'istanza di un'app per la logica in grado di avviare il flusso di lavoro. L'intervallo di polling può essere configurato nel trigger tramite la finestra di progettazione delle app per la logica. Ogni richiesta di polling viene considerata un'esecuzione, anche quando non viene creata alcuna istanza di app per la logica.
* **Trigger di webhook**: questo trigger attende che un client invii una richiesta a un determinato endpoint. Ogni richiesta inviata all'endpoint del webhook viene conteggiata come esecuzione di azione. I trigger Richiesta e Webhook HTTP, ad esempio, sono entrambi trigger di webhook.
* **Trigger di ricorrenza**: questo trigger crea un'istanza dell'app per la logica in base all'intervallo di ricorrenza configurato nel trigger. Un trigger di ricorrenza, ad esempio, può essere configurato per essere eseguito ogni tre giorni o in base a una pianificazione più complessa.

Le esecuzioni dei trigger sono disponibili nella sezione Cronologia trigger del riquadro Panoramica delle app per la logica.

### <a name="actions"></a>Azioni
Le azioni predefinite, ad esempio le azioni che chiamano HTTP, Funzioni di Azure o Gestione API, così come i passaggi del flusso di controllo, vengono misurati come azioni native e sono associate a tipi specifici. Alle azioni che chiamano [connettori](https://docs.microsoft.com/connectors), ad esempio, è associato il tipo "ApiConnection". I connettori si distinguono tra connettori standard e aziendali e vengono misurati in base al rispettivo [modello di prezzo][pricing].
Tutte le azioni di esecuzione, indipendentemente dall'esito, vengono conteggiate e misurate come esecuzioni di azioni. Non vengono tuttavia conteggiate come esecuzioni di azioni sia le azioni ignorate a causa di condizioni non soddisfatte, sia le azioni non eseguite perché l'app per la logica è stata terminata prima del completamento. Per le app per la logica disabilitate non possono essere create nuove istanze. Di conseguenza, non viene effettuato alcun addebito mentre sono disabilitate.

> [!NOTE]
> Dopo aver disabilitato un'app per la logica, è possibile che l'arresto delle istanze in esecuzione richieda qualche minuto.

Le azioni eseguite all'interno di cicli vengono conteggiate per ogni iterazione del ciclo. Una singola azione in un ciclo ForEach che elabora un elenco di 10 elementi, ad esempio, viene conteggiata moltiplicando il numero di elementi dell'elenco (10) per il numero di azioni nel ciclo (1) più 1 per l'avvio del ciclo. Per questo esempio, quindi, il calcolo è (10 * 1) + 1, da cui risultano 11 esecuzioni di azioni.

### <a name="integration-account-usage"></a>Utilizzo dell'account di integrazione
L'utilizzo a consumo include un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md) in cui è possibile esplorare, sviluppare e testare gratuitamente le funzionalità [B2B/EDI](logic-apps-enterprise-integration-b2b.md) e di [elaborazione XML](logic-apps-enterprise-integration-xml.md) di App per la logica. In ogni area è possibile configurare un solo account di integrazione, in cui è possibile archiviare fino a 10 contratti e 25 mappe. Non sono invece previsti limiti in merito al numero di partner, schemi e certificati che è possibile caricare.

App per la logica offre anche account di integrazione di base e standard con il contratto di servizio di App per la logica standard. Gli account di integrazione di base vengono usati, in genere, quando si vuole usare solo la gestione dei messaggi o se un piccolo partner commerciale intrattiene una relazione commerciale con un'entità più grande. Gli account di integrazione standard supportano invece relazioni B2B più complesse e aumentano il numero di entità che è possibile gestire. Per altre informazioni, vedere la pagina relativa ai [prezzi di Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="pricing"></a>Prezzi
Per altre informazioni, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni su App per la logica][whatis]
* [Creare la prima app per la logica][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

