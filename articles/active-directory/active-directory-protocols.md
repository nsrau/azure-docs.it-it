<properties
	pageTitle="Protocolli di autenticazione di Active Directory | Microsoft Azure"
	description="Questo articolo offre una panoramica dei vari protocolli di autenticazione e autorizzazione supportati da Azure Active Directory."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="priyamo"/>

# Protocolli di autenticazione di Active Directory

Azure Active Directory (Azure AD) supporta diversi dei protocolli di autenticazione e autorizzazione più ampiamente usati.

In questo set di articoli verranno esaminati i protocolli supportati e la relativa implementazione in Azure AD. Gli articoli contengono richieste e risposte di esempio e, grazie all'integrazione diretta con i protocolli, sono prevalentemente indipendenti dal linguaggio.

- [OAuth 2.0 in Azure AD](active-directory-protocols-oauth-code.md): informazioni sul flusso di concessione delle autorizzazioni di OAuth2.0 e sulla relativa implementazione in Azure AD.
- [OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md): informazioni su come usare il protocollo di autenticazione OpenID Connect in Azure AD.
- [Informazioni di riferimento sul protocollo SAML](active-directory-saml-protocol-reference.md): informazioni su come usare il protocollo SAML per supportare [Single Sign-On](active-directory-single-sign-on-protocol-reference.md) e [Single Sign-Out](active-directory-single-sign-out-protocol-reference.md) in Azure AD.


## Informazioni di riferimento e risoluzione dei problemi

Questo set di articoli offre informazioni aggiuntive che potrebbero risultare utili per risolvere i problemi delle applicazioni di Azure AD nonché per una conoscenza più approfondita di Azure AD.

- [Metadati della federazione](active-directory-federation-metadata.md): informazioni su come trovare e interpretare i documenti di metadati generati da Azure AD.
- [Token e tipi di attestazioni supportati](active-directory-token-and-claims.md): informazioni sulle diverse attestazioni dei token rilasciati da Azure AD.
- [Rollover della chiave di firma in Azure AD](active-directory-signing-key-rollover.md): informazioni sulla cadenza di rollover della chiave di firma di Azure AD e su come aggiornare la chiave per gli scenari di applicazione più comuni.
- [Risoluzione dei problemi dei protocolli di autenticazione](active-directory-error-handling.md): informazioni su come interpretare e risolvere gli errori più comuni quando si usano OAuth 2.0 e Azure AD.
- [Procedure consigliate per OAuth 2.0 in Azure Active Directory](active-directory-oauth-best-practices.md): informazioni sulle procedure consigliate per usare OAuth 2.0 in Azure AD ed evitare inconvenienti comuni.

<!---HONumber=AcomDC_0608_2016-->