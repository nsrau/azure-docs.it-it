<properties 
	pageTitle="Introduzione ad Azure Active Directory e ai servizi relativi a Visual Studio (progetti WebApi) |Microsoft Azure" 
	description="Come iniziare a utilizzare Azure Active Directory nei progetti WebApi dopo la connessione o la creazione ad Azure AD utilizzando i servizi relativi a Visual Studio" 
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
	ms.date="09/03/2015" 
	ms.author="patshea123"/>

# Introduzione ad Azure Active Directory e ai servizi relativi a Visual Studio (progetti WebApi)

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

##Richiesta di autenticazione ai controller di accesso
 
Tutti i controller del progetto sono dotati dell'attributo **Authorize**. Questo attributo richiede l'autenticazione dell'utente prima dell'accesso alle API definite dai controller. Per permettere l'accesso anonimo al controller, rimuovere l'attributo dal controller. Per configurare le autorizzazioni con un livello di granularità superiore, applicare l'attributo a ogni metodo che necessita di autorizzazione invece di applicarlo alla classe controller.

[Altre informazioni su Azure Active Directory](http://azure.microsoft.com/services/active-directory/)
 

<!---HONumber=Sept15_HO2-->