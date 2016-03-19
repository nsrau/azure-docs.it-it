<properties
	pageTitle="Uso di nomi di dominio personalizzati per semplificare l'esperienza di accesso degli utenti | Microsoft Azure"
	description="Questo argomento illustra come aggiungere un nome di dominio personalizzato e informazioni correlate ad Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/08/2016"
	ms.author="curtand;jeffsta"/>

# Uso di nomi di dominio personalizzati per semplificare l'esperienza di accesso degli utenti

È possibile usare nomi di dominio personalizzati per migliorare e semplificare l'accesso e le altre esperienze utente in Azure Active Directory. Ad esempio, se l'azienda è proprietaria del nome di dominio "contoso.com", gli utenti possono accedere con nomi utente che conoscono, come "joe@contoso.com".

Ogni directory in Azure Active Directory include un nome di dominio predefinito nel formato "contoso.onmicrosoft.com" che permette di iniziare a usare Azure o altri servizi Microsoft. [Informazioni sui domini predefiniti](#built-in-domain-names-for-azure-active-directory).

## Usare il nome di dominio personalizzato con Azure AD

Se l'azienda ha un nome di dominio personalizzato che gli utenti conoscono bene, è consigliabile usare quello con Azure Active Directory. Per usare un nome di dominio personalizzato con Azure Active Directory, eseguire le operazioni indicate di seguito.

-   [Aggiungere e verificare il nome di dominio personalizzato](active-directory-add-domain-add-verify-general.md)

-   [Assegnare utenti a un dominio personalizzato](active-directory-add-domain-add-users.md)

## Usare il nome di dominio personalizzato con Office 365 e altri servizi

Come altre risorse in Azure Active Directory, i nomi di dominio personalizzati aggiunti e verificati possono essere usati con Office 365, Intune e altre applicazioni che usano Azure AD. Ad esempio, l'uso di un nome di dominio personalizzato con Exchange Online permette agli utenti di inviare e ricevere messaggi di posta elettronica a indirizzi familiari, come joe@contoso.com. Per consentire alle altre applicazioni di usare domini personalizzati è necessario aggiungere altre voci DNS presso il registrar DNS, come indicato dall'applicazione.

-   [Uso di domini personalizzati con Office 365](https://support.office.com/article/Add-your-users-and-domain-to-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611?ui=it-IT&rs=it-IT&ad=US)

-   [Uso di domini personalizzati con Intune](https://technet.microsoft.com/library/dn646966.aspx#BKMK_DomainNames)

## Gestire i nomi di dominio in Azure AD

I nomi di dominio non richiedono in genere alcuna amministrazione o gestione continuativa in Azure Active Directory.

-   [Visualizzare l'elenco di nomi di dominio in Azure Active Directory](active-directory-add-domain-add-users.md)

-   [Cosa fare se si modifica il registrar DNS per il nome di dominio personalizzato](active-directory-add-domain-change-registrar.md)

## Eliminare un nome di dominio personalizzato

È possibile eliminare da Azure AD i nomi di dominio personalizzati che l'organizzazione non usa più o che vuole usare in un'altra directory di Azure AD.

-   [Eliminare un nome di dominio personalizzato](#_Deleting_a_custom)

## Nomi di dominio predefiniti per Azure Active Directory

C'è differenza tra i domini predefiniti in Azure Active Directory (Azure AD) e i domini personalizzati che vengono aggiunti.

-   Predefinito: dominio fornito con la directory di Azure AD, nel formato contoso.onmicrosoft.com

-   Personalizzato: nome di dominio già di proprietà dell'organizzazione, ad esempio contoso.com

## Usare API Graph o PowerShell per gestire i nomi di dominio

La maggior parte delle attività di gestione relative ai nomi di dominio in Azure Active Directory può anche essere eseguita usando Microsoft PowerShell oppure API Graph a livello di programmazione.

-   [Uso di PowerShell per gestire i nomi di dominio in Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Uso di API Graph per gestire i nomi di dominio in Azure AD (anteprima)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)


## Passaggi successivi

Se sono necessarie risorse aggiuntive per comprendere l'utilizzo dei nomi di dominio in Azure Active Directory, vedere:

- [Aggiungere informazioni personalizzate distintive dell'azienda alle pagine Accedi e Pannello di accesso](active-directory-add-company-branding.md)
- [Aggiungere e verificare un nome di dominio personalizzato in Azure Active Directory](active-directory-add-domain-add-verify-general.md)
- [Assegnare utenti a un dominio personalizzato](active-directory-add-domain-add-users.md)
- [Cosa fare se si modifica il registrar DNS per il nome di dominio personalizzato](active-directory-add-domain-change-registrar.md)
- [Eliminare un dominio personalizzato in Azure Active Directory](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0211_2016-->