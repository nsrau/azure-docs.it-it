<properties
   pageTitle="Informazioni sull'applicazione Tailspin Surveys | Microsoft Azure"
   description="Panoramica dell'applicazione Tailspin Surveys"
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

# Informazioni sull'applicazione Tailspin Surveys

Questo articolo fa [parte di una serie]. Per questa serie è anche disponibile un'[applicazione di esempio] completa.

Tailspin è una società fittizia che sta sviluppando un'applicazione SaaS denominata Surveys. Questa applicazione consente alle organizzazioni di creare e pubblicare sondaggi online.

- Le organizzazioni possono iscriversi per l'applicazione.
- Dopo aver iscritto l'organizzazione, gli utenti possono accedere all'applicazione con le proprie credenziali aziendali.
- Gli utenti possono creare, modificare e pubblicare i sondaggi.

> [AZURE.NOTE] Per iniziare con l'applicazione, vedere [Esecuzione dell'applicazione Surveys].

Questa schermata mostra la pagina Edit Survey:

![Modifica del sondaggio](media/guidance-multitenant-identity/edit-survey.png)

Si noti che l'utente è connesso con la propria identità aziendale, `bob@contoso.com`.

Gli utenti possono visualizzare i sondaggi creati da altri utenti all'interno dello stesso tenant.

![Sondaggi del tenant](media/guidance-multitenant-identity/tenant-surveys.png)

Quando crea un sondaggio, l'utente può invitare altre persone a collaborare nel sondaggio. I collaboratori possono modificare il sondaggio, ma non possono eliminarlo o pubblicarlo.

![Aggiungere un collaboratore](media/guidance-multitenant-identity/add-contributor.png)

L'utente può aggiungere collaboratori di altri tenant consentendo in questo modo la condivisione di risorse tra tenant. In questa schermata, Bob (`bob@contoso.com`) aggiunge Alice (`alice@fabrikam.com`) come collaboratore in un sondaggio creato da Bob.

Quando Alice esegue l'accesso, il sondaggio sarà visualizzato in "Surveys I can contribute to".

![Collaboratore del sondaggio](media/guidance-multitenant-identity/contributor.png)

Si noti che Alice accede al proprio tenant, non accede come guest del tenant Contoso. Alice dispone delle autorizzazioni di collaboratore solo per tale sondaggio. Alice non può visualizzare altri sondaggi dal tenant Contoso.

## Architettura

L'applicazione Surveys è costituita da un'API Web di front-end e un'API Web di back-end. Entrambe le API vengono implementate usando [ASP.NET Core 1.0].

L'applicazione Web usa Azure Active Directory (Azure AD) per l'autenticazione degli utenti. L'applicazione Web chiama inoltre Azure AD per ottenere i token di accesso OAuth 2 per l'API Web. I token di accesso sono memorizzati nella cache in Cache Redis di Azure. La cache consente a più istanze di condividere la stessa cache dei token (ad esempio, in una server farm).

![Architettura](media/guidance-multitenant-identity/architecture.png)

<!-- Links -->
[parte di una serie]: guidance-multitenant-identity.md
[Esecuzione dell'applicazione Surveys]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[ASP.NET Core 1.0]: https://docs.asp.net/en/latest/
[applicazione di esempio]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->