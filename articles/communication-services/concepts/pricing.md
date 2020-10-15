---
title: Scenari relativi ai prezzi per chiamate (vocali/video) e chat
titleSuffix: An Azure Communication Services concept document
description: Informazioni sul modello di determinazione prezzi di Servizi di comunicazione.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4eec258cf642688c87b363ff467f1f368727a013
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761227"
---
# <a name="pricing-scenarios"></a>Scenari relativi ai prezzi

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Looks like other resources point to a /pricing page that is managed by Commerce or Marketing? https://azure.microsoft.com/pricing/details/functions/ Should we? FOLLOWING UP WITH KRISTIN TO FIND THE RIGHT ACS PAGE
-->

I prezzi per Servizi di comunicazione di Azure sono basati su un modello con pagamento in base al consumo senza anticipi. Vengono fatturati solo il consumo e l'uso dei servizi.

## <a name="voicevideo-calling-and-screen-sharing"></a>Chiamate vocali/video e condivisione dello schermo

Servizi di comunicazione di Azure consente l'aggiunta di chiamate vocali/video e della condivisione dello schermo alle applicazioni. È possibile incorporare l'esperienza nelle applicazioni usando le librerie client JavaScript, Objective-C (Apple), Java (Android) o .NET. Vedere l'[elenco completo delle librerie client disponibili](./sdk-options.md).

### <a name="pricing"></a>Prezzi

I servizi di chiamata e condivisione dello schermo vengono addebitati al costo di 0,004 USD al minuto per ogni partecipante per le chiamate di gruppo. Per comprendere i vari flussi di chiamate possibili, vedere [questa pagina](./call-flows.md).

Per ogni partecipante alla chiamata verrà conteggiato in fattura il numero di minuti di connessione alla chiamata. Questo criterio vale indipendentemente dal fatto che l'utente effettui una chiamata video, una chiamata vocale o la condivisione dello schermo.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>Esempio di prezzi: chiamata vocale/video di gruppo tramite le librerie client JS e iOS

Alice ha effettuato una chiamata di gruppo con i colleghi Bob e Charlie. Alice e Bob hanno usato le librerie client JS, mentre Charlie ha usato le librerie client iOS.

- La chiamata è durata complessivamente 60 minuti.
- Alice e Bob hanno partecipato all'intera chiamata. Alice ha attivato il video per cinque minuti e ha condiviso lo schermo per 23 minuti. Bob ha tenuto il video attivo per l'intera chiamata (60 minuti) e ha condiviso lo schermo per 12 minuti.
- Charlie ha abbandonato la chiamata dopo 43 minuti. Charlie ha usato audio e video per l'intervallo di tempo in cui è stato presente (43 minuti).

**Calcoli dei costi**

- 2 partecipanti x 60 minuti x 0,004 USD per partecipante al minuto = 0,48 USD [video e audio vengono addebitati alla stessa tariffa]
- 1 partecipante x 43 minuti x 0,004 USD per partecipante al minuto = 0,172 USD [video e audio vengono addebitati alla stessa tariffa]

**Costo totale della chiamata di gruppo**: 0,48 USD + 0,172 USD = 0,652 USD

## <a name="chat"></a>Chat

Con Servizi di comunicazione è possibile potenziare l'applicazione con la funzionalità di invio e ricezione di messaggi di chat tra due o più utenti. Le librerie client di chat sono disponibili per JavaScript, .NET, Python e Java. Vedere [questa pagina per informazioni sulle librerie client](./sdk-options.md).

### <a name="price"></a>Prezzo

Vengono addebitati $ 0,0008 per ogni messaggio di chat inviato.

### <a name="pricing-example-chat-between-two-users"></a>Esempio di prezzi: chat tra due utenti 

Geeta avvia un thread di chat con Emily per condividere un aggiornamento e inviare 5 messaggi. La chat dura 10 minuti, in cui Geeta ed Emily inviano altri 15 messaggi ciascuna.

**Calcoli dei costi** 
- Numero di messaggi inviati (5 + 15 + 15) x 0,0008 USD = 0,028 USD

### <a name="pricing-example-group-chat-with-multiple-users"></a>Esempio di prezzi: chat di gruppo con più utenti 

Charlie avvia un thread di chat con gli amici Casey e Jasmine per pianificare una vacanza. Chattano per un intervallo di tempo durante il quale Charlie, Casey e Jasmine inviano rispettivamente 20, 30 e 18 messaggi. Si rendono conto che anche la loro amica Rose potrebbe essere interessata al viaggio, quindi l'aggiungono al thread di chat e condividono con lei tutta la cronologia dei messaggi. 

Rose visualizza i messaggi e inizia a chattare. Nel frattempo, Casey riceve una chiamata e decide di riprendere la conversazione in un secondo momento. Charlie, Jasmine e Rose decidono le date del viaggio e inviano rispettivamente altri 30, 25 e 35 messaggi.

**Calcoli dei costi** 

- Numero di messaggi inviati (20 + 30 + 18 + 30 + 25 + 35) x 0,0008 USD = 0,1264 USD


## <a name="telephony-and-sms"></a>Telefonia e SMS

## <a name="price"></a>Prezzo 

Per i servizi di telefonia i prezzi vengono calcolati al minuto, mentre per gli SMS in base a singolo messaggio. I prezzi vengono determinati in base al tipo e alla località del numero in uso, oltre che alla destinazione delle chiamate e dei messaggi SMS.

### <a name="telephone-calling"></a>Chiamate telefoniche

Le tradizionali chiamate telefoniche, ossia quelle effettuate tramite rete PSTN (Public Switched Telephone Network) sono disponibili con pagamento in base al consumo per i numeri di telefono degli Stati Uniti. Il prezzo è un addebito al minuto basato sul tipo di numero usato e sulla destinazione della chiamata. La tabella seguente include i dettagli relativi ai prezzi per le destinazioni più comuni delle chiamate. Per l'elenco completo di destinazioni, vedere l'[elenco dettagliato dei prezzi](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv).


#### <a name="united-states-calling-prices"></a>Prezzi per le chiamate negli Stati Uniti

I prezzi seguenti includono le imposte e le tariffe richieste per le comunicazioni fino al 30 giugno 2021:

|Tipo di numero   |Per effettuare chiamate   |Per ricevere chiamate|
|--------------|-----------|------------|
|Locale     |A partire da $ 0,013/min       |$ 0,0085/min        |
|Numero verde |$ 0,013/min   |$ 0,0220/min |

#### <a name="other-calling-destinations"></a>Altre destinazioni delle chiamate

I prezzi seguenti includono le imposte e le tariffe richieste per le comunicazioni fino al 30 giugno 2021:

|Per effettuare chiamate a   |Prezzo al minuto|
|-----------|------------|
|Canada     |A partire da $ 0,013/min   |
|Regno Unito     |A partire da $ 0,015/min   |
|Germania     |A partire da $ 0,015/min   |
|Francia     |A partire da $ 0,016/min   |


### <a name="sms"></a>SMS

Per gli SMS è disponibile il pagamento in base al consumo. Il prezzo è un addebito per messaggio basato sulla destinazione del messaggio. I messaggi possono essere inviati da numeri verdi a numeri di telefono situati all'interno degli Stati Uniti. Si noti che i numeri di telefono locali (geografici) non possono essere usati per l'invio di messaggi SMS.

I prezzi seguenti includono le imposte e le tariffe richieste per le comunicazioni fino al 30 giugno 2021:

|Paese   |Inviare messaggi|Ricevere messaggi|
|-----------|------------|------------|
|USA (numero verde)    |$ 0,0075/messaggio   | $ 0,0075/messaggio |