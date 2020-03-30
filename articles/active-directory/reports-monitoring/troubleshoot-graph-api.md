---
title: Risoluzione degli errori dell'API di creazione report di Azure AD | Microsoft Docs
description: Fornisce la risoluzione degli errori durante la chiamata delle API di creazione report di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d1fb4f49e4f9ad41f971d869873200e6180b5cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399287"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Risoluzione degli errori dell'API di creazione report di Azure Active Directory

In questo articolo vengono elencati i messaggi di errore comuni che possono verificarsi durante l'accesso ai report di attività utilizzando l'API Microsoft Graph e la procedura per la loro risoluzione.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Errore interno 500 del server HTTP durante l'accesso all'endpoint Microsoft Graph versione 2

L'endpoint Microsoft Graph versione 2 non è attualmente supportato: assicurarsi di accedere ai log attività usando l'endpoint Microsoft Graph versione 1.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Errore: Nessuno dei tenant è B2C o il tenant non ha una licenza Premium

L'accesso ai report sugli accessi richiede una licenza Azure Active Directory Premium 1 (P1). Se viene visualizzato questo messaggio di errore durante l'accesso agli accessi, assicurarsi che il tenant sia concesso in licenza con una licenza Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Errore: L'utente non appartiene a ruoli autorizzati 

Se viene visualizzato questo messaggio di errore durante il tentativo di accedere ai log di controllo o agli accessi usando l'API, assicurarsi che l'account faccia parte del **ruolo con autorizzazioni di lettura per la sicurezza** o del **ruolo lettore report** nel tenant di Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Errore: L'applicazione non possiede l'autorizzazione "Leggi i dati della directory" di AAD 

Seguire i passaggi descritti in [Prerequisiti di accesso all'API di creazione report di Azure AD](howto-configure-prerequisites-for-reporting-api.md) per assicurarsi che l'applicazione venga eseguita con il set di autorizzazioni corretto. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Errore: applicazione mancante Microsoft Graph API 'Leggi tutti i dati del log di controllo' autorizzazione

Seguire i passaggi descritti in [Prerequisiti di accesso all'API di creazione report di Azure AD](howto-configure-prerequisites-for-reporting-api.md) per assicurarsi che l'applicazione venga eseguita con il set di autorizzazioni corretto. 

## <a name="next-steps"></a>Passaggi successivi

[Usare il riferimento all'API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
di controllo Usare il riferimento all'API del report attività di accessoUse the audit API reference[Use the sign-in activity report API reference](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
