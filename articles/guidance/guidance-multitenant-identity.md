<properties
   pageTitle="Gestione delle identità per le applicazioni multi-tenant | Microsoft Azure"
   description="Procedure consigliate per l'autenticazione, l'autorizzazione e la gestione delle identità in applicazioni multi-tenant."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Gestione delle identità per le applicazioni multi-tenant in Microsoft Azure

Vengono descritte le procedure consigliate per il multi-tenancy, quando si usa Azure AD per l'autenticazione e la gestione delle identità.

Quando si crea un'applicazione multi-tenant, una delle attività principali è la gestione delle identità degli utenti, ognuno dei quali appartiene ora a un tenant. Ad esempio:

- Gli utenti accedono con le credenziali dell'organizzazione.
- Gli utenti devono avere accesso ai dati dell'organizzazione ma non ai dati appartenenti ad altri tenant.
- Un'organizzazione può effettuare l'iscrizione per l'applicazione e quindi assegnare i ruoli applicazione ai propri membri.

Azure Active Directory (Azure AD) include alcune funzionalità molto utili in tutti questi scenari.

A completamento di questa serie di articoli, è stata creata anche un'[implementazione end-to-end][tailspin] completa di un'applicazione multi-tenant. Gli articoli riflettono quanto appreso nel processo di compilazione dell'applicazione. Per iniziare con l'applicazione, vedere [Esecuzione dell'applicazione Surveys](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md).


## Sommario

- [Introduzione](guidance-multitenant-identity-intro.md)
- [Informazioni sull'applicazione Tailspin Surveys](guidance-multitenant-identity-tailspin.md)
- [Autenticazione con Azure AD](guidance-multitenant-identity-authenticate.md)
- [Uso delle identità basate sulle attestazioni](guidance-multitenant-identity-claims.md)
- [Iscrizione e caricamento del tenant](guidance-multitenant-identity-signup.md)
- [Definizione e gestione dei ruoli applicazione](guidance-multitenant-identity-app-roles.md)
- [Authorization](guidance-multitenant-identity-authorize.md)
- [Proteggere un'API Web di back-end](guidance-multitenant-identity-web-api.md)
- [Memorizzazione nella cache di token di accesso OAuth2](guidance-multitenant-identity-token-cache.md)
- [Federazione con il servizio AD FS del cliente](guidance-multitenant-identity-adfs.md)
- [Uso di asserzioni client per ottenere token di accesso da Azure AD](guidance-multitenant-identity-client-assertion.md)
- [Uso dell'insieme di credenziali delle chiavi per proteggere i segreti dell'applicazione](guidance-multitenant-identity-keyvault.md)

[tailspin]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->