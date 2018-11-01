---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133106"
---
Questo esempio prevede l'uso del servizio [Twilio](https://www.twilio.com/) per inviare messaggi SMS al telefono cellulare. Funzioni di Azure supporta già Twilio tramite l'[associazione a Twilio](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio) e l'esempio usa tale funzionalità.

È necessario per prima cosa disporre di un account Twilio. È possibile crearne uno gratuitamente all'indirizzo https://www.twilio.com/try-twilio. Dopo aver creato un account, aggiungere le tre **impostazioni** all'app per le funzioni.

| Nome impostazione app | Descrizione del valore |
| - | - |
| **TwilioAccountSid**  | SID dell'account Twilio |
| **TwilioAuthToken**   | Token di autenticazione per l'account Twilio |
| **TwilioPhoneNumber** | Numero di telefono associato all'account Twilio usato per inviare messaggi SMS. |