---
title: Filtri per gli argomenti del bus di servizio di Azure | Microsoft Docs
description: In questo articolo viene illustrato come i sottoscrittori possono definire quali messaggi desiderano ricevere da un argomento specificando i filtri.
services: service-bus-messaging
documentationcenter: ''
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: spelluru
ms.openlocfilehash: fb6092b7ccb3d1a4214f8d26119d9dc50b0ed317
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482057"
---
# <a name="topic-filters-and-actions"></a>Filtri e azioni per gli argomenti

I sottoscrittori possono definire i messaggi che vogliono ricevere da un argomento. Per specificare tali messaggi, vengono usate una o più regole di sottoscrizione denominate. Ogni regola è costituita da una condizione che seleziona determinati messaggi e da un'azione che annota il messaggio selezionato. Per ogni condizione di regola corrispondente, la sottoscrizione genera una copia del messaggio, che può essere annotata in modo diverso per ogni regola.

Ogni nuova sottoscrizione di argomento creata ha una regola di sottoscrizione predefinita iniziale. Se non si specifica in modo esplicito una condizione di filtro per la regola, viene applicato il filtro **true**, che consente la selezione di tutti i messaggi nella sottoscrizione. Alla regola predefinita non è associata alcuna azione di annotazione.

Il bus di servizio supporta tre condizioni di filtro.

-   *Filtri booleani*: **TrueFilter** e **FalseFilter** determinano la selezione per la sottoscrizione di tutti i messaggi in arrivo (**true**) o di nessun messaggio in arrivo (**false**).

-   *Filtri SQL*: un **SqlFilter** contiene un'espressione condizionale di tipo SQL che viene valutata nel broker rispetto alle proprietà di sistema e definite dall'utente dei messaggi in arrivo. Nell'espressione condizionale, tutte le proprietà di sistema devono essere precedute dal prefisso `sys.`. Il sottoinsieme del [linguaggio SQL per](service-bus-messaging-sql-filter.md) le`EXISTS`condizioni di`IS NULL`filtro verifica l'esistenza di proprietà ( ), valori null `LIKE`( ), NOT/AND/OR logici, operatori relazionali, aritmetica numerica semplice e criteri di ricerca di testo semplice con .

-   *Filtri di correlazione*: un **CorrelationFilter** contiene un set di condizioni che vengono confrontate con una o più proprietà utente e di sistema di un messaggio in arrivo. Un utilizzo comune consiste nel trovare una corrispondenza con la proprietà **CorrelationId,** ma l'applicazione può anche scegliere di eseguire la corrispondenza con le proprietà seguenti:A common use is to match against the CorrelationId property, but the application can also choose to match against the following properties:

    - **Contenttype**
     - **Etichetta**
     - **Messageid**
     - **Replyto**
     - **ReplyToSessionId**
     - **SessionId** 
     - **A**
     - qualsiasi proprietà definita dall'utente. 
     
     Esiste una corrispondenza quando il valore di una proprietà di un messaggio in arrivo è uguale al valore specificato nel filtro di correlazione. Per le espressioni stringa, nel confronto viene fatta distinzione tra maiuscole e minuscole. Quando si specificano più proprietà per la corrispondenza, vengono combinate dal filtro con una condizione di AND logico e il filtro quindi corrisponde se corrispondono tutte le condizioni.

Tutti i filtri valutano le proprietà del messaggio. I filtri non possono valutare il corpo del messaggio.

Regole di filtro complesse richiedono capacità di elaborazione. In particolare, l'uso delle regole di filtro SQL causa una velocità effettiva complessiva dei messaggi inferiore a livello di sottoscrizione, argomento e spazio dei nomi. Quando possibile, le applicazioni devono scegliere i filtri di correlazione rispetto ai filtri di tipo SQL perché sono molto più efficienti nell'elaborazione e hanno un impatto minore sulla velocità effettiva.

## <a name="actions"></a>Azioni

Con le condizioni di filtro SQL, è possibile definire un'azione per annotare il messaggio aggiungendo, rimuovendo o sostituendo le proprietà e i relativi valori. L'azione [usa un'espressione di tipo SQL](service-bus-messaging-sql-filter.md) basata approssimativamente sulla sintassi dell'istruzione SQL UPDATE. L'azione viene eseguita sul messaggio dopo che è stato abbinato e prima che il messaggio venga selezionato nella sottoscrizione. Le modifiche alle proprietà del messaggio sono private e limitate al messaggio copiato nella sottoscrizione.

## <a name="usage-patterns"></a>Modelli di utilizzo

Nello scenario di utilizzo più semplice per un argomento, ogni sottoscrizione riceve una copia di ogni messaggio inviato all'argomento e si ha così un modello di trasmissione.

I filtri e le azioni supportano altri due gruppi di modelli: partizionamento e routing.

Il partizionamento usa i filtri per distribuire i messaggi tra le diverse sottoscrizioni esistenti dell'argomento in modo prevedibile e reciprocamente esclusivo. Il modello di partizionamento viene usato quando si aumenta il numero di istanze di un sistema per gestire più contesti diversi in raggruppamenti identici a livello funzionale, ognuno contenente un subset dei dati complessivi, ad esempio le informazioni sui profili dei clienti. Con il partizionamento, un'entità di pubblicazione invia il messaggio a un argomento senza richiedere la conoscenza del modello di partizionamento. Il messaggio viene quindi spostato nella sottoscrizione corretta, da cui potrà quindi essere recuperato dal gestore di messaggi della partizione.

Il routing usa i filtri per distribuire i messaggi tra le sottoscrizioni dell'argomento in modo prevedibile, ma non necessariamente esclusivo. In combinazione con la funzionalità di [inoltro automatico](service-bus-auto-forwarding.md), i filtri per gli argomenti consentono di creare grafici di routing complessi all'interno di uno spazio dei nomi del bus di servizio per la distribuzione dei messaggi in un'area di Azure. Usando Funzioni di Azure o App per la logica di Azure come collegamento tra gli spazi dei nomi del bus di servizio di Azure, è possibile creare topologie globali complesse con integrazione diretta nelle applicazioni line-of-business.


> [!NOTE]
> Attualmente il portale di Azure non consente di specificare le regole di filtro per le sottoscrizioni. È possibile usare uno qualsiasi degli SDK o dei modelli di Azure Resource Manager supportati per definire le regole di sottoscrizione. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli esempi seguenti:See the following samples: 

- [.NET - Esercitazione di invio e ricezione di base con filtri](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET - Filtri per argomento](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/samples/javascript/advanced/topicFilters.js)
- [Tipo di script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/samples/typescript/src/advanced/topicFilters.ts)
- [Modello di Azure Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)


