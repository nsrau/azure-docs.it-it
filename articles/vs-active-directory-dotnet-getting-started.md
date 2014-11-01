<properties title="Introduzione all'autenticazione di Active Directory" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Introduzione][Introduzione]
> -   [Risultati][Risultati]

## Introduzione a Azure Active Directory (progetto .NET)

##### Richiesta di autenticazione ai controller di accesso

Tutti i controller del progetto sono dotati dell'attributo **Authorize**. Questo attributo richiede l'autenticazione dell'utente prima dell'accesso ai controller. Per permettere l'accesso anonimo al controller, rimuovere l'attributo dal controller. Per configurare le autorizzazioni con un livello di granularità superiore, applicare l'attributo a ogni metodo che necessita di autorizzazione invece di applicarlo alla classe controller.

##### Aggiunta di controlli SignIn/SignOut

Per aggiungere i controlli SignIn/SignOut alla visualizzazione, è possibile usare la visualizzazione parziale \*\*\_LoginPartial.cshtml\*\* per aggiungere la funzionalità a una delle proprie visualizzazioni. Di seguito è riportato un esempio della funzionalità aggiunta alla visualizzazione standard \*\*\_Layout.cshtml\*\*. (Notare l'ultimo elemento nella sezione div con class navbar-collapse):

<pre class="prettyprint">
    &lt;!DOCTYPE html&gt; 
     &lt;html&gt; 
     &lt;head&gt; 
         &lt;meta charset=&quot;utf-8&quot; /&gt; 
        &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width, initial-scale=1.0&quot;&gt; 
        &lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
        @Styles.Render(&quot;~/Content/css&quot;) 
        @Scripts.Render(&quot;~/bundles/modernizr&quot;) 
    &lt;/head&gt; 
    &lt;body&gt; 
        &lt;div class=&quot;navbar navbar-inverse navbar-fixed-top&quot;&gt; 
            &lt;div class=&quot;container&quot;&gt; 
                &lt;div class=&quot;navbar-header&quot;&gt; 
                    &lt;button type=&quot;button&quot; class=&quot;navbar-toggle&quot; data-toggle=&quot;collapse&quot; data-target=&quot;.navbar-collapse&quot;&gt; 
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
                    &lt;/button&gt; 
                    @Html.ActionLink(&quot;Application name&quot;, &quot;Index&quot;, &quot;Home&quot;, new { area = &quot;&quot; }, new { @class = &quot;navbar-brand&quot; }) 
                &lt;/div&gt; 
                &lt;div class=&quot;navbar-collapse collapse&quot;&gt; 
                    &lt;ul class=&quot;nav navbar-nav&quot;&gt; 
                        &lt;li&gt;@Html.ActionLink(&quot;Home&quot;, &quot;Index&quot;, &quot;Home&quot;)&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink(&quot;About&quot;, &quot;About&quot;, &quot;Home&quot;)&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink(&quot;Contact&quot;, &quot;Contact&quot;, &quot;Home&quot;)&lt;/li&gt; 
                    &lt;/ul&gt; 
                    @Html.Partial(&quot;_LoginPartial&quot;) 
                &lt;/div&gt; 
            &lt;/div&gt; 
        &lt;/div&gt; 
        &lt;div class=&quot;container body-content&quot;&gt; 
            @RenderBody() 
            &lt;hr /&gt; 
            &lt;footer&gt; 
                &lt;p&gt;© @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
            &lt;/footer&gt; 
        &lt;/div&gt; 
        @Scripts.Render(&quot;~/bundles/jquery&quot;) 
        @Scripts.Render(&quot;~/bundles/bootstrap&quot;) 
        @RenderSection(&quot;scripts&quot;, required: false) 
    &lt;/body&gt; 
    &lt;/html&gt;
</pre>

[Ulteriori informazioni su Azure Active Directory][Ulteriori informazioni su Azure Active Directory]

  [Introduzione]: /documentation/articles/vs-active-directory-dotnet-getting-started/
  [Risultati]: /documentation/articles/vs-active-directory-dotnet-what-happened/
  [Ulteriori informazioni su Azure Active Directory]: http://azure.microsoft.com/services/active-directory/
