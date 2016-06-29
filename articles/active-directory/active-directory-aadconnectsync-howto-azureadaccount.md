<properties
	pageTitle="Servizio di sincronizzazione Azure Active Directory Connect: come gestire l'account del servizio Azure Active Directory | Microsoft Azure"
	description="In questo argomento viene illustrato come ripristinare l'account del servizio Azure Active Directory."
	services="active-directory"
    keywords="Come reimpostare la password dell'account del servizio di sincronizzazione Azure Active Directory Connect"
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
	ms.date="06/10/2016"
	ms.author="andkjell"/>

# Servizio di sincronizzazione Azure Active Directory Connect: come gestire l'account del servizio Azure Active Directory
L'account di servizio utilizzato da Azure Active Directory Connector è progettato per non richiedere manutenzione. Tuttavia, in questo argomento viene descritto come reimpostare le credenziali in caso di necessità, ad esempio se un amministratore globale ha erroneamente reimpostato la password dell'account del servizio con PowerShell.

## Reimpostazione delle credenziali
Se l'account del servizio definito in Azure Active Directory Connector non riesce a contattare Azure Active Directory a causa di problemi di autenticazione, è possibile reimpostare la password.

1. Effettuare l'accesso al server di sincronizzazione Azure Active Directory Connect e avviare PowerShell.
2. Eseguire `Add-ADSyncAADServiceAccount`. ![Cmdlet PowerShell addadsyncaadserviceaccount](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Fornire le credenziali di amministratore globale di Azure Active Directory.

Con questo cmdlet verrà ripristinata la password dell'account del servizio, che verrà aggiornata sia in Azure Active Directory sia nel motore di sincronizzazione.

## Passaggi successivi
Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0615_2016-->