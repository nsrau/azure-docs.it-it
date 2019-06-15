---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132541"
---
Questo esempio prevede l'uso del servizio [Twilio](https://www.twilio.com/) per inviare messaggi SMS al telefono cellulare. Funzioni di Azure supporta già Twilio tramite l'[associazione a Twilio](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio) e l'esempio usa tale funzionalità.

È necessario per prima cosa disporre di un account Twilio. È possibile crearne uno gratuitamente all'indirizzo https://www.twilio.com/try-twilio. Dopo aver creato un account, aggiungere le tre **impostazioni** all'app per le funzioni.

| Nome impostazione app | Descrizione del valore |
| - | - |
| **TwilioAccountSid**  | SID dell'account Twilio |
| **TwilioAuthToken**   | Token di autenticazione per l'account Twilio |
| **TwilioPhoneNumber** | Numero di telefono associato all'account Twilio usato per inviare messaggi SMS. |