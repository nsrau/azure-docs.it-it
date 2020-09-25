---
title: includere file
description: includere file
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: c0ba8e76e069bf9dc1c96aecdc670699c32b3c96
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944589"
---
## <a name="object-model"></a>Modello a oggetti

I token di accesso utente consentono di autorizzare gli utenti dell'applicazione a connettersi direttamente alla risorsa di Servizi di comunicazione di Azure. Questi token vengono generati nel server, passati all'applicazione client e quindi usati per inizializzare le librerie client di Servizi di comunicazione. A tale scopo, è necessario compilare un endpoint del servizio attendibile in grado di autenticare gli utenti e rilasciare i token di accesso utente. Questo servizio deve mantenere un mapping permanente tra le identità utente dell'applicazione e gli utenti di Servizi di comunicazione di Azure.

La classe CommunicationIdentityClient fornisce tutte le funzionalità relative a utenti e token di accesso. È possibile crearne un'istanza con una stringa di connessione e quindi usarla per gestire gli utenti e rilasciare i token di accesso.

> [!WARNING]
> I token sono dati sensibili, perché garantiscono l'accesso alle risorse dell'utente. Pertanto, è fondamentale proteggere i token in modo che non vengano compromessi. È necessario creare un endpoint del servizio attendibile che rilasci solo token di accesso agli utenti autorizzati dell'applicazione. Se si memorizzano nella cache i token di accesso utente in un archivio di backup, si consiglia di usare la crittografia.

### <a name="access-token-scopes"></a>Ambiti del token di accesso

Gli ambiti consentono di specificare la funzionalità esatta di Servizi di comunicazione di Azure che un token di accesso utente sarà in grado di autorizzare. Gli ambiti vengono applicati ai singoli token di accesso utente. Servizi di comunicazione di Azure supporta gli ambiti per i token di accesso utente seguenti:

| Nome   | Descrizione                                                                         |
| ------ | ----------------------------------------------------------------------------------- |
| `Chat` | Consente di partecipare a una chat                                         |
| `VoIP` | Consente di effettuare e ricevere chiamate VoIP usando la libreria client per le chiamate* |
| `Pstn` | Consente di effettuare chiamate PSTN usando la libreria client per le chiamate*           |

\* La funzionalità non è ancora supportata e sarà presto disponibile

Se si vuole rimuovere la capacità di un utente di accedere ad alcune funzionalità specifiche, è necessario innanzitutto [revocare i token di accesso esistenti](#revoke-user-access-tokens) che possono includere ambiti indesiderati, prima di emettere un nuovo token con un set più limitato di ambiti.
