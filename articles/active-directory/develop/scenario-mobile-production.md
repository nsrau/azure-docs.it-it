---
title: App per dispositivi mobili che chiama le API Web (sposta in produzione)-piattaforma di identità Microsoft
description: Informazioni su come creare un'app per dispositivi mobili che chiama API Web (passa alla produzione)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67788dd9257a0a4685313dce44c6a3dfb5e514df
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413598"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>App per dispositivi mobili che chiama le API Web-passa all'ambiente di produzione

Questo articolo fornisce informazioni dettagliate su come migliorare la qualità e l'affidabilità dell'app prima di spostarla nell'ambiente di produzione.

## <a name="handling-errors-in-mobile-applications"></a>Gestione degli errori nelle applicazioni per dispositivi mobili

A questo punto possono verificarsi numerose condizioni di errore nell'app. Gli scenari principali da gestire sono gli errori e i fallback invisibile all'interazione. Altre condizioni che è opportuno prendere in considerazione per la produzione includono situazioni senza rete, interruzioni del servizio, requisiti per il consenso dell'amministratore e altri casi specifici dello scenario.

Ogni libreria MSAL include codice di esempio e contenuto wiki che descrive come gestire le condizioni seguenti:

- [Wiki di MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Wiki di MSAL iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [Wiki MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Attenuazione e analisi dei problemi

Per diagnosticare i problemi nell'app, è utile per raccogliere i dati. Per informazioni sui tipi di dati che è possibile raccogliere, vedere wiki della piattaforma MSAL.

- Gli utenti potrebbero chiedere assistenza quando si verificano problemi. Una procedura consigliata consiste nell'acquisire e archiviare temporaneamente i log e fornire un percorso in cui gli utenti possono caricarli. MSAL fornisce estensioni di registrazione per l'acquisizione di informazioni dettagliate sull'autenticazione.
- Se è disponibile, abilitare la telemetria tramite MSAL per raccogliere i dati sulle modalità di accesso degli utenti all'app.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

Provare esempi aggiuntivi disponibili negli [esempi | App client pubbliche per desktop e dispositivi mobili](sample-v2-code.md#desktop-and-mobile-public-client-apps)
