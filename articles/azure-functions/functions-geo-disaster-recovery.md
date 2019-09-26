---
title: Ripristino di emergenza geografico di funzioni di Azure | Microsoft Docs
description: Come usare le aree geografiche per eseguire il failover e usare il ripristino di emergenza in funzioni di Azure.
services: functions
documentationcenter: na
author: wesmc7777
manager: jeconnoc
keywords: funzioni di Azure, modelli, procedure consigliate, funzioni
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: jehollan
ms.openlocfilehash: 87f8b2fac3a123f2b92f77a70535f952b25557e0
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310269"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Ripristino di emergenza geografico di funzioni di Azure

Quando si verifica un tempo di inattività per intere aree o Data Center di Azure, è fondamentale che il calcolo continui l'elaborazione in un'area diversa.  In questo articolo vengono illustrate alcune strategie che è possibile usare per distribuire le funzioni per consentire il ripristino di emergenza.

## <a name="basic-concepts"></a>Concetti di base

Funzioni di Azure vengono eseguite in un'area specifica.  Per ottenere una disponibilità più elevata, è possibile distribuire le stesse funzioni in più aree.  In più aree è possibile fare in modo che funzioni in esecuzione nel modello attivo */attivo* o nel modello *attivo/passivo* .  

* Attivo/attivo. Entrambe le aree sono eventi attivi e di ricezione (duplicato o rotazionale). Active/Active è consigliato per le funzioni HTTPS in combinazione con lo sportello anteriore di Azure.
* Attivo/passivo. Un'area è attiva e riceve eventi, mentre un database secondario è inattivo.  Quando è necessario il failover, l'area secondaria viene attivata e acquisisce la sovrascrittura.  Questa operazione è consigliata per funzioni non HTTP come bus di servizio e hub eventi.

Per altre informazioni sulle distribuzioni [in più aree, vedere come eseguire app in più aree](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) .

## <a name="activeactive-for-https-functions"></a>Active/Active per le funzioni HTTPS

Per ottenere distribuzioni attive/attive di funzioni, è necessario un componente in grado di coordinare gli eventi tra entrambe le aree.  Per le funzioni HTTPS, questo coordinamento viene eseguito usando il front-end di [Azure](../frontdoor/front-door-overview.md).  Il front-end di Azure può instradare le richieste HTTPS Round Robin tra più funzioni internazionali.  Consente inoltre di controllare periodicamente l'integrità di ogni endpoint.  Se una funzione a livello di area smette di rispondere ai controlli di integrità, il front-end di Azure ne estrae la rotazione e trasmette il traffico solo alle funzioni integre.  

![Architettura per la porta e la funzione front-end di Azure](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>Attivo/attivo per le funzioni non HTTPS

È comunque possibile ottenere distribuzioni attive/attive per le funzioni non HTTPS.  Tuttavia, è necessario prendere in considerazione il modo in cui le due aree interagiranno o coordinano tra loro.  Se la stessa app per le funzioni è stata distribuita in due aree, ciascuna delle quali attiva sulla stessa coda del bus di servizio, fungerebbe da consumer concorrente in caso di rimozione dalla coda di tale coda.  Anche se ciò significa che ogni messaggio viene elaborato solo da una delle istanze, significa che esiste comunque un singolo punto di errore sul bus di servizio singolo.  Se si distribuiscono due code del bus di servizio (una in un'area primaria, una in un'area secondaria) e le due app per le funzioni che puntano alla propria coda di area, la richiesta di verifica è ora la modalità di distribuzione dei messaggi in coda tra le due aree.  Spesso ciò significa che ogni editore tenta di pubblicare un messaggio in *entrambe* le aree e ogni messaggio viene elaborato da entrambe le app per le funzioni attive.  Sebbene venga creato un modello attivo/attivo, vengono create altre richieste relative alla duplicazione del calcolo e al momento o al modo in cui i dati vengono consolidati.  Per questi motivi, è consigliabile usare il modello attivo/passivo per i trigger non HTTPS.

## <a name="activepassive-for-non-https-functions"></a>Attivo/passivo per le funzioni non HTTPS

Attivo/passivo consente solo a una singola funzione di elaborare ogni messaggio, ma fornisce un meccanismo per eseguire il failover in un'area secondaria in caso di emergenza.  Funzioni di Azure funziona con il ripristino [geografico del bus di servizio](../service-bus-messaging/service-bus-geo-dr.md) di Azure e il [ripristino geografico di hub eventi di Azure](../event-hubs/event-hubs-geo-dr.md).

Usando i trigger di hub eventi di Azure come esempio, il modello attivo/passivo implica quanto segue:

* Hub eventi di Azure distribuito in un'area primaria e secondaria.
* Emergenza geografica abilitata per associare l'hub eventi primario e secondario.  Viene anche creato un "alias" che è possibile usare per connettersi a hub eventi e passare dal database primario a quello secondario senza modificare le informazioni di connessione.
* App per le funzioni distribuite in un'area primaria e secondaria.
* Le app per le funzioni vengono attivate sulla stringa di connessione *diretta* (non alias) per il rispettivo Hub eventi. 
* I Publisher nell'hub eventi devono pubblicare nella stringa di connessione alias. 

![Architettura di esempio attivo-passivo](media/functions-geo-dr/active-passive.png)

Prima del failover, i Publisher che inviano all'alias condiviso vengono indirizzati all'hub eventi primario.  L'app per le funzioni primaria è in ascolto esclusivamente dell'hub eventi primario.  L'app per le funzioni secondarie sarà passiva e inattiva.  Non appena viene avviato il failover, i Publisher che inviano all'alias condiviso verranno ora indirizzati all'hub eventi secondario.  L'app per le funzioni secondaria ora diventa attiva e avvia automaticamente l'attivazione.  Un failover efficace in un'area secondaria può essere interamente gestito dall'hub eventi, con le funzioni che diventano attive solo quando il rispettivo Hub eventi è attivo.

Per altre informazioni, vedere informazioni e considerazioni sul failover con il [bus di servizio](../service-bus-messaging/service-bus-geo-dr.md) e [Hub eventi](../event-hubs/event-hubs-geo-dr.md).

## <a name="next-steps"></a>Passaggi successivi

* [Crea sportello anteriore di Azure](../frontdoor/quickstart-create-front-door.md)
* [Considerazioni sul failover di hub eventi](../event-hubs/event-hubs-geo-dr.md#considerations)
