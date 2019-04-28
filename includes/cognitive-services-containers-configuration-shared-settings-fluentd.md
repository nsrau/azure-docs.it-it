---
ms.openlocfilehash: ffd17f7a641e1481aa4c88f8b2eb12ec11fa7d8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60599424"
---
Fluentd è un agente di raccolta dati open source per la registrazione unificata. Le impostazioni `Fluentd` gestiscono la connessione del contenitore a un server [Fluentd](https://www.fluentd.org). Il contenitore include un provider di registrazione di Fluentd che consente al contenitore di scrivere log e, facoltativamente, dati delle metriche in un server di Fluentd.

La tabella seguente illustra le impostazioni di configurazione supportate nella sezione `Fluentd`.

| NOME | Tipo di dati | DESCRIZIONE |
|------|-----------|-------------|
| `Host` | string | Indirizzo IP o nome host DNS del server Fluentd. |
| `Port` | Integer | Porta del server Fluentd.<br/> Il valore predefinito è 24224. |
| `HeartbeatMs` | Integer | Intervallo di heartbeat, espresso in millisecondi. Se prima della scadenza di questo intervallo è non stato inviato alcun traffico dell'evento, viene inviato un heartbeat al server Fluentd. Il valore predefinito è 60000 millisecondi (1 minuto). |
| `SendBufferSize` | Integer | Spazio di buffer di rete, espresso in byte, allocato per le operazioni di invio. Il valore predefinito è 32768 byte (32 kilobyte). |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Timeout, espresso in millisecondi, per stabilire una connessione SSL/TLS con il server Fluentd. Il valore predefinito è 10000 millisecondi (10 secondi).<br/> Se `UseTLS` è impostato su false, questo valore viene ignorato. |
| `UseTLS` | Boolean | Indica se il contenitore deve usare SSL/TLS per comunicare con il server Fluentd. Il valore predefinito è False. |