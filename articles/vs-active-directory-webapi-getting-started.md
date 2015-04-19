<properties 
	pageTitle="" 
	description="Informazioni sulla procedura guidata iniziale di Azure Active Directory (progetti API Web)." 
	services="active-directory" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Per iniziare](vs-active-directory-webapi-getting-started.md)
> - [Risultati](vs-active-directory-webapi-what-happened.md)


##Introduzione a Azure Active Directory (progetti API Web)

#####Richiesta di autenticazione ai controller di accesso
 
A tutti i controller del progetto è stato assegnato l'attributo **Authorize**. Questo attributo richiede l'autenticazione dell'utente prima dell'accesso alle API definite dai controller. Per permettere l'accesso anonimo al controller, rimuovere l'attributo dal controller. Per configurare le autorizzazioni con un livello di granularità superiore, applicare l'attributo a ogni metodo che necessita di autorizzazione invece di applicarlo alla classe controller.

[Altre informazioni su Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!--HONumber=46--> 
