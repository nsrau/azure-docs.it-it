---
title: Come diagnosticare gli errori con il servizio connesso di Azure Active Directory
description: Il servizio connesso di Active Directory ha rilevato un tipo di autenticazione incompatibile.
services: active-directory
author: ghogen
manager: douge
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6f151251d76965cf1bc86216eac15a08f1adbc6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60296793"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnosi degli errori con il servizio connesso di Azure Active Directory

Durante il rilevamento di codice di autenticazione precedente, il server di connessione di Active Directory ha rilevato un tipo di autenticazione non compatibile.

Per rilevare correttamente il precedente codice di autenticazione in un progetto, è necessario che il progetto sia compilato.  Se viene visualizzato questo errore e non hai un codice di autenticazione precedente nel progetto, ricompilare e riprovare.

## <a name="project-types"></a>Tipi di progetto

Il servizio connesso verifica il tipo di progetto in corso di sviluppo, in modo da potervi inserire la logica di autenticazione corretta. Se è presente un controller che deriva da `ApiController` nel progetto, il progetto verrà considerato come un progetto WebAPI. Se nel progetto sono presenti solo controller che derivano da `MVC.Controller`, il progetto verrà considerato come un progetto MVC. Il servizio connesso non supporta altri tipi di progetto.

## <a name="compatible-authentication-code"></a>Codice di autenticazione compatibile

Il servizio connesso cerca inoltre le impostazioni di autenticazione configurate in precedenza o compatibili. Se sono presenti tutte le impostazioni, viene considerata come caso rientrante e il servizio connesso verrà aperto il impostazioni di visualizzazione.  Se solo alcune impostazioni sono presenti, viene considerata un caso di errore.

In un progetto MVC il servizio connesso cerca le impostazioni seguenti che derivano da usi precedenti del servizio:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Inoltre, il servizio connesso cerca le impostazioni seguenti in un progetto API Web, che derivano da usi precedenti del servizio:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Codice di autenticazione incompatibile

Il servizio connesso prova infine a rilevare le versioni del codice di autenticazione configurate con le versioni precedenti di Visual Studio. Se si riceve questo errore, significa che il progetto contiene un tipo di autenticazione non compatibile. Il servizio connesso rileva i tipi seguenti di autenticazione delle versioni precedenti di Visual Studio:

* Autenticazione di Windows
* Account utente singoli
* Account dell'organizzazione

Per individuare Autenticazione di Windows in un progetto MVC, il servizio connesso cerca l'elemento `authentication` nel file `web.config`.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Per individuare Autenticazione di Windows in un progetto WebAPI, il servizio connesso cerca l'elemento `IISExpressWindowsAuthentication` nel file `.csproj` del progetto:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Per individuare l'autenticazione per singoli account utente, il servizio connesso cerca l'elemento package nel file `packages.config`.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Per individuare una precedente forma di autenticazione di tipo account aziendale, il servizio connesso cerca il seguente elemento nel file `web.config`:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Per cambiare il tipo di autenticazione, rimuovere il tipo non compatibile e provare ad aggiungere di nuovo il servizio connesso.

Per altre informazioni, vedere [Scenari di autenticazione per Azure AD](authentication-scenarios.md).
