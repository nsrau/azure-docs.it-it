---
title: 'App per dispositivi mobili che chiama le API web (passare alla produzione): piattaforma delle identità Microsoft'
description: Informazioni su come compilare un'app per dispositivi mobili che chiama le API Web (passare alla produzione)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37d2de561a6f5841bf17a47fef86ad7639750d5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074951"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>App per dispositivi mobili che chiama web API: passare alla produzione

Questo articolo fornisce informazioni dettagliate su come migliorare la qualità e l'affidabilità dell'app per spostarlo nell'ambiente di produzione.

## <a name="handling-errors-in-mobile-applications"></a>Gestione degli errori nelle applicazioni per dispositivi mobili

In diversi flussi che abbiamo abbiamo presentato finora, sono presenti una varietà di condizioni di errore che può essere causato. Lo scenario principale per la gestione è errori invisibili all'utente e del fallback per l'interazione. Esistono condizioni aggiuntive, che è inoltre consigliabile per la produzione incluso alcun situazioni di rete, interruzioni del servizio, richiesto il consenso dell'amministratore e gli altri casi specifici dello scenario.

Tutte le librerie MSAL ha contenuto wiki e codice di esempio che approfondisce più approfondita la gestione di queste condizioni.

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Riduzione del rischio e l'analisi dei problemi

La raccolta dei dati consente all'app diagnosticare i problemi. Per altri dettagli sul tipo di dati è possibile raccogliere, vedere il wiki di piattaforme ogni MSAL.

- Gli utenti possono chiedere aiuto quando rileva un problema. Una procedura consigliata è per acquisire e archiviare temporaneamente i log e consentire agli utenti di caricarli in una posizione. MSAL fornisce estensioni di registrazione per acquisire informazioni dettagliate sull'autenticazione.
- Se disponibile, abilitare la telemetria tramite MSAL per raccogliere dati sul modo in cui gli utenti di accedere all'app.

## <a name="testing-your-app"></a>Testare l'app

Assicurarsi di testare l'app con il [elenco di controllo di integrazione](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
