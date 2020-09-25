---
title: Concetti sugli SMS di Servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Informazioni sui concetti relativi agli SMS di Servizi di comunicazione.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e5cfc1e27bae10a1c67e4506afe9db825664785f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944238"
---
# <a name="sms-concepts"></a>Concetti sugli SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Servizi di comunicazione di Azure consente di inviare e ricevere messaggi di testo SMS usando le librerie client per gli SMS di Servizi di comunicazione. Queste librerie client possono essere usate per supportare gli scenari del servizio clienti, i promemoria degli appuntamenti, l'autenticazione a due fattori e altre esigenze di comunicazione in tempo reale. Gli SMS di Servizi di comunicazione consentono di inviare messaggi in modo affidabile, esponendo al contempo informazioni dettagliate su recapitabilità e percentuale di risposta.

Le funzionalità principali delle librerie client per gli SMS di Servizi di comunicazione di Azure includono:

-  Esperienza di configurazione **semplice** per l'aggiunta di funzionalità SMS alle applicazioni.
- Supporto per i messaggi a **velocità elevata** su numeri verdi per i casi d'uso di A2P (da applicazione a persona) negli Stati Uniti.
- Conversazioni **bidirezionali** per supportare scenari come supporto clienti, avvisi e promemoria degli appuntamenti.
- **Recapito affidabile** con report di recapito in tempo reale per i messaggi inviati dall'applicazione.
- **Analisi** per tenere traccia dei modelli di utilizzo e del coinvolgimento dei clienti.
- **Rifiuto esplicito della gestione del supporto** per rilevare automaticamente e rispettare i rifiuti espliciti per i numeri verdi. Servizi di comunicazione rileva i messaggi di arresto e avvio e invia le risposte predefinite seguenti agli utenti finali: 
  - ARRESTA: *"È stata annullata la sottoscrizione ai messaggi provenienti da questo numero. Rispondi AVVIA per ripetere la sottoscrizione."*
  - AVVIA: *"È stata ripetuta la sottoscrizione ai messaggi provenienti da questo numero. Rispondi ARRESTA per annullare la sottoscrizione."*
  - I messaggi di arresto e avvio verranno inoltrati di nuovo all'utente. Servizi di comunicazione di Azure invita a monitorare e implementare questi rifiuti espliciti per assicurarsi che non vengano inviati altri messaggi ai destinatari che hanno rifiutato le comunicazioni.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione all'invio di SMS](../../quickstarts/telephony-sms/send.md)

I documenti seguenti possono essere interessanti:

- Acquisire familiarità con la [libreria client per gli SMS](../telephony-sms/sdk-features.md)
- Ottenere un [numero di telefono](../../quickstarts/telephony-sms/get-phone-number.md) con supporto per gli SMS
- [Pianificare la soluzione SMS](../telephony-sms/plan-solution.md)
