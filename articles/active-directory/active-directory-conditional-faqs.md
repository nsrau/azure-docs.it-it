<properties
	pageTitle="Domande frequenti sull'accesso condizionale | Microsoft Azure"
	description="Domande frequenti sull'accesso condizionale "
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="femila"/>

# Domande frequenti sull'accesso condizionale

## Quali applicazioni funzionano con i criteri di accesso condizionale?
Vedere l'argomento [Accesso condizionale - Applicazioni supportate](active-directory-conditional-access-supported-apps.md).

## I criteri di accesso condizionale vengono applicati a Collaborazione B2B e agli utenti guest?
I criteri vengono applicati agli utenti di Collaborazione B2B. Tuttavia, in alcuni casi l'utente potrebbe non soddisfare i criteri richiesti, ad esempio se l'organizzazione non supporta l'autenticazione Multi-Factor Authentication. Al momento i criteri non sono applicati agli utenti guest di SharePoint. La relazione guest viene comunque mantenuta all'interno di SharePoint, pertanto gli account degli utenti guest non sono soggetti a criteri di accesso al server di autenticazione. L'accesso guest può essere gestito in SharePoint.

## I criteri di SharePoint Online si applicano anche a OneDrive for Business?
Sì.
 
## Perché non è possibile impostare criteri nelle app client, ad esempio Word o Outlook?
I criteri di accesso condizionale stabiliscono requisiti per l'accesso a un servizio e vengono applicati al momento dell'autenticazione al servizio in questione. Pertanto, i criteri non sono impostati direttamente su un'applicazione client, ma vengono applicati quando quest'ultima effettua una chiamata a un servizio. Ad esempio, i criteri impostati in SharePoint verranno applicate ai client che chiamano SharePoint e i criteri impostati in Exchange verranno applicati ad Outlook.

<!---HONumber=AcomDC_0629_2016-->