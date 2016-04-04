<properties
	pageTitle="Anteprima di Azure Active Directory B2C: integrità del servizio | Microsoft Azure"
	description="Notifiche su problemi Azure Active Directory B2C secondari, stato e soluzioni di attenuazione"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="swkrish"/>

# Anteprima di Azure Active Directory B2C: integrità del servizio

Questa pagina fornisce notifiche relative a problemi secondari del servizio, allo stato e alle mitigazioni. È possibile creare un segnalibro su questa pagina per avere un riferimento futuro. Continuare a monitorare anche il [dashboard dello stato di Azure](https://azure.microsoft.com/status/).

### 22/03/2016: bug SSO su tenant B2C

Un bug SSO (Single Sign On) è stato introdotto alle 13.00 del 17/03/2016 ed è stato risolto alle 10.00 PST del 18/03/2016. In questo periodo di tempo, sono stati interessati < 100 utenti. Stiamo continuando a monitorare attentamente questo evento. Condizioni per l’utente per cui si è verificato il bug:

1. È stato impostato un criterio di accesso "account locale" come unico provider di identità configurato.
2. Un utente accede correttamente ad Azure AD B2C la prima volta.
3. Il consumer tenta di accedere nuovamente, ma anziché ottenere l’accesso Single Sign-On, viene visualizzato un messaggio di errore.

L’unica soluzione per l’utente (dopo il passaggio 3) è stata chiudere e riaprire il browser ed eseguire nuovamente l’autenticazione.

<!---HONumber=AcomDC_0323_2016-->