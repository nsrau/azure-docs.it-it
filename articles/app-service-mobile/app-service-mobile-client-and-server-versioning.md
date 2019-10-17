---
title: Controllo delle versioni SDK di client e server in App per dispositivi mobili e Servizi mobili | Documentazione Microsoft
description: Elenco degli SDK del client e compatibilità con versioni SDK del server per Servizi mobili e App per dispositivi mobili di Azure
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 5be72a4125b276d85174a7a056cbbc2c23053e89
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388875"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Controllo delle versioni client e server in App per dispositivi mobili e Servizi mobili
> [!NOTE]
> Visual Studio App Center supporta servizi end-to-end e integrati fondamentali per lo sviluppo di app per dispositivi mobili. Gli sviluppatori possono usare i servizi **Build**, **Test** e **Distribute** per configurare una pipeline di integrazione e distribuzione continue. Dopo la distribuzione dell'app, gli sviluppatori possono monitorarne lo stato e l'utilizzo tramite i servizi **Analytics** e **Diagnostics** e interagire con gli utenti tramite il servizio **Push**. Gli sviluppatori possono anche usare il servizio **Auth** per autenticare gli utenti e il servizio **Data** per salvare e sincronizzare i dati dell'app nel cloud.
>
> Per integrare i servizi cloud nelle applicazioni per dispositivi mobili, iscriversi ad [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

La versione più recente di Servizi mobili di Azure è la funzionalità **App per dispositivi mobili** del Servizio app di Azure.

Gli SDK del client e del server di App per dispositivi mobili in origine si basano su quelle in Servizi mobili, ma *non* sono compatibili tra loro.
È infatti necessario usare un SDK del client di *App per dispositivi mobili* con un SDK del server di *App per dispositivi mobili* e lo stesso vale per *Servizi mobili*. Questo contratto viene applicato tramite un valore di intestazione speciale utilizzato dagli SDK del client e del server, `ZUMO-API-VERSION`.

Nota: ogni volta che in questo documento si fa riferimento a un back-end di *Servizi mobili* , esso non deve necessariamente essere ospitato su Servizi mobili. È ora possibile eseguire la migrazione di un servizio mobile per l'esecuzione nel servizio app senza apportare modifiche al codice, ma il servizio continuerà a usare le versioni SDK di *Servizi mobili*.

## <a name="header-specification"></a>Specifica di intestazione
La chiave `ZUMO-API-VERSION` può essere specificata nell'intestazione HTTP o nella stringa di query. Il valore è una stringa di versione nel formato **x.y.z**.

ad esempio:

GET https://service.azurewebsites.net/tables/TodoItem

HEADERS: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Esclusione del controllo della versione
È possibile rifiutare esplicitamente il controllo della versione impostando un valore **true** per l'impostazione dell'app **MS_SkipVersionCheck**. Specificarlo nel file web.config o nella sezione Impostazioni applicazione del portale di Azure.

> [!NOTE]
> Esistono una serie di modifiche di comportamento tra Servizi mobili e App per dispositivi mobili, in particolare per quanto riguarda la sincronizzazione offline, l’autenticazione e le notifiche push. È consigliabile rifiutare esplicitamente solo il controllo della versione dopo aver completato un test per assicurarsi che queste modifiche del comportamento non interferiscano con le funzionalità dell'applicazione.

## <a name="2.0.0"></a>Client e server di App per dispositivi mobili di Azure
### <a name="MobileAppsClients"></a> SDK del client di *App* per dispositivi mobili
Il controllo della versione è stata introdotta a partire dalle seguenti versioni dell’SDK del client per **App per dispositivi mobili di Azure**:

| Piattaforma client | Versione | Valore dell'intestazione della versione |
| --- | --- | --- |
| Client gestito (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>SDK del server di *App* per dispositivi mobili
Il controllo della versione è incluso nelle seguenti versioni dell’SDK del server:

| Piattaforma server | SDK | Intestazione della versione accettata |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Comportamento dei back-end di app per dispositivi mobili
| ZUMO-API-VERSION | Valore di MS_SkipVersionCheck | Response |
| --- | --- | --- |
| x.y.z o Null |True |200 - OK |
| Null |False/Non specificato |400 - Richiesta non valida |
| 1.x.y |False/Non specificato |400 - Richiesta non valida |
| 2.0.0-2.x.y |False/Non specificato |200 - OK |
| 3.0.0-3.x.y |False/Non specificato |400 - Richiesta non valida |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
