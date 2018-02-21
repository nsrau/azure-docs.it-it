---
title: Come diagnosticare gli errori con Connessione guidata di Azure Active Directory
description: La procedura guidata di connessione di Active Directory ha rilevato un tipo di autenticazione incompatibile.
services: active-directory
documentationcenter: 
author: kraigb
manager: mtillman
editor: 
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/05/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 186bb1ede11c869b1352906b7ebafe57025f4f09
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connection-wizard"></a>Diagnosi degli errori con Connessione guidata di Azure Active Directory
Durante il rilevamento di codice di autenticazione precedente, la procedura guidata ha rilevato un tipo di autenticazione non compatibile.   

## <a name="what-is-being-checked"></a>Elementi verificati
**Nota:** per rilevare correttamente il precedente codice di autenticazione in un progetto, è necessario che il progetto sia compilato.  Se si è verificato questo errore e non si ha il precedente codice di autenticazione del progetto, ricompilare e riprovare.

### <a name="project-types"></a>Tipi di progetto
La procedura guidata verifica il tipo di progetto in corso di sviluppo, in modo da potervi inserire la logica di autenticazione corretta.  Se nel progetto è presente un controller che deriva da `ApiController`, il progetto verrà considerato come un progetto WebAPI.  Se nel progetto sono presenti solo controller che derivano da `MVC.Controller`, il progetto verrà considerato come un progetto MVC.  Qualsiasi altro elemento non è supportato dalla procedura guidata.

### <a name="compatible-authentication-code"></a>Codice di autenticazione compatibile
La procedura guidata cerca inoltre le impostazioni di autenticazione configurate in precedenza o che sono compatibili.  Se sono presenti tutte le impostazioni, viene considerato come caso rientrante. La procedura guidata verrà aperta e visualizzerà le impostazioni.  Se sono presenti solo alcune impostazioni, verrà considerato come caso di errore.

In un progetto MVC la procedura guidata cerca le impostazioni seguenti che derivano da usi precedenti della procedura guidata:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

La procedura guidata cerca inoltre le impostazioni seguenti di un progetto API Web che derivano da usi precedenti della procedura guidata:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

### <a name="incompatible-authentication-code"></a>Codice di autenticazione incompatibile
La procedura guidata prova infine a rilevare le versioni del codice di autenticazione configurate con le versioni precedenti di Visual Studio. Se si riceve questo errore, significa che il progetto contiene un tipo di autenticazione non compatibile. La procedura guidata rileva i tipi seguenti di autenticazione dalle versioni precedenti di Visual Studio:

* Autenticazione di Windows 
* Account utente singoli 
* Account dell'organizzazione 

Per individuare Autenticazione di Windows in un progetto MVC, la procedura guidata cerca l'elemento `authentication` nel file **web.config** .

<pre>
    &lt;configuration&gt;
        &lt;system.web&gt;
            <span style="background-color: yellow">&lt;authentication mode="Windows" /&gt;</span>
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Per individuare Autenticazione di Windows in un progetto API Web, la procedura guidata cerca l'elemento `IISExpressWindowsAuthentication` nel file con estensione **csproj** del progetto:

<pre>
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;</span>
        &lt;/PropertyGroup>
    &lt;/Project&gt;
</pre>

Per individuare l'autenticazione per singoli account utente, la procedura guidata cerca l'elemento package dal file **Packages.config** .

<pre>
    &lt;packages&gt;
        <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /&gt;</span>
    &lt;/packages&gt;
</pre>

Per individuare una precedente forma di autenticazione di tipo account aziendale, la procedura guidata cerca il seguente elemento dal file **web.config**:

<pre>
    &lt;configuration&gt;
        &lt;appSettings&gt;
            <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /&gt;</span>
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

Per cambiare il tipo di autenticazione, rimuovere il tipo non compatibile ed eseguire di nuovo la procedura guidata.

Per altre informazioni, vedere [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md).

#<a name="next-steps"></a>Passaggi successivi
- [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md)