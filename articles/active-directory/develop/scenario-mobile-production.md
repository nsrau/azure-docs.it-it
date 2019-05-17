---
title: 'App per dispositivi mobili che chiama le API web (passare alla produzione): piattaforma delle identità Microsoft'
description: Informazioni su come compilare un'app per dispositivi mobili di chiamate di web API (passare alla produzione)
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
ms.openlocfilehash: 5cc8b7c86495c2a60b07bef0a825e3872f787520
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550388"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>App per dispositivi mobili che chiama web API: passare alla produzione

Questo articolo fornisce informazioni dettagliate su come migliorare la qualità e affidabilità dell'app prima di spostarla nell'ambiente di produzione.

## <a name="handling-errors-in-mobile-applications"></a>Gestione degli errori nelle applicazioni per dispositivi mobili

Un numero di condizioni di errore può verificarsi nell'app a questo punto. Gli scenari principali per la gestione sono errori invisibili all'utente e fallback per l'interazione. Altre condizioni che è opportuno considerare per la produzione includono Nessuna rete situazioni, interruzioni dei servizi, i requisiti per il consenso dell'amministratore e gli altri casi specifici dello scenario.

Tutte le librerie MSAL ha contenuto wiki e codice di esempio che descrive come gestire queste condizioni:

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Riduzione del rischio e l'analisi dei problemi

Per diagnosticare i problemi nell'app, è utile per raccogliere i dati. Per informazioni sui tipi di dati è possibile raccogliere, vedere il wiki di piattaforma MSAL.

- Gli utenti potrebbero chiedere aiuto quando si verificano problemi. Una procedura consigliata è per acquisire e archiviare i log temporaneamente e fornire un percorso in cui gli utenti possono caricare li. MSAL fornisce estensioni di registrazione per acquisire informazioni dettagliate sull'autenticazione.
- Se è disponibile, abilitare la telemetria tramite MSAL per raccogliere dati sul modo in cui accedono gli utenti all'app.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
