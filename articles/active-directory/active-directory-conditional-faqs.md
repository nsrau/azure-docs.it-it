<properties
	pageTitle="Domande frequenti sull'accesso condizionale di Azure Active Directory | Microsoft Azure"
	description="Domande frequenti sull'accesso condizionale "
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="markvi"/>

# Domande frequenti sull'accesso condizionale di Azure Active Directory

## Quali applicazioni funzionano con i criteri di accesso condizionale?

**R:** Vedere l'argomento [Supporto dell'accesso condizionale per le applicazioni](active-directory-conditional-access-supported-apps.md).

## I criteri di accesso condizionale vengono applicati a Collaborazione B2B e agli utenti guest?

**R:** I criteri vengono applicati agli utenti di Collaborazione B2B. In alcuni casi tuttavia l'utente potrebbe non soddisfare i criteri richiesti, ad esempio se l'organizzazione non supporta l'autenticazione a più fattori.

I criteri non sono attualmente applicati agli utenti guest di SharePoint. La relazione guest viene gestita in SharePoint. Gli account utente guest non sono soggetti ai criteri di accesso nel server di autenticazione. L'accesso guest può essere gestito in SharePoint.

## I criteri di SharePoint Online si applicano anche a OneDrive for Business?

**R:** Sì.
 
## Perché non è possibile impostare criteri nelle app client, ad esempio Word o Outlook?

**R:** I criteri di accesso condizionale stabiliscono requisiti per l'accesso a un servizio e vengono applicati al momento dell'autenticazione al servizio in questione. I criteri non sono quindi impostati direttamente su un'applicazione client, ma vengono applicati quando quest'ultima effettua una chiamata a un servizio. Ad esempio, i criteri impostati in SharePoint verranno applicati ai client che chiamano SharePoint e i criteri impostati in Exchange verranno applicati ad Outlook.


## Un criterio di accesso condizionale è applicabile agli account del servizio?

**R:** I criteri di accesso condizionale vengono applicati a tutti gli account utente, inclusi gli account utente usati come account del servizio. In molti casi un account del servizio in esecuzione automatica non è in grado di rispettare un criterio, ad esempio nel caso in cui sia necessaria l'autenticazione MFA. In questi casi gli account del servizio possono essere esclusi da un criterio mediante le impostazione di gestione dei criteri di accesso condizionale. Per altre informazioni sull'applicazione di un criterio agli utenti, vedere qui.

<!---HONumber=AcomDC_0727_2016-->