---
title: File di inclusione
description: File di inclusione
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 04/28/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7d6718fb25b3743a50c52f11c8e19d80839b485c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557092"
---
<!-- operation names for the diag logs for IoT Hub -->

|Nome operazione|Level|Descrizione|
|------------- |-----|-----------|
|UndefinedRouteEvaluation|Informazioni|Il messaggio non può essere valutato con una condizione di concessione. Se, ad esempio, una proprietà nella condizione di query della route è assente nel messaggio. Altre informazioni sulla [sintassi di query di routing](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md).|
|RouteEvaluationError|Errore|Si è verificato un errore durante la valutazione del messaggio a causa di un problema con il formato del messaggio. Questo errore verrà registrato, ad esempio, se la codifica del contenuto non è specificata o il tipo di contenuto non è valido nel messaggio. Questi devono essere impostati nelle [proprietà di sistema](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties).|
|DroppedMessage|Errore|Il messaggio è stato eliminato e non è stato instradato. La causa potrebbe essere dovuta al fatto che il messaggio non corrisponde ad alcuna query o endpoint di routing è stato inattivo e non è stato possibile recapitare il messaggio dopo diversi tentativi. È consigliabile ottenere altri dettagli sull'endpoint usando l'API REST ottenere l' [integrità dell'endpoint](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointUnhealthy|Errore|L'endpoint non ha accettato messaggi dall'hub Internet e l'hub Internet sta provando a inviare di nuovo i messaggi. Si consiglia di osservare l'ultimo errore noto tramite l'API REST [ottenere l'integrità dell'endpoint](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointDead|Errore|L'endpoint non ha accettato messaggi dall'hub Internet per più di un'ora. Si consiglia di osservare l'ultimo errore noto tramite l'API REST [ottenere l'integrità dell'endpoint](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointHealthy|Informazioni|L'endpoint è integro e riceve messaggi dall'hub. Questo messaggio non viene registrato in modo continuo, ma viene registrato solo quando l'endpoint diventa di nuovo integro. Questo messaggio indica che l'hub Internet non è stato in grado di inviare messaggi all'endpoint, ma l'endpoint è ora integro.|
|OrphanedMessage|Informazioni|Il messaggio non corrisponde ad alcuna route.|
|InvalidMessage|Errore|Il messaggio non è valido a causa dell'incompatibilità con l'endpoint. Si consiglia di controllare le configurazioni dell'endpoint.|


Le operazioni *UndefinedRouteEvaluation*, *RouteEvaluationError* e *OrphanedMessage* sono limitate e registrate non più di una volta al minuto per l'hub.