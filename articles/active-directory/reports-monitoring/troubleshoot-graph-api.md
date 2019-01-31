---
title: Risoluzione degli errori dell'API di creazione report di Azure AD | Microsoft Docs
description: Fornisce la risoluzione degli errori durante la chiamata delle API di creazione report di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
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
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 2f40cca78db3d026de1c253f8694d553d88670dd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155153"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Risoluzione degli errori dell'API di creazione report di Azure Active Directory

Questo articolo elenca i messaggi di errore comuni che potrebbero verificarsi durante l'accesso ai report delle attività usando l'API Graph di Microsoft e i passaggi per la loro risoluzione.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Errore interno 500 del server HTTP durante l'accesso all'endpoint Microsoft Graph versione 2

L'endpoint Microsoft Graph versione 2 non è attualmente supportato: assicurarsi di accedere ai log attività usando l'endpoint Microsoft Graph versione 1.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Errore: Impossibile ottenere i ruoli utente da AD Graph

È possibile ricevere questo messaggio di errore quando si tenta di accedere agli accessi tramite Graph Explorer. Assicurarsi di essere connessi a un account usando entrambi i pulsanti di accesso nell'interfaccia utente di Graph Explorer, come illustrato nell'immagine seguente. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Errore: Impossibile eseguire operazioni di controllo della licenza Premium da AD Graph 

Se compare questo messaggio di errore durante il tentativo di accedere agli accessi tramite Graph Explorer, scegliere **Autorizzazioni di modifica** sotto l'account sulla barra di spostamento a sinistra e selezionare **Tasks.ReadWrite** e **Directory.Read.All**. 

![Interfaccia utente di Autorizzazioni di modifica](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Errore: Nessun tenant è B2C o il tenant non ha una licenza Premium

L'accesso ai report sugli accessi richiede una licenza Azure Active Directory Premium 1 (P1). Se viene visualizzato questo messaggio di errore durante l'accesso agli accessi, assicurarsi che il tenant sia concesso in licenza con una licenza Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Errore: L'utente non è associato ai ruoli autorizzati 

Se viene visualizzato questo messaggio di errore durante il tentativo di accedere ai log di controllo o agli accessi usando l'API, assicurarsi che l'account faccia parte del **ruolo con autorizzazioni di lettura per la sicurezza** o del **ruolo lettore report** nel tenant di Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Errore: L'applicazione non possiede l'autorizzazione "Leggi i dati della directory" di AAD 

Seguire i passaggi descritti in [Prerequisiti di accesso all'API di creazione report di Azure AD](howto-configure-prerequisites-for-reporting-api.md) per assicurarsi che l'applicazione venga eseguita con il set di autorizzazioni corretto. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Errore: L'applicazione non possiede l'autorizzazione "Leggere tutti i dati dei log di controllo" dell'API Microsoft Graph

Seguire i passaggi descritti in [Prerequisiti di accesso all'API di creazione report di Azure AD](howto-configure-prerequisites-for-reporting-api.md) per assicurarsi che l'applicazione venga eseguita con il set di autorizzazioni corretto. 

## <a name="next-steps"></a>Passaggi successivi

[Usare il riferimento all'API di controllo](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
[Usare il riferimento all'API di creazione report sull'attività di accesso](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
