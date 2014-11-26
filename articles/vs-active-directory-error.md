<properties title="Errore durante il rilevamento dell'autenticazione" pageTitle="Errore durante il rilevamento dell'autenticazione" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

### Errore durante il rilevamento dell'autenticazione

Durante il rilevamento di codice di autenticazione precedente, la procedura guidata ha rilevato un tipo di autenticazione non compatibile.

##### Elementi verificati

La procedura guidata tenta di rilevare le versioni del codice di autenticazione configurate con le versioni precedenti di Visual Studio. Se si riceve questo errore, significa che il progetto contiene un tipo di autenticazione non compatibile. La procedura guidata rileva i tipi seguenti di autenticazione dalle versioni precedenti di Visual Studio:

-   Autenticazione di Windows
-   Account utente singoli
-   Account dell'organizzazione

Per individuare Autenticazione Windows in un progetto MVC, la procedura guidata cerca l'elemento `authentication` dal file **web.config**.

<pre class="prettyprint">
    &lt;configuration&gt;
        &lt;system.web&gt;
            &lt;authentication mode=&quot;Windows&quot; /&gt;
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Per individuare Autenticazione Windows in un progetto API Web, la procedura guidata cerca l'elemento `IISExpressWindowsAuthentication` dal file **.csproj** del progetto:

<pre class="prettyprint">
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            &lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;
        &lt;/PropertyGroup&gt;
    &lt;/Project&gt;
</pre>

Per individuare l'autenticazione per singoli account utente, la procedura guidata cerca l'elemento package dal file **Packages.config**.

<pre class="prettyprint">
    &lt;packages&gt;
        &lt;package id=&quot;Microsoft.AspNet.Identity.EntityFramework&quot; version=&quot;2.1.0&quot; targetFramework=&quot;net45&quot; /&gt;
    &lt;/packages&gt;
</pre>

Per individuare una precedente forma di autenticazione di tipo account aziendale, la procedura guidata cerca il seguente elemento dal file **web.config**:

<pre class="prettyprint">
    &lt;configuration*gt;
        &lt;appSettings&gt;
            &lt;add key=&quot;ida:Realm&quot; value=&quot;***&quot; /&gt;
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

Per cambiare il tipo di autenticazione, rimuovere il tipo non compatibile ed eseguire di nuovo la procedura guidata.

Per altre informazioni, vedere [Scenari di autenticazione per Azure AD][Scenari di autenticazione per Azure AD].

  [Scenari di autenticazione per Azure AD]: http://msdn.microsoft.com/library/azure/dn499820.aspx
