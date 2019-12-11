---
title: Introduzione ai Azure AD nei progetti MVC .NET | Azure
description: Informazioni su come iniziare a usare Azure Active Directory nei progetti MVC .NET dopo la connessione o la creazione di una directory Azure AD tramite i servizi connessi di Visual Studio
author: ghogen
manager: jillfra
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a603f37850cba13328889fcd120851f3c97c8c3
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74966538"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Introduzione ad Azure Active Directory (progetti MVC ASP.NET)

> [!div class="op_single_selector"]
> - [Per iniziare](vs-active-directory-dotnet-getting-started.md)
> - [Risultati](vs-active-directory-dotnet-what-happened.md)

Questo articolo fornisce istruzioni aggiuntive da seguire dopo l'aggiunta di Active Directory a un progetto MVC ASP.NET tramite il comando **Progetto > Servizi connessi** di Visual Studio. Se il servizio non è ancora stato aggiunto al progetto, è possibile farlo in qualsiasi momento.

Vedere [Modifiche apportate al progetto MVC](vs-active-directory-dotnet-what-happened.md) per informazioni sulle modifiche apportate al progetto quando si aggiunge il servizio connesso.

## <a name="requiring-authentication-to-access-controllers"></a>Richiesta di autenticazione ai controller di accesso

Tutti i controller del progetto sono dotati dell'attributo `[Authorize]`. Questo attributo richiede che l'utente venga autenticato prima di accedere ai controller. Per permettere l'accesso anonimo al controller, rimuovere l'attributo dal controller. Per configurare le autorizzazioni con un livello di granularità superiore, applicare l'attributo a ogni metodo che necessita di autorizzazione invece di applicarlo alla classe controller.

## <a name="adding-signin--signout-controls"></a>Aggiunta di controlli SignIn/SignOut

Per aggiungere i controlli SignIn/SignOut alla visualizzazione, è possibile usare la visualizzazione parziale `_LoginPartial.cshtml` per aggiungere la funzionalità a una delle visualizzazioni. L'esempio seguente illustra l'aggiunta di funzionalità alla visualizzazione `_Layout.cshtml` standard. (Notare l'ultimo elemento nella sezione div con class navbar-collapse):

```html
<!DOCTYPE html>
 <html>
 <head>
     <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>Passaggi successivi

- [Scenari di autenticazione per Azure Active Directory](authentication-scenarios.md)
- [Aggiungere l'accesso con Microsoft a un'app Web ASP.NET](quickstart-v1-aspnet-webapp.md)
