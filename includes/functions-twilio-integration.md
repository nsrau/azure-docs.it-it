---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179948"
---
Questo esempio prevede l'uso del servizio [Twilio](https://www.twilio.com/) per inviare messaggi SMS al telefono cellulare. Funzioni di Azure supporta già Twilio tramite l'[associazione a Twilio](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio) e l'esempio usa tale funzionalità.

È necessario per prima cosa disporre di un account Twilio. È possibile crearne uno gratuitamente all'indirizzo https://www.twilio.com/try-twilio. Dopo aver creato un account, aggiungere le tre **impostazioni** all'app per le funzioni.

| Nome impostazione app | Descrizione del valore |
| - | - |
| **TwilioAccountSid**  | SID dell'account Twilio |
| **TwilioAuthToken**   | Token di autenticazione per l'account Twilio |
| **TwilioPhoneNumber** | Numero di telefono associato all'account Twilio usato per inviare messaggi SMS. |