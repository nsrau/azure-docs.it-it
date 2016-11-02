<properties
	pageTitle="Servizio di sincronizzazione Azure Active Directory Connect: come gestire l'account del servizio Azure Active Directory | Microsoft Azure"
	description="Questo argomento illustra come ripristinare l'account del servizio Azure AD."
	services="active-directory"
    keywords="AADSTS70002, AADSTS50054, Come reimpostare la password dell'account del servizio di sincronizzazione Azure AD Connect"
	documentationCenter=""
	authors="andkjell"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="andkjell"/>

# Servizio di sincronizzazione Azure Active Directory Connect: come gestire l'account del servizio Azure Active Directory
L'account di servizio utilizzato da Azure Active Directory Connector è progettato per non richiedere manutenzione. Questo argomento descrive come reimpostare le credenziali in caso di necessità, ad esempio se un amministratore globale ha erroneamente reimpostato la password dell'account del servizio con PowerShell.

## Reimpostazione delle credenziali
Se l'account del servizio definito in Azure Active Directory Connector non riesce a contattare Azure Active Directory a causa di problemi di autenticazione, è possibile reimpostare la password.

1. Effettuare l'accesso al server di sincronizzazione Azure AD Connect e avviare PowerShell.
2. Eseguire `Add-ADSyncAADServiceAccount`. ![Cmdlet PowerShell addadsyncaadserviceaccount](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Fornire le credenziali di amministratore globale di Azure Active Directory.

Questo cmdlet reimposta la password dell'account del servizio e la aggiorna sia in Azure AD, sia nel motore di sincronizzazione.

## Problemi noti che possono essere risolti con questi passaggi
Questa sezione riporta un elenco di errori segnalati dai clienti che sono stati corretti reimpostando le credenziali nell'account del servizio Azure AD.

-----------
Evento 6900 Il server ha rilevato un errore imprevisto durante l'elaborazione di una notifica di modifica password: AADSTS70002: errore durante la convalida delle credenziali. AADSTS50054: Per l'autenticazione verrà usata la password precedente.

----------
Evento 659 Errore durante il recupero della configurazione della sincronizzazione dei criteri password. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Errore di convalida delle credenziali. AADSTS50054: Per l'autenticazione verrà usata la password precedente.

## Passaggi successivi

**Argomenti generali**

- [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
- [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0907_2016-->