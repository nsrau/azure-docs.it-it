<properties
	pageTitle="Servizio di sincronizzazione Azure Active Directory Connect: come gestire l'account del servizio Azure Active Directory | Microsoft Azure"
	description="In questo argomento viene illustrato come ripristinare l'account del servizio Azure Active Directory."
	services="active-directory"
    keywords="AADSTS70002, AADSTS50054, Come reimpostare la password dell'account del servizio di sincronizzazione Azure AD Connect"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/27/2016"
	ms.author="andkjell"/>

# Servizio di sincronizzazione Azure Active Directory Connect: come gestire l'account del servizio Azure Active Directory
L'account di servizio utilizzato da Azure Active Directory Connector è progettato per non richiedere manutenzione. Tuttavia, in questo argomento viene descritto come reimpostare le credenziali in caso di necessità, ad esempio se un amministratore globale ha erroneamente reimpostato la password dell'account del servizio con PowerShell.

## Reimpostazione delle credenziali
Se l'account del servizio definito in Azure Active Directory Connector non riesce a contattare Azure Active Directory a causa di problemi di autenticazione, è possibile reimpostare la password.

1. Effettuare l'accesso al server di sincronizzazione Azure Active Directory Connect e avviare PowerShell.
2. Eseguire `Add-ADSyncAADServiceAccount`. ![Cmdlet PowerShell addadsyncaadserviceaccount](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Fornire le credenziali di amministratore globale di Azure Active Directory.

Con questo cmdlet verrà ripristinata la password dell'account del servizio, che verrà aggiornata sia in Azure Active Directory sia nel motore di sincronizzazione.

## Problemi noti che possono essere risolti con questi passaggi
Questo è un elenco di errori segnalati dai clienti che sono stati risolti con i passaggi seguenti.

-----------
Evento 6900 Il server ha rilevato un errore imprevisto durante l'elaborazione di una notifica di modifica password: AADSTS70002: errore durante la convalida delle credenziali. AADSTS50054: Per l'autenticazione verrà usata la password precedente.

----------
Evento 659 Errore durante il recupero della configurazione della sincronizzazione dei criteri password. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Errore di convalida delle credenziali. AADSTS50054: Per l'autenticazione verrà usata la password precedente.

## Passaggi successivi
Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0629_2016-->