---
title: Aumentare la resilienza dell'autenticazione e dell'autorizzazione nelle applicazioni daemon sviluppate
titleSuffix: Microsoft identity platform
description: Linee guida per aumentare la resilienza dell'autenticazione e dell'autorizzazione nell'applicazione daemon usando la piattaforma di identità Microsoft
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: 74bfc9eeeb8375fca2c88a3fd3c31f17e130fc99
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919628"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-daemon-applications-you-develop"></a>Aumentare la resilienza dell'autenticazione e dell'autorizzazione nelle applicazioni daemon sviluppate

Questo articolo fornisce indicazioni su come gli sviluppatori possono usare la piattaforma di identità Microsoft e Azure Active Directory per aumentare la resilienza delle applicazioni daemon. Sono inclusi processi in background, servizi, app da server a server e applicazioni senza utenti.

![Un'applicazione daemon che effettua una chiamata a Microsoft Identity](media/resilience-daemon-app/calling-microsoft-identity.png)

## <a name="use-managed-identities-for-azure-resources"></a>Usare identità gestite per le risorse di Azure

Gli sviluppatori che compilano app daemon in Microsoft Azure possono usare [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Le identità gestite eliminano la necessità per gli sviluppatori di gestire segreti e credenziali. La funzionalità migliora la resilienza evitando errori di scadenza, errori di rotazione o attendibilità del certificato. Sono inoltre disponibili diverse funzionalità incorporate destinate in modo specifico per aumentare la resilienza.

Le identità gestite usano i token di accesso di lunga durata e le informazioni di Microsoft Identity per acquisire in modo proattivo nuovi token entro un intervallo di tempo elevato prima della scadenza del token esistente. L'app può continuare a essere eseguita durante il tentativo di acquisire un nuovo token.

Le identità gestite usano anche endpoint regionali per migliorare le prestazioni e la resilienza in caso di errori fuori area. L'uso di un endpoint di area consente di proteggere tutto il traffico all'interno di un'area geografica. Ad esempio, se la risorsa di Azure è in WestUS2, tutto il traffico, incluso il traffico generato da Microsoft Identity, deve rimanere in WestUS2. In questo modo vengono eliminati i possibili punti di errore consolidando le dipendenze del servizio.

## <a name="use-the-microsoft-authentication-library"></a>Usare Microsoft Authentication Library

Gli sviluppatori di app daemon che non usano identità gestite possono usare [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview), che semplifica l'implementazione dell'autenticazione e dell'autorizzazione e usa automaticamente le procedure consigliate per la resilienza. MSAL renderà più semplice il processo di fornire le credenziali client richieste. Ad esempio, non è necessario che l'applicazione implementi la creazione e la firma di asserzioni di token Web JSON quando si usano credenziali basate sui certificati.

### <a name="use-microsoftidentityweb-for-net-developers"></a>Usare Microsoft. Identity. Web per gli sviluppatori .NET

Gli sviluppatori che compilano app daemon in ASP.NET Core possono usare la libreria [Microsoft. Identity. Web](https://docs.microsoft.com/azure/active-directory/develop/microsoft-identity-web) . Questa libreria è basata su MSAL per rendere ancora più semplice l'implementazione dell'autorizzazione per le app ASP.NET Core. Include diverse strategie per la [cache di token distribuiti](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#distributed-token-cache) per le app distribuite che possono essere eseguite in più aree.

## <a name="cache-and-store-tokens"></a>Memorizzare nella cache e archiviare i token

Se non si usa MSAL per implementare l'autenticazione e l'autorizzazione, è possibile implementare alcune procedure consigliate per la memorizzazione nella cache e l'archiviazione dei token. MSAL implementa e segue automaticamente queste procedure consigliate.

Un'applicazione acquisisce i token da un provider di identità per autorizzare l'applicazione a chiamare le API protette. Quando l'app riceve i token, la risposta che contiene i token contiene anche una proprietà "Expires \_ in" che indica all'applicazione il tempo di memorizzazione nella cache e il riutilizzo del token. È importante che le applicazioni usino la proprietà "Expires \_ in" per determinare la durata del token. L'applicazione non deve mai tentare di decodificare un token di accesso all'API. L'uso del token memorizzato nella cache impedisce il traffico non necessario tra l'app e l'identità Microsoft. L'utente può rimanere connesso all'applicazione per la durata della durata del token.

## <a name="properly-handle-service-responses"></a>Gestisci correttamente le risposte del servizio

Infine, mentre le applicazioni devono gestire tutte le risposte di errore, esistono alcune risposte che possono influisca sulla resilienza. Se l'applicazione riceve un codice di risposta HTTP 429, troppe richieste, l'identità Microsoft limita le richieste. Se l'app continua a eseguire troppe richieste, la limitazione continuerà a impedire che l'app riceva i token. L'applicazione non deve tentare di acquisire di nuovo un token finché non viene superato il tempo, in secondi, nel campo della risposta "Retry-After". La ricezione di una risposta 429 spesso indica che l'applicazione non memorizza nella cache e riutilizza correttamente i token. Gli sviluppatori devono esaminare il modo in cui i token vengono memorizzati nella cache e riutilizzati nell'applicazione.

Quando un'applicazione riceve un codice di risposta 5xx HTTP, l'app non deve immettere un ciclo di ripetizione dei tentativi veloce. Quando è presente, l'applicazione deve rispettare la stessa "ripetizione del tentativo di gestione", come avviene per una risposta 429. Se la risposta non include l'intestazione "Retry-After", è consigliabile implementare un nuovo tentativo di back-off esponenziale con il primo tentativo almeno 5 secondi dopo la risposta.

Quando si verifica il timeout di una richiesta, le applicazioni non devono eseguire immediatamente nuovi tentativi. Implementare un nuovo tentativo di disattivazione esponenziale con il primo tentativo di almeno 5 secondi dopo la risposta.

## <a name="next-steps"></a>Passaggi successivi

- [Rafforzare la resilienza nelle applicazioni che gli utenti di accesso](resilience-client-app.md)
- [Resilienza della compilazione nell'infrastruttura di gestione delle identità e dell'accesso](resilience-in-infrastructure.md)
- [Resilienza delle build nei sistemi CIAM](resilience-b2c.md)
