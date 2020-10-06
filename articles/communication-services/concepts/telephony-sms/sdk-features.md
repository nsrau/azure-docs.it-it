---
title: Panoramica della libreria client per gli SMS per Servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Fornisce una panoramica della libreria client per gli SMS e delle relative offerte.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4077bbe0a98f6b7788af9b6c44f73abbc936c6aa
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332239"
---
# <a name="sms-client-library-overview"></a>Panoramica della libreria client per gli SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Le librerie client per gli SMS di Servizi di comunicazione di Azure possono essere usate per aggiungere messaggistica SMS alle applicazioni.

## <a name="sms-client-library-capabilities"></a>Funzionalità della libreria client per gli SMS

L'elenco seguente presenta il set di funzionalità attualmente disponibili nelle librerie client.

| Gruppo di funzionalità | Funzionalità                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Funzionalità principali | Inviare e ricevere messaggi SMS </br> *Emoji Unicode supportate*                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Ricevere report di recapito per i messaggi inviati                                            | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Tutti i set di caratteri (supporto per lingua/Unicode)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Supporto per messaggi lunghi (fino a 2048 caratteri)                                           | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Concatenazione automatica di messaggi lunghi                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
| Eventi            | Usare Griglia di eventi per configurare i webhook per la ricezione di messaggi in ingresso e report di recapito | ✔️   | ✔️    | ✔️    | ✔️      |
| Numero di telefono      | Numeri verdi                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Regolamentazione        | Gestione esplicita                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| Monitoraggio        | Monitorare l'utilizzo dei messaggi inviati e ricevuti                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| Chiamata PSTN      | Aggiungere funzionalità di chiamata PSTN al numero verde abilitato per SMS (anteprima privata)                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione all'invio di SMS](../../quickstarts/telephony-sms/send.md)

I documenti seguenti possono essere interessanti:

- Acquisire familiarità con i [concetti generali relativi agli SMS](../telephony-sms/concepts.md)
- Ottenere un [numero di telefono](../../quickstarts/telephony-sms/get-phone-number.md) con supporto per gli SMS
- [Pianificare la soluzione SMS](../telephony-sms/plan-solution.md)
