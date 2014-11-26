<properties title="Introduzione all'autenticazione di Active Directory" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Introduzione][Introduzione]
> -   [Risultati][Risultati]

## Introduzione a Azure Active Directory (progetti API Web)

##### Richiesta di autenticazione ai controller di accesso

Tutti i controller del progetto sono dotati dell'attributo **Authorize**. Questo attributo richiede l'autenticazione dell'utente prima dell'accesso alle API definite dai controller. Per permettere l'accesso anonimo al controller, rimuovere l'attributo dal controller. Per configurare le autorizzazioni con un livello di granularità superiore, applicare l'attributo a ogni metodo che necessita di autorizzazione invece di applicarlo alla classe controller.

[Ulteriori informazioni su Azure Active Directory][Ulteriori informazioni su Azure Active Directory]

  [Introduzione]: /documentation/articles/vs-active-directory-webapi-getting-started/
  [Risultati]: /documentation/articles/vs-active-directory-webapi-what-happened/
  [Ulteriori informazioni su Azure Active Directory]: http://azure.microsoft.com/services/active-directory/
