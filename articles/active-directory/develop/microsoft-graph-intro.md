---
title: 'API di Microsoft Graph : Documenti Microsoft'
description: L'API Microsoft Graph è un'API Web RESTful che consente di accedere alle risorse del servizio cloud Microsoft.The Microsoft Graph API is a RESTful web API that enables you to access Microsoft Cloud service resources.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 6c1b4390282f6d54178365714b1e2e665b4cf061
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136501"
---
# <a name="microsoft-graph-api"></a>API Microsoft Graph

L'API Microsoft Graph è un'API Web RESTful che consente di accedere alle risorse del servizio cloud Microsoft.The Microsoft Graph API is a RESTful web API that enables you to access Microsoft Cloud service resources. Dopo aver registrato l'app e ottenuto i token di autenticazione per un utente o un servizio, puoi effettuare richieste all'API Microsoft Graph. Per ulteriori informazioni, vedere [Panoramica di Microsoft Graph](https://docs.microsoft.com/graph/overview).

Microsoft Graph espone le API REST e le librerie client per accedere ai dati nei seguenti servizi di Microsoft 365:
- Servizi di Office 365: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner e SharePoint
- Servizi di sicurezza e mobilità aziendale: Advanced Threat Analytics, Advanced Threat Protection, Azure Active Directory, Identity Manager e Intune
- Servizi di Windows 10: attività, dispositivi, notifiche
- Dynamics 365 Business Central

## <a name="versions"></a>Versioni

Microsoft Graph supporta attualmente due versioni: v1.0 e beta. La versione v1.0 include le API generalmente disponibili. Usa la versione v1.0 per tutte le app di produzione. La versione beta include le API attualmente in anteprima. Poiché potremmo introdurre modifiche di rilievo alle nostre API beta, ti consigliamo di usare la versione beta solo per testare le app in fase di sviluppo. non usare le API beta nelle app di produzione. Per ulteriori informazioni, vedere [Criteri di modifica di controllo delle versioni, supporto e interruzione per Microsoft Graph](https://docs.microsoft.com/graph/versioning-and-support).

Per iniziare a usare le API beta, vedere Informazioni di [riferimento sugli endpoint beta di Microsoft GraphTo](https://docs.microsoft.com/graph/api/overview?view=graph-rest-beta) start using the beta APIs, see Microsoft Graph beta endpoint reference

Per iniziare a usare le API v1.0, vedere Informazioni di riferimento sull'API REST di [Microsoft Graph v1.0To](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0) start using the v1.0 APIs, see Microsoft Graph REST API v1.0 reference

## <a name="get-started"></a>Introduzione

Per leggere o scrivere in una risorsa, ad esempio un utente o un messaggio di posta elettronica, è necessario creare una richiesta simile alla seguente:To read from or write to a resource such as a user or an email message, you construct a request that looks like the following:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Per altre informazioni sugli elementi della richiesta costruita, vedere [Usare l'API Microsoft GraphFor](https://docs.microsoft.com/graph/use-the-api) more information about the elements of the constructed request, see Use the Microsoft Graph API

Sono disponibili esempi di avvio rapido per mostrare come accedere alla potenza dell'API Microsoft Graph. Gli esempi disponibili accedono a due servizi con un'unica autenticazione: Account Microsoft e Outlook. Ogni guida introduttiva accede alle informazioni dai profili degli utenti dell'account Microsoft e visualizza gli eventi dal calendario.
Le guide introduttive prevedono quattro passaggi:
- Seleziona la tua piattaforma
- Ottenere l'ID app (ID client)
- Compilare l'esempio
- Accedere e visualizzare gli eventi nel calendario

Quando completi la guida introduttiva, hai un'app pronta per l'esecuzione. Per ulteriori informazioni, vedere le [domande frequenti sulla guida introduttiva](https://docs.microsoft.com/graph/quick-start-faq)di Microsoft Graph . Per iniziare a utilizzare gli esempi, vedere [Guida introduttiva](https://developer.microsoft.com/graph/quick-start)di Microsoft Graph .

## <a name="tools"></a>Strumenti

Microsoft Graph Explorer è uno strumento basato sul Web che è possibile utilizzare per compilare e testare le richieste utilizzando le API di Microsoft Graph. È possibile accedere a `https://developer.microsoft.com/graph/graph-explorer`Microsoft Graph Explorer all'indirizzo: .

Postman è uno strumento che puoi anche usare per compilare e testare le richieste usando le API di Microsoft Graph. È possibile scaricare Postman all'indirizzo: `https://www.getpostman.com/`. Per interagire con Microsoft Graph in Postman, utilizzare la raccolta Microsoft Graph in Postman. Per ulteriori informazioni, vedere [Usare Postman con l'API Microsoft Graph.](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)
