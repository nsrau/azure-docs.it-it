---
title: Introduzione ad Azure AD in progetti MVC di Visual Studio | Microsoft Docs
description: Come iniziare a utilizzare Azure Active Directory nei progetti MVC dopo la connessione o la creazione ad Azure AD utilizzando Visual Studio
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: kraigb
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 1b96ab084acae14b35634666ae465d406c25f75a
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017

---
# <a name="getting-started-with-azure-active-directory-and-visual-studio-connected-services-mvc-projects"></a>Introduzione ad Azure Active Directory e ai servizi relativi a Visual Studio (progetti MVC)
> [!div class="op_single_selector"]
> * [Per iniziare](vs-active-directory-dotnet-getting-started.md)
> * [Risultati](vs-active-directory-dotnet-what-happened.md)
> 
> 

## <a name="requiring-authentication-to-access-controllers"></a>Richiesta di autenticazione ai controller di accesso
Tutti i controller del progetto sono dotati dell'attributo **Authorize** . Questo attributo richiede che l'utente venga autenticato prima di accedere ai controller. Per permettere l'accesso anonimo al controller, rimuovere l'attributo dal controller. Per configurare le autorizzazioni con un livello di granularità superiore, applicare l'attributo a ogni metodo che necessita di autorizzazione invece di applicarlo alla classe controller.

## <a name="adding-signin--signout-controls"></a>Aggiunta di controlli SignIn/SignOut
Per aggiungere i controlli SignIn/SignOut alla visualizzazione, è possibile usare la visualizzazione parziale **_LoginPartial.cshtml** per aggiungere la funzionalità a una delle visualizzazioni. L'esempio seguente illustra l'aggiunta di funzionalità alla visualizzazione **_Layout.cshtml** standard. (Notare l'ultimo elemento nella sezione div con class navbar-collapse):

<pre>
    &lt;!DOCTYPE html&gt; 
     &lt;html&gt; 
     &lt;head&gt; 
         &lt;meta charset="utf-8" /&gt; 
        &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt; 
        &lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
        @Styles.Render("~/Content/css") 
        @Scripts.Render("~/bundles/modernizr") 
    &lt;/head&gt; 
    &lt;body&gt; 
        &lt;div class="navbar navbar-inverse navbar-fixed-top"&gt; 
            &lt;div class="container"&gt; 
                &lt;div class="navbar-header"&gt; 
                    &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt; 
                        &lt;span class="icon-bar"&gt;&lt;/span&gt; 
                        &lt;span class="icon-bar"&gt;&lt;/span&gt; 
                        &lt;span class="icon-bar"&gt;&lt;/span&gt; 
                    &lt;/button&gt; 
                    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) 
                &lt;/div&gt; 
                &lt;div class="navbar-collapse collapse"&gt; 
                    &lt;ul class="nav navbar-nav"&gt; 
                        &lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt; 
                    &lt;/ul&gt; 
                    <span style="background-color:yellow">@Html.Partial("_LoginPartial")</span> 
                &lt;/div&gt; 
            &lt;/div&gt; 
        &lt;/div&gt; 
        &lt;div class="container body-content"&gt; 
            @RenderBody() 
            &lt;hr /&gt; 
            &lt;footer&gt; 
                &lt;p&gt;&amp;copy; @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
            &lt;/footer&gt; 
        &lt;/div&gt; 
        @Scripts.Render("~/bundles/jquery") 
        @Scripts.Render("~/bundles/bootstrap") 
        @RenderSection("scripts", required: false) 
    &lt;/body&gt; 
    &lt;/html&gt;
</pre>

## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni su Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 


