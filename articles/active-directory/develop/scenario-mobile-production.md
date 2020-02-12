---
title: Preparare le API Web per la chiamata di app per dispositivi mobili per la produzione | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'app per dispositivi mobili che chiama le API Web. (Preparare le app per la produzione).
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
ms.openlocfilehash: 1ea19b8b76f4eb4a2c984f0e39eb0fd373c8b83c
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132378"
---
# <a name="prepare-mobile-apps-for-production"></a>Preparare le app per dispositivi mobili per la produzione

Questo articolo fornisce informazioni dettagliate su come migliorare la qualità e l'affidabilità dell'app per dispositivi mobili prima di spostarla nell'ambiente di produzione.

## <a name="handle-errors"></a>Gestire gli errori

Quando si prepara un'app per dispositivi mobili per la produzione, possono verificarsi diverse condizioni di errore. I casi principali che si gestiranno sono gli errori e i fallback invisibile all'interazione. Altre condizioni che è necessario considerare includono situazioni senza rete, interruzioni del servizio, requisiti per il consenso dell'amministratore e altri casi specifici dello scenario.

Per ogni tipo di Microsoft Authentication Library (MSAL), è possibile trovare il codice di esempio e il contenuto wiki che descrivono come gestire le condizioni di errore:

- [Wiki di MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Wiki di MSAL iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [Wiki MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>Attenuare ed esaminare i problemi

Per diagnosticare meglio i problemi nell'app, raccogliere i dati. Per informazioni sui tipi di dati che è possibile raccogliere, vedere [registrazione in applicazioni MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-logging).

Ecco alcuni suggerimenti per la raccolta dei dati:

- Gli utenti potrebbero chiedere assistenza in caso di problemi. Una procedura consigliata consiste nell'acquisire e archiviare temporaneamente i log. Specificare un percorso in cui gli utenti possono caricare i log. MSAL fornisce estensioni di registrazione per l'acquisizione di informazioni dettagliate sull'autenticazione.

- Se la telemetria è disponibile, abilitarla tramite MSAL per raccogliere i dati sul modo in cui gli utenti possono accedere all'app.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

Per provare esempi aggiuntivi, vedere [app client pubbliche per desktop e dispositivi mobili](sample-v2-code.md#desktop-and-mobile-public-client-apps).
