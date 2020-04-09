---
title: Preparare le API Web che chiamano app per dispositivi mobili per l'ambiente di produzione Azure
titleSuffix: Microsoft identity platform
description: Scopri come creare un'app per dispositivi mobili che chiama le API Web. (Preparare le app per l'ambiente di produzione.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 28ace84f9a80b71209d7963d02b66317292b151b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882540"
---
# <a name="prepare-mobile-apps-for-production"></a>Preparare le app per dispositivi mobili per la produzione

Questo articolo fornisce informazioni dettagliate su come migliorare la qualità e l'affidabilità dell'app per dispositivi mobili prima di spostarla nell'ambiente di produzione.

## <a name="handle-errors"></a>Gestire gli errori

Quando si prepara un'app per dispositivi mobili per la produzione, possono verificarsi diverse condizioni di errore. I casi principali che gestirai sono errori silenziosi e fallback all'interazione. Altre condizioni da considerare includono situazioni di non rete, interruzioni del servizio, requisiti per il consenso dell'amministratore e altri casi specifici dello scenario.

Per ogni tipo di libreria di autenticazione Microsoft (MSAL), è possibile trovare codice di esempio e contenuto wiki che descrive come gestire le condizioni di errore:

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Wiki MSAL iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>Mitigare e analizzare i problemi

Per diagnosticare meglio i problemi nell'app, raccogliere i dati. Per informazioni sui tipi di dati che è possibile raccogliere, vedere [Registrazione nelle applicazioni MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-logging).

Ecco alcuni suggerimenti per la raccolta dei dati:

- Gli utenti potrebbero chiedere aiuto in caso di problemi. È consigliabile acquisire e archiviare temporaneamente i log. Specificare un percorso in cui gli utenti possono caricare i log. MSAL fornisce estensioni di registrazione per acquisire informazioni dettagliate sull'autenticazione.

- Se la telemetria è disponibile, abilitarla tramite MSAL per raccogliere dati su come gli utenti accedono all'app.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

Per provare altri esempi, vedere [App client pubbliche desktop e mobili](sample-v2-code.md#desktop-and-mobile-public-client-apps).
