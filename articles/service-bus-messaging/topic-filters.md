---
title: Filtri per gli argomenti del bus di servizio di Azure | Microsoft Docs
description: Questo articolo illustra in che modo i sottoscrittori possono definire i messaggi che vogliono ricevere da un argomento specificando filtri.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 5df343ff63c01a7cf10315b758e3d6fba8ac5674
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88066747"
---
# <a name="topic-filters-and-actions"></a>Filtri e azioni per gli argomenti

I sottoscrittori possono definire i messaggi che vogliono ricevere da un argomento. Per specificare tali messaggi, vengono usate una o più regole di sottoscrizione denominate. Ogni regola è costituita da una condizione che seleziona determinati messaggi e da un'azione che annota il messaggio selezionato. Per ogni condizione di regola corrispondente, la sottoscrizione genera una copia del messaggio, che può essere annotata in modo diverso per ogni regola.

Ogni nuova sottoscrizione di argomento creata ha una regola di sottoscrizione predefinita iniziale. Se non si specifica in modo esplicito una condizione di filtro per la regola, viene applicato il filtro **true**, che consente la selezione di tutti i messaggi nella sottoscrizione. Alla regola predefinita non è associata alcuna azione di annotazione.

Il bus di servizio supporta tre condizioni di filtro.

-   *Filtri booleani*: **TrueFilter** e **FalseFilter** determinano la selezione per la sottoscrizione di tutti i messaggi in arrivo (**true**) o di nessun messaggio in arrivo (**false**).

-   *Filtri SQL*: un **SqlFilter** contiene un'espressione condizionale di tipo SQL che viene valutata nel broker rispetto alle proprietà di sistema e definite dall'utente dei messaggi in arrivo. Nell'espressione condizionale, tutte le proprietà di sistema devono essere precedute dal prefisso `sys.`. Il [subset di linguaggio SQL per le condizioni di filtro](service-bus-messaging-sql-filter.md) verifica l'esistenza di proprietà ( `EXISTS` ), valori null ( `IS NULL` ), logiche not/and/or, operatori relazionali, semplici aritmetici numerici e semplici criteri di ricerca di testo con `LIKE` .

-   *Filtri di correlazione*: un **CorrelationFilter** contiene un set di condizioni che vengono confrontate con una o più proprietà utente e di sistema di un messaggio in arrivo. Un uso comune è la corrispondenza con la proprietà **CorrelationId** , ma l'applicazione può anche scegliere di confrontare le proprietà seguenti:

    - **ContentType**
     - **Etichetta**
     - **MessageId**
     - **ReplyTo**
     - **ReplyToSessionId**
     - **SessionId** 
     - **To**
     - qualsiasi proprietà definita dall'utente. 
     
     Esiste una corrispondenza quando il valore di una proprietà di un messaggio in arrivo è uguale al valore specificato nel filtro di correlazione. Per le espressioni stringa, nel confronto viene fatta distinzione tra maiuscole e minuscole. Quando si specificano più proprietà per la corrispondenza, vengono combinate dal filtro con una condizione di AND logico e il filtro quindi corrisponde se corrispondono tutte le condizioni.

Tutti i filtri valutano le proprietà del messaggio. I filtri non possono valutare il corpo del messaggio.

Regole di filtro complesse richiedono capacità di elaborazione. In particolare, l'utilizzo delle regole di filtro SQL provoca una minore velocità effettiva del messaggio a livello di sottoscrizione, argomento e spazio dei nomi. Laddove possibile, le applicazioni devono scegliere filtri di correlazione su filtri simili a SQL perché sono molto più efficienti nell'elaborazione e hanno minore effetto sulla velocità effettiva.

## <a name="actions"></a>Azioni

Con le condizioni di filtro SQL, è possibile definire un'azione per annotare il messaggio aggiungendo, rimuovendo o sostituendo le proprietà e i relativi valori. L'azione [usa un'espressione di tipo SQL](service-bus-messaging-sql-filter.md) basata approssimativamente sulla sintassi dell'istruzione SQL UPDATE. L'azione viene eseguita sul messaggio dopo che è stata individuata una corrispondenza e prima che il messaggio venga selezionato nella sottoscrizione. Le modifiche alle proprietà del messaggio sono private e limitate al messaggio copiato nella sottoscrizione.

## <a name="usage-patterns"></a>Modelli di utilizzo

Nello scenario di utilizzo più semplice per un argomento, ogni sottoscrizione riceve una copia di ogni messaggio inviato all'argomento e si ha così un modello di trasmissione.

I filtri e le azioni supportano altri due gruppi di modelli: partizionamento e routing.

Il partizionamento usa i filtri per distribuire i messaggi tra le diverse sottoscrizioni esistenti dell'argomento in modo prevedibile e reciprocamente esclusivo. Il modello di partizionamento viene usato quando si aumenta il numero di istanze di un sistema per gestire più contesti diversi in raggruppamenti identici a livello funzionale, ognuno contenente un subset dei dati complessivi, ad esempio le informazioni sui profili dei clienti. Con il partizionamento, un'entità di pubblicazione invia il messaggio a un argomento senza richiedere la conoscenza del modello di partizionamento. Il messaggio viene quindi spostato nella sottoscrizione corretta, da cui potrà quindi essere recuperato dal gestore di messaggi della partizione.

Il routing usa i filtri per distribuire i messaggi tra le sottoscrizioni dell'argomento in modo prevedibile, ma non necessariamente esclusivo. In combinazione con la funzionalità di [inoltro automatico](service-bus-auto-forwarding.md), i filtri per gli argomenti consentono di creare grafici di routing complessi all'interno di uno spazio dei nomi del bus di servizio per la distribuzione dei messaggi in un'area di Azure. Usando Funzioni di Azure o App per la logica di Azure come collegamento tra gli spazi dei nomi del bus di servizio di Azure, è possibile creare topologie globali complesse con integrazione diretta nelle applicazioni line-of-business.


> [!NOTE]
> Poiché il portale di Azure supporta ora la funzionalità Service Bus Explorer, i filtri di sottoscrizione possono essere creati o modificati dal portale. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli esempi seguenti: 

- [Esercitazione di invio e ricezione .NET-Basic con filtri](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [Filtri di argomento .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Modello di Azure Resource Manager](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)