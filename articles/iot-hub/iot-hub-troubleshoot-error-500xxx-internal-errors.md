---
title: Risoluzione dei problemi relativi agli errori interni dell'hub IoT 500xxx di Azure IoT Hub
description: Comprendere come risolvere 500xxx Errori interni
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 7f3f5177e084693c45bed1088a4e1d091be100ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271044"
---
# <a name="500xxx-internal-errors"></a>Errori interni 500xxx

In questo articolo vengono descritte le cause e le soluzioni per **500xxx Errori interni**.

## <a name="symptoms"></a>Sintomi

La richiesta all'hub IoT ha esito negativo con un errore che inizia con 500 e/o una sorta di "errore del server". Alcune possibilità sono:

* **500001 ServerError:** l'hub IoT si è verificato un problema sul lato server.

* **500008 GenericTimeout:** l'hub IoT non è riuscito a completare la richiesta di connessione prima del timeout.

* **ServiceUnavailable (nessun codice di errore):** l'hub IoT ha rilevato un errore interno.

* **InternalServerError (nessun codice**di errore): l'hub IoT ha rilevato un errore interno.

## <a name="cause"></a>Causa

Ci possono essere una serie di cause per una risposta di errore 500xxx. In tutti i casi, il problema è molto probabilmente temporaneo. Anche se il team dell'hub IoT si impegna costantemente per il mantenimento del [contratto di servizio](https://azure.microsoft.com/support/legal/sla/iot-hub/), in piccoli subset di nodi dell'hub IoT possono occasionalmente verificarsi errori temporanei. Quando il dispositivo tenta di connettersi a un nodo in cui si sono verificati problemi, viene visualizzato questo errore.

## <a name="solution"></a>Soluzione

Per ridurre gli errori 500xxx, eseguire un nuovo tentativo dal dispositivo. Per [gestire automaticamente i tentativi](./iot-hub-reliability-features-in-sdks.md#connection-and-retry), assicurarsi di usare la versione più recente degli [Azure IoT SDK](./iot-hub-devguide-sdks.md). Per le procedure consigliate sulla gestione degli errori temporanei e la ripetizione dei tentativi, vedere [Gestione degli errori temporanei](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults).  Se il problema persiste, controllare [Integrità delle risorse](./iot-hub-monitor-resource-health.md#use-azure-resource-health) e Stato di [Azure](https://status.azure.com/) per verificare se l'hub IoT presenta un problema noto. È inoltre possibile utilizzare la funzionalità di [failover manuale](./tutorial-manual-failover.md). Se non sono presenti problemi noti e il problema persiste, [contattare](https://azure.microsoft.com/support/options/) il supporto tecnico per ulteriori indagini.
