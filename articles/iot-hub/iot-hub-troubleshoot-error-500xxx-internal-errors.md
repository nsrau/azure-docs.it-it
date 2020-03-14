---
title: Risoluzione dei problemi interni dell'hub 500xxx di Azure
description: Informazioni su come correggere gli errori interni di 500xxx
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 7f3f5177e084693c45bed1088a4e1d091be100ed
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271044"
---
# <a name="500xxx-internal-errors"></a>Errori interni 500xxx

Questo articolo descrive le cause e le soluzioni per gli **errori interni di 500xxx**.

## <a name="symptoms"></a>Sintomi

La richiesta all'hub delle cose ha esito negativo con un errore che inizia con 500 e/o una sorta di "errore del server". Di seguito sono riportate alcune possibilità:

* **500001 servererror**: l'hub Internet ha eseguito un problema sul lato server.

* **500008 GenericTimeout**: l'hub Internet non è riuscito a completare la richiesta di connessione prima del timeout.

* **ServiceUnavailable (nessun codice errore)** : si è verificato un errore interno dell'hub.

* **InternalServerError (nessun codice errore)** : si è verificato un errore interno dell'hub.

## <a name="cause"></a>Causa

Possono essere presenti diverse cause per una risposta di errore 500xxx. In tutti i casi, il problema è probabilmente temporaneo. Anche se il team dell'hub IoT si impegna costantemente per il mantenimento del [contratto di servizio](https://azure.microsoft.com/support/legal/sla/iot-hub/), in piccoli subset di nodi dell'hub IoT possono occasionalmente verificarsi errori temporanei. Quando il dispositivo tenta di connettersi a un nodo in cui si sono verificati problemi, viene visualizzato questo errore.

## <a name="solution"></a>Soluzione

Per attenuare gli errori di 500xxx, emettere un nuovo tentativo dal dispositivo. Per [gestire automaticamente i tentativi](./iot-hub-reliability-features-in-sdks.md#connection-and-retry), assicurarsi di usare la versione più recente degli [Azure IoT SDK](./iot-hub-devguide-sdks.md). Per le procedure consigliate sulla gestione degli errori temporanei e la ripetizione dei tentativi, vedere [Gestione degli errori temporanei](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults).  Se il problema persiste, controllare [integrità risorse](./iot-hub-monitor-resource-health.md#use-azure-resource-health) e [lo stato di Azure](https://status.azure.com/) per verificare se l'hub cose ha un problema noto. È anche possibile usare la [funzionalità di failover manuale](./tutorial-manual-failover.md). Se non sono presenti problemi noti e il problema persiste, [contattare il supporto tecnico](https://azure.microsoft.com/support/options/) per ulteriori indagini.
