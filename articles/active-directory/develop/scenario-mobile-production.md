---
title: Spostare l'app mobile che chiama le API Web nell'ambiente di produzione-piattaforma di identità Microsoft | Azure
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
ms.openlocfilehash: 1a82fc7dc1b18fa21657170af29f7de7e84d7c1f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702029"
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
