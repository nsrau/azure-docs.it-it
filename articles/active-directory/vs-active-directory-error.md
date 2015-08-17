<properties 
	pageTitle="Errore durante il rilevamento dell'autenticazione" 
	description="La procedura guidata di connessione di Active Directory ha rilevato un tipo di autenticazione incompatibile." 
	services="active-directory" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="patshea"/>


# Errore durante il rilevamento dell'autenticazione

Durante il rilevamento di codice di autenticazione precedente, la procedura guidata ha rilevato un tipo di autenticazione non compatibile.

##Elementi verificati

**Nota:** per rilevare correttamente il precedente codice di autenticazione in un progetto, è necessario che il progetto sia compilato. Se si è verificato questo errore e non si ha il precedente codice di autenticazione del progetto, ricompilare e riprovare.

###Tipi di progetto

La procedura guidata verifica il tipo di progetto di cui è in corso lo sviluppo, in modo da potervi inserire la logica di autenticazione corretta. Se nel progetto è presente un controller che deriva da `ApiController`, verrà considerato come un progetto WebAPI. Se nel progetto sono presenti solo controller che derivano da `MVC.Controller`, verrà considerato come un progetto MVC. Qualsiasi altro elemento verrà considerato come non supportato dalla procedura guidata. I progetti WebForms non sono al momento supportati.

###Codice di autenticazione compatibile

La procedura guidata cerca inoltre le impostazioni di autenticazione configurate in precedenza o che sono compatibili. Se sono presenti tutte le impostazioni, viene considerato come caso rientrante, di conseguenza la procedura guidata verrà aperta e visualizzerà le impostazioni. Se sono presenti solo alcune impostazioni, verrà considerato come caso di errore.

In un progetto MVC la procedura guidata cerca le impostazioni seguenti che derivano da usi precedenti della procedura guidata:

	<add key="ida:ClientId" value="" />

	<add key="ida:Tenant" value="" />

	<add key="ida:AADInstance" value="" />

	<add key="ida:PostLogoutRedirectUri" value="" />


La procedura guidata cerca inoltre le impostazioni seguenti di un progetto API Web che derivano da usi precedenti della procedura guidata:

	<add key="ida:ClientId" value="" />

	<add key="ida:Tenant" value="" />

	<add key="ida:Audience" value="" />


###Codice di autenticazione incompatibile

La procedura guidata prova infine a rilevare le versioni del codice di autenticazione configurate con le versioni precedenti di Visual Studio. Se viene visualizzato questo errore, significa che il progetto contiene un tipo di autenticazione non compatibile. La procedura guidata rileva i tipi seguenti di autenticazione dalle versioni precedenti di Visual Studio:

* Autenticazione di Windows 
* Account utente singoli 
* Account dell'organizzazione 
 

Per individuare Autenticazione di Windows in un progetto MVC, la procedura guidata cerca l'elemento `authentication` nel file **web.config**.

```
	&lt;configuration&gt;
	    &lt;system.web&gt;
	        <span style="background-color: yellow">&lt;authentication mode="Windows" /&gt;</span>
	    &lt;/system.web&gt;
	&lt;/configuration&gt;
```

Per individuare Autenticazione di Windows in un progetto API Web, la procedura guidata cerca l'elemento `IISExpressWindowsAuthentication` nel file con estensione **csproj** del progetto:

```
	&lt;Project&gt;
	    &lt;PropertyGroup&gt;
	        <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;</span>
	    &lt;/PropertyGroup>
	&lt;/Project&gt;
```

Per individuare l'autenticazione per singoli account utente, la procedura guidata cerca l'elemento package dal file **Packages.config**.

```
	&lt;packages&gt;
	    <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /&gt;</span>
	&lt;/packages&gt;
```

Per individuare una precedente forma di autenticazione di tipo account aziendale, la procedura guidata cerca il seguente elemento dal file **web.config**:

```
	&lt;configuration&gt;
	    &lt;appSettings&gt;
	        <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /&gt;</span>
	    &lt;/appSettings&gt;
	&lt;/configuration&gt;
```

Per cambiare il tipo di autenticazione, rimuovere il tipo non compatibile ed eseguire di nuovo la procedura guidata.

Per altre informazioni, vedere [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md).
 

<!---HONumber=August15_HO6-->