---
title: API Microsoft Graph
description: L'API Microsoft Graph è un'API Web RESTful che consente di accedere alle risorse del servizio Microsoft Cloud.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 13cbeca909b445d75fcbcf7b3751183421479d42
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052563"
---
# <a name="microsoft-graph-api"></a>API Microsoft Graph

L'API Microsoft Graph è un'API Web RESTful che consente di accedere alle risorse del servizio Microsoft Cloud. Dopo la registrazione dell'app e l'ottenimento dei token di autenticazione per un utente o un servizio, è possibile effettuare richieste all'API Microsoft Graph. Per ulteriori informazioni, vedere [Panoramica di Microsoft Graph](/graph/overview).

Microsoft Graph espone le API REST e le librerie client per accedere ai dati nei servizi Microsoft 365 seguenti:
- Servizi Microsoft 365: approfondimenti, Excel, prenotazioni Microsoft, Microsoft teams, OneDrive, OneNote, Outlook/Exchange, Planner e SharePoint
- Servizi di sicurezza e mobilità aziendali: Advanced Threat Analytics, Advanced Threat Protection, Azure Active Directory, Identity Manager e Intune
- Servizi di Windows 10: attività, dispositivi, notifiche
- Dynamics 365 Business Central

## <a name="versions"></a>Versioni

Microsoft Graph supporta attualmente due versioni: v 1.0 e beta. La versione v 1.0 include le API disponibili a livello generale. Usare la versione v 1.0 per tutte le app di produzione. La versione beta include le API attualmente disponibili in anteprima. Poiché è possibile che vengano introdotte modifiche di rilievo alle API beta, è consigliabile usare la versione beta solo per testare le app in fase di sviluppo; Non usare API beta nelle app di produzione. Per ulteriori informazioni, vedere [la pagina relativa al controllo delle versioni, al supporto e ai criteri di modifica di rilievo per Microsoft Graph](/graph/versioning-and-support).

Per iniziare a usare le API beta, vedere informazioni di [riferimento sull'endpoint Microsoft Graph beta](/graph/api/overview?view=graph-rest-beta)

Per iniziare a usare le API v 1.0, vedere informazioni di [riferimento sull'API REST di Microsoft Graph v 1.0](/graph/api/overview?view=graph-rest-1.0)

## <a name="get-started"></a>Introduzione

Per leggere o scrivere in una risorsa, ad esempio un utente o un messaggio di posta elettronica, creare una richiesta simile alla seguente:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Per altre informazioni sugli elementi della richiesta costruita, vedere [usare l'API Microsoft Graph](/graph/use-the-api)

Sono disponibili esempi di Guida introduttiva che illustrano come accedere alla potenza dell'API Microsoft Graph. Gli esempi disponibili possono accedere a due servizi con una sola autenticazione: account Microsoft e Outlook. Ogni guida introduttiva accede alle informazioni dai profili account Microsoft utenti e visualizza gli eventi dal calendario.
Le guide introduttive coinvolgono quattro passaggi:
- Seleziona la tua piattaforma
- Ottenere l'ID app (ID client)
- Compilare l'esempio
- Accedi e visualizza gli eventi nel calendario

Quando si completa la Guida introduttiva, è disponibile un'app pronta per essere eseguita. Per ulteriori informazioni, vedere le [domande frequenti sulla Guida introduttiva Microsoft Graph](/graph/quick-start-faq). Per iniziare a usare gli esempi, vedere [Microsoft Graph Guida introduttiva](https://developer.microsoft.com/graph/quick-start).

## <a name="tools"></a>Strumenti

Microsoft Graph Explorer è uno strumento basato sul Web che è possibile usare per compilare e testare le richieste con Microsoft Graph API. È possibile accedere a Microsoft Graph Explorer in: `https://developer.microsoft.com/graph/graph-explorer` .

Postazione è uno strumento che è possibile usare anche per compilare e testare le richieste usando le API Microsoft Graph. È possibile scaricare il post in: `https://www.getpostman.com/` . Per interagire con Microsoft Graph in un impostore, è possibile usare la raccolta di Microsoft Graph in postazione. Per altre informazioni, vedere [usare il post con l'API Microsoft Graph](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta).