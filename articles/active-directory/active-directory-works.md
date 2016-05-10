<properties
	pageTitle="Come funziona Azure Active Directory? | Microsoft Azure"
	description="Azure Active Directory crea un panorama di identità proprio dell'utente nel cloud. Può essere collegato al sistema di identità locale o usato in modo indipendente."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="curtand"/>



# Come funziona Azure Active Directory?

###Altri articoli su questo argomento
[Che cos'è Azure AD?](active-directory-whatis.md)<br> [Come funziona?](active-directory-works.md)<br> [Introduzione](active-directory-get-started.md)<br> [Passaggi successivi](active-directory-next-steps.md)<br> [Altre informazioni](active-directory-learn-map.md)


Azure Active Directory (Azure AD) crea un panorama di identità proprio dell'utente nel cloud. Può essere collegato al sistema di identità locale o usato in modo indipendente.

È possibile considerare un account in Azure AD come una patente di guida per il cloud: rappresenta l'ID univoco per l'accesso ai servizi online. In tal senso, Azure AD funziona come il proprio registrar privato nel cloud per quelle patenti di guida. Consente di usare le identità ovunque nel cloud e migliora la mobilità per gli utenti che accedono a risorse locali.

> [AZURE.NOTE] Per usare Azure Active Directory, è necessario un account Azure. Se non si dispone di un account, è possibile [iscriversi per un account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/).

## In che modo Azure AD supporta Office 365, Microsoft Intune e altri servizi di Azure?
Il portale di Azure, l'interfaccia di amministrazione di Office 365, il portale degli account di Microsoft Intune e i cmdlet dei moduli di Azure AD PowerShell leggono e scrivono tutti in una singola istanza condivisa di Azure AD associata alla directory. I portali (o i cmdlet) funzionano come un'interfaccia front-end che effettua il pull o modifica le informazioni della directory. [Altre informazioni sul supporto per altri servizi](active-directory-administer.md#what-is-an-azure-ad-tenant)

## In che modo Azure AD supporta applicazioni di terze parti?
Azure AD semplifica l'autenticazione per gli sviluppatori offrendo l'identità come un servizio, nonché librerie open source per diverse piattaforme per consentire di iniziare rapidamente a codificare. [Altre informazioni sugli scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md).


## In che modo Azure AD estende la directory personale locale?
Azure AD supporta diversi dei protocolli di autenticazione e autorizzazione più ampiamente usati. [Altre informazioni sui protocolli di autenticazione di Azure Active Directory](active-directory-authentication-scenarios.md).

## In che modo Azure consente di gestire le identità?
Si intende approfondire la conoscenza delle modalità di gestione di Azure AD? Come ottenere una directory? Come eliminare una directory? Come gestire i dati di directory? Altre informazioni sull'amministrazione di directory di Azure AD. [Altre informazioni su come amministrare Azure AD](active-directory-administer.md).

## Risorse aggiuntive

* [Iscriversi ad Azure come organizzazione](sign-up-organization.md)
* [Identità di Azure](fundamentals-identity.md)

<!---HONumber=AcomDC_0427_2016-->