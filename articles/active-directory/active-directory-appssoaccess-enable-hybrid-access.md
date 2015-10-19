<properties
	pageTitle="Abilitazione dell'accesso ibrido con il proxy dell'app| Microsoft Azure"
	description="Abilitare l'accesso alle applicazioni in esecuzione all'interno della rete privata dall'esterno della rete tramite Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/06/2015"
	ms.author="femila"/>

#Abilitazione dell'accesso ibrido con il proxy dell'app
Con il proxy dell'applicazione di Microsoft Azure Active Directory è possibile pubblicare applicazioni in esecuzione all'interno della rete privata, per potervi accedere in modo sicuro su qualsiasi dispositivo dall'esterno della rete. Il proxy dell'app di Azure AD può essere usato per pubblicare applicazioni all'interno della rete cloud o locale privata. Può essere configurato come un servizio predefinito all'interno di Azure AD e richiede solo l'installazione di un connettore Proxy applicazione in un server all'interno dell'ambiente.

##Funzionamento

La procedura seguente elenca le modalità di pubblicazione delle applicazioni in un ambiente ibrido.
 
1.	I connettori vengono distribuiti nella rete aziendale. Si possono distribuire più connettori ai fini della ridondanza e della scalabilità.
2.	Il connettore si connette automaticamente al servizio cloud.
3.	L'utente si connette al servizio cloud che inoltra il relativo traffico alle risorse tramite i connettori.

Il diagramma seguente fornisce altri dettagli sul funzionamento del proxy dell'applicazione:

 ![Funzionamento di proxy dell'applicazione di Azure AD](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

 
1.	L'utente accede all'applicazione tramite il proxy dell'applicazione e viene reindirizzato alla pagina di accesso di Azure AD per l'autenticazione.
2.	Una volta che l'accesso è riuscito, un token viene generato e inviato all'utente.
3.	L'utente invia il token al proxy dell'applicazione che recupera dal token il nome dell'entità utente (UPN) e il nome dell'entità sicurezza (SPN) e indirizza la richiesta al connettore.
4.	Il connettore rappresenta l'utente per richiedere un ticket Kerberos che può essere usato per l'autenticazione interna (Windows). Delega vincolata Kerberos
5.	 Un ticket Kerberos viene recuperato da Active Directory.
6.	Il ticket recuperato viene inviato al server applicazioni dove viene verificato.
7.	 La risposta verrà inviata tramite il proxy dell'applicazione all'utente finale. C'è solo un requisito per la pubblicazione di un'applicazione Web: l'applicazione Web nella rete privata deve essere accessibile dal server in cui viene installato il connettore. In altre parole, è possibile installare il connettore negli stessi server applicazioni o in qualsiasi altro server all'interno dell'ambiente. L'importante è che il connettore possa accedere all'applicazione Web.

##Come integrare le applicazioni locali tramite il proxy dell'applicazione di AD Azure
Il pannello di accesso di Azure AD consente di pubblicare le applicazioni locali in Azure AD. Ecco la procedura che è necessario seguire:

1. Abilitare il proxy dell'applicazione in Azure AD e installare e registrare il connettore. Per istruzioni dettagliate, vedere [Abilitazione del proxy dell'applicazione di Azure AD](active-directory-application-proxy-enable/#step-1-enable-application-proxy-in-azure-ad.md).
2. Pubblicare applicazioni usando il proxy dell'applicazione di AD Azure: per istruzioni dettagliate, vedere [Pubblicare applicazioni mediante il proxy dell'applicazione di AD Azure](active-directory-application-proxy-publish.md).

<!---HONumber=Oct15_HO2-->