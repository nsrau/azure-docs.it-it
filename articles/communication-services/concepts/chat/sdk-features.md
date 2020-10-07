---
title: Panoramica della libreria client di chat per Servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Informazioni sulla libreria client di chat di Servizi di comunicazione di Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 3b66c962758afc28da3c6be938c13d768c4f2572
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91666144"
---
# <a name="chat-client-library-overview"></a>Panoramica della libreria client di chat

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Le librerie client di chat di Servizi di comunicazione di Azure possono essere usate per aggiungere chat in tempo reale ed elaborate alle applicazioni.

## <a name="chat-client-library-capabilities"></a>Funzionalità della libreria client di chat

Nell'elenco seguente viene presentato il set di funzionalità attualmente disponibili nelle librerie client di chat di Servizi di comunicazione.

| Gruppo di funzionalità | Funzionalità                                                                                                          | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | --- | ----- | ---- | -----  |
| Funzionalità principali | Creare un thread di chat tra 2 o più utenti (fino a 250 utenti)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Aggiornare l'argomento di un thread di chat                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |
|                   | Aggiungere o rimuovere membri da un thread di chat                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Scegliere se condividere la cronologia dei messaggi di chat con i membri appena aggiunti - *tutti/nessuno/fino a una certa data-ora* | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | Ottenere un elenco di tutti i membri del thread di chat                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Eliminare un thread di chat                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Ottenere un elenco delle appartenenze ai thread di chat di un utente                                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Ottenere informazioni per un thread di chat specifico                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Inviare e ricevere messaggi in un thread di chat                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | Modificare il contenuto di un messaggio dopo l'invio                                                                   | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Eliminare un messaggio                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Contrassegnare un messaggio con priorità normale o alta al momento dell'invio                                               | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Inviare e ricevere conferme di lettura per i messaggi letti dai membri <br/> *Non disponibile quando sono presenti più di 20 membri in un thread di chat*    | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Inviare e ricevere notifiche di digitazione in corso quando un membro sta digitando attivamente un messaggio in un thread di chat <br/> *Non disponibile quando sono presenti più di 20 membri in un thread di chat*      | ✔️   | ✔️   | ✔️    | ✔️    |
|                   | Ottenere tutti i messaggi in un thread di chat <br/> *Emoji Unicode supportati*                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Inviare emoji come parte del contenuto del messaggio                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|Segnalazione in tempo reale (abilitata dal pacchetto di segnalazione proprietario)| Ricevere una notifica quando un utente riceve un nuovo messaggio in un thread di chat di cui è membro                                     | ✔️   | ❌    | ❌  | ❌  |
|                    | Ricevere una notifica quando un messaggio è stato modificato da un altro membro in un thread di chat di cui si è membri                | ✔️   | ❌    | ❌    | ❌  |
|                    | Ricevere una notifica quando un messaggio è stato eliminato da un altro membro in un thread di chat di cui si è membri                | ✔️   | ❌    | ❌    | ❌  |
|                    | Ricevere una notifica quando un altro membro del thread di chat sta digitando                                                             | ✔️   | ❌    | ❌    | ❌  |
|                    | Ricevere una notifica quando un altro membro ha letto un messaggio (conferma di lettura) nel thread di chat                               | ✔️   | ❌    | ❌    | ❌  |
| Eventi             | Usare Griglia di eventi per sottoscrivere l'attività degli utenti nei thread di chat e integrare servizi di notifica o logica di business personalizzati     | ✔️   | ✔️  | ✔️    | ✔️  |
| Monitoraggio        | Monitorare l'utilizzo in termini di messaggi inviati                                                                               | ✔️   | ✔️  | ✔️    | ✔️  |
|                    | Monitorare la qualità e lo stato delle richieste API effettuate dall'app e configurare gli avvisi tramite il portale                                                          | ✔️   | ✔️  | ✔️    | ✔️  |
|Funzionalità aggiuntive | Usare le [API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/) insieme alla libreria client di chat per abilitare le funzionalità intelligenti, *traduzione della lingua e analisi del sentiment del messaggio in arrivo in un client, conversione della voce in testo scritto per scrivere un messaggio mentre il membro parla e così via.*                                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione alle chat](../../quickstarts/chat/get-started.md)

I documenti seguenti possono essere interessanti:

- Acquisire familiarità con i [concetti relativi alle chat](../chat/concepts.md)
